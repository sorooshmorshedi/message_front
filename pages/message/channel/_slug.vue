<template>
  <div>
    <div>
      <v-row justify="center">
        <v-col
          :key=1
          cols="auto"
        >

          <v-card
            max-width="600"
            class="mx-auto mt-16"
            v-for="g in channels"
          >
            <v-app-bar
              dark
              color="blue darken-2"
            >
              <v-avatar right class="mr-2">
                <v-img
                  lazy-src="https://static10.tgstat.ru/channels/_0/61/61814e9a6d466ed8bdbf59d6cf0410b5.jpg"
                  :src=g.pic>
                </v-img>
              </v-avatar>

              <v-toolbar-title>{{ g.name }}</v-toolbar-title>

              <v-spacer></v-spacer>
              <v-dialog
                v-model="dialog"
                persistent
                max-width="290"
              >
                <template v-slot:activator="{ on, attrs }">
                  <v-btn
                    color="grey darken-3"
                    dark
                    x-small
                    v-bind="attrs"
                    v-on="on"
                  >
                    about
                  </v-btn>
                  <v-btn
                    v-if="channel_admins.includes(parseInt(slug))"
                    color="grey darken-3"
                    class="ma-2"
                    x-small
                    dark
                    @click="dialog2 = true"
                  >
                    invite link
                  </v-btn>
                  <v-btn
                    v-if="g.private == false"
                    color="grey darken-3"
                    class="ma-2"
                    x-small
                    dark
                    @click="dialog1 = true"
                  >
                    adress
                  </v-btn>
                  <nuxt-link :to="{ path: '/message/channel/edit/'+ $route.params.slug + '/?id=' + id}">
                    <v-btn
                      v-if="channel_admins.includes(parseInt(slug))"
                      color="grey darken-3"
                      class="ma-2"
                      x-small
                      dark
                    >
                      edit
                    </v-btn>
                  </nuxt-link>

                  <v-dialog
                    v-model="dialog2"
                    max-width="500px"
                  >
                    <v-card>
                      <v-card-title>
                        <span>http://127.0.0.1:8000/api/channel/link/{{ g.token }}</span>
                      </v-card-title>
                      <v-card-actions>

                      </v-card-actions>
                    </v-card>
                  </v-dialog>
                  <v-dialog
                    v-model="dialog1"
                    max-width="500px"
                  >
                    <v-card>
                      <v-card-title>
                        <a>http://127.0.0.1:8000/api/c/{{ g.link }}</a>
                      </v-card-title>
                      <v-card-actions>

                      </v-card-actions>
                    </v-card>
                  </v-dialog>
                  <v-dialog
                    v-model="dialog8"
                    max-width="300px"
                  >
                    <v-card style="background-color: steelblue">
                      <v-card v-for="seen in seens" width="280" class="mx-auto ma-1" height="55">
                        <v-card-title>
                          <v-avatar size="30">

                            <v-img
                              :src=seen.seen_pic>
                            </v-img>
                          </v-avatar>
                          <h4 style="margin-left: 20px">{{ seen.name }}</h4>
                        </v-card-title>
                        <v-card-actions>

                        </v-card-actions>
                      </v-card>
                    </v-card>
                  </v-dialog>


                </template>
                <v-card>
                  <v-avatar
                    class="ma-5"
                    v-for="g in channels"
                    size="200"
                  >
                    <v-img :src=g.pic></v-img>
                  </v-avatar>

                  <v-card-title class="text-h7">
                    creator
                  </v-card-title>
                  <v-card-text>{{
                      g.creator_name
                    }}
                  </v-card-text>
                  <v-card-title class="text-h7">
                    about
                  </v-card-title>
                  <v-card-text>{{
                      g.about
                    }}
                  </v-card-text>
                  <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn
                      color="green darken-1"
                      text
                      @click="dialog = false"
                    >
                      close
                    </v-btn>
                  </v-card-actions>
                </v-card>
              </v-dialog>
              <v-btn
                v-if="channel_admins.includes(parseInt(slug))"
                color="grey darken-3"
                class="ma-2"
                x-small
                dark
                @click="dialog5 = true"
              >
                adminstraitor
              </v-btn>
              <v-dialog
                v-model="dialog5"
                max-width="500px"
              >
                <v-card>
                  <v-card-title>
                    admins
                  </v-card-title>
                  <v-row class="justify-center">
                    <v-col v-for="admin in admins"
                           :key="admin.id"
                           cols="auto">
                      <div style="margin: 10px;">
                        <v-avatar>
                          <v-img
                            lazy-src="https://play-lh.googleusercontent.com/fgt7dyhffQu9eHEYf1rfrL_xYupnY4bWa1A3PUt_7xXAi5Gi6LxW3SLMaPQwEH37JV4"
                            :src=admin.admin_pic>
                          </v-img>
                        </v-avatar>
                        <h5>{{ admin.name }}
                          <v-icon
                            color="red"
                            small
                            v-for="c in channels"
                            v-if="admin.user != c.creator"
                            @click="deleteAdmin(admin.id)">
                            > mdi-delete
                          </v-icon>
                        </h5>
                      </div>
                    </v-col>
                  </v-row>
                  <v-card-title>
                    add admin
                  </v-card-title>

                  <v-autocomplete
                    class="ml-15 mr-15 "
                    v-model="account"
                    :items="accounts"
                    item-text="item.id"
                    item-value="id"
                    deletable-chips
                    filled
                    chips
                    rounded
                  >
                    <template v-slot:selection="data">
                      <v-chip
                        v-bind="data.attrs"
                        :input-value="data.selected"
                        close
                      >
                        <v-avatar left>
                          <v-img :src="data.item.pic"></v-img>
                        </v-avatar>
                        {{ data.item.name }} {{ data.item.lname }}
                      </v-chip>
                    </template>
                    <template v-slot:item="data">
                      <template>
                        <v-list-item-avatar>
                          <img :src="data.item.pic">
                        </v-list-item-avatar>
                        <v-list-item-content>
                          <v-list-item-title v-html="data.item.name"></v-list-item-title>
                          <v-list-item-subtitle v-html="data.item.lname"></v-list-item-subtitle>
                        </v-list-item-content>
                      </template>
                    </template>
                  </v-autocomplete>

                  <v-card-actions>
                    <v-btn @click="addAdmin">add</v-btn>
                  </v-card-actions>
                </v-card>
              </v-dialog>
            </v-app-bar>

            <v-container>
              <v-row dense>
                <v-col cols="12">
                  <v-btn v-if="messages.length > messagess.length" text color="blue" @click="messagess = messages">see
                    more
                  </v-btn>
                  <div v-for="message in messagess" v-if="channel_members.includes(parseInt(slug))">

                    <v-card
                      v-if="message.sender == slug && message.date < now"
                      color=#8fcbf2
                      width="400"
                      style="border-radius: 70px 10px 70px 70px;margin-left: 180px;margin-left: 180px;"
                      class="mt-2 mb-2"
                    >
                      <v-card-subtitle v-if="message.reply != null">
                        <v-banner
                          style="border-radius: 70px ;margin-left: 50px"
                          width="300"
                          color=#cfebfc
                          class="grey--text text--darken-3"
                          single-line
                        >
                          <v-icon
                            slot="icon"
                            color=primary
                            size="36"
                          >
                            mdi-arrow-left-bottom-bold
                          </v-icon>
                          replay to {{ message.replay_to }}
                          <v-chip small class="ml-2">{{ message.replay_text }}</v-chip>
                        </v-banner>
                      </v-card-subtitle>

                      <v-card-title>
                        <v-avatar>
                          <v-img :src=message.sender_pic></v-img>
                        </v-avatar>
                        <h4 style="color: black; margin-left: 5px;">{{ message.sender_name }}</h4>

                        <v-card-subtitle>{{ message.date }}</v-card-subtitle>
                      </v-card-title>
                      <v-img :src=message.pic></v-img>

                      <v-card-subtitle style="margin-left: 60px; color: black">{{ message.text }}</v-card-subtitle>
                      <v-icon @click="getSeen(message), dialog8 = true" small>mdi-eye</v-icon>

                      <v-btn class="mb-2 ml-16" x-small icon right>
                        <v-icon @click="deleteMessage(message.id)">mdi-delete-outline</v-icon>
                      </v-btn>
                      <v-icon class="mb-2" small @click="saveMessage(message)">mdi-bookmark</v-icon>

                    </v-card>
                    <v-card
                      width="400"
                      class="mt-2 mb-2"
                      v-if="message.sender != slug && message.date < now"
                      style="border-radius: 10px 70px 70px 70px;"
                      color="grey darken-3"
                      dark
                    >
                      <v-card-subtitle v-if="message.reply != null">
                        <v-banner
                          style="border-radius: 70px"
                          width="300"
                          color=#b5b5b5
                          class="grey--text text--darken-3"
                          single-line
                        >
                          <v-icon
                            slot="icon"
                            color=primary
                            size="36"
                          >
                            mdi-arrow-left-bottom-bold
                          </v-icon>
                          replay to {{ message.replay_to }}
                          <v-chip small class="ml-2">{{ message.replay_text }}</v-chip>
                        </v-banner>
                      </v-card-subtitle>

                      <v-card-title>
                        <v-avatar>
                          <v-img :src=message.sender_pic></v-img>
                        </v-avatar>
                        <h4 style="margin-left: 5px;">{{ message.sender_name }}</h4>

                        <v-card-subtitle style="color: dodgerblue">{{ message.date }}</v-card-subtitle>
                      </v-card-title>
                      <v-card-subtitle style="margin-left: 60px">{{ message.text }}</v-card-subtitle>
                      <v-btn class="ml-16 mb-2" x-small icon right>
                        <v-icon @click="LikeMessage(message)">mdi-heart</v-icon>
                      </v-btn>
                      <v-icon class="mb-2" small @click="saveMessage(message)">mdi-bookmark</v-icon>

                      <v-btn class="ma-2 float-right " x-small icon right>
                        <v-icon @click="setRep(message) ,dialog6 = true">mdi-arrow-left-bottom-bold</v-icon>
                      </v-btn>
                      <v-dialog
                        v-model="dialog6"
                        max-width="500px"
                      >
                        <v-card>
                          <v-card-title>
                            <span>replay</span>
                          </v-card-title>


                          <v-card-actions>
                            <v-text-field
                              class="ml-5 "
                              label="message"
                              v-model="rep_text"
                              outlined
                            ></v-text-field>
                            <v-btn fab small class="blue">
                              <v-icon @click=" replay() , dialog3 = false">mdi-send</v-icon>
                            </v-btn>
                          </v-card-actions>
                        </v-card>
                      </v-dialog>

                    </v-card>
                  </div>
                </v-col>
              </v-row>
            </v-container>

          </v-card>
          <v-card
            width="600"
            class="mx-auto mt-1"
            v-for="channel in channels"
            v-if="channel_admins.includes(parseInt(slug))"
          >
            <v-card-subtitle>choose image</v-card-subtitle>
            <v-card-text>
              <input id="image-upload" type="file" ref="file" @change="uploadFile"/>
            </v-card-text>
            <v-card-actions>
              <v-text-field
                class="ma-2"
                label="message"
                v-model="text"
                outlined
              ></v-text-field>
              <v-btn fab small class="primary">
                <v-icon @click="send">mdi-send</v-icon>
              </v-btn>
              <v-btn fab x-small class="primary">
                <v-icon @click="dialog11 = true">mdi-clock</v-icon>
              </v-btn>
              <v-dialog
                v-model="dialog11"
                max-width="500px"
              >
                <v-card class="pa-3">
                  <v-card-title>
                    <span>timing message</span>
                  </v-card-title>

                  <v-time-picker
                    v-model="time"
                    :landscape="$vuetify.breakpoint.mdAndUp"
                    full-width
                    type="month"
                  ></v-time-picker>
                  <v-date-picker v-model="picker" class="ml-16"></v-date-picker>
                  <v-card-actions>
                    <v-text-field
                      class="ml-5 "
                      label="message"
                      v-model="rep_text"
                      outlined
                    ></v-text-field>
                    <v-btn fab small class="blue">
                      <v-icon @click=" timeSend() , dialog11 = false">mdi-send</v-icon>
                    </v-btn>
                  </v-card-actions>
                </v-card>
              </v-dialog>

            </v-card-actions>
          </v-card>
        </v-col>
        <v-col
          :key=2
          cols="auto"
        >
          <v-card
            max-width="300"
            class="mx-auto mt-16"
            v-for="g in channels"
          >
            <v-app-bar
              dark
              color="blue darken-2"
            >

              <v-toolbar-title>members</v-toolbar-title>
              <v-spacer></v-spacer>
              <v-btn
                v-if="channel_admins.includes(parseInt(slug))"
                color="grey darken-3"
                class="ma-2"
                fab
                small
                dark
                @click="dialog3 = true"
              >
                <v-icon>mdi-plus</v-icon>
              </v-btn>
              <div v-if="g.private == false">
                <v-btn
                  v-if="!channel_members.includes(parseInt(slug))"
                  color="grey darken-3"
                  class="ma-2"
                  small
                  dark
                  @click="joinMember"
                >
                  join
                </v-btn>
              </div>

              <div v-if="g.creator != slug">

                <v-btn
                  v-if="channel_members.includes(parseInt(slug))"
                  color="grey darken-3"
                  class="ma-2"
                  small
                  dark
                  @click="leave(slug)"
                >
                  leave
                </v-btn>
              </div>
              <v-dialog
                v-model="dialog3"
                max-width="500px"
              >
                <v-card>
                  <v-card-title>
                    <span>add members</span>
                    <v-spacer></v-spacer>
                    <v-menu
                      bottom
                      left
                    >
                      <template v-slot:activator="{ on, attrs }">
                        <v-autocomplete
                          class="ml-2 mr-2 "
                          v-model="account"
                          :items="accounts"
                          item-text="item.id"
                          item-value="id"
                          deletable-chips
                          filled
                          chips
                          multiple
                          rounded
                        >
                          <template v-slot:selection="data">
                            <v-chip
                              v-bind="data.attrs"
                              :input-value="data.selected"
                              close
                            >
                              <v-avatar left>
                                <v-img :src="data.item.pic"></v-img>
                              </v-avatar>
                              {{ data.item.name }} {{ data.item.lname }}
                            </v-chip>
                          </template>
                          <template v-slot:item="data">
                            <template>
                              <v-list-item-avatar>
                                <img :src="data.item.pic">
                              </v-list-item-avatar>
                              <v-list-item-content>
                                <v-list-item-title v-html="data.item.name"></v-list-item-title>
                                <v-list-item-subtitle v-html="data.item.lname"></v-list-item-subtitle>
                              </v-list-item-content>
                            </template>
                          </template>
                        </v-autocomplete>
                      </template>
                    </v-menu>
                  </v-card-title>
                  <v-card-actions>
                    <v-btn
                      color="green"
                      text
                      @click="dialog3 = false"
                    >
                      Close
                    </v-btn>
                    <v-btn
                      color="orange"
                      text
                      @click="addMember"
                    >
                      add
                    </v-btn>

                  </v-card-actions>
                </v-card>
              </v-dialog>
            </v-app-bar>

            <v-container>
              <v-row dense>
                <v-col cols="12">
                  <div v-for="member in members">

                    <v-card
                      width="400"
                      class="mt-2 mb-2"
                      color="grey darken-3"
                      dark
                    >
                      <v-card-title>
                        <v-avatar size="30">
                          <v-img :src=member.profile_picture></v-img>
                        </v-avatar>
                        <h4 style="margin-left: 5px;">{{ member.first_name }} {{ member.last_name }}
                          <v-btn class="ml-8" x-small color="red darken-2" fab v-for="g in channels"
                                 v-if=" member.id != g.creator">
                            <v-icon
                              v-if="channel_admins.includes(parseInt(slug))"
                              @click="deletemember(member.id)"
                              style="position: center"
                            >mdi-delete-outline
                            </v-icon>
                          </v-btn>
                        </h4>

                      </v-card-title>
                    </v-card>
                  </div>
                </v-col>
              </v-row>
            </v-container>

          </v-card>
        </v-col>
      </v-row>


    </div>


  </div>
</template>

<script>
import moment from 'moment'
export default {
  data() {
    return {
      picker:'',
      time: '11:15',
      datetime : new Date(),
      now: moment().format(),
      data: [],
      name: '',
      pic: null,
      dialog: false,
      slug: this.$route.params.slug,
      id: this.$route.query.id,
      dialog3: false,
      dialog2: false,
      dialog5: false,
      dialog1: false,
      dialog6: false,
      account: null,
      dialog8: false,
      dialog11: false,
      username: '',
      f_name: '',
      l_name: '',
      phone: '',
      about: '',
      file: null,
      channel_members: [],
      channel_admins: [],
      account_name: [],
      accounts: [],
      my_user: '',
      messages: '',
      messagess: '',
      friend: '',
      text: '',
      channels: '',
      members: '',
      admins: '',
      rep: '',
      rep_text: '',
      seens: null,

    }
  },
  mounted() {
    this.$axios.$get('http://127.0.0.1:8000/api/seen/channel/' + this.id + '/' + this.slug)
      .then(response => {
        console.log(response)
      });

    this.$axios.$get('http://127.0.0.1:8000/api/channel/chats/' + this.id)
      .then(response => {
        this.messages = response
        this.messagess = this.messages.slice(-10)

        console.log(response)
      });
    this.$axios.$get('http://127.0.0.1:8000/api/channel/' + this.id)
      .then(response => {
        this.channels = response
        console.log(response)
      });
    this.$axios.$get('http://127.0.0.1:8000/api/channel/members/' + this.id)
      .then(response => {
        this.members = response
        for (const x in response) {
          this.channel_members.push(response[x].id)
        }
        console.log(response)
        console.log(this.channel_members)
      });
    this.$axios.$get('http://127.0.0.1:8000/api/channel/admins/' + this.id)
      .then(response => {
        this.admins = response
        for (const y in response) {
          this.channel_admins.push(response[y].user)
        }
        console.log(this.channel_admins)
      });

    this.$axios.$get('http://127.0.0.1:8000/api/user/')
      .then(response => {
        this.members = response
        console.log(response)
      });
    this.$axios.$get('http://127.0.0.1:8000/api/user')
      .then(response => {

        for (let ac in response) {
          this.accounts.push({
            'name': response[ac].first_name,
            'lname': response[ac].last_name,
            'id': response[ac].id,
            'pic': response[ac].profile_picture
          })
          this.account_name.push(response[ac].first_name)
        }
      })


  },

  methods: {
    timeSend() {
      console.log(this.picker)
      console.log(this.time.slice(3))
      console.log(this.time.slice(0,2))
      this.datetime = new Date(this.picker)
      this.datetime.setHours(parseInt(this.time.slice(0,2)))
      this.datetime.setMinutes(parseInt(this.time.slice(3)))
      console.log(this.datetime)
      this.$axios.$post('http://127.0.0.1:8000/api/message', {
        text: this.rep_text,
        sender: this.slug,
        channel: this.id,
        date: this.datetime
      })
        .then(response => {
          console.log(response)
          window.alert('timer message activate')
          window.location.href = "http://127.0.0.1:3000/message/channel/" + this.slug + '/?id=' + this.id
        }).catch(response => {
        window.alert(response)
      })
    },

    setRep(message) {
      console.log(message.id)
      this.rep = message.id

    },
    getSeen(message) {
      this.$axios.$get('http://127.0.0.1:8000/api/message/seen/' + message.id)
        .then(response => {
          console.log(response)
          this.seens = response
        })
    },

    uploadFile() {
      this.file = this.$refs.file.files[0];
      console.log(this.file)
      console.log(this.$refs.file.files[0])
    },

    send() {
      const formData = new FormData()
      formData.append("sender", this.slug);
      formData.append("channel", this.id);
      formData.append("text", this.text);
      if (this.file != null) {
        formData.append('pic', this.file)
      }
      this.data = formData
      console.log(this.data)
      this.$axios.$post('http://127.0.0.1:8000/api/message', this.data)
        .then(response => {
          console.log(response)
          window.alert('message sent')
          window.location.href = "http://127.0.0.1:3000/message/channel/" + this.slug + '/?id=' + this.id
        }).catch(response => {
        window.alert(response)
      })
    },
    replay() {
      this.$axios.$post('http://127.0.0.1:8000/api/message', {
        text: this.rep_text,
        sender: this.slug,
        channel: this.id,
        reply: this.rep,

      })
        .then(response => {
          console.log(response)
          window.alert('replay sent')
          window.location.href = "http://127.0.0.1:3000/message/channel/" + this.slug + '/?id=' + this.id
        }).catch(response => {
        window.alert(response)
      })
    },


    deleteMessage(id) {
      this.$axios.$delete('http://127.0.0.1:8000/api/message/' + id)
        .then(response => {
          console.log(response)
          window.alert('message deleted')
          window.location.href = "http://127.0.0.1:3000/message/channel/" + this.slug + '/?id=' + this.id
        }).catch(response => {
        window.alert(response)
      })
    },
    LikeMessage(message) {
      this.$axios.$post('http://127.0.0.1:8000/api/like', {
        message: message.id,
        user: this.slug
      })
        .then(response => {
          console.log(response)
          window.alert('message liked')
        }).catch(response => {
        this.$axios.$get('http://127.0.0.1:8000/api/unlike/' + this.slug + '/' + message.id).then(response => {
          console.log(response)
          window.alert('message unliked')
        })
      })
    },
    addMember() {
      this.$axios.$post('http://127.0.0.1:8000/api/channel/add/' + this.id, {
        members: this.account
      })
        .then(response => {
          console.log(response)
          window.alert('members  added')
          window.location.href = "http://127.0.0.1:3000/message/channel/" + this.slug + '/?id=' + this.id

        })
    },
    joinMember() {
      this.$axios.$get('http://127.0.0.1:8000/api/channel/join/' + this.id + '/' + this.slug)
        .then(response => {
          console.log(response)
          window.alert('you joined')
          window.location.href = "http://127.0.0.1:3000/message/channel/" + this.slug + '/?id=' + this.id

        })
    },
    leave(id) {
      this.$axios.$get('http://127.0.0.1:8000/api/channel/leave/' + this.id + '/' + id)
        .then(response => {
          console.log(response)
          window.alert('you leaved')
          window.location.href = "http://127.0.0.1:3000/message/" + this.slug

        })
    },
    deletemember(id) {
      this.$axios.$get('http://127.0.0.1:8000/api/channel/leave/' + this.id + '/' + id)
        .then(response => {
          console.log(response)
          window.alert('member deleted')
          window.location.href = "http://127.0.0.1:3000/message/group/" + this.slug + '/?id=' + this.id

        })
    },

    addAdmin() {
      this.$axios.$post('http://127.0.0.1:8000/api/admin', {
        user: this.account,
        channel: this.id
      })
        .then(response => {
          console.log(response)
          window.alert('admin added')
          window.location.href = "http://127.0.0.1:3000/message/channel/" + this.slug + '/?id=' + this.id

        })
    },
    deleteAdmin(id) {
      this.$axios.$delete('http://127.0.0.1:8000/api/admin/' + id + '/')
        .then(response => {
          console.log(response)
          console.log(id)

          window.alert('admin deleted')
          window.location.href = "http://127.0.0.1:3000/message/channel/" + this.slug + '/?id=' + this.id

        })
    },
    saveMessage(message) {
      this.$axios.$post('http://127.0.0.1:8000/api/archive', {
        user: this.slug,
        message: message.id
      })
        .then(response => {
          console.log(response)
          window.alert('archived')
        }).catch(response => {
        window.alert('you arechived this')
      })
    },



  },


}
</script>
