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
                <tr v-for="({ name, created_at, updated_at}, index) in sortedUsers" :key="index">
                <th scope="row">{{index + 1}}</th>
                <td>{{ name }}</td>
                <td>{{ created_at.date | dateFilter }} <span class="mb-1 text-muted">{{ created_at.date | datefromNow }}</span></td>
                <td>{{ updated_at.date | dateFilter }} <span class="mb-1 text-muted">{{ updated_at.date | datefromNow }}</span></td>
                </tr>
            </tbody>
        </table>

        <nav v-if="users">
            <ul class="pagination mt-2 justify-content-center">
                <button v-if="users" @click.prevent="loadBack" :class="{'page-link': buttonIsActivePrev}" :disabled="isDisabledPrev">Prev</button>
                <button v-if="users" @click.prevent="loadMore" :class="{'page-link': buttonIsActiveNext}" :disabled="isDisabledNext">Next</button>
            </ul>
        </nav>

    </div>
</template>

<script>
import axios from 'axios'
import Lodash from 'lodash'
import moment from 'moment'

export default {
    data() {
        return {
            loading: false,
            users: null,
            error: null,
            buttonIsActivePrev: false,
            buttonIsActiveNext: false
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
                    this.users = response.data
                }).catch(error => {
                    this.loading = false
                    this.error = error.response.data.message
                })
        },
        loadMore() {
            axios
                .get(this.users.links.next)
                .then(response => {
                    this.users = response.data
                })
            },
        loadBack() {
            axios
                .get(this.users.links.prev)
                .then(response => {
                    this.users = response.data
                })
            }
    },
    computed: {
        sortedUsers() {
            return _.orderBy(this.users.data, 'name')
        },
        isDisabledNext() {
            if (this.users.links.next) {
                this.buttonIsActiveNext = true
                return false
            } else {
                this.buttonIsActiveNext = false
                return true
            }
        },
        isDisabledPrev() {
            if (this.users.links.prev) {
                this.buttonIsActivePrev = true
                return false
            } else {
                this.buttonIsActivePrev = false
                return true
            }
        }
    },
    filters: {
        dateFilter(date) {
            return moment(date).format('MM/DD/YYYY')
        },
        datefromNow(date) {
            return moment(date).fromNow()
        }
        
    }
}
</script>
