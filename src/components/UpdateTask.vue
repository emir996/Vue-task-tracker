<template>
    
    <form @submit="onSubmit" class="add-form">
    <div class="form-control">
      <label>Task</label>
      <input type="text" v-model="selectedTask.text" name="text" placeholder="Add Task" />
    </div>

    <div class="form-control">
      <label>Day & Time</label>
      <input
        type="text"
        v-model="selectedTask.day"
        name="day"
        placeholder="Add Day & Time"
      />
    </div>
    <button @click="$emit('close-update-form')" type="button" class="btn btn-block cancel">Cancel</button>
    <input type="submit" value="Update Task" class="btn btn-block" />
    
  </form>
  
</template>

<script>
    export default {
        name: 'UpdateTask',
        props: {
            selectedTask: Object,
        },
        data(){
            return {
            }
        },
        methods: {
            onSubmit(e){
                e.preventDefault()

                if(!this.selectedTask.text){
                    alert('Field must not be empty')
                    return false
                }

                const updateTask = {
                    id: this.selectedTask.id,
                    text: this.selectedTask.text,
                    day: this.selectedTask.day,
                }
                
                const closeForm = false
                this.$emit('update-task', updateTask,closeForm)
                
                this.selectedTask.text = ''
                this.selectedTask.day = ''

            }
        }
    }
</script>

<style scoped>
    .add-form {
  margin-bottom: 40px;
}
.form-control {
  margin: 20px 0;
}
.form-control label {
  display: block;
}
.form-control input {
  width: 100%;
  height: 40px;
  margin: 5px;
  padding: 3px 7px;
  font-size: 17px;
}
.form-control-check {
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.form-control-check label {
  flex: 1;
}
.form-control-check input {
  flex: 2;
  height: 20px;
}
.cancel {
    color: #fff;
    background-color: red;
}
</style>