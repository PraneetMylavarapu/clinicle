<script>
  import { initializeApp, getApps, getApp } from "firebase/app";
  import { getFirestore, collection, onSnapshot} from "firebase/firestore";
  import {firebaseConfig} from "$lib/firebaseConfig";
  import { browser } from "$app/env";

  const firebaseApp = 
  browser && (getApps().length === 0 ? initializeApp(firebaseConfig) : getApp());

  const db = browser && getFirestore();

  const colRef = browser && collection(db, "todos");
  const unsubscribe = browser && onSnapshot(colRef, (querySnapshot) => {
    let fbTodos = [];
    querySnapshot.forEach((doc) => {
        let todo = {...doc.data(), id: doc.id };
        fbTodos = [todo, ...fbTodos];
    });
    console.table(fbTodos)
  });
  let task = "";
  let error = "";

  const addTodo = () => { 
    let todo = {
      task: task,
      isComplete: false,
      createdAt: new Date(),
      addorder: 0,
    };
    
    if(task !== ""){
      todos = [todo, ...todos];
      task = "";
      error = "";
      todos.sort((a,b)=>(a.task>b.task)?1:-1)
    } else {
      error = "Task is empty";
    }
  };

  const markTodoAsComplete = (index) => {
    todos[index].isComplete = !todos[index].isComplete;
  };

  const deleteTodo = (index) => {
    let deleteItem = todos[index]
    todos = todos.filter((item) => item != deleteItem);
  };
  
  const keyIsPressed = (event) => {
    if(event.key === "Enter") {
      addTodo()
    }
  };
</script>

<input type = "text" placeholder="Add a task" bind:value={task}>
<button on:click={addTodo}>Add</button>

<ol>
  {#each todos as item, index}
    <li class:complete={item.isComplete}>
      <span>
        {item.task}
      </span>
      <span>
        <button on:click={() => markTodoAsComplete(index)}>o</button>
      </span>
      <span>
        <button on:click={() => deleteTodo(index)}>x</button>
      </span>
    </li>
  {:else}
    <p>No todos found</p>
  {/each}
    <p class = "error">{error}</p>
</ol>
<svelte:window on:keydown={keyIsPressed}/>
<style>
  .complete{
    text-decoration: line-through;
  }
  .error{
    color: red;
  }
</style>
