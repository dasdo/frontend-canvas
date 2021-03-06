<template>
    <div class="row">
        <div class="col">
            <h5>Selecciona los permisos <button v-if="!isNewRole" class="btn btn-primary" @click="cloneRole">Clone role</button></h5>
            <!-- Role Form-->
            <form class="row" role="form">
                <div class="col-md-6">
                    <div class="form-group form-group-default required">
                        <label>Name role</label>
                        <input
                            v-validate="'required'"
                            v-model="roleData.name"
                            name="name"
                            type="text"
                            class="form-control"
                            required>
                    </div>
                </div>
                <div class="col-md-6">
                    <div class="form-group form-group-default">
                        <label>Description</label>
                        <input v-model="roleData.description" type="text" class="form-control">
                    </div>
                </div>
            </form>
            <!-- Role Select -->
            <div class="row">
                <div class="col">
                    <div
                        id="accordion"
                        class="card-group horizontal"
                        role="tablist"
                        aria-multiselectable="true">

                        <div v-for="(group, groupName) in accessGroup" :key="groupName" class="card card-default m-b-0">
                            <div id="headingOne" class="card-header " role="tab">
                                <h4 class="card-title access-group__title">
                                    <div class="checkbox check-success">
                                        <input
                                            id="checkbox1"
                                            v-model="group.isGroupSelected"
                                            type="checkbox"
                                            checked="checked"
                                            @click="checkGroup($event, groupName)">
                                        <label for="checkbox1"/>
                                    </div>

                                    <a
                                        :href="`#collapse-${groupName}`"
                                        data-toggle="collapse"
                                        data-parent="#accordion"
                                        aria-expanded="true"
                                        aria-controls="collapseOne">
                                        {{ groupName }}
                                    </a>
                                </h4>
                            </div>

                            <div
                                :id="`collapse-${groupName}`"
                                class="collapse show"
                                role="tabcard"
                                aria-labelledby="headingOne">
                                <div class="card-body">
                                    <div class="row">

                                        <div v-for="(access, accessName) in group.permissions" :key="`${groupName}-${accessName}`" class="col-md-6 row">
                                            <div class="col">
                                                <span>{{ accessName | capitalize }}</span>
                                            </div>
                                            <div class="col-xs-1">
                                                <div class="checkbox check-success">
                                                    <input
                                                        :id="`checkbox1-${groupName}-${accessName}`"
                                                        v-model="access.allowed"
                                                        type="checkbox"
                                                        @change="checkSelected(groupName)">
                                                    <label :for="`checkbox1-${groupName}-${accessName}`"/>
                                                </div>
                                            </div>
                                        </div>

                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <div class="row">
                <div class="col-12 col-xl d-flex justify-content-end mt-2">
                    <button class="btn btn-danger m-r-10" @click="rolesList">Cancel</button>
                    <button class="btn btn-primary" @click="save">Save</button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    props:{
        accessList: {
            type: Array,
            default() {
                return [];
            }
        },
        role: {
            type: Object,
            default() {
                return {}
            }
        }
    },
    data() {
        return {
            accessListData: null,
            accessGroup: {},
            roleData: {
                name: "",
                description: ""
            }
        }
    },
    computed: {
        isNewRole() {
            return !this.role.id
        }
    },
    watch: {
        accessList() {
            this.setRole();
        }
    },
    created() {
        this.setRole();
    },
    methods: {
        isGroupChecked(resourcesName) {
            const group = this.accessGroup[resourcesName].permissions;
            const allowedAccesses = Object.values(group);
            return allowedAccesses.every(access => access.allowed);
        },

        checkGroup(event, resourcesName) {
            const group = this.accessGroup[resourcesName];
            for (const access in group.permissions) {
                if (group.permissions.hasOwnProperty(access)) {
                    group.permissions[access].allowed = event.target.checked;
                }
            }
        },

        checkSelected(resourcesName) {
            this.accessGroup[resourcesName]["isGroupSelected"] = this.isGroupChecked(resourcesName);
        },
        save() {
            let url;
            let method;

            if (this.isNewRole) {
                url = "/roles-acceslist/";
                method = "POST";
            } else {
                url = `/roles-acceslist/${this.role.id}`;
                method = "PUT";
            }

            const formData = {
                roles: this.roleData,
                access: this.getFalsePermissions()
            }

            this.sendRequest(url, method, formData);
        },

        setRole() {
            this.accessListData = _.clone(this.accessList);
            this.roleData = _.clone(this.role);
            this.groupPermissions()
        },

        rolesList() {
            this.$emit("changeView", "rolesList");
        },
        groupPermissions() {
            this.accessListData.forEach(access => {
                if (access.access_name != "*") {
                    access.allowed = Boolean(Number(access.allowed));

                    if (!this.accessGroup[access.resources_name]) {
                        this.accessGroup[access.resources_name] = {permissions: {[access.access_name]: access}};
                    } else {
                        this.accessGroup[access.resources_name]["permissions"][access.access_name] = access;
                    }
                }
            });

            for (const accessName in this.accessGroup) {
                if (this.accessGroup.hasOwnProperty(accessName)) {
                    this.checkSelected(accessName);
                }
            }

        },
        getFalsePermissions() {
            return this.accessListData.map(access => {
                if (!access.allowed) {
                    const accessLocal = _.clone(access);
                    accessLocal.allowed = Number(accessLocal.allowed);
                    return accessLocal;
                }
            }).filter(access => access);
        },

        cloneRole() {
            this.$emit("cloneRole", this.role);
        },
        sendRequest(url, method, formData) {
            if (!this.isLoading) {
                this.isLoading = true;

                axios({
                    url,
                    method,
                    data: formData
                }).then(() => {
                    let message = method == "POST" ? "created" : "updated";

                    this.$notify({
                        group: null,
                        title: "Confirmation",
                        text: `The role has been ${message}!`,
                        type: "success"
                    });

                    this.rolesList();
                }).catch((error) => {
                    this.$notify({
                        group: null,
                        title: "Error",
                        text: error.response.data.errors.message,
                        type: "error"
                    });
                }).finally(() => {
                    this.isLoading = false;
                });
            }
        }
    }
}
</script>

<style lang="scss" scoped>
#accordion
    .checkbox,
        .radio{
            margin-top: 0%;
}

.access-group__title {
    display: flex !important;

    label {
        padding-left: 0
    }

    a{
        padding-top: 0
    }
}
</style>
