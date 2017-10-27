<template>
  <div id="app" class="container">

    <!-- basic title -->
    <div class="page-header">
      <h1>Vue.js 2 & Firebase database example</h1>
    </div>

    <!-- form for adding a book -->
    <div class="panel panel-default">
      <div class="panel-heading">
        <h3>Add Book</h3>
      </div>
      <div class="panel-body">
        <!-- using submit.prevent to prevent/override the default form submit behaviour -->
        <form id="form" class="form-inline" v-on:submit.prevent="addBook">
          <div class="form-group">
            <label for="bookTitle">Title:</label>
            <input type="text" id="bookTitle" class="form-control" v-model="newBook.title">
          </div>
          <div class="form-group">
            <label for="bookAuthor">Author:</label>
            <input type="text" id="bookAuthor" class="form-control" v-model="newBook.author">
          </div>
          <div class="form-group">
            <label for="bookUrl">URL:</label>
            <input type="text" id="bookUrl" class="form-control" v-model="newBook.url">
          </div>
          <input type="submit" class="btn btn-primary" value="Add Book">
        </form>
      </div>
    </div>

    <!-- table of books -->
    <div class="panel panel-default">
      <div class="panel-heading">
        <h3>List of Books:</h3>
      </div>
      <div class="panel-body">
        <table class="table table-striped">
          <thead>
            <tr>
              <th>Title</th>
              <th>Author</th>
              <th>Action</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="book in books">
              <td><a v-bind:href="book.url">{{book.title}}</a></td>
              <td>{{book.author}}</td>
              <td><span class="glyphicon glyphicon-trash" v-on:click="removeBook(book)"></span></td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

  </div>
</template>

<script>
  // import fancy toastr notifications
  import toastr from 'toastr';

  // get access to my firebase database
  import Firebase from 'firebase';
  var config = { // TODO: you need to copy from your personal firebase database private access info etc. here:
    apiKey: "...",
    authDomain: "...",
    databaseURL: "...",
    projectId: "...",
    storageBucket: "...",
    messagingSenderId: "..."
  };

  // get reference to firebase database node 'books'
  let app = Firebase.initializeApp(config);
  let db = app.database();
  let booksRef = db.ref('books');

  // set up vue code that connects to firebase
  export default {
    name: 'app',
    firebase: {
      books: booksRef // this will be binded in that v-for="book in books"
    },
    data() {
      return {
        newBook: {
          title: '',
          author: '',
          url: ''
        }
      }
    },
    methods: {
      addBook: function() {
        booksRef.push(this.newBook);
        this.newBook.title = '';
        this.newBook.author = '';
        this.newBook.url = '';
      },
      removeBook: function(book) {
        // remove from firebase database (vue will auto-update v-for book in books)
        // select child to delete by getting key with book['.key']
        booksRef.child(book['.key']).remove();
        // show fancy notification using toastr for user-friendliness
        toastr.success("Book removed.");
      }
    }
  };
</script>

<style>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    color: #2c3e50;
    margin-top: 60px;
  }
</style>
