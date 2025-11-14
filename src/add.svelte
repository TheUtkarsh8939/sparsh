<svelte:options runes={false} />

<script lang="ts">
    export let params
  import Nav from "./lib/nav.svelte";
  import { initializeApp } from "firebase/app";
  import { getFirestore, addDoc, collection } from "firebase/firestore";
  // TODO: Add SDKs for Firebase products that you want to use
  // https://firebase.google.com/docs/web/setup#available-libraries

  // Your web app's Firebase configuration
  // For Firebase JS SDK v7.20.0 and later, measurementId is optional
  const firebaseConfig = {
    apiKey: "AIzaSyALezIVphAz4DTAPqenE00AAxCypslrvE4",
    authDomain: "sparsh-82105.firebaseapp.com",
    projectId: "sparsh-82105",
    storageBucket: "sparsh-82105.firebasestorage.app",
    messagingSenderId: "276941362383",
    appId: "1:276941362383:web:29b6b0d49a16c70d1fd3ba",
    measurementId: "G-GMNGD3JSTL",
  };
  const app = initializeApp(firebaseConfig)
  const db = getFirestore(app)
  let pvt:string;
  let msg:string;
  
  let login = JSON.parse(localStorage.getItem("login"))
  let col = `${login.id}&${params.to}`
  if (login.isTchrs == 1){
    col = `${params.to}&${login.id}`
  }
  async function send(){
    let docRef = await addDoc(collection(db,col),{
        pvt: pvt,
        msg:msg,
        sender:login.id,
        reciever: params.to,
        timestamp: Date.now()
    })
    console.log(docRef)
    alert("Added succesfully")
    msg = ""
  }
</script>

<Nav />
<section class="h-screen w-screen flex flex-col items-center justify-center">
  <div
    class="card h-[clamp(500px,50%,200px)] w-[clamp(300px,40%,200px)] shadow-purple-600 shadow-2xl rounded-xl flex flex-col items-center pt-10 justify-between pb-10"
  >
    <h1 class="text-3xl">Add Message</h1>
    <div class="">
      <label for="type">Shown/Private</label>
      <select
        bind:value={pvt}
        class="ring-purple-800/50 h-10 ring-1 w-full rounded-md cursor-text"
      >
        <option value="visible">Visible</option>
        <option value="private">Private</option>
      </select>
    </div>
    <div class="mt-[0%] cursor-pointer">
      <label for="msg">Message</label>
      <input
      bind:value={msg}
        type="text"
        id="msg"
        class="ring-purple-800/50 h-10 ring-1 w-full rounded-md cursor-text"
      />
    </div>
    <button on:click={send} class="ring-1 ring-purple-800 py-2 px-4 rounded-full">Add</button>
  </div>
</section>
