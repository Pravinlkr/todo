<template>
    <p>Type Something...</p>
    <p>Press Enter</p>
    <input v-on:keyup.enter="addValue()" type="text" v-model="task">
    <br/><br/>
    <p>To Do Tasks</p>
    <table>
        <tr style="color:white; background-color:black">
            <th>Index</th>
            <th>Task Name</th>
            <th>Edit</th>
            <th>Delete</th>
        </tr>
        <tr v-for="(t,index) in toDoList" v-bind:key="index">
            <td><b>{{index+1}}</b></td>
            <td><b>{{t}}</b></td>
            <td><button class="editButton" v-on:click="editTask(index)">Edit</button></td>
            <td><button class="deleteButton" v-on:click="deleteTask(index)">Delete</button></td>
        </tr>
    </table>
</template>
<script>
export default{
    name:'MainComponent',
    data(){
        return{
            task:'',
            editTaskIndex:null,
            isEdit:false,
            toDoList:[]
        }
    },
    methods:{
        addValue(){
            if(this.task.length > 0){
                if(this.isEdit){
                    this.toDoList[this.editTaskIndex] = this.task;
                    this.task = '';
                    this.isEdit = false;
                    this.editTaskIndex = null;

                }
                else{
                    this.toDoList.push(this.task);
                    this.task = '';
                }
            }   
        },
        deleteTask(index){
            this.toDoList.splice(index,1);
        },
        editTask(index){
            this.task = this.toDoList[index];
            this.editTaskIndex = index;
            this.isEdit = true;
        }
    }
}
</script>
<style scoped>
p{
    text-align: center;
}
input{
    height: 30px;
    width:20%;
    border:1px solid #d9d9d9;
    outline:none;
}
table{
    width:80%;
    margin:2% auto;
}
.editButton{
    background-color:#54a3e3;
    color:white;
    border:none;
    outline:none;
    height:30px;
    width:30%;
}
.deleteButton{
    background-color:#de2821;
    color:white;
    border:none;
    outline:none;
    height:30px;
    width:30%;
}
</style>
