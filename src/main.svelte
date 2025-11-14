<script>
  import Nav from "./lib/nav.svelte";
  import { initializeApp } from "firebase/app";
  import {
    getFirestore,
    doc,
    getDoc,
    where,
    query,
    collection,
    getDocs,
    setDoc,
  } from "firebase/firestore";
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
  /**
   * @type {import("@firebase/firestore").QuerySnapshot<import("@firebase/firestore").DocumentData, import("@firebase/firestore").DocumentData> | import("@firebase/firestore").QueryDocumentSnapshot<import("@firebase/firestore").DocumentData, import("@firebase/firestore").DocumentData>[]}
   */
  let docs;
  /**
   * @type {never[]}
   */
  let docList = [];
  const app = initializeApp(firebaseConfig);
  const db = getFirestore(app);
  const login = localStorage.getItem("login");
  if (login !== null) {
    const logJson = JSON.parse(login);
    const uid = logJson.id;
    const docQuery = doc(db, "user", uid);
    // @ts-ignore
    const docData = await getDoc(docQuery);
    const data = docData.data();
    let related = [];
    let querySnap = query(
      collection(db, "user"),
      where("students", "array-contains", uid)
    );
    if (data === undefined) {
      throw "Major error";
    }
    if (data.isTchrs === true) {
      console.log("Teacher");
      related = data.students;
      querySnap = query(
        collection(db, "user"),
        where("teachers", "array-contains", uid)
      );
    } else {
      console.log("Student");
      related = data.teachers;
      querySnap = query(
        collection(db, "user"),
        where("students", "array-contains", uid)
      );
    }
    // @ts-ignore
    docs = await getDocs(querySnap);
    docs.forEach((doc) => {
      console.log(doc.id, "=>", doc.data());
      let dcd = doc.data();
      dcd.uid = doc.id;
      docList.push(dcd);
    });
    console.log(docList);
  }
  let localSt = [];
  docList.forEach((doc) => localSt.push({ name: doc.name, id: doc.uid }));
  localStorage.setItem("names", JSON.stringify(localSt));


</script>

<Nav />
<section class="pt-[200px] flex flex-col items-center">
  <h1 class="text-4xl" style="font-family: Alex brush, sans-serif;">
    {#if login === null}
      Log In To See More
    {:else}
      Hi {JSON.parse(login).name}
    {/if}
  </h1>
  {#each docList as doc}
    <a href="#/page/{doc.uid}">
      <div
        class="card h-[200px] w-[200px] rounded-lg inset-ring-1 inset-ring-purple-500 flex items-center justify-center"
        style="font-family: Alex brush, sans-serif;"
      >
        <h1 class="text-2xl">{doc.name}</h1>
      </div>
    </a>
  {/each}
</section>
