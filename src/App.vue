<template>
  <div id="app">
    <h1>Shopping List</h1>
    <ul class="list">
      <Item v-bind:key="item.id" v-for="item in items" :content="item.content" :id="item.id"/>
      <li>
        <input placeholder="..." class="textinput" type="text">
      </li>
    </ul>
    <button class="btn btn--clear">Clear Checked Items</button>
  </div>
</template>

<script>
import firebase from 'firebase';
import Item from './components/Item.vue'

// Your web app's Firebase configuration
var firebaseConfig = {
  apiKey: "AIzaSyCmGU31xCrlUbx07BvXXhxa0TFRo_pNkvQ",
  authDomain: "pwa-shopping.firebaseapp.com",
  databaseURL: "https://pwa-shopping.firebaseio.com",
  projectId: "pwa-shopping",
  storageBucket: "pwa-shopping.appspot.com",
  messagingSenderId: "1092029981247",
  appId: "1:1092029981247:web:175b1f15ca4d46be"
};
// Initialize Firebase
firebase.initializeApp(firebaseConfig);

const db = firebase.firestore();
const messaging = firebase.messaging();
messaging.usePublicVapidKey("BG8BOD2KffiPxCfzKcLKEo5qYdXUDoWKGEs1VRGLhUb86CddjGyrkQ_-yrBdOe68LrrarodDkRmfSryKoaEMsvE"); // 1. Generate a new key pair
let notificationPermission = false;

export default {
  name: 'app',
  components: {
    Item
  },
  data: function() {
    return {
      items: []
    }
  },
  mounted() {
    // Load Initial Items
    db.collection('items').orderBy('date').onSnapshot((snapshot) => {
      this.items = [];
      snapshot.docs.forEach((doc) => {
        this.items.push({content: doc.data().content, id: doc.id});
      })
    });

    // Add Item
    const textinput = document.querySelector('.textinput')
    textinput.addEventListener('keypress', (event) => {
      const key = event.which || event.keyCode;
      if (key === 13) { // 13 is enter
        db.collection('items').add({
          content: textinput.value,
          date: Date.now(),
        });
        textinput.value = '';

        if (!notificationPermission) {
          // Request Permission of Notifications
          messaging.requestPermission().then(() => {
            console.log('Notification permission granted.');
            notificationPermission = true;
            // Get Token
            messaging.getToken().then((token) => {
              console.log(token)
            })
          }).catch((err) => {
            console.log('Unable to get permission to notify.', err);
          });
        }
      }
    })

    // Delete all Checked Items
    const btnClear = document.querySelector('.btn--clear');
    btnClear.addEventListener('click', () => {
      if (!notificationPermission) {
        // Request Permission of Notifications
        messaging.requestPermission().then(() => {
          console.log('Notification permission granted.');
          notificationPermission = true;
          // Get Token
          messaging.getToken().then((token) => {
            console.log(token)
          })
        }).catch((err) => {
          console.log('Unable to get permission to notify.', err);
        });
      }

      document.querySelectorAll('input[type=checkbox]:checked').forEach((cb) => {
        db.collection('items').doc(cb.id).delete();
      })
  });
  },
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

* {
  box-sizing: border-box;
}

body {
  background-color: cadetblue;
}

.list {
  list-style-type: none;
  padding: 0;
  max-width: 600px;
  margin: 0 auto;
}

.textinput {
  width: 100%;
  line-height: 3;
  padding: 0 1rem;
  border: none;
  border-bottom: cadetblue 3px solid;
  background-color: lightblue;
  font-size: 1rem;
  font-weight: bold;
  color: #2c3e50;
}

.btn {
  padding: 1rem;
  background-color:mediumseagreen;
  border: none;
  color: whitesmoke;
  font-weight: bold;
  margin-top: 1rem;
}

.btn:hover {
  background-color: teal;
  cursor: pointer;
}
</style>
