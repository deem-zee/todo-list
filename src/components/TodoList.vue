<template>
  <div class="container">
    <h1>Список задач</h1>
    <div class="btnBlock">
            <button @click="allTasksShow" class="btnAll">Все задачи</button>
            <button @click="inprogressShow" class="btnAll">Текущие задачи</button>
            <button @click="completedShow" class="btnAll">Выполненные задачи</button>
        </div>
    <div v-if="inputShow" class="inputBlock">
        <input type="text" @keydown.enter="addTask" class="taskInput" v-model="task">
        <div>
            <button @click="addTask"  class="addTask">Добавить</button>
            <button @click="cancel" class="cancel">Отмена</button>
        </div>
        
    </div>
    <div v-if="displayAll" class="allTasks">
        <h2>Все задачи:</h2>
        <ol>
            <li v-for="(task, index) in tasks.allTasks" :key="index" @click="completedTask" v-bind:class="{completed: task.isComplete}">{{ task.name }}</li>
        </ol>
    </div>

    <div v-if="displayCompleted" class="completedTasks">
        <h2>Выполненные задачи:</h2>
        <ol>
            <li v-for="(task, index) in tasks.completedTasks" :key="index">{{ task}}</li>
        </ol>
    </div>

    <div v-if="displayInprogress" class="inprogressTasks">
        <h2>Текущие задачи:</h2>
        <ol>
            <li v-for="(task, index) in tasks.inprogressTasks" :key="index">{{task}}</li>
        </ol>
    </div>
    <button @click="newTask" class="newTask">+</button>
  </div>
</template>

<script>
import Vue from 'vue'
import axios from 'axios'
import VueAxios from 'vue-axios'

Vue.use(VueAxios, axios)

export default {
    data() {
        return {
            data: [],
            tasks: {
                allTasks: [],
                inprogressTasks: [],
                completedTasks: [],
            },
            isCompleted: false,
            task: null,
            displayAll: true,
            displayInprogress: false,
            displayCompleted: false,
            inputShow: false,
        }
    },
    methods: {
        addTask() {
            if(this.task == null){
                alert('Ошибка ввода: не оcтавляйте строку ввода пустой');
            }  else if(this.task != null && this.task.length < 100) {
              this.tasks.allTasks.push({name: this.task, isComplete: false});
              this.tasks.inprogressTasks.push(this.task)
              axios.put("https://todolist-e0681-default-rtdb.europe-west1.firebasedatabase.app/todolist.json", this.tasks)
              .then((response) => {
                console.log(response);
                }, (error) => {
                console.log(error);
                });
              this.task = null;
              this.inputShow = false;
            } else if (this.task.length >= 100) {
                alert('Ошибка ввода: слишком много символов');
                this.task = null;
            }

        },
        completedTask(event) {
            if(this.tasks.completedTasks.indexOf(event.target.innerText) == -1) {
                this.tasks.completedTasks.push(event.target.innerText);
            for(let prop of this.tasks.allTasks) {
                if (event.target.innerText == prop.name) {
                    prop.isComplete = true
                }
                
            }
            let idx = this.tasks.inprogressTasks.indexOf(event.target.innerText);
            this.tasks.inprogressTasks.splice(idx,1);
             axios.put("https://todolist-e0681-default-rtdb.europe-west1.firebasedatabase.app/todolist.json", this.tasks)
              .then((response) => {
                console.log(response);
                }, (error) => {
                console.log(error);
                });

            }
            
        },
        newTask() {
            this.inputShow = true;
        },
        inprogressShow() {
            this.displayInprogress = true;
            this.displayAll = false;
            this.displayCompleted = false;
        },
        allTasksShow() {
            this.displayInprogress = false;
            this.displayAll = true;
            this.displayCompleted = false;
        },
        completedShow() {
            this.displayInprogress = false;
            this.displayAll = false;
            this.displayCompleted = true;
        },
        cancel() {
            this.inputShow = false;
            this.task = null;
        }
    
    },
    created() {
        axios.get('https://todolist-e0681-default-rtdb.europe-west1.firebasedatabase.app/todolist.json')
        .then(response => {
           return response.data 
        } 
            
        )
        .then(data => {
            if(data != null) {
                if(Object.prototype.hasOwnProperty.call(data, "allTasks")) {
                    this.tasks.allTasks = data.allTasks;   
                }
                if(Object.prototype.hasOwnProperty.call(data, "inprogressTasks")) {
                    this.tasks.inprogressTasks = data.inprogressTasks;  
                }
                 if(Object.prototype.hasOwnProperty.call(data, "completedTasks")) {
                    this.tasks.completedTasks = data.completedTasks;  
                }
            }
           
            
        });

    },

}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Be+Vietnam+Pro&family=Comfortaa:wght@300;400;500;600&display=swap');
@media screen and (min-width: 360px){
     .container {
    display: flex;
    justify-content: center;
    flex-direction: column;
    flex-wrap: nowrap;
    border: 1px solid grey;
    background: rgb(240, 235, 235);
    position: relative;
    font-family: 'Be Vietnam Pro', sans-serif;
    font-family: 'Comfortaa', cursive;
}
.btnBlock {
    position: absolute;
    top: 80px;
    align-self: center;
    display: flex;
    flex-direction: column;
}
.btnBlock :hover {
    cursor: pointer;
    
}
.btnAll {
    height: 30px;
    border: 1px solid white;
    background: rgb(18, 223, 114);
    font-size: 18px;
    font-family: 'Be Vietnam Pro', sans-serif;
    font-family: 'Comfortaa', cursive;
}
.btnPrgrs {
    height: 30px;
    border: 1px solid white;
    background: rgb(18, 223, 114);
    font-size: 18px;
    font-family: 'Be Vietnam Pro', sans-serif;
    font-family: 'Comfortaa', cursive;
}
.btnComp {
    height: 30px;
    border: 1px solid white;
    background: rgb(18, 223, 114);
    font-size: 18px;
    font-family: 'Be Vietnam Pro', sans-serif;
    font-family: 'Comfortaa', cursive;
}
.container h1 {
    height: 200px;
    background: rgb(240, 235, 235);
    margin-bottom: 0;
    border-bottom: 1px solid grey;
}
.newTask {
    transform: scale(0.6);
    box-sizing: border-box;
    border-radius: 50%;
    border: none;
    background: rgb(18, 223, 114);
    width: 70px;
    height: 4rem;
    font-size: 2em;
    align-self: center;
    overflow: hidden;
    position: absolute;
    top: 190px;
    right: 40px;
    color: white;
}
.newTask:hover {
    cursor: pointer;
}
.inputBlock {
    
    align-self: center;
    display: flex;
    flex-direction: column;
    margin: 10px 10px;
}
.taskInput {
    transform: scale(0.5);
    margin: 10px;
    height: 80px;
    width: 40rem;
    border-radius: 10px;
    padding-left: 20px;
    font-size: 20px;
    font-family: 'Be Vietnam Pro', sans-serif;
    font-family: 'Comfortaa', cursive;
}
.addTask {
    width: 150px;
    height: 40px;
    border-radius: 5em;
    background: rgb(18, 223, 114);
    margin: 5px;
    border: none;
    font-size: 18px;
    font-family: 'Be Vietnam Pro', sans-serif;
    font-family: 'Comfortaa', cursive;
    color: white;
}
.addTask:hover {
    background: rgb(0, 244, 114);
}
.cancel {
    display: block;
    position: relative;
    left: 265px;
    width: 150px;
    height: 40px;
    border-radius: 5em;
    background: rgb(235, 44, 44);
    margin: 5px;
    border: none;
    font-size: 18px;
    font-family: 'Be Vietnam Pro', sans-serif;
    font-family: 'Comfortaa', cursive;
    color: white;
}
.cancel:hover {
    background: rgb(253, 2, 2);
}
.btn {
    width: 100px;
    height: 40px;
    border-radius: 5em;
    background: rgb(18, 223, 114);
    margin: 5px;
}
.allTasks, .completedTasks, .inprogressTasks {
    background: rgb(249, 247, 247);
}
ol {
    padding: 0;
    list-style-type: none;
    margin: 0;
}
 li {
    font-size: 16px;
    height: 50px;
    border-bottom: 1px solid grey;
    width: 90%;
    text-align: center;
    padding-top: 30px;
}
li:last-child {
    border-bottom: none;
}
.allTasks li:hover {
    cursor: pointer;
}
.completed {
    text-decoration: line-through;
    opacity: 0.5;
}
}
@media screen and (min-width: 768px){
         .container {
    display: flex;
    justify-content: center;
    flex-direction: column;
    flex-wrap: nowrap;
    border: 1px solid grey;
    background: rgb(240, 235, 235);
    position: relative;
    font-family: 'Be Vietnam Pro', sans-serif;
    font-family: 'Comfortaa', cursive;
}
.btnBlock {
    position: absolute;
    top: 80px;
    align-self: center;
    display: flex;
    flex-direction: column;
}
.btnBlock :hover {
    cursor: pointer;
    
}
.btnAll {
    height: 30px;
    border: 1px solid white;
    background: rgb(18, 223, 114);
    font-size: 18px;
    font-family: 'Be Vietnam Pro', sans-serif;
    font-family: 'Comfortaa', cursive;
}
.btnPrgrs {
    height: 30px;
    border: 1px solid white;
    background: rgb(18, 223, 114);
    font-size: 18px;
    font-family: 'Be Vietnam Pro', sans-serif;
    font-family: 'Comfortaa', cursive;
}
.btnComp {
    height: 30px;
    border: 1px solid white;
    background: rgb(18, 223, 114);
    font-size: 18px;
    font-family: 'Be Vietnam Pro', sans-serif;
    font-family: 'Comfortaa', cursive;
}
.container h1 {
    height: 200px;
    background: rgb(240, 235, 235);
    margin-bottom: 0;
    border-bottom: 1px solid grey;
}
.newTask {
    transform: scale(0.6);
    box-sizing: border-box;
    border-radius: 50%;
    border: none;
    background: rgb(18, 223, 114);
    width: 70px;
    height: 4rem;
    font-size: 2em;
    align-self: center;
    overflow: hidden;
    position: absolute;
    top: 190px;
    right: 110px;
    color: white;
}
.newTask:hover {
    cursor: pointer;
}
.inputBlock {
    
    align-self: center;
    display: flex;
    flex-direction: column;
    margin: 10px 10px;
}
.taskInput {
    transform: scale(0.5);
    margin: 10px;
    height: 80px;
    width: 40rem;
    border-radius: 10px;
    padding-left: 20px;
    font-size: 30px;
    font-family: 'Be Vietnam Pro', sans-serif;
    font-family: 'Comfortaa', cursive;
}
.addTask {
    width: 150px;
    height: 40px;
    border-radius: 5em;
    background: rgb(18, 223, 114);
    margin: 5px;
    border: none;
    font-size: 18px;
    font-family: 'Be Vietnam Pro', sans-serif;
    font-family: 'Comfortaa', cursive;
    color: white;
}
.addTask:hover {
    background: rgb(0, 244, 114);
}
.cancel {
    display: block;
    position: relative;
    left: 265px;
    width: 150px;
    height: 40px;
    border-radius: 5em;
    background: rgb(235, 44, 44);
    margin: 5px;
    border: none;
    font-size: 18px;
    font-family: 'Be Vietnam Pro', sans-serif;
    font-family: 'Comfortaa', cursive;
    color: white;
}
.cancel:hover {
    background: rgb(253, 2, 2);
}
.btn {
    width: 100px;
    height: 40px;
    border-radius: 5em;
    background: rgb(18, 223, 114);
    margin: 5px;
}
.allTasks, .completedTasks, .inprogressTasks {
    background: rgb(249, 247, 247);
}
.allTasks h2, .completedTasks h2, .inprogressTasks h2 {
    font-size: 36px;
}
ol {
    padding: 0;
    list-style-type: none;
    margin: 0;
}
 li {
    font-size: 16px;
    height: 50px;
    border-bottom: 1px solid grey;
    width: 100%;
    text-align: center;
    padding-top: 30px;
    font-size: 26px;
}
li:last-child {
    border-bottom: none;
}
.allTasks li:hover {
    cursor: pointer;
}
}

/* @media screen 1024px */

@media screen and (min-width: 1024px) {
    .container {
    display: flex;
    justify-content: center;
    flex-direction: column;
    flex-wrap: nowrap;
    border: 1px solid grey;
    background: rgb(240, 235, 235);
    position: relative;
    font-family: 'Be Vietnam Pro', sans-serif;
    font-family: 'Comfortaa', cursive;
    width: 768px;
    margin: 0 auto;
}
.btnBlock {
    display: flex;
    flex-direction: row;
}
.btnBlock :hover {
    cursor: pointer;
}
h2 {
    font-size: 34px;
}
.btnAll {
    height: 50px;
    border: 1px solid white;
    background: rgb(18, 223, 114);
    font-size: 24px;
    font-family: 'Be Vietnam Pro', sans-serif;
    font-family: 'Comfortaa', cursive;
    padding: 10px;
}
.btnPrgrs {
    height: 50px;
    border: 1px solid white;
    background: rgb(18, 223, 114);
    font-size: 18px;
    font-family: 'Be Vietnam Pro', sans-serif;
    font-family: 'Comfortaa', cursive;
}
.btnComp {
    height: 50px;
    border: 1px solid white;
    background: rgb(18, 223, 114);
    border-top-right-radius: 10px;
    border-bottom-right-radius: 10px;
    font-size: 18px;
    font-family: 'Be Vietnam Pro', sans-serif;
    font-family: 'Comfortaa', cursive;
}
.container h1 {
    height: 200px;
    background: rgb(240, 235, 235);
    margin-bottom: 0;
    border-bottom: 1px solid grey;
}
.newTask {
    box-sizing: border-box;
    border-radius: 50%;
    border: none;
    background: rgb(18, 223, 114);
    width: 70px;
    height: 4rem;
    font-size: 2em;
    align-self: center;
    overflow: hidden;
    position: absolute;
    top: 190px;
    right: 50px;
    color: white;
    transform: scale(1.4)
}
.newTask:hover {
    cursor: pointer;
}
.inputBlock {
    margin: 10px 10px;
}
.taskInput {
    margin: 10px;
    height: 80px;
    width: 700px;
    border-radius: 10px;
    padding-left: 20px;
    font-size: 60px;
    font-family: 'Be Vietnam Pro', sans-serif;
    font-family: 'Comfortaa', cursive;
}
.addTask {
    width: 200px;
    height: 60px;
    border-radius: 5em;
    background: rgb(18, 223, 114);
    margin: 5px;
    border: none;
    font-size: 26px;
    font-family: 'Be Vietnam Pro', sans-serif;
    font-family: 'Comfortaa', cursive;
    color: white;
    margin: 0 auto;
}
.addTask:hover {
    background: rgb(0, 244, 114);
}
.cancel {
    width: 200px;
    height: 60px;
    border-radius: 5em;
    background: rgb(235, 44, 44);
    margin: 5px;
    border: none;
    font-size: 26px;
    font-family: 'Be Vietnam Pro', sans-serif;
    font-family: 'Comfortaa', cursive;
    color: white;
    position: relative;
    left: 270px;
    
}
.cancel:hover {
    background: rgb(253, 2, 2);
}
.btn {
    width: 100px;
    height: 40px;
    border-radius: 5em;
    background: rgb(18, 223, 114);
    margin: 5px;
}
.allTasks, .completedTasks, .inprogressTasks {
    background: rgb(249, 247, 247);
}
ol {
    padding: 0;
    list-style-type: none;
    margin: 0;
}
 li {
    font-size: 30px;
    height: 70px;
    border-bottom: 1px solid grey;
    width: 100%;
    text-align: center;
    padding-top: 30px;
}
li:last-child {
    border-bottom: none;
}
.allTasks li:hover {
    cursor: pointer;
}
}
.completed {
    text-decoration: line-through;
    opacity: 0.5;
}
button:hover {
    cursor: pointer;
}

</style>