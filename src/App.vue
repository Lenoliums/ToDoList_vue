<script>
import {ref, onMounted, watch} from 'vue'
export default {
  setup(){
    const newTaskText = ref('')
    const doList = ref([]);
    function recording(){
      if(newTaskText){
        let taskId=crypto.randomUUID();
        doList.value.push({
          id: taskId,
          value: newTaskText.value
        })
        newTaskText.value='';
      }
    }
    function DoneTask(e){
      console.log(e.currentTarget)
      if(e.currentTarget.classList.contains('done')){
        e.currentTarget.classList.remove('done');
        localStorage.setItem(e.currentTarget.id, e.currentTarget.getElementsByTagName('label')[0].innerHTML);
      }
      else{
        e.currentTarget.classList.add('done');
        localStorage.removeItem(e.currentTarget.id);

      }
    }

    watch(doList.value, ()=>{
      let newTask = doList.value[doList.value.length-1]
      localStorage.setItem(newTask.id, newTask.value);
    })
    
    onMounted(()=>{
      if(typeof(Storage)!==undefined){
        for(let key in localStorage) {
            if (!localStorage.hasOwnProperty(key))
              continue; 
              doList.value.push({id: key, value: localStorage.getItem(key)});
        }
      }
    })
    
    return {
      doList,
      recording,
      newTaskText,
      DoneTask
    }
    
  }
};
</script>

<template>
    <h1>ToDo List</h1>
    <div id="bigCont">
        <form onclick="event.stopPropagation()">
          <div @click="DoneTask" v-for="task in doList" class="task_cont" v-bind:id="task.id">
            <input type="checkbox">
            <label>{{ task.value }}</label>
          </div>
        </form>
        <div id="smalCont">
        <input type="text" name="ToDo" placeholder="what to do" v-model="newTaskText">
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
    text-align: left;
    border-bottom: 1px solid rgb(209, 209, 209);
    margin: 10px 20px 0 20px;
}

label{
    margin-left:20px;
}
input[type="checkbox"]{
    margin: 3px 3px 3px 4px;
}

input[type="text"]{
    text-decoration: none;
    width: 60%;
    margin: 15px;
}
button{
  margin: 15px;
}
input,button{cursor: pointer;}
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
}
@media screen and (max-width: 1030px) {
    #bigCont {
      width: 80%;
    }
    input[type="text"]{
        width: 80%;
        margin: 15px;
    }
    button{width:80%; margin: 15px;margin: 15px auto;}
  }
.done{
  background-color: rgb(240, 240, 240);
  border-radius: 40px;
}
.done label{
  text-decoration: line-through;
}

</style>
