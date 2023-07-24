<script>
  import {
    userDisplayName,
    userEmail,
    userPhotoURL,
    username,
  } from "./Auth.svelte";
  import {
    getFirestore,
    collection,
    doc,
    addDoc,
    getDoc,
    getDocs,
  } from "firebase/firestore";
  import { initializeApp, getApps, getApp } from "firebase/app";
  import { firebaseConfig } from "../lib/firebaseConfig.svelte";
  import {
    AppBar,
    Button,
    Icon,
    Dialog,
    Container,
    Row,
    Col,
    MaterialApp,
    Card,
    TextField,
  } from "svelte-materialify";
  import { mdiBookPlusMultipleOutline } from "@mdi/js";

  let active = false;
  let title = "";
  const app = !getApps().length ? initializeApp(firebaseConfig) : getApp();
  const db = getFirestore(app);
  let Documents = [];

  function goHome() {
    location.replace(`${window.location.protocol}${window.location.host}`);
  }
  async function addDocument() {
    if (!title) {
      return;
    }
    const docRef = doc(db, "userDocs", $userEmail);
    const colRef = collection(docRef, "docs");
    const result = await addDoc(colRef, {
      title,
      Content: "",
    });
    // @ts-ignore
    getDocument(result._path.segments[3]);
  }
  async function getDocument(docId) {
    const docRef = doc(db, "userDocs", $userEmail);
    const colRef = doc(docRef, "docs", docId);
    const docSnap = await getDoc(colRef);

    if (docSnap.exists()) {
      console.log("Document data:", docSnap.data());
      location.replace(`${window.location.href}#/doc/${$userEmail}/${docId}`);
    }
  }
  async function getDocuments() {
    const querySnapshot = await getDocs(
      collection(db, "userDocs", $userEmail, "docs")
    );
    querySnapshot.forEach((doc) => {
      // doc.data() is never undefined for query doc snapshots
      let document = doc;
      Documents = [...Documents, [doc.data(), doc.id]];
      console.log(Documents);
    });
  }
  getDocuments();
</script>

<MaterialApp>
  <AppBar>
    <a slot="title" href="localhost:5173/"
      ><img src="assets/doc.png" alt="doc" /></a
    >
  </AppBar>

  <div class="align-self-center grey lighten-2">
    <Container><p class="text-h6">Start a new document</p></Container>
    <div class="d-flex justify-center">
      <div>
        <Row>
          <Col>
            <Button
              on:click={() => (active = true)}
              style="padding: 100px 50px"
            >
              <Icon size="32px" path={mdiBookPlusMultipleOutline} />
            </Button>
          </Col>
        </Row>
      </div>
    </div>
  </div>

  <Container>
    <p class="text-h6">Documents</p>
    {#each Documents as document}
      <div class="d-flex justify-center" style="margin-top: 10px;">
        <Button on:click={() => getDocument(document[1])} block
          >{document[0].title}</Button
        >
      </div>
    {/each}
  </Container>

  <Dialog class="pa-4 text-center" bind:active>
    <TextField filled bind:value={title}>Document name</TextField>
    <Button on:click={addDocument}>Add document</Button>
  </Dialog>
</MaterialApp>
