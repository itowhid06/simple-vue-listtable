<template>
    <div>
        <h2> {{ msg }} </h2>
        <br/>
        <label> Search </label>
        <br/>
        <input type="text" v-model="searchItem"/>
        <br/>
        <table class="table table-striped table-hover">
            <thead>
            <tr>
                <th v-if="!searchItem.length">
                    <label class="form-checkbox">
                        <input type="checkbox" v-model="selectAll" @click="select">
                        <i class="form-icon"></i>
                    </label>
                </th>
                <th @click="tablesort('name')">Name</th>
                <th @click="tablesort('age')">Age</th>
                <th @click="tablesort('email')">Email</th>
                <td>Edit</td>
            </tr>
            </thead>
            <tbody>
            <template v-if="searchItem.length && foundItems">
                <tr v-for="fitem in foundItems">
                    <td>{{ fitem.name }}</td>
                    <td>{{ fitem.age }}</td>
                    <td>{{ fitem.email }}</td>
                </tr>
            </template>
            <template v-else>
                <tr v-for="item in pagedItems">
                    <td>
                        <label class="form-checkbox">
                            <input type="checkbox" :value="item.id" v-model="selected">
                            <i class="form-icon"></i>
                        </label>
                    </td>
                    <td><input type="text" v-model="item.name" :disabled="item.noedit"/></td>
                    <td><input type="text" v-model="item.age" :disabled="item.noedit"/></td>
                    <td><input type="text" v-model="item.email" :disabled="item.noedit"/></td>
                    <td> <a href="#" @click.prevent="makeEditable(item.id)">Edit</a> </td>
                </tr>
            </template>

            </tbody>
        </table>
        <pagination
            :items="items"
            @pageUpdate="updatePage"
            :currentPage="currentPage"
            :pageSize="pageSize">
        </pagination>
    </div>
</template>

<script>

    import Pagination from './Pagination.vue'

    export default {
        name: 'TableData',
        props: {
            msg: String
        },
        components: {
            Pagination
        },
        data: function () {
            return {
                items: [
                    {
                        id: "id1",
                        name: "John Doe",
                        age: 22,
                        email: "email1@example.com",
                        noedit: true
                    },
                    {
                        id: "id2",
                        name: "Jane Doe",
                        age: 21,
                        email: "email2@example.com",
                        noedit: true
                    },
                    {
                        id: "id3",
                        name: "Mr. Smith",
                        age: 28,
                        email: "email3@example.com",
                        noedit: true
                    },
                    {
                        id: "id4",
                        name: "Mrs. Smith",
                        age: 25,
                        email: "email4@example.com",
                        noedit: true
                    }
                ],
                selected: [],
                searchItem: '',
                order: true,
                selectAll: false,
                currentPage: 0,
                pageSize: 3,
                pagedItems: []
            };
        },
        created: function() {
            this.updatepagedItems();
        },
        computed: {
            foundItems: function() {
                let find = this.searchItem;
                return this.items.filter( function (el) {
                    return ( el.name == find ||
                        el.age == find ||
                        el.email == find );
                });
            }
        },
        methods: {
            select: function() {
                this.selected = [];
                if (!this.selectAll) {
                    for (let i in this.items) {
                        this.selected.push(this.items[i].id);
                    }
                }
            },
            tablesort: function( key, e ) {
                let sort_order = 'asc';
                if ( this.order === false  ) {
                    sort_order = 'desc';
                }
                this.items.sort( this.compareValues( key, sort_order ) );
                this.updatepagedItems();
                this.order = !this.order;
            },
            compareValues: function(key, order='asc') {
                return function(a, b) {
                    if(!a.hasOwnProperty(key) ||
                        !b.hasOwnProperty(key)) {
                        return 0;
                    }

                    const varA = (typeof a[key] === 'string') ?
                        a[key].toUpperCase() : a[key];
                    const varB = (typeof b[key] === 'string') ?
                        b[key].toUpperCase() : b[key];

                    let comparison = 0;
                    if (varA > varB) {
                        comparison = 1;
                    } else if (varA < varB) {
                        comparison = -1;
                    }
                    return ( (order === 'desc') ? (comparison * -1) : comparison );
                };
            },
            updatepagedItems: function(){
                this.pagedItems = this.items.slice(this.currentPage * this.pageSize, (this.currentPage * this.pageSize) + this.pageSize);

                if (this.pagedItems.length === 0 && this.currentPage > 0) {
                    this.updatePage(this.currentPage -1);
                }
            },
            updatePage: function(pageNumber){
                this.currentPage = pageNumber;
                this.updatepagedItems();
            },
            makeEditable: function(item_id){
                for (let item in this.items) {
                    if ( this.items[item].id === item_id ) {
                        this.items[item].noedit = !this.items[item].noedit;
                    }
                }
            }
        },
        watch: {
            searchItem: function( newval) {
                this.searchItem = newval;
            }
        }
    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    body {
        padding: 50px
    }
</style>
