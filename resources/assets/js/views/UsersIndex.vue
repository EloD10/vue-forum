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
        
        <ul v-if="users" class="list-group">
            <li class="list-group-item active"> Members's list</li>
            <a v-for="{ name, email, created_at, updated_at} in users" :key="name" class="list-group-item list-group-item-action">
                Name: <strong>{{ name }}</strong>,
                Created at: <strong>{{ created_at.date }}</strong>,
                Updated at:<strong>{{ updated_at.date }}</strong>,
            </a>
        </ul>

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
                }).catch(error => {
                    this.loading = false
                    this.error = error.response.data.message
                });
        }
    }
}
</script>
