<template>
  <div id="app">
    <header>
      <h1>Vue App</h1>
    </header>
    <main>
      <v-form v-model="valid" ref="form">
        <v-container>
          <v-row>
            <v-col
              cols="12"
              md="4"
            >
              <v-text-field
                v-model="login"
                :rules="nameRules"
                :counter="10"
                label="Login"
                required
              ></v-text-field>
            </v-col>

            <v-col
              cols="12"
              md="4"
            >
              <v-text-field
                v-model="password"
                :rules="nameRules"
                :counter="10"
                label="Password"
                required
              ></v-text-field>
            </v-col>

            <v-col
              cols="12"
              md="4"
            >
              <v-text-field
                v-model="ip"
                :rules="ipRules"
                label="RoomKit IP"
                required
              ></v-text-field>
            </v-col>
            <v-btn
              @click="submit"
              :disabled="!fieldIsEmpty || !valid"
              v-if="!progress">
              submit
            </v-btn>
            <v-btn
              @click="close"
              :disabled="!connection"
              v-if="!progress">
              Close connection
            </v-btn>
          </v-row>
        </v-container>
      </v-form>
      <div class="content">
      </div>


      <v-container class="grey lighten-5">
        <v-row no-gutters>
          <v-col
          >
            <v-card
              class="pa-2"
              outlined
              tile
            >
              <v-card
                max-width="375"
                class="mx-auto"
              >
                <v-list two-line>

                  <v-list-item @click="">
                    <v-list-item-icon>
                      <v-icon color="indigo">mdi-card-bulleted-outline</v-icon>
                    </v-list-item-icon>

                    <v-list-item-content>
                      <v-list-item-title>{{this.SystemName}}</v-list-item-title>
                      <v-list-item-subtitle>System Name</v-list-item-subtitle>
                    </v-list-item-content>

                  </v-list-item>

                  <v-list-item @click="">
                    <v-list-item-icon>
                      <v-icon color="indigo">mdi-alarm</v-icon>
                    </v-list-item-icon>

                    <v-list-item-content>
                      <v-list-item-title>{{this.dateTime}}</v-list-item-title>
                      <v-list-item-subtitle>Date and Time</v-list-item-subtitle>
                    </v-list-item-content>

                  </v-list-item>

                  <v-list-item @click="">
                    <v-list-item-icon>
                      <v-icon color="indigo">mdi-blur</v-icon>
                    </v-list-item-icon>

                    <v-list-item-content>
                      <v-list-item-title>{{this.netMac}}</v-list-item-title>
                      <v-list-item-subtitle>MAC Address</v-list-item-subtitle>
                    </v-list-item-content>

                  </v-list-item>

                  <v-divider inset></v-divider>

                  <v-list-item @click="">
                    <v-list-item-icon>
                      <v-icon color="indigo">mdi-album</v-icon>
                    </v-list-item-icon>

                    <v-list-item-content>
                      <v-list-item-title>{{this.ipv4}}</v-list-item-title>
                      <v-list-item-subtitle>IPv4</v-list-item-subtitle>
                    </v-list-item-content>
                  </v-list-item>

                  <v-list-item @click="">
                    <v-list-item-icon>
                      <v-icon color="indigo">mdi-cloud-download-outline</v-icon>
                    </v-list-item-icon>

                    <v-list-item-content>
                      <v-list-item-title>{{this.SoftUpStat}}</v-list-item-title>
                      <v-list-item-subtitle>Software Upgrade Status</v-list-item-subtitle>
                    </v-list-item-content>
                  </v-list-item>

                  <v-divider inset></v-divider>

                  <v-list-item @click="">
                    <v-list-item-icon>
                      <v-icon color="indigo">mdi-rename-box</v-icon>
                    </v-list-item-icon>

                    <v-list-item-content>
                      <v-list-item-title>{{this.SoftDispName}}</v-list-item-title>
                      <v-list-item-subtitle>SystemUnit Software DisplayName</v-list-item-subtitle>
                    </v-list-item-content>
                  </v-list-item>
                </v-list>
              </v-card>
            </v-card>
          </v-col>
          <v-col
          >
            <v-card
              class="pa-2"
              outlined
              tile
            >
              <v-card
                max-width="375"
                class="mx-auto"
              >
                <v-text-field
                  v-model="newSystemName"
                  label="System Unit Name"
                ></v-text-field>
                <v-btn
                  @click="setname"
                  :disabled="!connection"
                  v-if="!progress">
                  Set System Unit Name
                </v-btn>
                <v-list-item @click="">
                  <v-list-item-icon>
                    <v-icon color="indigo">mdi-phone-in-talk-outline</v-icon>
                  </v-list-item-icon>

                  <v-list-item-content>
                    <v-list-item-title>{{this.NumberOfActiveCalls}}</v-list-item-title>
                    <v-list-item-subtitle>Number Of Active Calls</v-list-item-subtitle>
                  </v-list-item-content>
                </v-list-item>
              </v-card>
            </v-card>
          </v-col>
        </v-row>
      </v-container>

    </main>
  </div>
</template>

<script>
  import axios from 'axios'

  // import jsxapi module
  const jsxapi = require('jsxapi');


  export default {
    data() {
      return {
        // the block where we initialize the variables for further reassignment in functions
        wsconnection: null,
        feedbackGroup: null,
        fireAction: null,
        historyEntries: [],
        connection: false,
        dateTime: [],
        netMac: null,
        ipv4: null,
        SoftUpStat: null,
        SoftDispName: null,
        NumberOfActiveCalls: null,
        valid: false,
        SystemName: '',
        newSystemName: '',
        login: '',
        password: '',
        nameRules: [
          v => !!v || 'Login is required',
          v => v.length <= 10 || 'Name must be less than 10 characters',
        ],
        ip: '',
        ipRules: [
          v => !!v || 'IP is required'
        ],
        progress: false
      }
    },
    // the computed property where the variable 'fieldIsEmpty' can take on the value of 'true' or 'false',
    // depending on whether the fields are filled or unfilled, respectively
    computed: {
      fieldIsEmpty: function () {
        return !!(this.login && this.password && this.ip)
      },
    },
    created() {

    },
    methods: {
      // when users clicked on the 'submit' button function below will be called
      submit() {
        this.initConnection();
      },
      // when users clicked on the 'close connection' button function below will be called, and we'll clean variables and call function for close WebSockets connection
      close() {
        this.login = '';
        this.password = '';
        this.ip = '';
        this.closeConnection();
      },
      // New style API
      setname() {
        if (this.wsconnection) {
          this.wsconnection.Config.SystemUnit.Name.set(this.newSystemName);
          // xConfiguration SystemUnit Name: "Kit 006"
        }
      },
      initConnection() {
        this.ip = 'wss://'.concat(this.ip); // string concatenation for creating WebSocket URI
        console.log(this.ip);
        this.wsconnection = jsxapi.connect(this.ip, {username: this.login, password: this.password}); // initializing WebSockets connection
        this.connection = true;


        // Run functions for get information
        this.wsconnection.on('ready', () => {
          this.getCallHistory();
          this.getDateTime();
          this.getNetworkMac();
          this.getNetworkIpv4();
          this.getSoftUpStat();
          this.getSoftDispName();
          this.getNumberOfActiveCalls();
          this.getSystemName();
        });
        // Bundle feedback listeners for easy unsubscription
        // https://cisco-ce.github.io/jsxapi/classes/feedback.html
        this.feedbackGroup = this.wsconnection.feedback.group([
          // Register listener for track changing UserInterface Name
          this.wsconnection.status.on('UserInterface/ContactInfo/Name', (newName) => {
            console.log('SystemUnit Name', newName);
            this.SystemName = newName;
          }),
          // More about Feedback mechanism pages 78-79
          // https://www.cisco.com/c/dam/en/us/td/docs/telepresence/endpoint/ce910/collaboration-endpoint-software-api-reference-guide-ce910-temp.pdf
          this.wsconnection.status.on('SystemUnit/State/NumberOfActiveCalls', (newNumberCalls) => {
            console.log('NumberOfActiveCalls', newNumberCalls);
            this.NumberOfActiveCalls = newNumberCalls;
          })
        ]);

      },
      closeConnection() {
        this.wsconnection.close();
        // Disable feedback listening for all listeners of the group.
        this.feedbackGroup.off();
        console.log('Connection closed');
      },
      // Block where we call the functions to get the data
      // xapi has xConfiguration, xCommand and xStatus commands, for use it you need different methods
      // all list of available commands you can find in API reference guide
      // https://www.cisco.com/c/dam/en/us/td/docs/telepresence/endpoint/ce910/collaboration-endpoint-software-api-reference-guide-ce910-temp.pdf


      // in finction below we used OLD style API. So you can copy command like 'Provisioning Software UpgradeStatus Status'
      // and find it in API reference guide
      getCallHistory() {
        if (this.wsconnection) {
          this.wsconnection.command("CallHistory Get", {
            DetailLevel: "Full"
          })
            .then((history) => {
              if (history.status === 'OK') {
                this.historyEntries = history.Entry
              }
            });
        }
      },
      getDateTime() {
        if (this.wsconnection) {
          this.wsconnection.command("Time DateTime Get")
            .then((history) => {
              if (history.status === 'OK') {
                this.dateTime = [history.Day, history.Hour, history.Minute, history.Month, history.Second, history.Year]
              }
            });
        }
      },
      getNetworkMac() {
        if (this.wsconnection) {
          this.wsconnection.status.get("Network 1 Ethernet MacAddress")
            .then((history) => {
              this.netMac = history
            });
        }
      },
      getNetworkIpv4() {
        if (this.wsconnection) {
          this.wsconnection.status.get("Network 1 IPv4 Address")
            .then((history) => {
              this.ipv4 = history
            });
        }
      },
      getSoftUpStat() {
        if (this.wsconnection) {
          this.wsconnection.status.get("Provisioning Software UpgradeStatus Status")
            .then((history) => {
              this.SoftUpStat = history
            });
        }
      },
      getSoftDispName() {
        if (this.wsconnection) {
          this.wsconnection.status.get("SystemUnit Software DisplayName")
            .then((history) => {
              this.SoftDispName = history
            });
        }
      },
      // You can use sip addresses below for testing (automatically enabled a call):
      // 111@bjn.vc, fireplace@ivr.vc, goldfish@selfie.vc, halloween@ivr.vc, havnen@expressway.dk
      getNumberOfActiveCalls() {
        if (this.wsconnection) {
          this.wsconnection.status.get("SystemUnit State NumberOfActiveCalls")
            .then((history) => {
              this.NumberOfActiveCalls = history
            });
        }
      },
      getSystemName() {
        if (this.wsconnection) {
          this.wsconnection.status.get("UserInterface ContactInfo Name")
            .then((history) => {
              this.SystemName = history
            });
        }
      },
    }
  }
</script>

<style>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }

  h1, h2 {
    font-weight: normal;
  }

  ul {
    list-style-type: none;
    padding: 0;
  }

  li {
    display: inline-block;
    margin: 0 10px;
  }

  a {
    color: #42b983;
  }

  .mx-auto {
    margin-left: 170px;
    margin-buttom: 100px;
  }
</style>
