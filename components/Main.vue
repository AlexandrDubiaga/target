<template>
  <div class="login navbar-form" >
    <div v-if="checkUser == ''">
        <div class="form-group">
            <input v-model="login" type="text" class="form-control" name="username" placeholder="Username">
        </div>
        <div class="form-group">
            <input v-model="pass" type="password" class="form-control" name="password" placeholder="Password">
        </div>
        <button v-on:click="loginFun()" type="submit" class="btn btn-primary">Sign In</button>
        <p><span class="alert-danger">{{errorMsg}}</span></p>
    </div>
    
    <div v-else class="form-group">
     
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'loginForm',
  data () {
    return {
      login: '',
      pass: '',
      checkUser: '',
      errorMsg: '',
      user: {
        id: '',
        hash: '',
        firstName: '',
      },
      role: '',
      config: {
        headers: {
            'Content-Type': 'application/x-www-form-urlencoded'
            }
      },
    }
  }, 
  methods: {
     getCheck: function(){
                var self = this
                if (localStorage['id'] && localStorage['hash'])
                {
                
                    self.checkUser = 1
                }
                else{
                    self.checkUser = ''
                }
            },
    loginFun: function(){
      var self = this
      self.errorMsg = ''
        if (self.login && self.pass)
        {
          axios.put('http://192.168.0.15/~user2/Booker/client/api/users/', {
            login: self.login,
            pass: self.pass
          }, self.config)
          .then(function (response) {

            if (response.data.id && response.data.hash)
            {
              self.user.id = response.data.id
              self.user.hash = response.data.hash 
              self.user.firstName = response.data.first_name
              self.role = response.data.role
              localStorage['user'] = JSON.stringify(self.user)
            
              self.checkUserFun()
              self.getCheck()
            if (response.data.role == "admin") {
                  self.$router.push("/calendar");
                }
                else
                {
                  self.$router.push("/");
                }
            return true;
          } else {
            self.error = response.data;
            
          }
        })
          .catch(function (error) {
            console.log(error)
          });
        }
        else
        {
          self.errorMsg = 'Enter data in all fields!'
        }
    },
    checkUserFun: function(){
      var self = this
      if (localStorage['user'])
      {    
        self.user = JSON.parse(localStorage['user'])
        axios.get('http://192.168.0.15/~user2/Booker/client/api/users/' + self.user.id)
            .then(function (response) {
                if (self.user.hash === response.data[0].hash)
                {
                      
                    self.checkUser = 1;
                    self.role = response.data[0].role
                    return true
                }
                else
                {
                  self.checkUser = ''
                  delete localStorage['user']
                }
            })
            .catch(function (error) {
              console.log(error)
            });
      }
      else{
        self.checkUser = ''
        return false
      }
    },
    logoutFun: function(){
      var self = this
      delete localStorage['user']
      self.checkUser = ''
      self.pass = ''
      self.getCheck()
    },
  },
  created(){
    this.checkUserFun()
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.login{
 
  padding-top: 7px;
  padding-bottom: 8px;
  bottom: 7px;
}
.form-group{
  padding-bottom: 10px;
}
.regist{
  margin-left: 30px;
}
.hello {
  font-weight: bold;
  font-size: 18px;
  color: darkblue;
}
.myCart {
  position: absolute;
  top: 10px;
  left: 260px;
}
.admin{
  position: absolute;
  top: 84px;
  left: 0px;
}
.myOrders
{
   position: absolute;
  top: 10px;
  left: 150px;
}
</style>