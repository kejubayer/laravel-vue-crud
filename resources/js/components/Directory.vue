<template>
    <div>
        <h2></h2>
        <div class="form-group">
            <label for="name">Name</label>
            <input type="text" id="name" placeholder="Enter name" class="form-control"
                   v-model="item.name"
            >
        </div>
        <div class="form-group">
            <label for="phone">Phone</label>
            <input type="text" id="phone" placeholder="Enter phone number" class="form-control"
                   v-model="item.phone"
            >
        </div>
        <button class="btn btn-success mt-2" @click="save">{{ isEditing ? 'Update' : 'Save' }}</button>
        <div class="row">
            <div class="col-md-12 mt-3" v-if="lists && lists.data.length>0">
                <h2 class="text-center">Telephone Numbers</h2>
                <div class="table-responsive">
                    <table class="table text-center">
                        <thead>
                        <tr>
                            <th scope="col">#</th>
                            <th scope="col">Name</th>
                            <th scope="col">Phone Number</th>
                            <th scope="col">Actions</th>
                        </tr>
                        </thead>
                        <tbody>
                        <tr v-for="(item,index) in lists.data" :key="item.id">
                            <th scope="row">{{ index + 1 }}</th>
                            <td>{{ item.name }}</td>
                            <td>{{ item.phone }}</td>
                            <td>
                                <button class="btn btn-warning btn-sm mr-2" @click="editTel(item)">Edit</button>
                                <button class="btn btn-danger btn-sm mr-2" @click="deleteTel(item.id)">Delete</button>
                            </td>
                        </tr>
                        </tbody>
                    </table>
                    <pagination align="center" :data="lists" @pagination-change-page="fetchAll"></pagination>
                </div>
            </div>
        </div>

    </div>
</template>

<script>
import pagination from 'laravel-vue-pagination'

export default {
    name: "Directory",
    components:{
        pagination
    },
    data() {
        return {
            lists: [],
            item: {
                name: "",
                phone: "",
            },
            temp_id: null,
            isEditing: false,
        }
    },
    mounted() {
        this.fetchAll();
    },
    methods: {
        /*fetchAll() {
            axios.get('/api/tel')
                .then(res => this.lists = res.data)
        },*/
        fetchAll(page = 1) {
            try {
                axios.get(`/api/tel?page=${page}`)
                    .then(res => this.lists = res.data)
            } catch (e) {
                console.log(e)
            }
        },
        save() {
            let method = axios.post;
            let url = '/api/tel';
            if (this.isEditing) {
                method = axios.put;
                url = `/api/tel/${this.temp_id}`;
            }
            try {
                method(url, this.item)
                    .then(res => {
                        this.fetchAll();
                        this.item = {
                            name: "",
                            phone: "",
                        };
                        this.temp_id = null;
                        this.isEditing = false;
                    })
            } catch (e) {
                console.log(e)
            }
        },
        deleteTel(id) {
            try {
                axios.delete(`/api/tel/${id}`)
                    .then(res => this.fetchAll())
            } catch (e) {
                console.log(e)
            }
        },
        editTel(tel) {
            this.item = {
                name: tel.name,
                phone: tel.phone,
            }
            this.temp_id = tel.id;
            this.isEditing = true;
        }
    }
}
</script>

<style scoped>
.pagination {
    margin-bottom: 0;
}
</style>
