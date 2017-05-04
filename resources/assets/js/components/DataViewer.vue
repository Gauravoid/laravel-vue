<template>
    <div class="panel panel-default">
        <div class="panel-heading">
            <span class="panel-title">{{title}}</span>
            <div>
                <router-link :to="create" class="btn btn-primary btn-sm">Create</router-link>
            </div>
        </div>
        <div class="panel-body">
            <table class="table table-striped">
                <thead>
                    <tr>
                        <th v-for="item in thead">
                            <div>
                                <span>{{item.title}}</span>
                            </div>
                        </th>
                    </tr>
                </thead>
                <tbody>
                    <slot v-for="item in model.data" :item="item"></slot>
                </tbody>
            </table>
        </div>
        <div class="panel-footer pagination-footer">
            <div class="pagination-item">
                <span>Per page: </span>
                <select v-model="params.per_page" @change="fetchData">
                    <option>10</option>
                    <option>25</option>
                    <option>50</option>
                </select>
            </div>
            <div class="pagination-item">
                <small>Showing {{model.from}} - {{model.to}} of {{model.total}}</small>
            </div>
            <div class="pagination-item">
                <small>Current page: </small>
                <input type="text" class="pagination-input" v-model="params.page"
                    @keyup.enter="fetchData">
                <small> of {{model.last_page}}</small>
            </div>
            <div class="pagination-item">
                <button @click="prev" class="btn btn-default btn-sm">Prev</button>
                <button @click="next" class="btn btn-default btn-sm">Next</button>
            </div>
        </div>
    </div>
</template>
<script>
    import Vue from 'vue'
    import axios from 'axios'

    export default {
        props: ['source', 'thead', 'create', 'title'],
        data() {
            return {
                model: {
                    data: []
                },
                params: {
                    per_page: 10,
                    page: 1
                }
            }
        },
        beforeMount() {
            this.fetchData()
        },
        methods: {
            next() {
                if(this.model.next_page_url) {
                    this.params.page++
                    this.fetchData()
                }
            },
            prev() {
                if(this.model.prev_page_url) {
                    this.params.page--
                    this.fetchData()
                }
            },
            fetchData() {
                var vm = this
                axios.get(this.buildURL())
                    .then(function(response) {
                        Vue.set(vm.$data, 'model', response.data.model)
                    })
                    .catch(function(error) {
                        console.log(error)
                    })
            },
            buildURL() {
                var p = this.params
                return `${this.source}?per_page=${p.per_page}&page=${p.page}`
            }
        }
    }
</script>