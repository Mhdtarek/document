<script>
  import StarterKit from "@tiptap/starter-kit";
  import { Editor } from "@tiptap/core";
  import { onMount } from "svelte";
  import { initializeApp, getApps, getApp } from "firebase/app";
  import { firebaseConfig } from "../lib/firebaseConfig.svelte";
  import { getFirestore, doc, getDoc, updateDoc } from "firebase/firestore";
  import {
    AppBar,
    Button,
    Icon,
    Container,
    MaterialApp,
    Menu,
    List,
    ListItem,
    Divider,
    Dialog,
  } from "svelte-materialify";
  import {
    mdiFormatBold,
    mdiFormatItalic,
    mdiUndo,
    mdiRedo,
    mdiFormatListBulletedSquare,
    mdiFormatListNumbered,
    mdiFormatStrikethroughVariant,
    mdiXml,
    mdiCodeJson,
    mdiFormatQuoteClose,
    mdiMinus,
  } from "@mdi/js";

  export let params = {};
  let element;
  let editor;

  const app = !getApps().length ? initializeApp(firebaseConfig) : getApp();
  const db = getFirestore(app);

  let active = false;
  let docTitle = "";

  async function getDocument() {
    const docRef = doc(db, "userDocs", params.userEmail);
    const colRef = doc(docRef, "docs", params.docId);
    const docSnap = await getDoc(colRef);

    if (docSnap.exists()) {
      console.log("Document data:", docSnap.data());
      docTitle = docSnap.data().title;
      editor = new Editor({
        element: element,
        extensions: [StarterKit],
        content: docSnap.data().Content,
        onTransaction: () => {
          // force re-render so `editor.isActive` works as expected
          editor = editor;
        },
      });
    }
  }
  async function updateDocument() {
    const docRef = doc(db, "userDocs", params.userEmail);
    const colRef = doc(docRef, "docs", params.docId);
    await updateDoc(colRef, {
      Content: element.innerHTML,
    });
    active = true;
  }
  getDocument();
</script>

<MaterialApp>
  <AppBar>
    <a slot="title" href="localhost:5173/#"
      ><img src="assets/doc.png" alt="doc" /></a
    >
    <p>{docTitle}</p>
    <div style="flex-grow:1" />
    <Button on:click={updateDocument}>Save</Button>
  </AppBar>
  {#if editor}
    <Container>
      <div>
        <div>
          <Menu>
            <div slot="activator">
              <Button size="small">Text size</Button>
            </div>
            <List>
              <ListItem
                on:click={() => editor.chain().focus().setParagraph().run()}
                class={editor.isActive("paragraph") ? "is-active" : ""}
              >
                paragraph
              </ListItem>
              <ListItem
                on:click={() =>
                  editor.chain().focus().toggleHeading({ level: 1 }).run()}
                class={editor.isActive("heading", { level: 1 })
                  ? "is-active"
                  : ""}
              >
                h1
              </ListItem>
              <ListItem
                on:click={() =>
                  editor.chain().focus().toggleHeading({ level: 2 }).run()}
                class={editor.isActive("heading", { level: 2 })
                  ? "is-active"
                  : ""}
              >
                h2
              </ListItem>
              <ListItem
                on:click={() =>
                  editor.chain().focus().toggleHeading({ level: 3 }).run()}
                class={editor.isActive("heading", { level: 3 })
                  ? "is-active"
                  : ""}
              >
                h3
              </ListItem>
              <ListItem
                on:click={() =>
                  editor.chain().focus().toggleHeading({ level: 4 }).run()}
                class={editor.isActive("heading", { level: 4 })
                  ? "is-active"
                  : ""}
              >
                h4
              </ListItem>
              <ListItem
                on:click={() =>
                  editor.chain().focus().toggleHeading({ level: 5 }).run()}
                class={editor.isActive("heading", { level: 5 })
                  ? "is-active"
                  : ""}
              >
                h5
              </ListItem>
              <ListItem
                on:click={() =>
                  editor.chain().focus().toggleHeading({ level: 6 }).run()}
                class={editor.isActive("heading", { level: 6 })
                  ? "is-active"
                  : ""}
              >
                h6
              </ListItem>
            </List>
          </Menu>
          <Button
            on:click={() =>
              console.log && editor.chain().focus().toggleBold().run()}
            disabled={!editor.can().chain().focus().toggleBold().run()}
            class={editor.isActive("bold") ? "is-active" : ""}
            size="small"
          >
            <Icon path={mdiFormatBold} />
          </Button>
          <Button
            on:click={() => editor.chain().focus().toggleItalic().run()}
            disabled={!editor.can().chain().focus().toggleItalic().run()}
            class={editor.isActive("italic") ? "is-active" : ""}
            size="small"
          >
            <Icon path={mdiFormatItalic} />
          </Button>
          <Button
            on:click={() => editor.chain().focus().toggleStrike().run()}
            disabled={!editor.can().chain().focus().toggleStrike().run()}
            class={editor.isActive("strike") ? "is-active" : ""}
            size="small"
          >
            <Icon path={mdiFormatStrikethroughVariant} />
          </Button>

          <Divider vertical />
          <Button
            on:click={() => editor.chain().focus().unsetAllMarks().run()}
            size="small"
          >
            clear marks
          </Button>
          <Button
            on:click={() => editor.chain().focus().clearNodes().run()}
            size="small"
          >
            clear nodes
          </Button>
          <Button
            on:click={() => editor.chain().focus().toggleBulletList().run()}
            class={editor.isActive("bulletList") ? "is-active" : ""}
            size="small"
          >
            <Icon path={mdiFormatListBulletedSquare} />
          </Button>
          <Button
            on:click={() => editor.chain().focus().toggleOrderedList().run()}
            class={editor.isActive("orderedList") ? "is-active" : ""}
            size="small"
          >
            <Icon path={mdiFormatListNumbered} />
          </Button>
          <Button
            on:click={() => editor.chain().focus().toggleBlockquote().run()}
            class={editor.isActive("blockquote") ? "is-active" : ""}
            size="small"
          >
            <Icon path={mdiFormatQuoteClose} />
          </Button>
          <Button
            on:click={() => editor.chain().focus().toggleCodeBlock().run()}
            class={editor.isActive("codeBlock") ? "is-active" : ""}
            size="small"
          >
            <Icon path={mdiCodeJson} />
          </Button>
          <Button
            on:click={() => editor.chain().focus().toggleCode().run()}
            disabled={!editor.can().chain().focus().toggleCode().run()}
            class={editor.isActive("code") ? "is-active" : ""}
            size="small"
          >
            <Icon path={mdiXml} />
          </Button>
          <Button
            on:click={() => editor.chain().focus().setHorizontalRule().run()}
            size="small"
          >
            <Icon path={mdiMinus} />
          </Button>
          <Button
            on:click={() => editor.chain().focus().setHardBreak().run()}
            size="small"
          >
            hard break
          </Button>
          <Button
            on:click={() => editor.chain().focus().undo().run()}
            disabled={!editor.can().chain().focus().undo().run()}
            size="small"
          >
            <Icon path={mdiUndo} />
          </Button>
          <Button
            on:click={() => editor.chain().focus().redo().run()}
            disabled={!editor.can().chain().focus().redo().run()}
            size="small"
            ><Icon path={mdiRedo} />
          </Button>
        </div>
      </div>
    </Container>
  {/if}
  <div class="grey lighten-2">
    <Container>
      <div bind:this={element} />
    </Container>
  </div>
  <Dialog class="pa-4" bind:active>Saved</Dialog>
</MaterialApp>
