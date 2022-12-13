<template>
    <div>
        <div class="container mt-5">
            <div class="card">
                <div class="card-header">
                    <div class="d-flex justify-content-between">
                        <h3>Manage Employees</h3>
                        <div>
                            <button type="button" class="btn btn-danger me-3" v-on:click="deleteAlert"><i
                                    class="bi bi-trash"></i>Delete</button>
                            <button type="button" class="btn btn-success" data-bs-toggle="modal"
                                data-bs-target="#add"><i class="bi bi-plus"></i>Add New
                                Employee</button>
                        </div>
                    </div>

                </div>
                <div class="card-body">
                    <table class="table">
                        <thead>
                            <tr>
                                <th scope="col"></th>
                                <th scope="col">Name</th>
                                <th scope="col">Email</th>
                                <th scope="col">Address</th>
                                <th scope="col">Phone</th>
                                <th scope="col">Actions</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="(empItem, index) in employeeList" :key="index">
                                <td><input class="form-check-input" type="checkbox" :id="'check_' + index"
                                        @click="selectedEmp(empItem, index)"></td>
                                <td>{{ empItem.name }}</td>
                                <td>{{ empItem.email }}</td>
                                <td>{{ empItem.address }}</td>
                                <td>{{ empItem.phone }}</td>
                                <td><a href="#" role="button" class="me-3" v-on:click="showEdit(empItem)"><i
                                            class="bi bi-pencil"></i></a>
                                    <a href="#" role="button" @click="showAlert(index)"><i class="bi bi-trash"></i></a>
                                </td>
                            </tr>
                        </tbody>
                        <tfoot>

                        </tfoot>
                    </table>
                </div>
            </div>

            <!-- add-emp-modal -->

            <div class="modal fade" id="add" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
                <div class="modal-dialog">
                    <div class="modal-content">
                        <div class="modal-header">
                            <h5 class="modal-title" id="exampleModalLabel">Add Employee</h5>
                            <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                        </div>
                        <div class="modal-body">
                            <form>
                                <div class="mb-3">
                                    <label for="name" class="form-label">Name</label>
                                    <input type="text" class="form-control" v-model="name">
                                    <p class="error-msg" v-if="attemptSubmit && missingName">Name is required.</p>
                                </div>
                                <div class="mb-3">
                                    <label for="name" class="form-label">Email</label>
                                    <input type="text" class="form-control" v-model="email">
                                    <p class="error-msg" v-if="attemptSubmit && missingEmail">Email is required.</p>
                                    <p class="error-msg" v-if="attemptSubmit && !validEmail">Enter a valid Email.</p>
                                </div>
                                <div class="mb-3">
                                    <label for="name" class="form-label">Address</label>
                                    <input type="text" class="form-control" v-model="address">
                                    <p class="error-msg" v-if="attemptSubmit && missingAddress">Address is required</p>
                                </div>
                                <div class="mb-3">
                                    <label for="name" class="form-label">Phone</label>
                                    <input type="text" class="form-control" v-model="phone"
                                        @keypress="preventCharacter">
                                    <p class="error-msg" v-if="attemptSubmit && missingPhone">Phone number required</p>
                                </div>
                            </form>
                        </div>
                        <div class="modal-footer">
                            <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cancel</button>
                            <button type="button" class="btn btn-success" v-on:click="addEmployee"
                                v-if="isShowAdd">Add</button>
                            <button type="button" class="btn btn-success" v-on:click="update" v-else>Update</button>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Alert-modal -->
            <div class="modal" tabindex="-1" id="alert">
                <div class="modal-dialog">
                    <div class="modal-content">
                        <div class="modal-header">
                            <h5 class="modal-title">Delete Employee</h5>
                            <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                        </div>
                        <div class="modal-body">
                            <p>Are you sure to want to delete these records</p>
                        </div>
                        <div class="modal-footer">
                            <button type="button" class="btn" data-bs-dismiss="modal">Cancel</button>
                            <button type="button" class="btn btn-danger" v-on:click="deleteSelectedEmp"
                                v-if="isShowD">Delete All</button>
                            <button type="button" class="btn btn-danger" v-on:click="deleteEmployee"
                                v-else>Delete</button>
                        </div>
                    </div>
                </div>
            </div>

        </div>
    </div>
</template>
<script>
import { Modal } from 'bootstrap';
export default {
    data() {
        return {
            employeeList: [
                {
                    name: 'Anwar',
                    email: 'anwarv75@gmail.com',
                    address: 'vs house',
                    phone: '+917736292285'
                },
                {
                    name: 'sam',
                    email: 'sam@gmail.com',
                    address: 'sam house',
                    phone: '+91586515568'
                },
                {
                    name: 'Sreenivas',
                    email: 'sree@gmail.com',
                    address: 'abc house',
                    phone: '+91956425684'
                },
                {
                    name: 'arun',
                    email: 'arun@gmail.com',
                    address: 'vs house',
                    phone: '+919865642584'
                }
            ],
            name: '',
            email: '',
            address: '',
            phone: '',
            selectedEmpList: [],
            alert: null,
            modal: null,
            selectedIndex: '',
            selectedForEdit: '',
            isShowAdd: true,
            isShowD: false,
            attemptSubmit: false
        }
    },
    mounted() {
        this.alert = new Modal(document.getElementById('alert'));
        this.modal = new Modal(document.getElementById('add'));
        let myModalEl = document.getElementById('add')
        let alertModal = document.getElementById('alert')
        var vm = this;
        myModalEl.addEventListener('hidden.bs.modal', function (event) {
            vm.isShowAdd = true;
            vm.attemptSubmit = false;
            vm.clearField();
        })
        alertModal.addEventListener('hidden.bs.modal', function (event) {
            vm.isShowD = false;
        })
    },
    methods: {
        addEmployee() {
            if (!this.name || !this.email || !this.address || !this.phone || this.validEmail == false) {
                this.attemptSubmit = true;
                return false;
            }
            this.employeeList.push(
                {
                    name: this.name,
                    email: this.email,
                    address: this.address,
                    phone: this.phone
                }
            )
            this.attemptSubmit = false;
            this.modal.hide();
            this.clearField();

        },
        deleteEmployee: function () {
            this.employeeList.splice(this.selectedIndex, 1);
            this.alert.hide()
        },
        update() {
            if (!this.name || !this.email || !this.address || !this.phone || this.validEmail == false) {
                this.attemptSubmit = true;
                return false;
            }
            if (this.selectedForEdit) {
                let obj = {
                    name: this.name,
                    email: this.email,
                    address: this.address,
                    phone: this.phone
                }
                const i = this.employeeList.indexOf(this.selectedForEdit)
                this.employeeList[i] = obj;
                this.attemptSubmit = false;
                this.modal.hide();
            }
        },
        showAlert(index) {
            this.alert.show()
            this.selectedIndex = index;
        },
        showEdit(item) {
            this.isShowAdd = false;
            this.modal.show();
            this.name = item.name;
            this.email = item.email;
            this.address = item.address;
            this.phone = item.phone;
            this.selectedForEdit = item;
        },
        deleteAlert() {
            if (this.selectedEmpList.length > 0) {
                this.isShowD = true;
                this.alert.show();
            }

        },
        deleteSelectedEmp() {
            for (var i = this.employeeList.length - 1; i >= 0; i--) {
                for (var j = 0; j < this.selectedEmpList.length; j++) {
                    if (this.employeeList[i] && (this.employeeList[i] === this.selectedEmpList[j])) {
                        this.employeeList.splice(i, 1);
                    }
                }
            }
            this.selectedEmpList = [];
            this.alert.hide();
            var checkedList = document.getElementsByTagName("input");
            for (var i = 0; i < checkedList.length; ++i) { checkedList[i].checked = false; }
        },
        selectedEmp(emp, index) {
            if (this.selectedEmpList[index] == emp) {
                this.selectedEmpList.splice(index, 1);
            } else {
                this.$nextTick(() => {
                    this.selectedEmpList.push(emp)
                })
            }

        },
        clearField() {
            this.name = "";
            this.email = "";
            this.address = "";
            this.phone = "";
        },
        preventCharacter(event) {
            if (!/^[0-9]+$/.test(event.key) && event.key !== '+') return event.preventDefault();
        }
    },
    computed: {
        missingName() { return this.name == ''; },
        missingEmail() { return this.email == ''; },
        missingAddress() { return this.address == ''; },
        missingPhone() { return this.phone == ''; },
        validEmail() {
            var re = /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
            return re.test(this.email);
        }
    }
}
</script>
<style scoped>
.bi-pencil {
    color: #ffc107;
}

.bi-trash,
.error-msg {
    color: red;
}

.card-header .bi-trash {
    color: #ffff;
}
</style>