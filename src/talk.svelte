<script>
  import Nav from "./lib/nav.svelte";
  import { initializeApp } from "firebase/app";
  import { GoogleGenerativeAI } from "@google/generative-ai";
  import {
    getFirestore,
    doc,
    getDoc,
    where,
    query,
    collection,
    getDocs,
    QuerySnapshot,
  } from "firebase/firestore";
  import { onMount } from "svelte";
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

  const app = initializeApp(firebaseConfig);
  const db = getFirestore(app);
  const login = JSON.parse(localStorage.getItem("login"));
  const uid = login.id;
  let querySnap = query(
    collection(db, "user"),
    where("students", "array-contains", uid)
  );
  if (login.isTchrs === true) {
    console.log("Teacher");
    querySnap = query(
      collection(db, "user"),
      where("teachers", "array-contains", uid)
    );
  } else {
    console.log("Student");
    querySnap = query(
      collection(db, "user"),
      where("students", "array-contains", uid)
    );
  }
  const docs = await getDocs(querySnap);
  let docIds = [];
  let mainRes = {};
  docs.forEach(
    (doc) => (docIds = [...docIds, { id: doc.id, name: doc.data().name }])
  );
  docIds.forEach(async (docId) => {
    let col = `${uid}&${docId.id}`;
    if (login.isTchrs == 1) {
      col = `${docId.id}&${uid}`;
    }
    const docsRef = collection(db, col);
    const docsSnap = await getDocs(docsRef);
    let restructureTemp = [];
    docsSnap.forEach(
      (doc) => (restructureTemp = [...restructureTemp, doc.data()])
    );
    // @ts-ignore
    mainRes[docId.name] = restructureTemp;
  });
  console.log(mainRes);
  const genAI = new GoogleGenerativeAI(
    "AIzaSyBgCxBkpjrB3AreQdkg38eEhj_TqsYQvlk"
  );
  async function askGemini(prompt) {
    const model = genAI.getGenerativeModel({ model: "gemini-2.5-pro" });

    const result = await model.generateContent(prompt);
    const responseText = result.response.text();

    console.log(responseText);
    return responseText;
  }

  let msgHistory = [];
  let msg;
  async function sendMsg() {
    msgHistory = [...msgHistory, { msg: msg, sender: "user" }];
    let reply = await askGemini(msg);
    msgHistory = [...msgHistory, { msg: reply, sender: "model" }];
  }
  onMount(() => {
    console.log("Init Ai")
    askGemini(
      "CONTEXT:You are a emotional-support/helper bot of a student or a teacher in an App called sparsh, teachers and students here can add notes to each other, some notes are priavte while some are public, during your resut generation you can use either of these notes but while you can refrence public notes, never reference private notes directly Here are the note details for user from each of their student/teacher, these notes are context helping you to generate, you are here to ask actual questions. here are the notes: in json formt" +
        JSON.stringify(mainRes)
    );
  });
  console.log(msgHistory)
</script>

<Nav />
<section class="pt-[200px]">
  <div class="w-screen flex justify-end">
    <div class="msg bg-purple-100 max-w-2/5 rounded-xl p-3">
      <pre>By You</pre>
      Hello
    </div>
  </div>
  <div class="w-screen flex justify-start">
    <div class="msg bg-purple-100 max-w-2/5 rounded-xl p-3">
      <pre>By Model</pre>
      Hello
    </div>
  </div>
</section>
<div
  class="bottom flex absolute bottom-0 w-screen h-20 p-5 items-center justify-around bg-purple-100"
>
  <input
    type="text"
    bind:value={msg}
    class="text bg-white w-8/10 rounded-lg h-10 ring-1 ring-purple-700"
  />
  <button
    on:click={sendMsg}
    class="ring-1 ring-purple-500 bg-white h-10 w-10 rounded-lg">Send</button
  >
</div>
