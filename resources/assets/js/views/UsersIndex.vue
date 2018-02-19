<template>
    <div>
        <div class="loading" v-if="loading">
            Loading...
        </div>

        <div v-if="error">
            <p>{{ error }}</p>

            <p>
                <button @click.prevent="fetchData">
                    Try Again
                </button>
            </p>
        </div>

        <table v-if="users" class="table table-striped">
            <thead>
                <tr>
                <th scope="col">#</th>
                <th scope="col">Name</th>
                <th scope="col">Created at</th>
                <th scope="col">Updated at</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="({ name, created_at, updated_at}, index) in users" :key="index">
                <th scope="row">{{index + 1}}</th>
                <td>{{ name }}</td>
                <td>{{ created_at.date }}</td>
                <td>{{ updated_at.date }}</td>
                </tr>
            </tbody>
        </table>

        <nav>
            <ul class="pagination mt-2 justify-content-center">
                <li class="page-item"><a class="page-link" href="#">Previous</a></li>
                <li class="page-item"><a class="page-link" href="#">1</a></li>
                <li class="page-item"><a class="page-link" href="#">Next</a></li>
            </ul>
        </nav>

    </div>
</template>

<script>
import axios from 'axios';

export default {
    data() {
        return {
            loading: false,
            users: null,
            error: null,
        };
    },
    created() {
        this.fetchData();
    },
    methods: {
        fetchData() {
            this.error = this.users = null;
            this.loading = true;
            axios
                .get('/api/users')
                .then(response => {
                    this.loading = false
                    this.users = response.data.data
                    console.log(response.data)
                }).catch(error => {
                    this.loading = false
                    this.error = error.response.data.message
                });
        }
    }
}
</script>
