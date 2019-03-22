<template>
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
                <button class="btn btn-outline-primary" @click="update()">Spremi</button>
                <button class="btn btn-outline-danger" @click="remove()">&#10008 Briši</button>
            </div>
            <pre>{{record}}</pre>
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
               debugger;
                if (!this.record.name) {
                    alert('Please enter name')
                } else {
                    this.handleUpdate()
                }
            },
            handleUpdate() {
                this.loadRecords();
                alert('TODO')
               // this.records[this.curridx] = this.record;
               // this.saveToLocalStorage(this.records);
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
                alert('TODO')
            }
        }
    }
</script>