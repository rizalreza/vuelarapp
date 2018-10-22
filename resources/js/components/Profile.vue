<style>
.widget-user-header{
    height: 300px !important;
    background-position: center center;
    background-size: cover;
}    
</style>

<template>
    <div class="container">
        <div class="row">
            <div class="col-md-8 offset-md-2">
                <!-- User Widget -->
                <!-- Profile -->
                <div class="card card-widget widget-user">
                  <!-- Add the bg color to the header using any of the bg-* classes -->
                  <div class="widget-user-header text-white" style="background-image:url('./img/banner2.jpg')">
                    <h3 class="widget-user-username">Elizabeth Pierce</h3>
                    <h5 class="widget-user-desc">Web Designer</h5>
                  </div>
                  <div class="widget-user-image">
                    <img class="img-circle" :src="getProfilePict()" alt="User Avatar">
                  </div>
                  <div class="card-footer">
                    <div class="row">
                      <div class="col-sm-4 border-right">
                        <div class="description-block">
                          <h5 class="description-header">3,200</h5>
                          <span class="description-text">SALES</span>
                        </div>
                        <!-- /.description-block -->
                      </div>
                      <!-- /.col -->
                      <div class="col-sm-4 border-right">
                        <div class="description-block">
                          <h5 class="description-header">13,000</h5>
                          <span class="description-text">FOLLOWERS</span>
                        </div>
                        <!-- /.description-block -->
                      </div>
                      <!-- /.col -->
                      <div class="col-sm-4">
                        <div class="description-block">
                          <h5 class="description-header">35</h5>
                          <span class="description-text">PRODUCTS</span>
                        </div>
                        <!-- /.description-block -->
                      </div>
                      <!-- /.col -->
                    </div>
                    <!-- /.row -->
                  </div>
                </div>
                <!-- End of Profile -->
                <!-- Setting Profile -->
                    <div class="card">
                      <div class="card-header p-2">
                        <ul class="nav nav-pills">
                          <li class="nav-item"><a class="nav-link" href="#activity" data-toggle="tab">Activity</a></li>
                          <!-- <li class="nav-item"><a class="nav-link" href="#timeline" data-toggle="tab">Timeline</a></li> -->
                          <li class="nav-item"><a class="nav-link active show" href="#settings" data-toggle="tab">Settings</a></li>
                        </ul>
                      </div><!-- /.card-header -->
                      <div class="card-body">
                        <div class="tab-content">
                          <div class="tab-pane" id="activity">
                                User Activity
                          </div>


                          <div class="tab-pane active show" id="settings">
                            <form class="form-horizontal">

                              <div class="form-group">
                                <label for="inputName" class="col-sm-2 control-label">Name</label>
                                <div class="col-sm-10">
                                  <input v-model="form.name" type="text" class="form-control" id="inputName" placeholder="Name"
                                         :class="{ 'is-invalid': form.errors.has('name') }"
                                  >
                                  <has-error :form="form" field="name"></has-error>
                                </div>
                              </div>

                              <div class="form-group">
                                <label for="inputEmail" class="col-sm-2 control-label">Email</label>
                                <div class="col-sm-10">
                                  <input v-model="form.email" type="email" class="form-control" id="inputEmail" placeholder="Email"
                                         :class="{ 'is-invalid': form.errors.has('email') }"
                                  >
                                  <has-error :form="form" field="email"></has-error>
                                </div>
                              </div>

                              <div class="form-group">
                                <label for="inputPassword" class="col-sm-2 control-label">Password</label>
                                <div class="col-sm-10">
                                  <input v-model="form.password" type="password" class="form-control" id="inputPassword" placeholder="Password"
                                        :class="{ 'is-invalid': form.errors.has('password') }"
                                  >
                                  <has-error :form="form" field="password"></has-error>
                                </div>
                              </div>

                              <div class="form-group">
                                <label for="inputExperience" class="col-sm-2 control-label">Bio</label>

                                <div class="col-sm-10">
                                  <textarea v-model="form.bio" class="form-control" id="inputExperience" placeholder="Bio"></textarea>
                                </div>
                              </div>

                              <div class="form-group">
                                <label for="photo" class="col-sm-4 control-label">Profile Photo</label>
                                  <div class="col-sm-12">
                                    <input type="file" @change="updateProfile" name="photo" class="form-input">
                                  </div>
                              </div>

                              <div class="form-group">
                                <div class="col-sm-offset-2 col-sm-10">
                                  <button @click.prevent="updateData" type="submit" class="btn btn-danger">Submit</button>
                                </div>
                              </div>
                            </form>
                          </div>
                          <!-- /.tab-pane -->
                        </div>
                        <!-- /.tab-content -->
                      </div><!-- /.card-body -->
                    </div>
                <!-- Edn Setting Profile -->

                <!-- End of User Widget -->
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
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

          getProfile() {
            axios.get("api/profile")
            .then(({ data }) => (this.form.fill(data)));
          },

          getProfilePict(){
                let photo = (this.form.photo.length > 200) ? this.form.photo : "img/profile/"+ this.form.photo;
                // return "img/profile/"+ this.form.photo;
                return photo;
          },

          updateData() {
            this.form.put('api/profile')
            .then(() => {

                this.$Progress.finish();
                Fire.$emit('AfterUpdate');
            })
            .catch(() => {

                this.$Progress.fail();
            });
          },

          updateProfile(e) {
            let file = e.target.files[0];
            // console.log(file)
            let reader = new FileReader();
            if (file['size'] < 1527309) {
                  reader.onloadend = (file) => {
                    this.form.photo = reader.result;
                  }
                  reader.readAsDataURL(file); 
            } else {
                swal({
                    type: 'error',
                    title: 'Oops...',
                    text: 'Your file too large !'
                })
            }

          }
        },

        mounted() {
            // console.log(this.form)
            this.getProfile(); 
        },

        created() {
            Fire.$on('AfterUpdate', () => {
                this.getProfile();
            });
        }
    }
</script>
