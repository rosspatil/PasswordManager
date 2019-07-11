<template>
  <view class="list-container">
    <text-input
      v-model="orgName"
      class="input-name"
      :style="{height: 40,width:370, borderColor: 'gray', borderWidth: 1}"
      placeholder="Enter name for search"
    ></text-input>

    <scroll-view class="scroll-view" v-if="showPasswordList.length > 0">
      <view v-for="(password, index) in showPasswordList" v-bind:key="index">
        <view class="list-object">
          <text>Name: {{password.name}}</text>
          <text>Password: *************</text>

          <view class="row-delete">
            <touchable-opacity :onPress="() => deleteMe(password.name)">
              <image :style="{width: 20, height: 20}" :source="require('./delete.png')" />
            </touchable-opacity>
          </view>
          <view class="row-copy">
            <touchable-opacity :onPress="() => copyText(password.password)">
              <image :style="{width: 20, height: 20}" :source="require('./copy.png')" />
            </touchable-opacity>
          </view>
        </view>
      </view>
    </scroll-view>
    <view v-else>
      <text class="no-password">No Password Found</text>
    </view>
  </view>
</template>

<script>
import { Clipboard, Alert } from "react-native";

export default {
  props: {
    passwords: {
      type: Array,
      default: []
    },
    app: {
      type: Object,
      default: null
    }
  },
  mounted() {
    this.passwordsToShow = JSON.parse(JSON.stringify(this.passwords));
  },
  computed: {
    showPasswordList() {
      if (this.orgName === null || this.orgName === "") {
        return this.passwords;
      }
      this.passwordsToShow = [];
      for (let i = 0; i < this.passwords.length; i++) {
        if (
          this.passwords[i].name
            .toLowerCase()
            .includes(this.orgName.toLowerCase())
        ) {
          this.passwordsToShow.push(this.passwords[i]);
        }
      }
      return this.passwordsToShow;
    }
  },
  data() {
    return {
      orgName: "",
      passwordsToShow: [],
      allPasswords: JSON.parse(JSON.stringify(this.passwords))
    };
  },
  methods: {
    copyText(password) {
      Clipboard.setString(password);
      alert("Password Copied!!");
    },
    deleteMe(name) {
      if (this.app === null) {
        alert("Something went wrong!!");
        return;
      }
      let self = this;
      Alert.alert(
        "Alert",
        "Do you want to delete " + name + " ?",
        [
          {
            text: "Yes",
            onPress: () => {
              self.app
                .database()
                .ref(name)
                .remove()
                .then(res => {
                  alert(name + " deleted!!");
                });
            }
          },
          {
            text: "Cancel",
            onPress: () => {},
            style: "cancel"
          }
        ],
        { cancelable: true }
      );
    }
  }
};
</script>

<style>
.list-container {
  top: 300px;
  position: absolute;
}
.no-password {
  height: 350px;
  position: relative;
  font-size: 20px;
}
.scroll-view {
  margin-top: 4px;
  height: 350px;
  position: relative;
}
.list-object {
  width: 370px;
  height: 50px;
  border-color: black;
  border-width: 1px;
}
.input-name {
  position: relative;
}
.btn-me {
  margin-top: 4px;
  position: relative;
  width: 370px;
}
.row-edit {
  margin-top: 15px;
  position: absolute;
  right: 100px;
}
.row-delete {
  margin-top: 15px;
  position: absolute;
  right: 90px;
}
.row-copy {
  margin-top: 15px;
  position: absolute;
  right: 50px;
}
</style>
