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
                <template v-for="key in tableHeaders">
                    <template v-if="key !== 'id' && key !=='noedit'">
                        <th @click="tablesort(key)">{{ key[0].toUpperCase() + key.substring(1) }}</th>
                    </template>
                </template>
                <th v-if="!searchItem.length">Edit</th>
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
                    <td><input type="text" v-model="item.created" :disabled="item.noedit"/></td>
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
                        created: "01-01-2015",
                        noedit: true
                    },
                    {
                        id: "id2",
                        name: "Jane Doe",
                        age: 21,
                        created: "01-05-2018",
                        noedit: true
                    },
                    {
                        id: "id3",
                        name: "Mr. Smith",
                        age: 28,
                        created: "10-02-2016",
                        noedit: true
                    },
                    {
                        id: "id4",
                        name: "Mrs. Smith",
                        age: 25,
                        created: "01-02-2017",
                        noedit: true
                    },
                    {
                        id: "id5",
                        name: "Hello World",
                        age: 20,
                        created: "01-01-2018",
                        noedit: true
                    }
                ],
                selected: [],
                searchItem: '',
                order: true,
                selectAll: false,
                currentPage: 0,
                pageSize: 3,
                formattedItems: [],
                pagedItems: []
            };
        },
        created: function() {
            this.formatItems();
            this.updatepagedItems();
        },
        computed: {
            foundItems: function() {
                this.$router.push({ path: '/', query: { search: this.searchItem }});
                let find = this.searchItem,
                    searchCallback = this.fuzzyMatch;
                return this.items.filter( function (el) {
                    return ( searchCallback(find, el.name) ||
                        searchCallback(find, String(el.age)) )
                });
            },
            tableHeaders: function(){
                return Object.keys( this.items[0] );
            }
        },
        methods: {
            formatItems: function() {
                for (let i in this.items) {
                    for (let key in this.items[i]) {
                        if ( key === 'created'  ) {
                            this.items[i][key] = this.$moment(this.items[i][key], 'YYYY MM DD');
                        }
                    }
                }
            },
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
                this.$router.push({ path: '/', query: { order: key, order_by: sort_order }});
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
            },
            fuzzyMatch: function(needle, haystack) {
                if(needle === "" || haystack === "") return true;

                needle = needle.toUpperCase().replace(/ /g, "");
                haystack = haystack.toUpperCase();

                // All characters in needle must be present in haystack
                let j = 0; // haystack position
                for(let i = 0; i < needle.length; i++) {
                    // Go down the haystack until we find the current needle character
                    while(needle[i] !== haystack[j]) {
                        j++;

                        // If we reached the end of the haystack, then this is not a match
                        if(j === haystack.length) {
                            return false;
                        }
                    }
                }

                return true;
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
