<template>
    <div class="bg-white pb-2 rounded" style="box-shadow: 0 0 2em rgb( 219, 219, 219);">
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

        <table v-if="users" class="table table-striped border-bottom">
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
            <li :class="{'page-item': paginate.buttonIsActivePrev, 'page-item disabled': paginate.buttonIsDisabledPrev}">
                <button v-if="users" @click.prevent="goToBack" class="page-link" :disabled="isDisabledPrev">Prev</button>
            </li>
            <li :class="{'page-item': paginate.buttonIsActiveNext, 'page-item disabled': paginate.buttonIsDisabledNext}">
                <button v-if="users" @click.prevent="goToNext" class="page-link" :disabled="isDisabledNext">Next</button>
            </li>
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
            paginate: {
                buttonIsActivePrev: false, 
                buttonIsDisabledPrev: false,
                buttonIsActiveNext: false,
                buttonIsDisabledNext: false
            }    
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
        goToNext() {
            axios
                .get(this.users.links.next)
                .then(response => {
                    this.users = response.data
                })
            },
        goToBack() {
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
                this.paginate.buttonIsDisabledNext = false
                this.paginate.buttonIsActiveNext = true
                return false
            } else {
                this.paginate.buttonIsDisabledNext = true
                return true
            }
        },
        isDisabledPrev() {
            if (this.users.links.prev) {
                this.paginate.buttonIsDisabledPrev = false
                this.paginate.buttonIsActivePrev = true
                return false
            } else {
                this.paginate.buttonIsDisabledPrev = true
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
