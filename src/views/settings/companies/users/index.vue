<template>
    <div class="row">
        <div class="col">
            <transition name="fade" mode="out-in">
                <component 
                    :is="currentComponent" 
                    :users="users" 
                    @usersCRUD="usersCRUD" 
                    @usersList="usersList"/>
            </transition>
        </div>
    </div>
</template>

<script>
import UsersCRUD from "./crud.vue";
import UsersList from "./list.vue";

export default {
    components: {
        UsersCRUD,
        UsersList
    },
    data() {
        return {
            currentComponent: "UsersList",
            users: [],
            selectedUser: null,
            isEditable: true
        }
    },
    mounted() {
        this.getUsers();
        this.currentUser = _.clone(this.$store.state.User.data);
    },
    methods: {
        isCurrentUser(userId) {
            return this.currentUser.id == userId;
        },

        getUsers() {
            axios("/users").then(({data}) => {
                this.users = data;
            })
        },

        getUser(userId) {
            return axios(`/users/${userId}`).then(({ data }) => {
                this.selectedUser = data;
            })
        },

        async editUser(userId, isEditable = true) {
            this.isEditable = isEditable
            await this.getUser(userId);
            this.$modal.show("user-modal");
        },

        deleteUser(id) {
            if (this.isCurrentUser) {
                return
            }

            axios({
                url: `/users/${id}`,
                method: "DELETE"
            }).then(() => {
                this.$notify({
                    group: null,
                    title: "Deleted",
                    text: "The user has been deleted",
                    type: "success"
                });
            }).catch((error) => {
                this.$notify({
                    group: null,
                    title: "Error",
                    text: error.response.data.errors.message,
                    type: "error"
                });
            }).finally(() => {

            })
        },
        usersCRUD() {
            this.selectedUser = {};
            this.currentComponent = "UsersCRUD";
        },
        usersList() {
            this.currentComponent = "UsersList";
        }
    }
};
</script>

<style lang="scss" scoped>
.fade-enter-active, .fade-leave-active {
  transition: opacity .2s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
</style>

