<template>
<div>
    <h5>
        Users
        <button class="btn btn-primary" @click="usersCRUD">New user</button>
    </h5>
    <div class="table-responsive">
        <table class="table table-hover table-condensed">
            <thead>
                <tr>
                    <th style="width:30%">Name</th>
                    <th style="width:25%">Rol</th>
                    <th style="width:20%">Last activity</th>
                    <th style="width:10%">Status</th>
                    <th style="width:15%">Actions</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="user in users" :key="`users-${user.id}`">
                    <td class="v-align-middle semi-bold"> {{ user.firstname }} {{ user.lastname }} </td>
                    <td class="v-align-middle semi-bold"> {{ user.role_id }}</td>
                    <td class="v-align-middle semi-bold">{{ user.lastvisit }}</td>
                    <td class="v-align-middle semi-bold">{{ user.status }}</td>
                    <td class="v-align-middle semi-bold">
                        <button class="btn btn-primary m-l-5" @click="editUser(user.id, false)"><i class="fa fa-eye" aria-hidden="true"/></button>
                        <button class="btn btn-complete m-l-5" @click="editUser(user.id)"><i class="fa fa-edit" aria-hidden="true"/></button>
                        <button
                            :disabled="isCurrentUser(user.id)"
                            class="btn btn-danger m-l-5"
                            @click="deleteUser(user.id)">
                            <i class="fa fa-trash" aria-hidden="true" />
                        </button>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</div>
</template>

<script>
export default {
    name: "UsersList",
    props: {
        users: {
            type: Array,
            default() {
                return [
                    {
                        id: '',
                        firstname: '',
                        lastname: '',
                        role_id: '',
                        lastvisit: '',
                        status: ''
                    }
                ];
            }
        }
    },
    methods: {
        usersCRUD() {
            this.$emit("usersCRUD");
        },
        isCurrentUser() {

        },
        editUser() {

        },
        deleteUser() {

        }
    }
};
</script>
