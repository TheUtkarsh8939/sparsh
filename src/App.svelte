<script>
  import Router from "svelte-spa-router"
  import Main from "./main.svelte";
  import Login from "./login.svelte"
  import Page from "./page.svelte"
  import Add from "./add.svelte"
  import Talk from "./talk.svelte"
// Import the functions you need from the SDKs you need
import { initializeApp } from "firebase/app";
import { getAnalytics } from "firebase/analytics";
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
const analytics = getAnalytics(app);
  
</script>
<Router routes={{
 "/":Main,
 "/login":Login,
 "/page/:abc": Page,
 "/addNote/:to": Add,
 "/talk":Talk
}}/>