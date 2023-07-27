<script context="module">
  import { writable } from "svelte/store";
  import { firebaseConfig } from "../lib/firebaseConfig.svelte";
  import { initializeApp, getApps, getApp } from "firebase/app";
  import { GoogleAuthProvider, signInWithPopup, getAuth } from "firebase/auth";
  import { Button } from "svelte-materialify";
  import { getFirestore, collection, doc, addDoc } from "firebase/firestore";

  const app = !getApps().length ? initializeApp(firebaseConfig) : getApp();
  const provider = new GoogleAuthProvider();
  const auth = getAuth(app);
  const db = getFirestore(app);

  export let isAuthenticated = writable(false);
  export const username = writable(" ");
  export const userDisplayName = writable("");
  export const userPhotoURL = writable("");
  export const userEmail = writable("");

  function login() {
    signInWithPopup(auth, provider)
      .then((result) => {
        // This gives you a Google Access Token. You can use it to access the Google API.
        const credential = GoogleAuthProvider.credentialFromResult(result);
        const token = credential.accessToken;
        // The signed-in user info.
        const user = result.user;
        // ...
        console.log(user);
        username.set(user.uid);
        userDisplayName.set(user.displayName);
        userPhotoURL.set(user.photoURL);
        userEmail.set(user.email);
        isAuthenticated.set(true);
      })
      .catch((error) => {
        // Handle Errors here.
        const errorCode = error.code;
        const errorMessage = error.message;
        // The email of the user's account used.
        const email = error.customData.email;
        // The AuthCredential type that was used.
        const credential = GoogleAuthProvider.credentialFromError(error);
        // ...
      });
  }
</script>

<script>
</script>

{$userEmail}
<Button on:click={login}>Login</Button>
