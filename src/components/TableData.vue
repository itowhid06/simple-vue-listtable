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
                <th>Name</th>
                <th>Age</th>
                <th>Email</th>
            </tr>
            </thead>
            <tbody>
            <tr v-for="i in items">
                <td>
                    <label class="form-checkbox">
                        <input type="checkbox" :value="i.id" v-model="selected">
                        <i class="form-icon"></i>
                    </label>
                </td>
                <td>{{i.name}}</td>
                <td>{{i.age}}</td>
                <td>{{i.email}}</td>
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
                        age: 20,
                        email: "email@example.com"
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
            }
        },
    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    body {
        padding: 50px
    }
</style>
