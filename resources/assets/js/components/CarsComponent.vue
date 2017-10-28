<style>
    .loader {
        border: 5px solid #f3f3f3;
        border-radius: 50%;
        border-top: 5px solid #3498db;
        width: 20px;
        height: 20px;
        -webkit-animation: spin 0.5s linear infinite;
        animation: spin 0.5s linear infinite;
    }

    @-webkit-keyframes spin {
        0% { -webkit-transform: rotate(0deg); }
        100% { -webkit-transform: rotate(360deg); }
    }

    @keyframes spin {
        0% { transform: rotate(0deg); }
        100% { transform: rotate(360deg); }
    }
</style>

<template>
    <div>
        <table class="table">
            <tbody>
            <tr v-for="car in cars">
                <td>
                    <div v-if="car.isEditing" class="loader"></div>
                    <div v-else>{{car.model}}</div>
                </td>
                <td class="text-right">
                    <a @click="handleEdit(car)" data-toggle="modal" data-target="#editModal" class="btn btn-info btn-xs">
                        <i class="fa fa-pencil"></i>
                    </a>
                    <a @click="deleteId = car.id" data-toggle="modal" data-target="#confirmDelete" class="btn btn-danger btn-xs">
                        <i class="fa fa-trash"></i>
                    </a>
                </td>
            </tr>
            </tbody>
        </table>
        <div class="panel-footer text-center">
            <a data-toggle="modal" data-target="#createModal" class="btn btn-primary">Criar Novo</a>
        </div>

        <!-- Modal Delete -->
        <div class="modal fade" id="confirmDelete" role="dialog">
            <div class="modal-dialog">

                <!-- Modal content-->
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal">&times;</button>
                        <h4 class="modal-title text-danger text-center">Atenção</h4>
                    </div>
                    <div class="modal-body">
                        <p>Após concluir esta ação, ela é irreversível!</p>
                        <p>Tem certeza que deseja prosseguir?</p>
                        <div class="text-right">
                            <button type="button" class="btn btn-danger" data-dismiss="modal" @click="deleteCar(deleteId)">Sim</button>
                            <button type="button" class="btn btn-default" data-dismiss="modal">Cancelar</button>
                        </div>
                    </div>
                </div>

            </div>
        </div>

        <!-- Modal Edit -->
        <div class="modal fade" id="editModal" role="form">
            <div class="modal-dialog">

                <!-- Modal content-->
                <div class="modal-content">
                    <div class="modal-header">
                        <h4 class="modal-title text-success text-center">Editar</h4>
                    </div>
                    <div class="modal-body">
                        <form id="editForm">
                            <div class="form-group">
                                <label for="edit_model">Modelo</label>
                                <input v-model="editForm.model" id="edit_model" name="model" class="form-control" autofocus>
                            </div>
                            <div class="form-group">
                                <label for="edit_year">Ano</label>
                                <input type="number" min="1900" max="2100" v-model="editForm.year" id="edit_year" name="year" class="form-control">
                            </div>
                            <div class="form-group">
                                <label for="edit_brand_id">Marca</label>
                                <select v-model="editForm.brand_id" id="edit_brand_id" name="brand_id" class="form-control">
                                    <option v-for="brand in brands" :value="brand.id">{{brand.name}}</option>
                                </select>
                            </div>

                            <div class="text-right">
                                <button type="button" class="btn btn-success" data-dismiss="modal" @click="editCar()">Salvar</button>
                                <button type="button" class="btn btn-default" data-dismiss="modal" @click="handleCancel(editForm.id)">Cancelar</button>
                            </div>
                        </form>
                    </div>
                </div>

            </div>
        </div>

        <!-- Modal Create -->
        <div class="modal fade" id="createModal" role="form">
            <div class="modal-dialog">

                <!-- Modal content-->
                <div class="modal-content">
                    <div class="modal-header">
                        <h4 class="modal-title text-success text-center">Novo</h4>
                    </div>
                    <div class="modal-body">
                        <form id="createForm">

                            <div class="form-group">
                                <label for="create_model">Modelo</label>
                                <input v-model="createForm.model" id="create_model" name="model" class="form-control" autofocus>
                            </div>
                            <div class="form-group">
                                <label for="create_year">Ano</label>
                                <input type="number" min="1900" max="2100" v-model="createForm.year" id="create_year" name="year" class="form-control">
                            </div>
                            <div class="form-group">
                                <label for="create_brand_id">Marca</label>
                                <select v-model="createForm.brand_id" id="create_brand_id" name="brand_id" class="form-control">
                                    <option v-for="brand in brands" :value="brand.id">{{brand.name}}</option>
                                </select>
                            </div>

                            <div class="text-right">
                                <button type="button" class="btn btn-success" data-dismiss="modal" @click="createCar()">Criar</button>
                                <button type="button" class="btn btn-default" data-dismiss="modal">Cancelar</button>
                            </div>
                        </form>
                    </div>
                </div>

            </div>
        </div>
    </div>

</template>

<script>
    export default {
        /*
         * The component's data.
         */
        data() {
            return {
                cars: [],
                brands: [],
                deleteId: 0,
                editForm: {
                    model:'',
                    year:'',
                    brand_id:0
                },
                createForm: {
                    model:'',
                    year:'',
                    brand_id:0
                }
            };
        },

        /**
         * Prepare the component (Vue 1.x).
         */
        ready() {
            this.prepareComponent();
        },

        /**
         * Prepare the component (Vue 2.x).
         */
        mounted() {
            this.prepareComponent();
        },

        methods: {
            /**
             * Prepare the component (Vue 2.x).
             */
            prepareComponent() {
                this.getCarros();
                this.getBrands();
            },

            /**
             * Get all of the authorized tokens for the user.
             */
            getCarros() {
                axios.get('/api/cars')
                    .then(response => {
                        this.cars = response.data;
                    });
            },
            getBrands() {
                axios.get('/api/brands')
                    .then(response => {
                        this.brands = response.data;
                    });
            },
            /**
             * Delete a Car
             */
            deleteCar(id) {
                axios.delete('/api/cars/' + id)
                    .then(response => {
                        if(response.status === 204){
                            this.popFromCar(id);
                        }
                    });
            },
            popFromCar(id){
                let car = this.getCarById(id), cars = this.cars;
                let i = cars.indexOf(car);
                cars.splice(i, 1);
            },
            editCar(){
                let data = this.editForm;
                axios.put('/api/cars/' + data.id, data)
                    .then(response => {
                        let editedCar = response.data;
                        if(response.status === 200){
                            let cars = this.cars;
                            let car = this.getCarById(data.id);
                            let tempCar = editedCar;
                            tempCar.isEditing = false;
                            cars.splice(cars.indexOf(car), 1, tempCar);
                        }else if(response.status === 422){
                            console.log(editedCar);
                        }
                    });
            },
            handleEdit(car){
                car.isEditing = true;
                this.editForm = Object.assign({}, car);
            },
            handleCancel(id){
                let car = this.getCarById(id);
                let cars = this.cars;
                car.isEditing = false;
                cars.splice(cars.indexOf(car), 1, car);
            },
            createCar(){
                let data = this.createForm;
                axios.post('/api/cars', data)
                    .then(response => {
                        if(response.status === 201){
                            this.cars.push(response.data);
                        }else if(response.status === 422 ){
                            console.log(response.data);
                        }
                        data.model = null;
                        data.year = null;
                        data.brand_id = null;
                    });
            },
            getCarById(id){
                let cars = this.cars;
                for(let i in cars){
                    let car = cars[i];
                    if(car.id === id){
                        return car;
                    }
                }
                return false;
            }
        }
    }
</script>
