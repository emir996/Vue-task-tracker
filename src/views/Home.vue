<template>
    
    <AddTask v-show="showAddTask" @add-task="addTask"/>
    
    <Tasks @toggle-reminder="toggleReminder" @select-task="selectTask" @delete-task="deleteTask" :tasks="tasks" />
    <UpdateTask @close-update-form="closeUpdateForm" @update-task="updateTask" v-show="showUpdateForm" :selectedTask="selectedTask"/>
</template>

<script>
    import Tasks from '../components/Tasks'
    import AddTask from '../components/AddTask'
    import UpdateTask from '../components/UpdateTask'

    export default {
        name: 'Home',
        props: {
            showAddTask: Boolean,
        },
        components: {
            Tasks,
            AddTask,
            UpdateTask
        },
        data(){
            return {
                tasks: [],
                showUpdateForm: false,
                selectedTask: {}
            }
        },
        methods: {
        async updateTask(task, closeForm){
            
            const taskToUpdate = await this.fetchTask(task.id)

            const updateTask = {...taskToUpdate, id: task.id, text: task.text, day: task.day}

            const res = await fetch(`api/tasks/${task.id}`,{
                method: 'PUT',
                headers: {
                    'Content-type': 'application/json'
                },
                body: JSON.stringify(updateTask)
            })

            const data = await res.json()
            
            this.tasks = this.tasks.map((task) => task.id === taskToUpdate.id ? {...task, text: data.text, day: data.day, reminder: data.reminder} : task)

            this.showUpdateForm = closeForm
        },
        selectTask(task){
            this.showUpdateForm = true
            const selectedTask = {
                id: task.id,
                text: task.text,
                day: task.day,
            }

            this.selectedTask = {...selectedTask}
        },
        closeUpdateForm(){
            this.showUpdateForm = false
        },
        async deleteTask(id){
        if(confirm('Are you sure')){

            const res = await fetch(`api/tasks/${id}`, {
            method: 'DELETE'
            })

            res.status === 200 ? (this.tasks = this.tasks.filter((task) => task.id !== id)) : alert('Error deleteing task')

        }
        },
        async toggleReminder(id){

        const taskToToggle = await this.fetchTask(id)
        const updTask = {...taskToToggle, reminder: !taskToToggle.reminder}

        const res = await fetch(`api/tasks/${id}`, {
            method: 'PUT',
            headers: {
            'Content-type': 'application/json'
            },
            body: JSON.stringify(updTask)
        })

        const data = await res.json()

        this.tasks = this.tasks.map((task) => task.id === id ? {...task, reminder: data.reminder} : task)
        },
        async addTask(task){
        const res = await fetch('api/tasks', {
            method: 'POST',
            headers: {
            'Content-type': 'application/json',
            },
            body: JSON.stringify(task)
        })

        const data = await res.json()

        this.tasks = [...this.tasks, data]
        },
        
        async fetchTasks(){
        const res = await fetch('api/tasks')

        const data = await res.json()

        return data
        },
        async fetchTask(id){
        const res = await fetch(`api/tasks/${id}`)

        const data = await res.json()

        return data
        }
    },
    async created(){
        this.tasks = await this.fetchTasks()
    } 
    }
</script>