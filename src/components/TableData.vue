<template>
    <div>
        <h2> {{ msg }} </h2>
        <br/>
        <table class="table table-striped table-hover">
            <thead>
            <tr>
                <th>
                    <label class="form-checkbox">
                        <input type="checkbox" v-model="selectAll" @click="select">
                        <i class="form-icon"></i>
                    </label>
                </th>
                <th @click="tablesort('name')">Name</th>
                <th @click="tablesort('age')">Age</th>
                <th @click="tablesort('email')">Email</th>
            </tr>
            </thead>
            <tbody>
            <tr v-for="item in items">
                <td>
                    <label class="form-checkbox">
                        <input type="checkbox" :value="item.id" v-model="selected">
                        <i class="form-icon"></i>
                    </label>
                </td>
                <td>{{ item.name }}</td>
                <td>{{ item.age }}</td>
                <td>{{ item.email }}</td>
            </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
    export default {
        name: 'TableData',
        props: {
            msg: String
        },
        data: function () {
            return {
                items: [
                    {
                        id: "id1",
                        name: "John Doe",
                        age: 22,
                        email: "email1@example.com"
                    },
                    {
                        id: "id2",
                        name: "Jane Doe",
                        age: 21,
                        email: "email2@example.com"
                    },
                    {
                        id: "id3",
                        name: "Mr. Smith",
                        age: 28,
                        email: "email3@example.com"
                    },
                    {
                        id: "id4",
                        name: "Mrs. Smith",
                        age: 25,
                        email: "email4@example.com"
                    }
                ],
                selected: [],
                order: true,
                selectAll: false
            };
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
        },
    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    body {
        padding: 50px
    }
</style>
