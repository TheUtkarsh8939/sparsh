<svelte:options runes={false} />

<script lang="ts">
  import Nav from "./lib/nav.svelte";
  import { initializeApp } from "firebase/app";
  import {
    getFirestore,
    doc,
    getDoc,
    query,
    collection,
    where,
    or,
    getDocs,
    and,
  } from "firebase/firestore";
  export let params;
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

  // Initialize Firebase
  const app = initializeApp(firebaseConfig);
  const db = getFirestore(app);
  let dt = [];
  const docQuery = doc(db, "user", params.abc);
  let data: any = {
    name: "Loading",
    id: "",
  };
  getDoc(docQuery).then((doc) => {
    data = doc.data();
    data.id = doc.id;
  });

  const log = JSON.parse(localStorage.getItem("login"));
  let col = `${log.id}&${params.abc}`;
  if (log.isTchrs == 1) {
    col = `${params.abc}&${log.id}`;
  }
  const q =collection(db,col);
  console.log(col)
  let load = async () => {
    let docs = await getDocs(q);
    docs.forEach((doc) => {
      dt = [...dt, doc.data()];
    });
    dt.sort((a,b)=> a.timestamp - b.timestamp)
  };
  load();
  
  console.log(dt);
</script>

<Nav />
<section class="pt-[100px] flex items-center flex-col justify-center">
  <div class="w-screen flex items-center justify-center">
    <h1 class="text-2xl">
      Talk to {data.name}
    </h1>
  </div>
  {#each dt as d}
    {#if d.sender == log.id}
      <div class="w-4/5 bg-lime-300 mt-2 h-20 rounded-lg p-2">
        <pre>By You</pre>
        <span>{d.msg}</span>
      </div>
    {:else if d.pvt == "visible"}
      <div class="w-4/5 bg-amber-300 mt-2 h-20 rounded-lg p-2">
        <pre>By {data.name}</pre>
        <span>{d.msg}</span>
      </div>
    {/if}
  {/each}

  <a
    href="#/addNote/{data.id}"
    class="h-10 w-20 ring-2 ring-cyan-300/50 text-center flex items-center justify-center rounded-2xl mt-10"
    >Add Note</a
  >
</section>
