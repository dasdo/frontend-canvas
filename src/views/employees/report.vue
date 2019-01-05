<template>
    <div class="browse-list">
        <h4 class="section-title p-l-10">Employees Reports</h4>

        <div class="card">
            <div class="card-block">
                <table class="table table-striped">
                    <thead>
                        <tr>
                            <th>Employees</th>
                            <th>1ra. Quinc.</th>
                            <th>2da. Quinc.</th>
                            <th>Total Gross Revenue</th>
                            <th>Extra Hours</th>
                            <th>Other Income</th>
                            <th>Total</th>
                            <th>R.S.F.S</th>
                            <th>R.P</th>
                            <th>Total Net Income</th>
                            <th>Desc. ISR</th>
                            <th>To pay on {{ q1_date }} </th>
                            <th>To pay on {{ q2_date }} </th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="employee in employees" :key="employee.id">
                            <td>
                                <p>{{ employee.identification_card }}</p>
                                <p>{{ employee.first_name }} {{ employee.last_name }}</p>
                            </td>
                            <td>
                                <p>{{ employee.q1_salary }}</p>
                            </td>
                            <td>
                                <p>{{ employee.q2_salary }}</p>
                            </td>
                            <td>
                                <p>{{ employee.total_gross_revenue }}</p>
                            </td>
                            <td>
                                <input
                                    v-model="employee.extra_hours"
                                    type="text"
                                    name="extra_hours"
                                    placeholder="0.00"
                                    class="form-control"
                                >
                            </td>
                            <td>
                                <input
                                    v-model="employee.other_income"
                                    type="text"
                                    name="other_income"
                                    placeholder="0.00"
                                    class="form-control"
                                >
                            </td>
                            <td>
                                <p>{{ employee.total_gross_revenue*1 + employee.other_income*1 + employee.extra_hours*1 }}</p>
                            </td>
                            <td>
                                <p>{{ employee.rsfs }}</p>
                            </td>
                            <td>
                                <p>{{ employee.rp }}</p>
                            </td>
                            <td>
                                <p>{{ (employee.total_gross_revenue*1 + employee.other_income*1 + employee.extra_hours*1) - (employee.rsfs*1 + employee.rp*1) }}</p>
                            </td>
                            <td>
                                <p>{{ employee.isr }}</p>
                            </td>
                            <td>
                                <p>{{ employee.q1_salary }}</p>
                            </td>
                            <td>
                                <p>{{ employee.q2_salary*1 - employee.rsfs*1 - employee.rp*1 - employee.isr*1 }}</p>
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
    </div>
</template>

<script>

export default {
    name: "EmployeesReport",
    data() {
        return {
            q1_date: "2019-01-15",
            q2_date: "2019-01-30",
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
        handleResponse(response) {
            let data = [];
            let k = 0;
            response.map((employee) => { 
                data[k] = employee;
                data[k].extra_hours = 0;
                data[k].other_income = 0;
                data[k].rsfs = 0;
                data[k].rp = 0;
                data[k].isr = 0;
                data[k].q1_salary = employee.payment_period==15 ? employee.salary : employee.salary/2;
                data[k].q2_salary = employee.payment_period==15 ? employee.salary : employee.salary/2;
                data[k].total_gross_revenue = employee.payment_period==30 ? employee.salary : employee.salary*2;
                k++;
            });
            return data;
        },
        pageChanged(page) {
            this.getEmployees(page);
        },
        getEmployees(page = 1) {
            axios({
                url: `/employees?sort=created_at|DESC&page=${page}`,
                method: "GET"
            }).then((response) => {
                this.employees = this.handleResponse(response.data);
            }).catch((error) => {
                this.$notify({
                    title: "Error",
                    text: error,
                    type: "error"
                });
            });
        }
    }
}
</script>
