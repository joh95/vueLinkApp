
<template>
  <div id="app" class="container">
      <h1>Tasks List</h1>

    <div class ="card">
      <div class = "card-header">
        <h3>Add a Task</h3>
      </div>
      <div class="card-body">
        <form v-on:submit.prevent="addLink"
              class="form-inline">
          <div class="form-group">
            <label for="">Title</label>
            <input v-model="newLink.title" class= "form-control" placeholder ="Title" type="text">
          </div>

          <div class="form-group">
            <label for="">Descripción</label>
            <input v-model="newLink.description" class= "form-control" placeholder ="description" type="text">
          </div>

          <div class="form-group">
            <label for="">Link</label>
            <input v-model="newLink.link" class= "form-control" placeholder ="Url" type="text">
          </div>

          <input type="submit" class="btn btn-success" value="Add a task">  
        </form>
      </div>
    </div>

    <hr>
    <div class="card">
      <div class="card-header">
        <h3>Task list</h3>
      </div>
      <div class="card-body">
        <table class="table table-striped">
          <thead>
            <tr>
              <th>#</th>
              <th>Start date</th>
              <th>Final date</th>
              <th>Title</th>
              <th>Description</th>
              <th>State</th>
              <th>Delete</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(link, index) in links" :key="link.title">
              <td >
                {{index + 1}}
              </td>
              <td>
                  {{link.startDate}}
              </td>
              <td>
                  {{link.finalDate}}
              </td>
              <td>
                  <a target="_blank" v-bind:href="link.link">{{ link.title }}</a>
              </td>
              <td>
                {{link.description}}
              </td>
              <td>
                <button
                  v-if="link.state==false"
                  v-bind:class="'btn btn-danger'"
                  v-on:click.stop="link.state=!link.state" 
                  v-on:click="updateLink(link)">
                  <i class="fas fa-window-close"></i>
                </button>
                <button
                  v-else
                  v-bind:class="'btn btn-success'"
                  @click="link.state=!link.state"
                  v-on:click="updateLink(link)">
                 <i class="fas fa-check-circle"></i>
                </button>
              </td>
              <td>
                <button v-on:click="deleteLink(link)" class="btn btn-danger">
                  <i class="fas fa-trash-alt"></i>
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld'
import Firebase,{ functions } from 'firebase'
import toastr from 'toastr'
import moment from 'moment'

//Configuración firebase
let config = {
    apiKey: "AIzaSyArKPeYHl1tyikixr5sA-yUrTMM2xMaxqM",
    authDomain: "vuelinkapp-d91d5.firebaseapp.com",
    databaseURL: "https://vuelinkapp-d91d5.firebaseio.com",
    projectId: "vuelinkapp-d91d5",
    storageBucket: "vuelinkapp-d91d5.appspot.com",
    messagingSenderId: "980225663570"
};

let app = Firebase.initializeApp(config);
let db = app.database();

let linksRefs = db.ref('links');

export default {
  name: 'App',
  firebase:{
    links: linksRefs
  },
  data(){
    return {
      newLink: {
        title: '',
        startDate: moment().format('MMMM DD, h:mm a'),
        finalDate: "Sin definir",
        description: '',
        link: '',
        state: false,
      }
    }
  },
  methods:{
    addLink: function () {
      linksRefs.push(this.newLink);
      toastr.success('Saved task');
      this.newLink.title = '';
      this.newLink.description = '';
      this.newLink.link = '';
    },
    deleteLink: function (link) {
       linksRefs.child(link['.key']).remove();
       toastr.success('Deleted task');   
    },
    updateLink: function (link){
      linksRefs.child(link['.key']).update({"state":link.state});
      let date = moment().format('MMMM DD, h:mm a');
      linksRefs.child(link['.key']).update({"finalDate":date})
      toastr.success('Changes completed');
    }
  }
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
</style>
