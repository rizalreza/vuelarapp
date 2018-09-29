<template>
    <div class="container">
        <div class="row">
          <div class="col-md-12">
            <div class="card">
              <div class="card-header">
                <h3 class="card-title">User List</h3>

                <div class="card-tools">
                 <!--  <div class="input-group input-group-sm" style="width: 150px;">
                    <input type="text" name="table_search" class="form-control float-right" placeholder="Search">

                    <div class="input-group-append">
                      <button type="submit" class="btn btn-default"><i class="fa fa-search"></i></button>
                    </div>
                  </div> -->
                  <button class="btn btn-success" @click="createUserModal()">
                      Add New <i class="fas fa-user-plus fa-fw"></i>
                  </button>
                </div>
              </div>
              <!-- /.card-header -->
              <div class="card-body table-responsive p-0">
                <table class="table table-hover">
                  <tbody><tr>
                    <th>ID</th>
                    <th>Name</th>
                    <th>Email</th>
                    <th>Type</th>
                    <th>Registered At</th>
                    <th>Action</th>
                  </tr>
                  <tr v-for="user in users" :key="user.id">
                    <td>{{user.id}}</td>
                    <td>{{user.name}}</td>
                    <td>{{user.email}}</td>
                    <td>{{user.type | uptext}}</td>
                    <td>{{user.created_at | configDate}}</td>
                    <td>
                        <a href="#"> <i class="fa fa-eye green"></i></a> |
                        <a href="#" @click="updateUserModal(user)"> <i class="fa fa-edit purple"></i></a> |
                        <a href="#" @click="deleteUser(user.id)"> <i class="fa fa-trash red"></i></a>
                    </td>
                  </tr>
                </tbody></table>
              </div>
              <!-- /.card-body -->
            </div>
            <!-- /.card -->
          </div>
        </div>

        <!-- Modal -->
        <div class="modal fade" id="userModal" tabindex="-1" role="dialog" aria-labelledby="userModalLabel" aria-hidden="true">
          <div class="modal-dialog modal-dialog-centered" role="document">
            <div class="modal-content">
              <div class="modal-header">
                <h5 class="modal-title" v-show="!updatemode" id="userModalLabel">Create New User</h5>
                <h5 class="modal-title" v-show="updatemode" id="userModalLabel">Update User</h5>
                <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                  <span aria-hidden="true">&times;</span>
                </button>
              </div>
            <form @submit.prevent="updatemode ? updateUser() : createUser()">
                <div class="modal-body">                
                    <div class="form-group">
                        <input v-model="form.name" type="text" name="name" placeholder="Name" class="form-control" :class="{ 'is-invalid': form.errors.has('name') }">
                        <has-error :form="form" field="name"></has-error>
                    </div>

                     <div class="form-group">
                        <input v-model="form.email" type="email" name="email" placeholder="Email Address" class="form-control" :class="{ 'is-invalid': form.errors.has('email') }">
                        <has-error :form="form" field="email"></has-error>
                    </div>

                     <div class="form-group">
                        <textarea v-model="form.bio" name="bio" id="bio"
                        placeholder="Short bio for user (Optional)"
                        class="form-control" :class="{ 'is-invalid': form.errors.has('bio') }"></textarea>
                        <has-error :form="form" field="bio"></has-error>
                    </div>

                    <div class="form-group">
                        <select v-model="form.type" name="type" class="form-control" :class="{ 'is-invalid': form.errors.has('type') }">
                            <option value="">Select Role</option>
                            <option value="admin">Admin</option>
                            <option value="user">User</option>
                            <option value="author">Author</option>
                        </select>
                        <has-error :form="form" field="name"></has-error>
                    </div>

                    <div class="form-group">
                        <input v-model="form.password" type="password" name="password" placeholder="Password" class="form-control" :class="{ 'is-invalid': form.errors.has('password') }">
                        <has-error :form="form" field="password"></has-error>
                    </div>
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                    <button type="submit" v-show="!updatemode" class="btn btn-primary">Create</button>
                    <button type="submit" v-show="updatemode" class="btn btn-success">Update</button>
                </div>
            </form>
            </div>
          </div>
        </div>
    </div>

</template>

<script>
    export default {
        data() {
            return {
                updatemode : false, 
                users : {},
                form: new Form({
                  id : '',
                  name : '',
                  email : '',
                  password : '',
                  type : '',
                  bio : '',
                  photo : ''
                })
            }
        },
        methods: {
            getUsers() {
                axios.get('api/user').then(({data}) => (this.users = data.data));
            },

            createUserModal(){
                this.updatemode = false;
                this.form.reset();
                $('#userModal').modal('show');
            },

            updateUserModal(user){
                this.updatemode = true;
                this.form.reset();
                $('#userModal').modal('show');
                this.form.fill(user);
            },

            createUser() {
                this.$Progress.start();

                this.form.post('api/user')
                .then(() => {
                    Fire.$emit('AfterCreate');
                    $('#userModal').modal('hide')

                    toast({
                        type: 'success',
                        title: 'User Create Successfully'
                    })

                    this.$Progress.finish();
                })

                .catch(() => {



                })
            },

            updateUser(id) {
                this.$Progress.start();
                this.form.put('api/user/'+this.form.id)
                .then(() => {
                    Fire.$emit('AfterCreate');
                    // toast({
                    //     type: 'success',
                    //     title: 'User Update Successfully'
                    // })

                    swal(
                        'Updated!',
                        'User has been updated.',
                        'success'
                        );
                    $('#userModal').modal('hide')
                    this.$Progress.finish();
                    
                })
                .catch(() => {
                    this.$Progress.fail();
                })
            },


            deleteUser(id){
                swal({
                    title: 'Are you sure?',
                    text: "You won't be able to revert this!",
                    type: 'warning',
                    showCancelButton: true,
                    confirmButtonColor: '#3085d6',
                    cancelButtonColor: '#d33',
                    confirmButtonText: 'Yes, delete it!'
                    }).then((result) => {
                        // Send request to the server
                         if (result.value) {
                                this.form.delete('api/user/'+id).then(()=>{
                                        swal(
                                        'Deleted!',
                                        'Your file has been deleted.',
                                        'success'
                                        )
                                    Fire.$emit('AfterCreate');
                                }).catch(()=> {
                                    swal("Failed!", "There was something wronge.", "warning");
                                });
                         }
                    })
            }, 
        },

        created() {
            Fire.$on('AfterCreate', () => {
              this.getUsers();
            });
        },

        mounted() {
           this.getUsers(); 
        }
    }
</script>
