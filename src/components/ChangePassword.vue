<template>
  <v-container fill-height>
    <v-layout justify-center fill-height align-center>
      <v-flex xs6>
        <v-card>
          <v-card-title>
            <v-flex>
              <h3 class="display-1 font-weight-thin text-lg-center">Change password</h3>
            </v-flex>
          </v-card-title>
          <v-form>
            <v-container>
              <v-alert
                :value="showAlert"
                transition="scale-transition"
                type="error"
                class="mb-3"
              >Invalid user</v-alert>
              <v-text-field
                label="Current Password"
                type="password"
                v-model="info.currentPassword"
                outline
              />
              <v-text-field label="New Password" type="password" v-model="info.newPassword" outline/>
              <v-layout justify-center>
                <div>
                  <v-btn color="primary" @click="changePassword" round outline class="my-0">CHANGE PASSWORD</v-btn>
                </div>
              </v-layout>
            </v-container>
          </v-form>
        </v-card>
      </v-flex>
    </v-layout>
  </v-container>
</template>


<script>
import { mapState, mapActions, mapMutations } from "vuex";
export default {
  data() {
    return {
      info: {
        currentPassword: "",
        newPassword: ""
      },
      showAlert: false
    };
  },
  computed: {
    ...mapState(["userRoles", "user"])
  },
  methods: {
    loginUserAndRedirect() {
      this.loginUser(this.info)
        .then(({ data, status }) => {
          if (status == 200) {
            this.updateStoreUser(data);
            switch (this.user.role) {
              case this.userRoles.VOTER:
                this.$router.push({ name: "party" });
                break;
              case this.userRoles.ADMIN:
                this.$router.push({ name: "admin" });
                break;
              case this.userRoles.CANDIDATE:
                if (user.post == "CHIARMAN")
                  this.$router.push({ name: "chairman" });
                else this.$router.push({ name: "party" });
                break;
              default: // stay on this page
            }
          } else throw Error("Invalid user");
        })
        .catch(error => {
          this.showAlert = true;
        });
    },
    changePassword() {
      this.userPasswordReset({
        id: this.user.id,
        password: this.info.currentPassword,
        newPassword: this.info.newPassword
      }).then(({ data, status }) => {
        if (status == 200) {
          this.$router.push({ name: "create-party" });
        } else {
          showAlert = true;
        }
      });
    },
    ...mapActions(["loginUser", "userPasswordReset"]),
    ...mapMutations(["updateStoreUser"])
  }
};
</script>