<template>
  <view class="container">
    <text class="heading">Password Manager</text>
    <view v-if="!auth">
      <text-input
        v-model="authPassword"
        class="input-name"
        :secureTextEntry="true"
        :style="{height: 40,width:370, borderColor: 'gray', borderWidth: 1}"
        placeholder="Enter Password"
      ></text-input>
      <button :onPress="authMe" class="saveBtn" title="Login"></button>
    </view>
    <view v-if="auth" class="auth-after">
      <new-registration :app="app" :passwords="allPasswords"></new-registration>
      <list v-if="isChanged" :app="app" :passwords="allPasswords"></list>
    </view>
  </view>
</template>
<script>
import firebase from "firebase";
import NewRegistration from "./new-registration";
import List from "./list-password";
export default {
  components: { NewRegistration, List },
  data: function() {
    return {
      authPassword: "",
      auth: false,
      app: null,
      db: null,
      isChanged: true,
      allPasswords: []
    };
  },
  methods: {
    init() {
      let self = this;
      this.app
        .database()
        .ref()
        .on("value", function(params) {
          self.isChanged = false;
          let tmp = [];
          params.forEach(function(data) {
            tmp.push(data.val());
          });
          self.allPasswords = tmp;
          self.isChanged = true;
        });
    },
    authMe() {
      if (
        this.authPassword === undefined ||
        this.authPassword === null ||
        this.authPassword === ""
      ) {
        alert("Auth is Empty");
        return;
      }
      let self = this;
      this.app = firebase.initializeApp({
        databaseURL: "",
        projectId: "",
        apiKey: ""
      });

      this.app
        .auth()
        .signInWithEmailAndPassword(
          "patilroshan443@gmail.com",
          this.authPassword
        )
        .then(res => {
          self.auth = true;
          self.init();
        })
        .catch(function(error) {
          self.auth = false;
          alert("Something wents Wrong");
        });
    }
  }
};
</script>
<style>
.container {
  background-color: white;
  align-items: center;
  justify-content: center;
  flex: 1;
}
.text-container {
  color: blue;
  padding: 2;
  font-size: 22;
}
.text-input-container {
  width: 300;
  height: 40;
  font-size: 22;
  border-color: gray;
}
.heading {
  top: 50px;
  font-size: 30px;
  position: absolute;
}
.input-name {
  margin-bottom: 2px;
}
.auth-after {
  top: -390px;
  left: -185px;
}
</style>
