<template>
    <div>
        <nav class="navbar navbar-expand-sm bg-dark navbar-dark">
        <a class="navbar-brand" href="#">Test</a>
        <ul class="navbar-nav ml-auto">
            <li class="nav-item">
            <a class="nav-link" href="#">Autor: Igor Gašparović</a>
            </li>
        </ul>
        </nav>

        <div class="container mt-5">

        <button class="btn btn-outline-primary" v-b-modal.newrecordmodal>&#10010 Dodaj novi zadatak</button>

        <!-- Modal Component -->
        <b-modal id="newrecordmodal" ref="modal" title="Zadatak" @ok="add" @shown="clearform">
            <form @submit.stop.prevent="handleSubmit">
            <div class="form-group">
                <label for="date">Datum / vrijeme:</label>
                <input v-model="record.date" type="text" class="form-control" id="date" :readonly=true >
            </div>
            <div class="form-group">
                <label for="name">Ime zadatka:</label>
                <input v-model="record.name" type="text" class="form-control" id="name" >
            </div>
            <div class="form-group">
                <label for="description">Opis zadatka:</label>
                <input v-model="record.description" type="text" class="form-control" id="description" >
            </div>
            </form>
        </b-modal>

        <!-- Modal Component -->
        <!-- <b-modal id="editrecordmodal" ref="modal_edit" title="Zadatak" @ok="update" >
            <form @submit.stop.prevent="handleUpdate">
            <div class="form-group">
                <label for="date">Datum / vrijeme:</label>
                <input v-model="record.date" type="text" class="form-control" id="date" >
            </div>
            <div class="form-group">
                <label for="name">Ime zadatka:</label>
                <input v-model="record.name" type="text" class="form-control" id="name" >
            </div>
            <div class="form-group">
                <label for="description">Opis zadatka:</label>
                <input v-model="record.description" type="text" class="form-control" id="description" >
            </div>
            </form>
        </b-modal> -->

        <!-- Modal Delete -->
        <b-modal id="deleterecordmodal" ref="modal_delete" :title="delmsg" @ok="remove" @shown="displayrecords" >
            <form @submit.stop.prevent="deleteSubmit">
            <ul v-for="(x,k) in recordstoremove" :key="k">
                <li>{{x.name}} ({{x.date}})</li>
            </ul>
            </form>
        </b-modal>

        <div class="mt-5">
        <div class="table-responsive">
        <div class="row">
            <div class="col-4">
                <div class="input-group mb-3">
                <input v-model="search" type="text" class="form-control" placeholder="Pretraži..." aria-label="Recipient's username" aria-describedby="basic-addon2">
                <div class="input-group-append">
                    <span class="input-group-text" id="basic-addon2">&#128270</span>
                </div>
                </div>
            </div>
        </div>
        

        <table class="table">
            <thead>
                <tr>
                <th><button class="btn btn-outline-danger"  v-b-modal.deleterecordmodal >&#10008 Briši</button></th>
                <th>ID</th>
                <th>Ime Zadatka 
                    <div class="btn-group-vertical">
                    <button type="button" class="btn btn-default" @click="sort('name')">&#8693</button>
                    </div>
                </th>
                <th>Opis Zadatka
                    <button type="button" class="btn btn-default" @click="sort('description')">&#8693</button>
                </th>
                <th>Zapis kreiran</th>
                <th></th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(x,k) in filteredList.slice(pageslice, pageslice+recordsperpage)" :key="k">
                <td><input type="checkbox" v-model="x.isselected" /></td>
                <td>{{x.id}}</td>
                <td>{{x.name}}</td>
                <td>{{x.description}}</td>
                <td>{{x.date}}</td>
                <td>
                    <button class="btn btn-outline-primary" @click="edit(x,k)">&#9998 Uredi</button>
                    <router-link :to="{ name: 'Details', params: { record: x } }" class="btn btn-outline-primary">&#63 Detalji</router-link>
                </td>
                </tr>
            </tbody>
            </table>

            <ul class="pagination">
            <li class="page-item"><a class="page-link" href="#" @click="setPage(page-1)">Previous</a></li>
            <li class="page-item" :class="x==page?'active':''" v-for="(x,k) in pages" :key="k"><a class="page-link" href="#" @click="setPage(x)">{{x}}</a></li>
            <li class="page-item"><a class="page-link" href="#" @click="setPage(page+1)">Next</a></li>
            </ul>

        </div>
      </div>
    </div>
        
        <pre>{{filteredList}}</pre>
        <h2>{{page}} ({{pageslice}})</h2>
        <pre>{{pages}}</pre>
        <pre>{{records}}</pre>

    </div>
    
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      record: {
        id: null,
        name: null,
        description: null,
        date: null,
        isselected: false
      },
      records: [],
      recordstoremove: [],
      delmsg: null,
      page: 1,
      pageslice: 0,
      recordsperpage: 5,
      editmode: false,
      curridx: 0,
      search: null
    }
  },
  methods: {
    clearform() {
      this.record.name = null;
      this.record.description = null;
      var d = new Date();
      this.record.date = d.toLocaleDateString() + ', ' + d.toLocaleTimeString();
      this.record.isselected = false;
    },
    add(e) {
      e.preventDefault();
      if (!this.record.name) {
        alert('Please enter name')
      } else {
        this.handleSubmit()
      }
    },
    handleSubmit() {
        this.record.id = this.records.length;
        var record = JSON.parse(JSON.stringify(this.record))
        this.records.push(record);
        this.saveToLocalStorage(this.records);
      this.$nextTick(() => {
        this.$refs.modal.hide()
      })
    },
    update(e) {
      e.preventDefault();
      if (!this.record.name) {
        alert('Please enter name')
      } else {
        this.handleUpdate()
      }
    },
    handleUpdate() {
      this.records[this.curridx] = this.record;
      this.saveToLocalStorage(this.records);
      this.$nextTick(() => {
        this.$refs.modal.hide()
      })
    },
    edit(x, idx) {
      this.record = x;
      this.curridx = idx;
    },
    remove() {
      if(this.recordstoremove.length>0){
        this.deleteSubmit();
      } 
    },
    displayrecords() {
      this.delmsg = null;
      var records = this.records;
      this.recordstoremove = [];
      var _records =  this.recordstoremove;
      this.recordstoremove = [];
      records.forEach(function(record, key){
        if(record.isselected==true) {
          _records.push(record);
        }
      })
      this.recordstoremove = _records;
      if(this.recordstoremove.length==0){
        this.delmsg = 'Odaberi zapis/e.'
      } else {
        this.delmsg = 'Are you sure you want to delete this records?'
      }
    },
    deleteSubmit() {
      var records = this.records;
      var _records = [];
      records.forEach(function(record, key){
        if(record.isselected==false){
          _records.push(record);
        }
      })
      this.records = _records;
      this.saveToLocalStorage(this.records);
    },
    setPage (x) {
      if (x < 0 || x > this.pages.length) return false;
      this.page = x;
      if (x > 1) {
        this.pageslice = (x-1) * this.recordsperpage;
      } else {
        this.pageslice = 0;
      }
    },
    sort(val) {
      var records = this.records;
        records.reverse(function(a, b){
        switch(val){
             case 'name':
              var x = a.name.toLowerCase();
              var y = b.name.toLowerCase();
             break;
             case 'description':
              var x = a.description.toLowerCase();
              var y = b.description.toLowerCase();
             break;
           }
        if (x < y) {return -1;}
        if (x > y) {return 1;}
        return 0;
      });
    },
    saveToLocalStorage(x){
      localStorage.setItem('records', JSON.stringify(x));
    }
  },
  computed: {
    pages() {
      var p = 0;
      var x = this.records.length;
        var pages = [];
        for (var i = 1; i < (x / this.recordsperpage) + 1; i++) {
            pages.push(i);
        }
      return pages;
    },
    filteredList() {
      debugger;
      if(this.search){
         return this.records.filter(x => {
          return x.name.toLowerCase().includes(this.search.toLowerCase())
        })
      } else {
        return this.records;
      }
    }
  },
  created: function() {
    if(localStorage.records){
        this.records = JSON.parse(localStorage.records);
    }
  }
}
</script>