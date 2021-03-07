<template>
	<div id="list-note-wrapper">
    <router-link 
      to="/n/new"
    >
    <button id="btn-add-new-note">Add New Note</button>
  </router-link>
		<div 
      id="notes"
      v-if="notes.length"
      >
			<div 
        class="note" 
        v-for="(note, index) in notes" 
        :key="index"
        >
        <p class="order">{{ index + 1 }}</p>
        <router-link :to="`/n/${ note.key }`">
          <p class="snippet">{{ snippet(note.text) }}</p>
        </router-link>
      </div>
		</div>
		<div
      id="empty" 
      v-else>
			<p>~ Empty ~<br> Click <b>Add New Note</b> to add a new note</p>
		</div>
	</div>
</template>

<script>
import { ref, onMounted } from 'vue'
import Localbase from 'localbase'

export default {
	setup () {
		let notes = ref([])

    const snippet = text => text.length > 20 ? `${ text.substring(0, 20) }...` : text

    onMounted (async () => {
      let db = new Localbase('db')

      db.config.debug = false

      await db.collection('notes').get().then( data => {
        if (data) { 
          notes.value = data
        }
      })
    })

		return {
			notes,
      snippet
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
  }

  #list-note-wrapper {
    margin: 5vh auto;
    font-family: Montserrat, Verdana;
    width: 60vw;
  }

	#btn-add-new-note {
    width: 100%;
    padding: 0.5rem;
    font-family: inherit;
    font-weight: bold;
    text-decoration: none;
    cursor: pointer;
    background-color: $gray;
    border: none;
    color: white;
    outline: none;

    &:hover {
      background-color: lighten($gray, 5%);
    }
  }

  #notes {
    width: 100%;
    margin-top: 2rem;

    & > * {
      margin: 1.5rem 0;
    }
  }

  .note {
    padding: 0;
    width: 100%;
    display: flex;
    flex-direction: row;
    box-sizing: border-box;

    .order {
      width: 5%;
      margin-right: 2%;
      padding: 0.5rem;
      background-color: $gray;
      color: white;
      box-sizing: border-box;
    }

    a {
      width: 93%;
      text-decoration: none;

      .snippet {
        text-align: left;
        min-width: 100%;
        padding: 0.5rem 1rem;
        background-color: $gray;
        color: white;
        box-sizing: border-box;

        &:hover {
          background-color: lighten($gray, 5%);
        }
      }
    }
  }

  #empty {
    margin-top: 2rem;
    width: 100%;
    color: white;

    p {
      letter-spacing: 1px;
      line-height: 2rem;
    }
  }

  @media only screen and (max-width: 768px) {
    #list-note-wrapper {
      margin: 5vh auto;
      width: 95vw;
    }

    .note {
      .order {
        width: 8%;
        margin-right: 5%;
      }

      a {
        width: 87%;
      }
    }
  }


</style>