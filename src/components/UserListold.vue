<template>
  <v-main>
    <!-- Welcome title -->
    <v-container fluid>
      <v-row align="center" justify="center">
        <v-col cols="12" align="center" justify="center">
          <blockquote>

            <footer>
              <small>
                <em>&mdash;Eagle Financial Services, your Midwest Financial Services Partner.</em>
              </small>
            </footer>
          </blockquote>
        </v-col>

      </v-row>

      <!-- Data table -->
      <v-row align="center" justify="center">
        <v-col cols="12" md="10" v-resize="onResize">
            <v-data-table
              :headers="headers"
              :items="users"
              class="elevation-1"
              style="max-height: 300px; overflow-y: auto"
              v-if="!isMobile"
            >
                    <template v-slot:item="props">
                      <tr>
                        <td align="left">{{ props.item.first_name }}</td>
                        <td align="left">{{ props.item.last_name }}</td>
                        <td nowrap="true" align="left">{{ props.item.email }}</td>
                        <td nowrap="true" align="left">{{ props.item.username }}</td>

                      </tr>
                    </template>
              </v-data-table>

        </v-col>
      </v-row>
    </v-container>
  </v-main>
</template>


<script>

  import router from '../router';
  import {APIService} from '../http/APIService';
  const apiService = new APIService();

  export default {
    name: "UserList",
    data: () => ({
      users: [],
      validUserName: "Guest",
      userSize: 0,
      showMsg: '',
      isMobile: false,
      headers: [
        {text: 'First Name', sortable: false, align: 'left',},
        {text: 'Last Name', sortable: false, align: 'left',},
        {text: 'Email', sortable: false, align: 'left',},
        {text: 'Username', sortable: false, align: 'left',}

      ],

    }),
    mounted() {
      this.getUsers();
      this.showMessages();
    },
    methods: {
      onResize() {
          if (window.innerWidth > 600)
            this.isMobile = false;
          else
            this.isMobile = true;
        },
      showMessages(){
        console.log(this.$route.params.msg)
        if (this.$route.params.msg) {
          this.showMsg = this.$route.params.msg;
        }
      },
      getUsers() {
        apiService.getUserList().then(response => {
          this.Users = response.data.data;
          this.userSize = this.users.length;
          if (localStorage.getItem("isAuthenticates")
            && JSON.parse(localStorage.getItem("isAuthenticates")) === true) {
            this.validUserName = JSON.parse(localStorage.getItem("log_user"));
          }
        }).catch(error => {
          if (error.response.status === 401) {
            localStorage.removeItem('isAuthenticates');
            localStorage.removeItem('log_user');
            localStorage.removeItem('token');
            router.push("/auth");
          }
        });
      }


    }
  };
</script>
