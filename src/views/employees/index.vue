<template>
    <div class="browse-list">
        <h4 class="section-title p-l-10">Employees</h4>
        <div class="card">
            <div class="card-block">
                <div class="browse-list-row">
                    <div class="dropdown bulk-actions">
                        <button
                            id="bulk-actions"
                            class="btn btn-info dropdown-toggle"
                            type="button"
                            data-toggle="dropdown"
                            aria-haspopup="true"
                            aria-expanded="false">
                            Actions
                        </button>
                        <div class="dropdown-menu" aria-labelledby="bulk-actions">
                            <router-link :to="{ name: 'employees-report'}">
                                <a class="dropdown-item" href="javascript:void(0);">
                                    Reports
                                </a>
                            </router-link>
                        </div>
                    </div>
                    <router-link :to="{ name: 'employees-create'}">
                        <button class="add-record-btn btn btn-primary">
                            <i class="fa fa-plus-circle"/> Add Employees
                        </button>
                    </router-link>
                </div>
            </div>
        </div>
        <div class="card">
            <div class="card-block">
                <div class="table-responsive">
                    <table class="table table-hover">
                        <thead>
                            <tr>
                                <th style="width:1%" class="text-center">
                                    <div class="checkbox text-center">
                                        <input id="checkbox1" type="checkbox" value="3">
                                        <label for="checkbox1" class="no-padding no-margin"/>
                                    </div>
                                </th>
                                <th>Identification Card</th>
                                <th>First Name</th>
                                <th>Last Name</th>
                                <th>Admission Date</th>
                                <th>Job</th>
                                <th>Salary</th>
                            </tr>
                        </thead>
                        <tbody>
                            <template v-if="employees.length">
                                <tr v-for="employee in employees" :key="employee.id">
                                    <td>
                                        <div class="checkbox text-center">
                                            <input id="checkbox1" type="checkbox" value="3">
                                            <label for="checkbox1" class="no-padding no-margin"/>
                                        </div>
                                    </td>
                                    <td>
                                        <p>{{ employee.identification_card }}</p>
                                    </td>
                                    <td>
                                        <p>{{ employee.first_name }}</p>
                                    </td>
                                    <td>
                                        <p>{{ employee.last_name }}</p>
                                    </td>
                                    <td>
                                        <p>{{ employee.admission_date }}</p>
                                    </td>
                                    <td>
                                        <p>{{ employee.job }}</p>
                                    </td>
                                    <td>
                                        <p>{{ employee.salary }}</p>
                                    </td>
                                </tr>
                            </template>
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>
</template>

<script>

export default {
    name: "EmployeesIndex",
    data() {
        return {
            pages: [],
            employees: [],
            order: "DESC",
            currentPageNumber: 0,
            perPage: 50,
            paginationOptions: {
                first: "«",
                initiateStartPageClick: false,
                last: "»",
                prev: "<",
                next: ">",
                visiblePages: 10
            },
            totalPages: 1
        }
    },
    computed: {
        actualPages() {
            return this.pages[this.currentPageNumber - 1] || this.pages[0];
        }
    },
    created() {
        this.getEmployees();
    },
    methods: {
        pageChanged(page) {
            this.getEmployees(page);
        },
        getEmployees(page = 1) {
            axios({
                url: `/employees?sort=created_at|DESC&page=${page}`,
                method: "GET"
            }).then((response) => {
                //this.currentPageNumber = +response.page;
                //this.totalPages = response.total_pages;

                console.log(response.data);
                this.employees = response.data;
            }).catch((error) => {
                this.$notify({
                    title: "Error",
                    text: error.response.data.errors.message,
                    type: "error"
                });
            });
        }
    }
}
</script>


<style lang="scss">
.browse-list {
    .browse-list-row {
        display: flex;
        align-items: center;
        padding: 8px;

        .bulk-actions {
            padding-left: 5px;
            padding-right: 15px;
        }

        .search-bar {
            margin-left: 25px;
            max-width: 320px;
        }

        .browse-list-filters {
            margin-left: auto;
            max-width: 450px;

            .multiselect__tags {
                min-width: 280px;
            }

            .add-custom-filter-btn {
                background-color: var(--base-color);
                color: white;
                padding: 5px;
                cursor: pointer;
            }
        }
    }

    table {
        table-layout: initial !important;

        thead, tbody {
            tr {
                th, td {
                    white-space: normal !important;
                    overflow: visible !important;
                    text-overflow: initial !important;
                    vertical-align: middle;
                }
            }
        }

        thead {
            background-color: white;

            tr {
                th {
                    padding-top: 0;
                    padding-bottom: 0;
                    font-weight: bold;
                    color: black;
                }
            }
        }

        tr {
            td {
            padding: 10px;

                .checkbox label:before {
                    top: 0;
                }

                p {
                    margin-bottom: 0;
                }
            }
        }
    }
}
</style>