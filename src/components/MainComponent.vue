<template>
<input type="text" v-model="task" placeholder="Task...">
<input type="datetime-local" v-model="dueDate" class="dueDate" v-on:click="minDate()" v-bind:min="todayDate">
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
    <tr v-for="(t,index) in filteredList" v-bind:key="index" :class="{displayRow:t.showRow}">
        <td><input type="checkbox" v-model="t.status" v-on:click="taskStatusModifier(t.taskId)"></td>
        <td><span v-if="!t.isEdit"><b>{{t.taskName}}</b></span>
            <span v-if="t.isEdit"><input type="text" v-model="eTask" v-on:keyup.enter="editTaskValue()" class="editInput"></span>
        </td>
        <td><b>{{t.dueDat}} {{t.dueType}}</b></td>
        <td><span v-if="!t.isEdit"><button class="editButton" v-on:click="editTask(t.taskId)">Edit</button></span>
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
            taskId:0,
            eTask: '', //EDITABLE TASK
            dueDate: '',
            todayDate: '',
            diffBDate: 0,
            dueTimeType: '', //in hours, minutes or days
            msgForUser: '', //error message if user type to add with empty input field
            editTaskIndex: null,
            filter: 'all',
            allList: true,
            toDoList: [{"showRow":false,"taskId":2,"taskName":"one","dueDat":7,"dueType":"Days","isEdit":false,"status":false},
                       {"showRow":false,"taskId":4,"taskName":"two","dueDat":2,"dueType":"Days","isEdit":false,"status":false},
                       {"showRow":false,"taskId":6,"taskName":"three","dueDat":7,"dueType":"Days","isEdit":false,"status":false},
                       {"showRow":false,"taskId":8,"taskName":"four","dueDat":17,"dueType":"Days","isEdit":false,"status":false},
                       {"showRow":false,"taskId":10,"taskName":"five","dueDat":27,"dueType":"Days","isEdit":false,"status":false}],
            filteredList: [],
            isall:true,
            iscompleted:false,
            isnotcompleted:false,
            isdue:false
        }
    },
    watch:{
        //runs if filter changes
        filter(val){
            if(val == 'all'){
                this.isall = true;
                this.iscompleted = false;
                this.isnotcompleted = false;
                this.isdue = false;
            }
            else if(val == 'completed'){
                this.isall = false;
                this.iscompleted = true;
                this.isnotcompleted = false;
                this.isdue = false;
                
            }
            else if(val == 'notcompleted'){
                this.isall = false;
                this.iscompleted = false;
                this.isnotcompleted = true;
                this.isdue = false;
            }
            else{
                this.isall = false;
                this.iscompleted = false;
                this.isnotcompleted = false;
                this.isdue = true;
            }
        }
    },
    computed:{
        //update filteredlist every time it is called
        updateSection(){
            this.toDoList.forEach(element => {
                element.showRow = false;
            });
            if(this.filter == 'all'){
                this.toDoList.forEach(element => {
                    element.showRow = false;
                    this.filteredList.push(element);
            });
            }
            if(this.filter == 'completed'){
                this.toDoList.forEach(element => {
                if(element.status == true && element.dueType != 'Expired'){
                    this.filteredList.push(element);
                }
            });
            }
            if(this.filter == 'notcompleted'){
                this.toDoList.forEach(element => {
                if(element.status == false && element.dueType != 'Expired'){
                    this.filteredList.push(element);
                }
            });
            }
            if(this.filter == 'due'){
                this.toDoList.forEach(element => {
                if(element.dueType == 'Expired'){
                    this.filteredList.push(element);
                }
            });
            }
            return this.filteredList;
        }
    },
    methods: {
        minDate(){
            //to disable all previous date from current date in date picker
            var current = new Date().toISOString();
            current = current.substring(0,current.length-1);
            this.todayDate = current;
        },
        editTaskValue() {
            //edit a single task in line the table
            if (this.eTask.length > 0) {
                this.msgForUser = ''
                this.toDoList[this.editTaskIndex].taskName = this.eTask;
                this.eTask = '';
                this.toDoList[this.editTaskIndex].isEdit = false;
                this.editTaskIndex = null;
                this.filteredList = [];
                this.updateSection;

            } else {
                this.msgForUser = 'Can not add empty field to task, please type something'
            }
        },
        addValue() {
            //add new task to the list
            if (this.task.length > 0) {
                if(this.dueDate != ''){
                    this.taskId = this.taskId + 2;
                    this.msgForUser = ''
                    this.dueDateDiff();
                    this.toDoList.push({
                        taskId: this.taskId,
                        taskName: this.task,
                        dueDat: this.diffBDate,
                        dueType: this.dueTimeType,
                        showRow: false,
                        isEdit:false,
                        status: false
                    });
                    this.task = '';
                    this.dueDate = '';
                    this.filteredList = [];
                    this.updateSection;
                }else{
                    this.msgForUser = 'Please Choose a date, empty date can not be added'
                }
                
            } else {
                this.msgForUser = 'Can not add empty field to task, please type something'
            }
        },
        deleteTask(index) {
            this.toDoList.splice(index, 1);
            this.filteredList = [];
            this.updateSection;
        },
        editTask(id) {
            var len = this.toDoList.length;
            let i=0;
            for(i=0; i<len; i++){
                if(this.toDoList[i].taskId == id){
                    this.eTask = this.toDoList[i].taskName;
                    this.editTaskIndex = i;
                    this.toDoList[i].isEdit = true;
                }
            }
        },
        taskStatusModifier(id) {
             this.toDoList.forEach(element => {
                if(element.taskId === id){
                    if (element.status == true) {
                        element.status = false;
                        if(this.filter == 'completed' || this.filter == 'notcompleted'){
                            element.showRow = true;
                        }   
                    } 
                    if (element.status == false){
                        element.status = true;
                        if(this.filter == 'completed' || this.filter == 'notcompleted'){
                            element.showRow = true;
                        } 
                    }    
                }
            });
        },
        dueDateDiff() {
            var cdate = new Date();
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
            this.filteredList = [];
            this.updateSection;
        },
        completedFilter(){
            this.filter = 'completed';
            this.filteredList = [];
            this.updateSection;
        },
        notCompletedFilter(){
            this.filter = 'notcompleted';
            this.filteredList = [];
            this.updateSection;
        },
        dueFilter(){
            this.filter = 'due';
            this.filteredList = [];
            this.updateSection;
        }
    },
    mounted() {
        this.updateSection;
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
.displayRow{
    display:none;
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
