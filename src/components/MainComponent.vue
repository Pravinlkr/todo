<template>
<input type="text" v-model="task" placeholder="Task...">
<input type="date" v-model="dueDate" class="dueDate" v-on:click="minDate()" v-bind:min="todayDate">
<button class="addButton" v-on:click="addValue()">Add</button>
<br /><br />
<p style="color:orange">{{msgForUser}}</p>


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
        <td><span v-if="!t.isEdit"><b>{{t.taskName}}</b></span>
            <span v-if="t.isEdit"><input type="text" v-model="eTask" v-on:keyup.enter="editTaskValue()" class="editInput"></span>
        </td>
        <td><b>{{t.dueDat}} {{t.dueType}}</b></td>
        <td><span v-if="!t.isEdit"><button class="editButton" v-on:click="editTask(index)">Edit</button></span>
            <span v-if="t.isEdit"><button class="editButton" v-on:click="editTaskValue()">Update</button></span>
        </td>
        <td><button class="deleteButton" v-on:click="deleteTask(index)">Delete</button></td>
    </tr>
</table>


<!--Filter Box sticked At Bottom Of the page-->
<div class="filterBox">
    <input type="checkbox" id="all" value="all" v-on:click="allFilter()" :checked="isall">
    <label for="all">All</label>

    <input type="checkbox" id="completed" value="completed" v-on:click="completedFilter()" :checked="iscompleted">
    <label for="completed">Completed</label>

    <input type="checkbox" id="notcompleted"  value="notcompleted" v-on:click="notCompletedFilter()" :checked="isnotcompleted">
    <label for="notcompleted">Not Completed</label>

    <input type="checkbox" id="due" value="due" v-on:click="dueFilter()" :checked="isdue">
    <label for="due">Due</label>
</div>
</template>

<script>
export default {
    name: 'MainComponent',
    data() {
        return {
            task: '',
            eTask: '', //EDITABLE TASK
            dueDate: '',
            todayDate: '',
            diffBDate: 0,
            dueTimeType: '', //in hours, minutes or days
            msgForUser: '', //error message if user type to add with empty input field
            editTaskIndex: null,
            filter: 'all',
            allList: true,
            /*toDoList data is like {taskName:'first task',dueDate:'2021-08-21',dueType status:true},
                                    {taskName:'second task',dueDate:'2021-08-25',status:false},
                                    {taskName:'third task',dueDate:'2021-08-12' ,status:true},
                                    {taskName:'fourth task',dueDate:'2021-08-01' ,status:false}*/
            toDoList: [],
            filteredList: [],
            isall:true,
            iscompleted:false,
            isnotcompleted:false,
            isdue:false
        }
    },
    methods: {
        minDate(){
            //to disable all previous date from current date in date picker
            const current = new Date();
            var fullYear = `${current.getFullYear()}`;
            var month = `${current.getMonth()+1}`;
            var day = `${current.getDate()}`;
            if(month < 10){
                month = '0'+month;
            }
            if(day < 10){
                day = '0'+day;
            }
            var tdate = fullYear+'-'+month+'-'+day;
            this.todayDate = tdate;
            console.log(this.todayDate);
        },
        editTaskValue() {
            //edit a single task in line the table
            if (this.eTask.length > 0) {
                this.msgForUser = ''
                this.toDoList[this.editTaskIndex].taskName = this.eTask;
                this.eTask = '';
                this.toDoList[this.editTaskIndex].isEdit = false;
                this.editTaskIndex = null;
                this.updateList();

            } else {
                this.msgForUser = 'Can not add empty field to task, please type something'
            }
        },
        addValue() {
            //add new task to the list
            if (this.task.length > 0) {
                if(this.dueDate != ''){
                    this.msgForUser = ''
                    this.dueDateDiff();
                    this.toDoList.push({
                        taskName: this.task,
                        dueDat: this.diffBDate,
                        dueType: this.dueTimeType,
                        isEdit:false,
                        status: false
                    });
                    this.task = '';
                    this.dueDate = '';
                    console.log(this.toDoList);
                    this.updateList();
                }else{
                    this.msgForUser = 'Please Choose a date, empty date can not be added'
                }
                
            } else {
                this.msgForUser = 'Can not add empty field to task, please type something'
            }
        },
        deleteTask(index) {
            this.toDoList.splice(index, 1);
            this.updateList();
        },
        editTask(index) {
            this.eTask = this.toDoList[index].taskName;
            this.editTaskIndex = index;
            this.toDoList[index].isEdit = true;
        },
        taskStatusModifier(index) {
            if (this.toDoList[index].status == true) {
                this.toDoList[index].status = false;
            } else {
                this.toDoList[index].status = true;
            }
        },
        updateList() {
            this.filteredList = [];
            const len = this.toDoList.length;
            var i = 0;
            if (this.filter == 'all') {
                for (i = 0; i < len; i++) {
                    this.filteredList.push(this.toDoList[i]);
                }
            } else if (this.filter == 'completed') {
                for (i = 0; i < len; i++) {
                    if (this.toDoList[i].status == true && this.toDoList[i].dueType != 'Expired') {
                        this.filteredList.push(this.toDoList[i]);
                    }
                }
            } else if (this.filter == 'notcompleted') {
                for (i = 0; i < len; i++) {
                    if (this.toDoList[i].status == false && this.toDoList[i].dueType != 'Expired') {
                        this.filteredList.push(this.toDoList[i]);
                    }
                }
            } else if(this.filter == 'due'){
                for(i=0; i<len; i++){
                    if(this.toDoList[i].dueType == 'Expired'){
                        this.filteredList.push(this.toDoList[i]);
                    }
                }
            }
        },
        dueDateDiff() {
            const current = new Date();
            var cdate = `${current.getFullYear()}-${current.getMonth()+1}-${current.getDate()}`;
            cdate = new Date(cdate);
            var ddate = this.dueDate;
            ddate = new Date(ddate);
            //console.log(cdate+' '+ddate);
            this.diffBDate = (ddate - cdate) / (1000 * 3600 * 24);
            if (this.diffBDate < 1) {
                var hoursRemaining = (ddate - cdate) / (1000 * 3600);
                if (hoursRemaining < 1) {
                    //minutes remaining
                    var minutesRemaining = (ddate - cdate) / (1000 * 60);
                    if(minutesRemaining < 1){
                        this.dueTimeType = 'Expired'
                        this.diffBDate = null;
                    }else{
                        this.dueTimeType = 'Minutes';
                        this.diffBDate = Math.floor(minutesRemaining);
                    }
                } else {
                    //hours remaining
                    this.dueTimeType = 'Hours';
                    this.diffBDate = Math.floor(hoursRemaining);
                }
            } else {
                //Days remaining
                this.dueTimeType = 'Days';
                this.diffBDate = Math.floor(this.diffBDate);
            }
        },
        allFilter(){
            this.filter = 'all';
            this.isall = true;
            this.iscompleted = false;
            this.isnotcompleted = false;
            this.isdue = false;
            this.updateList();
        },
        completedFilter(){
            this.filter = 'completed';
            this.isall = false;
            this.iscompleted = true;
            this.isnotcompleted = false;
            this.isdue = false;
            this.updateList();
        },
        notCompletedFilter(){
            this.filter = 'notcompleted';
            this.isall = false;
            this.iscompleted = false;
            this.isnotcompleted = true;
            this.isdue = false;
            this.updateList();
        },
        dueFilter(){
            this.filter = 'due';
            this.isall = false;
            this.iscompleted = false;
            this.isnotcompleted = false;
            this.isdue = true;
            this.updateList();
        }
    },
    mounted() {
        this.updateList();
    }
}
</script>

<style scoped>
input {
    height: 30px;
    width: 15%;
    border: 1px solid #d9d9d9;
    outline: none;
}

input.dueDate {
    height: 33px;
    width: 15%;
    margin-left: 1%;
    margin-right: 1%;
}

button.addButton {
    border: none;
    outline: none;
    height: 34px;
    color: white;
    background-color: green;
    width: 70px;
}

table {
    width: 80%;
    margin: 2% auto;
    margin-bottom: 7%;
}

.editInput {
    width: 70%;
}

.editButton {
    background-color: #54a3e3;
    color: white;
    border: none;
    outline: none;
    height: 30px;
    width: 80px;
}

.deleteButton {
    background-color: #de2821;
    color: white;
    border: none;
    outline: none;
    height: 30px;
    width: 80px;
}

/*Filter box design*/
.filterBox{
    height: 50px;
    background-color: #f2f2f2;
    color:black;
    font-size: 15px;
    position:fixed;
    bottom: 0;
    width: 100%;
}
.filterBox input{
    line-height: 50px;
    height: 13px;
    margin-top: 20px;
    width:25px;
}
.filterBox label{
    margin-top: 20px;
    margin-right: 30px;
}
</style>
