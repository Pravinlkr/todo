<template>
    <p>Type Something...</p>
    <p>Press Enter</p>
    <input v-on:keyup.enter="addValue()" type="text" v-model="task">
    <br/><br/>
    <p>To Do Tasks</p>
    <ul>
        <li v-for="(t,index) in toDoList" v-bind:key="index">{{t}}</li>
    </ul>
</template>
<script>
export default{
    name:'MainComponent',
    data(){
        return{
            task:'',
            toDoList:[]
        }
    },
    methods:{
        addValue(){
            if(this.task.length > 0){
                this.toDoList.push(this.task);
                this.task = '';
            }   
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
</style>
