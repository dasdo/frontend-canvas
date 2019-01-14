<template>
    <div>
        <div class="container-fluid container-fixed-lg">
            <div class="row">
                <h4 class="section-title p-l-10 col-lg-10">Create Employees</h4>
            </div>
        </div>
        <div class="container-fluid container-fixed-lg">
            <div class="row">
                <div class="col-lg-12">
                    <div class="col-lg-6">
                        <!-- START card -->
                        <div class="card card-default">
                            <div class="card-body">
                                <h5>
                                    Pages default style
                                </h5>
                                <form class="" role="form" @submit.prevent="submitData()">
                                    <div class="form-group form-group-default required ">
                                        <label>Identification Card</label>
                                        <input
                                            v-model="identification_card"
                                            type="text"
                                            name="identification_card"
                                            placeholder="000-0000000-0"
                                            class="form-control"
                                            required
                                        >
                                    </div>
                                    <div class="row">
                                        <div class="col-md-6">
                                            <div class="form-group form-group-default required">
                                                <label>First name</label>
                                                <input
                                                    v-model="first_name"
                                                    type="text"
                                                    name="first_name"
                                                    placeholder="Daniel Arturo"
                                                    class="form-control"
                                                    required
                                                >
                                            </div>
                                        </div>
                                        <div class="col-md-6">
                                            <div class="form-group form-group-default">
                                                <label>Last name</label>
                                                <input
                                                    v-model="last_name"
                                                    type="text"
                                                    name="last_name"
                                                    placeholder="Sanchez De Oleo"
                                                    class="form-control"
                                                    required
                                                >
                                            </div>
                                        </div>
                                    </div>
                                    <div class="form-group form-group-default required">
                                        <label>Puesto de Trabajo</label>
                                        <select 
                                            v-model="jobs_id"
                                            name="jobs_id"
                                            class="cs-select cs-skin-slide"
                                            required>
                                            <option v-for="job in jobs" v-bind:value="job.id" :key="job.id">{{ job.name }}</option>
                                        </select>

                                    </div>
                                    <div class="row">
                                        <div class="col-md-6">
                                            <div class="form-group form-group-default required">
                                                <label>Salary</label>
                                                <input
                                                    v-model="hired_salary"
                                                    type="text"
                                                    name="hired_salary"
                                                    placeholder="1000.00"
                                                    class="form-control"
                                                    required
                                                >
                                            </div>
                                        </div>
                                        <div class="col-md-6">
                                            <div class="form-group form-group-default">
                                                <label>Payment Period</label>
                                                <select 
                                                    v-model="payment_period"
                                                    name="payment_period"
                                                    class="cs-select cs-skin-slide"
                                                    required
                                                    data-init-plugin="cs-select">
                                                    <option value="1">daily</option>
                                                    <option value="15">biweekly</option>
                                                    <option value="30">monthly</option>
                                                </select>
                                            </div>
                                        </div>
                                    </div>
                                    <div class="form-group form-group-default required ">
                                        <label>Admission Date</label>
                                        <input
                                            v-model="admission_date"
                                            type="date"
                                            name="admission_date"
                                            class="form-control"
                                            required
                                        >
                                    </div>
                                    <button class="btn btn-primary btn-cons m-t-10" type="submit">Save</button>
                                </form>
                            </div>
                        </div>
                        <!-- END card -->
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: "EmployeesForm",
    data() {
        return {
            identification_card: "",
            first_name: "",
            last_name: "",
            admission_date: "",
            jobs_id: "",
            hired_salary: "",
            payment_period: "30",
            jobs: []
        }
    },
    created() {
        this.getJobs();
    },
    methods: {
        getJobs() {
            axios({
                url: `/jobs?sort=name|ASC`,
                method: "GET"
            }).then((response) => {
                this.jobs = response.data;
            }).catch((error) => {
                this.$notify({
                    title: "Error",
                    text: error.response.data.errors.message,
                    type: "error"
                });
            });
        },
        submitData() {
            const data = this.$data;
            axios({
                url: "/employees",
                method: "POST",
                data
            }).then((response) => {
                let text = "New employee added " + response.first_name + " " + response.last_name;
                this.$notify({
                    title: "Confirmation",
                    text: text,
                    type: "success"
                });

                this.$router.push({ name: "employees" });
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