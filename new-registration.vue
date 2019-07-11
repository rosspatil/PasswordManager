<template>
  <view class="new-form">
    <text-input
      v-model="org.name"
      class="input-name"
      :style="{height: 40,width:370, borderColor: 'gray', borderWidth: 1}"
      placeholder="Enter name"
    ></text-input>
    <text-input
      v-model="org.password"
      class="input-name"
      :secureTextEntry="true"
      :style="{height: 40,width:370, borderColor: 'gray', borderWidth: 1}"
      placeholder="Enter Password"
    ></text-input>
    <text-input
      v-model="repassword"
      class="input-name"
      :style="{height: 40,width:370, borderColor: textBoxColor, borderWidth: 1}"
      placeholder="Re-Enter Password"
    ></text-input>
    <button :disabled="!enableMe" :onPress="saveData" class="saveBtn" title="Save"></button>
  </view>
</template>

<script>
export default {
  props: {
    app: {
      type: Object,
      default: null
    },
    passwords: {
      type: Array,
      default: []
    }
  },
  computed: {
    textBoxColor() {
      if (
        this.org.password !== undefined &&
        this.org.password !== "" &&
        (this.repassword === "" || this.org.password !== this.repassword)
      ) {
        return "red";
      }
      return "gray";
    },
    enableMe() {
      return (
        this.org.name !== "" &&
        this.org.password !== "" &&
        this.repassword !== "" &&
        this.org.password === this.repassword
      );
    }
  },
  methods: {
    resetMe() {
      this.org.name = "";
      this.org.password = "";
      this.repassword = "";
    },
    saveData() {
      for (let i = 0; i < this.passwords.length; i++) {
        if (
          this.passwords[i].name.toLowerCase() === this.org.name.toLowerCase()
        ) {
          alert("Org Name Already Available");
          return;
        }
      }
      if (this.app === null) {
        alert("Something went wrong!!");
        return;
      }
      this.app
        .database()
        .ref(this.org.name)
        .set(this.org)
        .then(() => {
          alert("Saved SuccessFully");
          this.resetMe();
        })
        .catch(err => {
          alert("Something went wrong!!");
        });
    }
  },
  data() {
    return {
      allPasswords: [],
      org: {
        name: "",
        password: ""
      },
      repassword: ""
    };
  }
};
</script>

<style>
.input-name {
  font-size: 15px;
  margin-top: 5px;
}
.new-form {
  position: absolute;
  top: 100px;
}
.saveBtn {
  margin-top: 110px;
}
</style>
