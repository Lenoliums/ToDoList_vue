<script lang="ts">
import {ref, onMounted} from 'vue'
interface Task{
  id: string,
  value: string
}
export default {
  setup(){
    const newTaskText = ref<string>('')
    const doList = ref<Array<Task>>([]);
    function recording(): void{
      if(newTaskText){
        let taskId: string=crypto.randomUUID();
        doList.value.push({
          id: taskId,
          value: newTaskText.value
        })
        localStorage.setItem(taskId, newTaskText.value);
        newTaskText.value='';
      }
    }
    function doneTask(e: MouseEvent): void{
      if((e.target as HTMLInputElement).type !== 'checkbox'){
        return
      }
      let currentTarget = e.currentTarget as HTMLElement
      if(currentTarget.classList.contains('done')){
        currentTarget.classList.remove('done');
        localStorage.setItem(currentTarget.id, currentTarget.getElementsByTagName('input')[1].value);
      }
      else{
        currentTarget.classList.add('done');
        localStorage.removeItem(currentTarget.id);
      }
    }
    function editTask(task: Task, targetElement: EventTarget): void{
      task.value = (targetElement as HTMLInputElement).value; 
      localStorage[task.id]=(targetElement as HTMLInputElement).value;
    }
    
    onMounted(()=>{
      if(typeof(Storage)!==undefined){
        for(let key in localStorage) {
            if (!localStorage.hasOwnProperty(key))
              continue; 
            doList.value.push({id: key, value: localStorage.getItem(key) ?? ''});
        }
      }
    })
    
    return {
      doList,
      recording,
      newTaskText,
      doneTask,
      editTask
    }
    
  }
};
</script>

<template>
    <h1>ToDo List</h1>
    <div id="bigCont">
        <form onclick="event.stopPropagation()">
          <div @click="doneTask" v-for="task in doList" class="task_cont" v-bind:id="task.id">
            <input type="checkbox"/>
            <input type="text" v-bind:value="task.value" @change="(e)=>editTask(task, e.target)"/>
          </div>
        </form>
        <div id="smalCont">
          <input type="text" name="ToDo" placeholder="what to do" v-model="newTaskText"/>
          <button @click="recording">recording task</button>
        </div>
    </div>
</template>

<style>
h1{
  color: black;
  font-weight: bold;
  margin: 21px 0;
}
body{background-image: url(https://gas-kvas.com/uploads/posts/2023-02/1675459062_gas-kvas-com-p-fonovii-risunok-frukti-32.jpg);
    background-repeat: no-repeat;
    background-position: center center;
    background-attachment: fixed;
    -webkit-background-size: cover;
    -moz-background-size: cover;
    -o-background-size: cover;
    background-size: cover;
    text-align: center;
  }
.task_cont{
    display: flex;
    text-align: left;
    border-bottom: 1px solid rgb(209, 209, 209);
    margin: 10px 20px 0 20px;
    padding: 0 5px;
}

input[type="checkbox"]{
    margin: 3px 3px 3px 4px;
}

#smalCont input[type="text"]{
    text-decoration: none;
    width: 60%;
    margin: 15px;
}
button{
  margin: 15px;
}
#smalCont input,button{cursor: pointer;}
#smalCont{
    text-align: center;
}

#bigCont{
    padding-top: 10px;
    width: 50%;
    margin: 0 auto;
    border: 2px solid black;
    background: white;
    min-width: 107px;
    overflow: hidden;
}
@media screen and (max-width: 1030px) {
    #bigCont {
      width: 80%;
    }
    #smalCont input[type="text"]{
        width: 80%;
        margin: 15px;
    }
    button{width:80%; margin: 15px;margin: 15px auto;}
  }
.done{
  background-color: rgb(240, 240, 240);
  border-radius: 40px;
}
.task_cont input[type="text"]{
  margin-left:20px;
  border: 0;
  background-color: rgba(0,0,0,0);
  flex: 1;
}
.done input[type="text"]{
  text-decoration: line-through;
}

</style>
