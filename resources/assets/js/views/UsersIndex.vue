<template>
    <div class="bg-white pb-2 rounded" style="box-shadow: 0 0 2em rgb( 219, 219, 219);">

        <div v-if="error">
            <p>{{ error }}</p>

            <p>
                <button @click.prevent="fetchData">
                    Try Again
                </button>
            </p>
        </div>

        <table class="table table-striped border-bottom">
            <thead>
                <tr>
                <th scope="col">#</th>
                <th scope="col">Name</th>
                <th scope="col">Created at</th>
                <th scope="col">Updated at</th>
                </tr>
            </thead>
            <tbody v-if="users">
                <tr v-for="({ name, created_at, updated_at}, index) in sortedUsers" :key="index">
                <th scope="row">{{index + 1}}</th>
                <td>{{ name }}</td>
                <td>{{ created_at.date | dateFormat }} <span class="mb-1 text-muted">{{ created_at.date | datefromNow }}</span></td>
                <td>{{ updated_at.date | dateFormat }} <span class="mb-1 text-muted">{{ updated_at.date | datefromNow }}</span></td>
                </tr>
            </tbody>
        </table>

        <nav v-if="users">
            <ul class="pagination mt-2 justify-content-center">
            <li :class="{'page-item': style.buttonIsActiveFirst, 'page-item disabled': style.buttonIsDisableFirst}">
                <button :disabled="! firstPage" @click.prevent="goToFirst" class="page-link">First</button>
            </li>
            <li :class="{'page-item': style.buttonIsActivePrev, 'page-item disabled': style.buttonIsDisablePrev}">
                <div :disabled="! prevPage" @click.prevent="goToPrev" class="page-link">Previous</div>
            </li>
            <li :class="{'page-item': style.buttonIsActiveNext, 'page-item disabled': style.buttonIsDisableNext}">
                <button :disabled="! nextPage" @click.prevent="goToNext" class="page-link">Next</button>
            </li>
            <li :class="{'page-item': style.buttonIsActiveLast, 'page-item disabled': style.buttonIsDisableLast}">
                <button :disabled="! lastPage" @click.prevent="goToLast" class="page-link">Last</button>
            </li>
            </ul>
            <div class="mb-1 text-muted d-flex justify-content-center">{{ paginatonCount }}</div>
        </nav>
    </div>
</template>

<script>
import axios from 'axios'
import Lodash from 'lodash'
import moment from 'moment'

const getUsers = (page, callback) => {
    const params = { page };

    axios
        .get('/api/users', { params })
        .then(response => {
            callback(null, response.data);
        }).catch(error => {
            callback(error, error.response.data);
        });
};

export default {
    data() {
        return {
            users: null,
            meta: null,
            links: {
                first: null,
                last: null,
                next: null,
                prev: null,
            },
            style: {
                buttonIsActivePrev: false, 
                buttonIsDisablePrev: false,
                buttonIsActiveNext: false,
                buttonIsDisableNext: false,
                buttonIsActiveFirst: false,
                buttonIsDisableFirst: false,
                buttonIsActiveLast: false,
                buttonIsDisableLast: false
            },
            error: null,
        }
    },
    computed: {
        sortedUsers() {
            return _.orderBy(this.users, 'name')
        },
        prevPage() {
            if (! this.meta || this.meta.current_page === 1) {
                this.style.buttonIsDisablePrev = true
                this.style.buttonIsActivePrev = false
            } else {
                this.style.buttonIsDisablePrev = false
                this.style.buttonIsActivePrev = true
            }
            return this.meta.current_page - 1
        },
        nextPage() {
            if (! this.meta || this.meta.current_page === this.meta.last_page) {
                this.style.buttonIsDisableNext = true
                this.style.buttonIsActiveNext = false
            } else {
                this.style.buttonIsDisableNext = false
                this.style.buttonIsActiveNext = true
            }
            return this.meta.current_page + 1
        },
        firstPage() {
            if (! this.meta || this.meta.current_page === 1) {
                this.style.buttonIsDisableFirst = true
                this.style.buttonIsActiveFirst = false
            } else {
                this.style.buttonIsDisableFirst = false
                this.style.buttonIsActiveFirst = true
            }
            return this.meta.current_page + (1 - this.meta.current_page)
        },
        lastPage() {
            if (! this.meta || this.meta.current_page === this.meta.last_page) {
                this.style.buttonIsDisableLast = true
                this.style.buttonIsActiveLast = false
            } else {
                this.style.buttonIsDisableLast = false
                this.style.buttonIsActiveLast = true
            }
            return this.meta.current_page + (this.meta.last_page - this.meta.current_page)
        },
        paginatonCount() {
            if (! this.meta) {
                return;
            }

            const { current_page, last_page } = this.meta;

            return `${current_page} of ${last_page}`;
        },
    },
    beforeRouteEnter (to, from, next) {
        getUsers(to.query.page, (err, data) => {
            next(vm => vm.setData(err, data));
        });
    },

    beforeRouteUpdate (to, from, next) {
        this.users = this.links = this.meta = null
        getUsers(to.query.page, (err, data) => {
            this.setData(err, data);
            next();
        });
    },
    methods: {
       goToPrev() {
            this.$router.push({
                name: 'users.index',
                query: {
                    page: this.prevPage,
                }
            });
       },
       goToNext() {
            this.$router.push({
                name: 'users.index',
                query: {
                    page: this.nextPage,
                }
            });
       },
       goToFirst() {
            this.$router.push({
                name: 'users.index',
                query: {
                    page: this.firstPage,
                }
            });
       },
       goToLast() {
            this.$router.push({
                name: 'users.index',
                query: {
                    page: this.lastPage,
                }
            });
       },
        setData(err, { data: users, links, meta }) {
            if (err) {
                this.error = err.toString();
            } else {
                this.users = users;
                this.links = links;
                this.meta = meta;
            }
        }
    },
    filters: {
        dateFormat(date) {
            return moment(date).format('MM/DD/YYYY')
        },
        datefromNow(date) {
            return moment(date).fromNow()
        }  
    }
    
}
</script>
