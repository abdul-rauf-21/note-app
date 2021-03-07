<template>
  <div id="note-wrapper">
    <div id="action">
      <button
        id="btn-back" 
        @click="backToHome"
      >
      Back to Home
      </button>
      <div id="doc-manipulation">
        <button
          id="btn-save" 
          @click="saveNote"
        >
        Save
        </button>
        <button
          id="btn-delete"
          ref="btnDelete" 
          @click="deleteNote"
        >
        Delete
        </button>
      </div>
    </div>
    <div
      id="note-text"
      ref="noteText"
      contenteditable
    >
    </div>
    <div
      ref="overlay" 
      id="overlay"
    ></div>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import Localbase from 'localbase'

export default {
  name: 'Note',
  setup() {
    const router = useRouter()
    const route = useRoute()
    
    let db = new Localbase('db')
    let noteText = ref(null)
    let btnDelete = ref(null)
    let key = route.params.key

    db.config.debug = false

    const generateKey = () => {
      const combination = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z']
      let key = ''

      Array(10).fill().forEach(_ => {
        key += combination[Math.floor(Math.random() * (combination.length - 1))]
      })

      return key
    }

    const saveNote = () => {
      if ((/\w/g).test(noteText.value.textContent)) {
        if (key === 'new') {
          key = generateKey()
          db.collection('notes').add({
            key,
            text: noteText.value.textContent
          })
          router.push(key)
          btnDelete.value.disabled = false
        } else {
          db.collection('notes').doc({ key }).update({
            text: noteText.value.textContent
          })
        }
      } else {
        alert('Note can\'t be empty.')
      }
    }

    const deleteNote = async () => {
      if (key !== 'new') {
        if (confirm('Are you sure want to delete this note ?')) {
          await db.collection('notes').doc({ key }).delete()
          backToHome()
        }
      }
    }

    const backToHome = () => {
      router.replace('/')
    }

    onMounted (async () => {
      if (key === 'new') {
        btnDelete.value.disabled = true
      } else {
        btnDelete.value.disabled = false
        noteText.value.contenteditable = false
        noteText.value.textContent = await db.collection('notes').doc({ key }).get().then( data => {
          if (data) return data.text
          router.replace('/not-found')
        })
        noteText.value.contenteditable = true
      }
    })

    return {
      noteText,
      btnDelete,
      saveNote,
      deleteNote,
      backToHome,
    }
  }
}

</script>

<style lang="scss">
  $dark: #000000;
  $gray: #1F1F1F;

  * {
    margin: 0;
    padding: 0;
  }

  body {
    background-color: $dark;
    font-family: Montserrat, Verdana;
  }

  #note-wrapper {
    margin: 5vh auto;
    width: 60vw;
    font-family: Montserrat, Verdana;
    box-sizing: border-box;
    

    button {
      padding: 0.5rem 0.8rem;
      font-family: inherit;
      font-weight: bold;
      cursor: pointer;
      outline: none;
      background-color: $gray;
      border: none;
    }

    button[disabled] {
      cursor: no-drop;
    }

    #action {
      box-sizing: border-box;
      width: 100%;
      display: flex;
      flex-direction: center;
      justify-content: space-between;

      #btn-back {
        background-color: $gray;
        color: white;
      }
    }

    #doc-manipulation {
      
      #btn-save {
        color: #3498db;
      }

      #btn-delete {
        margin-left: 2rem;
        color: #e74c3c;
      }
    }
  }
  
  #note-text {
    margin: 1rem 0;
    min-height: 40vh;
    padding: 1rem;
    background-color: $gray;
    outline: none;
    color: white;
    text-align: left;
    font-family: inherit;
  }

  @media only screen and (max-width: 768px) {
    #note-wrapper {
      width: 95vw;
    }
  }
</style>