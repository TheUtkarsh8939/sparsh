<script>
    import Nav from "./lib/nav.svelte"
    // Import the functions you need from the SDKs you need
import { initializeApp } from "firebase/app";
import { getAnalytics } from "firebase/analytics";
import {getFirestore, doc,getDoc} from "firebase/firestore"
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
  measurementId: "G-GMNGD3JSTL"
};

// Initialize Firebase
const app = initializeApp(firebaseConfig);
const db = getFirestore(app)
    let uid = ""
    let pwd = ""
    async function login(){
        const docRef = doc(db, "user", uid)
        const docSnap = await getDoc(docRef)
        if (docSnap.exists()) {
                const actPwd = docSnap.data().pwd
                console.log()
                if(pwd===actPwd){
                    localStorage.setItem("login",JSON.stringify({
                        id:uid,
                        name:docSnap.data().name,
                        isTchrs:docSnap.data().isTchrs? 1:0
                    }))
                }else{
                    alert("Wrong Password!")
                }
        } else {
        // docSnap.data() will be undefined in this case
        console.log("No such users");
        }
        uid=""
        pwd=""
    }
</script>
<Nav/>
<section class="flex items-center justify-center h-screen w-screen">
<div class="card h-[clamp(500px,50%,200px)] w-[clamp(300px,40%,200px)] shadow-purple-600 shadow-2xl rounded-xl  flex flex-col items-center pt-10 justify-between pb-10">
<h1 class="text-3xl">
    Log In
</h1>
<div class="">
    <label for="uid">UID</label>
<input type="text" bind:value={uid} id="uid" class="ring-purple-800/50 h-10 ring-1 w-full rounded-md cursor-text">
</div>
<div class="mt-[0%] cursor-pointer">
    <label for="pwd">Password</label>
<input type="password" bind:value={pwd} id="pwd" class="ring-purple-800/50 h-10 ring-1 w-full rounded-md cursor-text">
</div>
<button class="ring-1 ring-purple-800 py-2 px-4 rounded-full" on:click={login}>Login</button>
</div>

</section>
<style>
    section{
    font-family: "Alex Brush";

    }
    input{
        font-family: sans-serif;
        padding:5px;
    }
</style>