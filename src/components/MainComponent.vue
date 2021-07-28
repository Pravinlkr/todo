<template>
    <p>Type Something...</p>
    <p>Press Enter</p>
    <input v-on:keyup.enter="addValue()" type="text" v-model="task">
    <br/><br/>
    <p style="color:orange">{{msgForUser}}</p>
    <label for="filter">Filter List : </label>
    <select name="filter" id="filter" v-model="filter" v-on:change="updateList()">
        <option disabled>Select an Option</option>
        <option value="all">All</option>
        <option value="completed">Completed</option>
        <option value="notcompleted">Not Completed</option>
    </select>
    <table>
        <tr style="color:white; background-color:black">
            <th>Status</th>
            <th>Task Name</th>
            <th>Edit</th>
            <th>Delete</th>
        </tr>
        <!--show all items in to do list-->
        <tr v-for="(t,index) in filteredList" v-bind:key="index">
            <td><input type="checkbox" v-model="t.status" v-on:click="taskStatusModifier(index)"></td>
            <td><b>{{t.taskName}}</b></td>
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
            msgForUser:'',
            editTaskIndex:null,
            isEdit:false,
            filter:'all',
            allList:true,
            toDoList:[{taskName:'first task',status:true},{taskName:'second task',status:false},
                     {taskName:'third task',status:true},{taskName:'fourth task',status:false}],
            filteredList:[]
        }
    },
    methods:{
        addValue(){
            if(this.task.length > 0){
                this.msgForUser = ''
                if(this.isEdit){
                    this.toDoList[this.editTaskIndex].taskName = this.task;
                    this.task = '';
                    this.isEdit = false;
                    this.editTaskIndex = null;
                }
                else{
                    this.toDoList.push({taskName:this.task,status:false});
                    this.task = '';
                }
                this.updateList();
            }
            else{
                this.msgForUser = 'Can not add empty field to task, please type something'
            }   
        },
        deleteTask(index){
            this.toDoList.splice(index,1);
            this.updateList();
        },
        editTask(index){
            this.task = this.toDoList[index].taskName;
            this.editTaskIndex = index;
            this.isEdit = true;
            this.updateList();
        },
        taskStatusModifier(index){
            if(this.toDoList[index].status == true){
                this.toDoList[index].status = false;
            }
            else{
                this.toDoList[index].status = true;
            }
        },
        updateList(){
            this.filteredList = [];
            const len = this.toDoList.length;
            var i=0;
            if(this.filter == 'all'){
                for(i=0; i<len; i++){
                    this.filteredList.push(this.toDoList[i]);
                }
            }
            else if(this.filter == 'completed'){
                for(i=0; i<len; i++){
                    if(this.toDoList[i].status == true){
                        this.filteredList.push(this.toDoList[i]);
                    }
                }
            }
            else if(this.filter == 'notcompleted'){
                for(i=0; i<len; i++){
                    if(this.toDoList[i].status == false){
                        this.filteredList.push(this.toDoList[i]);
                    }
                }
            }
        }
    },
    mounted(){
        this.updateList();
    }
}
</script>
<style scoped>
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
    width:80px;
}
.deleteButton{
    background-color:#de2821;
    color:white;
    border:none;
    outline:none;
    height:30px;
    width:80px;
}
</style>
