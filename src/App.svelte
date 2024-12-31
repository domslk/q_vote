<script>
    
    import { initializeApp } from "firebase/app";
    import { getFirestore, collection, query, where, getDocs, addDoc } from "firebase/firestore";
    // TODO: Add SDKs for Firebase products that you want to use
    // https://firebase.google.com/docs/web/setup#available-libraries
    
    const firebaseConfig = {
      apiKey: "AIzaSyBXmXezC-F-3sEY8nHxhqq-604Bm6iO_SE",
      authDomain: "qvote-a2daa.firebaseapp.com",
      projectId: "qvote-a2daa",
      storageBucket: "qvote-a2daa.firebasestorage.app",
      messagingSenderId: "695706303805",
      appId: "1:695706303805:web:a6f2015b98ca2b0fd71954"
    };
    
    const app = initializeApp(firebaseConfig);
    const database = getFirestore(app);
    
    $:schools = []
    let newSchool = ""
    let searchInput = ""
    
    const addSchool = async () => {
        if (newSchool != "") {
            try {
                await addDoc(collection(database, "schools"), {
                name: newSchool,
            }); 
            } catch (e) {
                console.error("Error adding doc", e);
            }
        }
    }
    
    
    const fetchSchools = async () => {
        const querySnapshot = await getDocs(collection(database, "schools"));
        schools = [];
        querySnapshot.forEach((doc) => {
            schools.push({ id: doc.id, ...doc.data() });
        });
        console.log("Fetched!")
    };
    
    const searchSchools = async () => {
        const q = query(
            collection(database, "schools"),
            where("name", ">=", searchInput),
        );
        const querySnapshot = await getDocs(q);
        schools = []; 
        querySnapshot.forEach((doc) => {
          schools.push({ id: doc.id, ...doc.data() });
        });

    }

    fetchSchools();
    $: filteredSchools = schools.filter((school) =>
  school.name.toLowerCase().includes(searchInput.toLowerCase())
);

    </script>
    
    <main>
        <h1>Qvote</h1>

        <input type="text" bind:value={newSchool} placeholder="add school">
        <button on:click={addSchool}>Add school</button>

        <input type="text" placeholder="search" bind:value={searchInput} on:input={searchSchools}>
        <h2>List of schools below</h2>
        <ul>
            {#each filteredSchools as school}
            <li>{school.name}</li>
        
            {/each}
        </ul>
    </main>
    
    <style>
        main {
            text-align: center;
            padding: 1em;
            max-width: 240px;
            margin: 0 auto;
        }
    
        h1 {
            color: #ff3e00;
            text-transform: uppercase;
            font-size: 4em;
            font-weight: 100;
        }
    
        @media (min-width: 640px) {
            main {
                max-width: none;
            }
        }
    </style>
