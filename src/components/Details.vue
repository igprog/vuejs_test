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
        <div class="text-right">
            <button class="btn btn-outline-primary" @click="navigate()">Nazad</button>
        </div>
        <h1>Detalji</h1>
        <div class="form-group">
                <label for="id">ID:</label>
                <input v-model="record.id" type="text" class="form-control" id="name"  >
            </div>
            <div class="form-group">
                <label for="date">Datum / vrijeme:</label>
                <input v-model="record.date" type="text" class="form-control" id="date"  >
            </div>
            <div class="form-group">
                <label for="name">Ime zadatka:</label>
                <input v-model="record.name" type="text" class="form-control" id="name" >
            </div>
            <div class="form-group">
                <label for="description">Opis zadatka:</label>
                <input v-model="record.description" type="text" class="form-control" id="description" >
            </div>
            <div class="text-right">
                <button class="btn btn-outline-primary" @click="update()">&#10004 Spremi</button>
                <button class="btn btn-outline-danger" v-b-modal.deleterecordmodal>&#10008 Briši</button>
            </div>

            <!-- Modal Delete -->
            <b-modal id="deleterecordmodal" ref="modal_delete" title="Briši" @ok="remove" >
                <form @submit.stop.prevent="deleteSubmit">
                    Are you sure you want to delete this records?
                </form>
            </b-modal>
        </div>
    </div>
</template>

<script>
    import router from '../router'

    export default {
        name: 'Details',
        data () {
            return {
                record: null
            }
        },
        created() {
            this.record = this.$route.params.record;
        },
        methods: {
            navigate() {
                router.go(-1);
            },
            update() {
                if (!this.record.name) {
                    alert('Please enter name')
                } else {
                    this.handleUpdate()
                }
            },
            handleUpdate() {
                this.loadRecords();
                var id = this.record.id;
                var record_ = this.record;
                var records = this.records;
                this.records.forEach(function(record, key){
                    if(record.id==id){
                        records[key] = record_;
                    }
                })
                this.saveToLocalStorage(records);
            },
            saveToLocalStorage(x){
                localStorage.setItem('records', JSON.stringify(x));
            },
            loadRecords() {
                if(localStorage.records){
                    this.records = JSON.parse(localStorage.records);
                }
            },
            remove() {
                this.deleteSubmit();
            },
            deleteSubmit() {
                this.loadRecords();
                var records = this.records;
                var _records = this.records;
                var id = this.record.id;
                records.forEach(function(record, key){
                    if(record.id==id){
                        _records.splice(key, 1);
                    }
                })
                this.records = _records;
                this.saveToLocalStorage(this.records);
                router.go(-1);
            }
        }
    }
</script>