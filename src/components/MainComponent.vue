<template>
    <input type="text" v-model="task" placeholder="Task...">
    <input type="date" v-model="dueDate" class="dueDate">
    <button class="addButton" v-on:click="addValue()">Add</button>
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
            <th>Due In</th>
            <th>Edit</th>
            <th>Delete</th>
        </tr>
        <!--show all items in to do list-->
        <tr v-for="(t,index) in filteredList" v-bind:key="index">
            <td><input type="checkbox" v-model="t.status" v-on:click="taskStatusModifier(index)"></td>
            <td><b>{{t.taskName}}</b></td>
            <td><b>{{t.dueDat}} {{t.dueType}}</b></td>
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
            dueDate:'',
            diffBDate:0,
            dueTimeType:'',//in hours, minutes or days
            msgForUser:'', //error message if user type to add with empty input field
            editTaskIndex:null,
            isEdit:false,
            filter:'all',
            allList:true,
            /*toDoList data is like {taskName:'first task',dueDate:'2021-08-21', status:true},
                                    {taskName:'second task',dueDate:'2021-08-25',status:false},
                                    {taskName:'third task',dueDate:'2021-08-12' ,status:true},
                                    {taskName:'fourth task',dueDate:'2021-08-01' ,status:false}*/
            toDoList:[],
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
                    this.dueDateDiff();
                    this.toDoList.push({taskName:this.task, dueDat: this.diffBDate, dueType:this.dueTimeType ,status:false});
                    this.task = '';
                    this.dueDate = '';
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
        },
        dueDateDiff(){
            const current = new Date();
            var cdate = `${current.getFullYear()}-${current.getMonth()+1}-${current.getDate()}`;
            cdate = new Date(cdate);
            var ddate = this.dueDate;
            ddate = new Date(ddate);
            //console.log(cdate+' '+ddate);
            this.diffBDate = (ddate-cdate)/ (1000 * 3600 * 24);
            if(this.diffBDate <1){
                var hoursRemaining = (ddate-cdate)/(1000 * 3600);
                if(hoursRemaining < 1){
                    //minutes remaining
                    var minutesRemaining = (ddate-cdate)/(1000*60);
                    this.dueTimeType = 'Minutes';
                    this.diffBDate = Math.floor(minutesRemaining);
                }else{
                    //hours remaining
                    this.dueTimeType = 'Hours';
                    this.diffBDate = Math.floor(hoursRemaining);
                }
            }
            else{
                //Days remaining
                this.dueTimeType = 'Days';
                this.diffBDate = Math.floor(this.diffBDate);
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
    width:15%;
    border:1px solid #d9d9d9;
    outline:none;
}
input.dueDate{
    height: 33px;
    width:15%;
    margin-left: 1%;
    margin-right: 1%;
}
button.addButton{
    border:none;
    outline:none;
    height: 34px;
    color:white;
    background-color: green;
    width:70px;
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
