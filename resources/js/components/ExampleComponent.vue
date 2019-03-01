<template>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-12 text-center">
                <h1>Todo List ({{countTasks}})</h1>
            </div>
            <div class="col-md-8">
                <div class="row">
                    <div class="col-10">
                        <input type="text" class="form-control" v-model="task">
                    </div>
                    <div class="col-2">
                        <button type=button class="btn btn-primary col-12 mr-4" @click="createTask" @keyup.enter="createTask">create</button>
                    </div>
                </div>
                <div class="card card-default">
                    <div class="card-header"  v-for="(item, index) in tasks">

                            <div class="row" v-if="item.status == 0">
                                <div class="col-md-8">
                                    <transition appear name="slide-fade">
                                        <p>{{item.name}}</p>
                                    </transition>
                                </div>
                                <div class="col-2 p-0">
                                    <button type="button" class="btn btn-success col-12" @click="changeStatus(item.id)">Done</button>
                                </div>
                                <div class="col-2 p-0">
                                    <button type="button" class="btn btn-danger col-12" @click="removeTask(item.id)">Remove</button>
                                </div>
                            </div>

                            <div class="row" v-else>
                                <div class="col-md-8">
                        <transition appear name="slide-fade">
                                    <del>{{item.name}}</del>
                        </transition>
                                </div>
                                <div class="col-2 p-0">
                                    <button type="button" class="btn btn-warning col-12" @click="changeStatus(item.id)">unDone</button>
                                </div>
                                <div class="col-2 p-0">
                                    <button type="button" class="btn btn-danger col-12" @click="removeTask(item.id)">Remove</button>
                                </div>
                            </div>
                    </div>
                    
                </div>
            </div>
        </div>
    </div>
    
</template>
<style>
.slide-fade-enter-active {
  transition: all .3s ease;
}
.slide-fade-leave-active {
  transition: all .5s cubic-bezier(1.0, 0.5, 0.8, 1.0);
}
.slide-fade-enter, .slide-fade-leave-to
/* .slide-fade-leave-active до версии 2.1.8 */ {
  transform: translateX(10px);
  opacity: 0;
}
</style>

<script>
    export default {
        data() {
            return {
                tasks: [],
                task: '',
                count: ''
            }
        },
        // props: {
        //     tasks: {default: []}
        // },
        
        mounted() {
            this.getTasks()
        },
        computed: {
            countTasks: function () {
                var count = 0;
                this.tasks.filter(function(task) {
                        if (task.status == 0)
                            count++;
                })
                return count;
            }
        },
        methods: {
            createTask: function () {
                var newTask = this.task
                this.task = ''
                var _this = this
                axios
                    .post('/task', {
                        name: newTask
                    })
                    .then(function (response) {
                        console.log(response)
                        _this.tasks.push(response.data)
                    })
                // this.getTasks()
            },
            getTasks: function () {
                var _this = this
                axios
                    .get('/task')
                    .then(function (response) {
                        _this.tasks = response.data
                        
                    })
            },
            changeStatus: function (id) {
                var _this = this

                axios
                    .put('/task/'+id, {
                        id:id
                    })
                    .then(function (response) {
                        _this.getTasks()
                    })
            },
            removeTask: function (id) {
                var _this = this
                axios
                    .delete('/task/'+id, {
                        id:id
                    })
                    .then(function (response) {
                        _this.getTasks()
                    })
            }
        }
    }
</script>

