<template>
  <div
    class="fixed-bottom-right"
    style="display: flex; flex-direction: row-reverse"
  >
    <q-btn
      v-for="(window, index) in windows"
      :key="index"
      color="primary"
      :label="window.name"
      style="width: 300px"
    >
      <q-menu transition-show="jump-up" transition-hide="jump-down" persistent>
        <q-card style="width: 300px">
          <q-card-section v-if="window.type != 'chat'" class="messages">
            <div>
              <q-toolbar-title>Mesajlar</q-toolbar-title>

              <q-input
                class="q-py-sm"
                dense
                v-model="search"
                placeholder="Sohbet Ara"
                @update:model-value="searchFriends"
              >
                <template v-slot:prepend>
                  <q-icon name="search" />
                </template>
              </q-input>
            </div>

            <div>
              <q-list bordered separator>
                <q-item-label header>{{ chatType }}</q-item-label>
                <q-scroll-area style="height: 550px; max-width: 300px">
                  <div style="height: 100vh">
                    <q-item
                      v-for="chat in chatList"
                      :key="chat.id"
                      clickable
                      v-ripple
                      @click="createChatWindows(chat)"
                    >
                      <q-item-section avatar>
                        <q-avatar color="primary" text-color="white">
                          {{ createAvatarLetter(chat.name) }}
                        </q-avatar>
                      </q-item-section>

                      <q-item-section>
                        <q-item-label>{{ chat.name }}</q-item-label>
                        <q-item-label caption>{{ chat.email }}</q-item-label>
                        <q-item-label caption>{{
                          chat.lastMessage
                        }}</q-item-label>
                      </q-item-section>

                      <q-item-section side>
                        <span v-if="unreadMessage">
                          {{ chat.LastTime }}
                        </span>
                        <span v-else>
                          <q-icon name="message" color="red" />
                        </span>
                      </q-item-section>
                    </q-item>
                  </div>
                </q-scroll-area>

                <q-separator />

                <!-- <q-item-label header>Kişiler</q-item-label> -->

                <!-- <q-scroll-area style="height: 100vh; max-width: 300px">
									<q-item style="height: 52px" v-for="contact in chatList" :key="contact.id" class="q-mb-sm" clickable v-ripple @click="createChatWindows(contact)">
										<q-item-section avatar>
											<q-avatar color="primary" text-color="white">
												{{ createAvatarLetter(contact.name) }}
											</q-avatar>
										</q-item-section>

										<q-item-section>
											<q-item-label>{{ contact.name }}</q-item-label>
											<q-item-label caption lines="1">{{ contact.lastMessage }}</q-item-label>
										</q-item-section>
									</q-item>
								</q-scroll-area> -->
              </q-list>
            </div>
          </q-card-section>
          <q-card-section v-else class="chat-view">
            <q-layout
              container
              style="height: 400px"
              class="shadow-2 rounded-borders"
            >
              <q-header elevated>
                <q-toolbar>
                  <q-avatar color="white" text-color="primary">
                    <!-- {{ createAvatarLetter(contact.name) }} -->
                    U
                  </q-avatar>
                  <q-toolbar-title>{{ selectedChat.name }}</q-toolbar-title>

                  <q-btn flat round dense icon="close" />
                </q-toolbar>
              </q-header>

              <q-page-container>
                <q-page>
                  <div class="q-pa-sm">
                    <q-chat-message
                      v-for="(message, index) in selectedChat.messages"
                      :key="index"
                      :text="[message.text]"
                      :sent="message.sender == 'You'"
                    >
                      <template v-slot:name>{{ message.sender }}</template>
                      <template v-slot:stamp>7 minutes ago</template>
                      <template v-slot:avatar>
                        <img
                          v-if="message.sender == 'You'"
                          class="q-message-avatar q-message-avatar--sent"
                          src="https://cdn.quasar.dev/img/avatar4.jpg"
                        />
                        <img
                          v-else
                          class="q-message-avatar q-message-avatar--received"
                          src="https://cdn.quasar.dev/img/avatar2.jpg"
                        />
                      </template>
                    </q-chat-message>
                    <q-spinner-dots
                      v-if="selectedChat.messageIncoming"
                      size="2rem"
                    />
                  </div>
                </q-page>
              </q-page-container>
              <q-footer elevated>
                <q-toolbar>
                  <q-input label-color="white" v-model="search"> </q-input>
                </q-toolbar>
              </q-footer>
            </q-layout>
          </q-card-section>
        </q-card>
      </q-menu>
      <q-space />
      <q-btn
        v-if="window.name != 'Messages'"
        size="sm"
        dense
        flat
        icon="close"
        @click="closeChatWindows(index)"
      />
    </q-btn>
  </div>
</template>

<script setup>
import { ref, watch } from "vue";

const search = ref("");
const chatType = ref("Kişiler");
const friendList = ref([
  {
    id: 4,
    name: "Ayşe Çelik",
    lastMessage: "Son mesaj içeriği",
    email: "ayse@ors.com",
  },
  {
    id: 5,
    name: "Mustafa Şahin",
    lastMessage: "Son mesaj içeriği",
    email: "mustafa@ors.com",
  },
  {
    id: 6,
    name: "Fatma Yıldız",
    lastMessage: "Son mesaj içeriği",
    email: "fatma@ors.com",
  },
  {
    id: 7,
    name: "Ali Öztürk",
    lastMessage: "Son mesaj içeriği",
    email: "ali@ors.com",
  },
  {
    id: 8,
    name: "Emine Aydın",
    lastMessage: "Son mesaj içeriği",
    email: "emine@ors.com",
  },
  {
    id: 9,
    name: "Hüseyin Özdemir",
    lastMessage: "Son mesaj içeriği",
    email: "huseyin@ors.com",
  },
  {
    id: 10,
    name: "Zehra Arslan",
    lastMessage: "Son mesaj içeriği",
    email: "zehra@ors.com",
  },
  {
    id: 11,
    name: "Osman Kılıç",
    lastMessage: "Son mesaj içeriği",
    email: "osman@ors.com",
  },
  {
    id: 12,
    name: "Elif Aslan",
    lastMessage: "Son mesaj içeriği",
    email: "elif@ors.com",
  },
  {
    id: 13,
    name: "Murat Çetin",
    lastMessage: "Son mesaj içeriği",
    email: "murat@ors.com",
  },
  {
    id: 14,
    name: "Selin Kara",
    lastMessage: "Son mesaj içeriği",
    email: "selin@ors.com",
  },
  {
    id: 15,
    name: "Emre Koç",
    lastMessage: "Son mesaj içeriği",
    email: "emre@ors.com",
  },
  {
    id: 16,
    name: "Aylin Kurt",
    lastMessage: "Son mesaj içeriği",
    email: "aylin@ors.com",
  },
  {
    id: 17,
    name: "Burak Özkan",
    lastMessage: "Son mesaj içeriği",
    email: "burak@ors.com",
  },
  {
    id: 18,
    name: "Esra Şen",
    lastMessage: "Son mesaj içeriği",
    email: "esra@ors.com",
  },
  {
    id: 19,
    name: "Cem Güler",
    lastMessage: "Son mesaj içeriği",
    email: "cem@ors.com",
  },
  {
    id: 20,
    name: "Deniz Öz",
    lastMessage: "Son mesaj içeriği",
    email: "deniz@ors.com",
  },
  {
    id: 21,
    name: "Gizem Ateş",
    lastMessage: "Son mesaj içeriği",
    email: "gizem@ors.com",
  },
  {
    id: 22,
    name: "Tolga Polat",
    lastMessage: "Son mesaj içeriği",
    email: "tolga@ors.com",
  },
  {
    id: 23,
    name: "Pınar Aksoy",
    lastMessage: "Son mesaj içeriği",
    email: "pinar@ors.com",
  },
  {
    id: 24,
    name: "Serkan Korkmaz",
    lastMessage: "Son mesaj içeriği",
    email: "serkan@ors.com",
  },
  {
    id: 25,
    name: "Bengü Aktaş",
    lastMessage: "Son mesaj içeriği",
    email: "bengu@ors.com",
  },
  {
    id: 26,
    name: "Onur Bulut",
    lastMessage: "Son mesaj içeriği",
    email: "onur@ors.com",
  },
  {
    id: 27,
    name: "Ceren Gül",
    lastMessage: "Son mesaj içeriği",
    email: "ceren@ors.com",
  },
  {
    id: 28,
    name: "Kaan Güneş",
    lastMessage: "Son mesaj içeriği",
    email: "kaan@ors.com",
  },
  {
    id: 29,
    name: "Merve Yalçın",
    lastMessage: "Son mesaj içeriği",
    email: "merve@ors.com",
  },
  {
    id: 30,
    name: "Umut Acar",
    lastMessage: "Son mesaj içeriği",
    email: "umut@ors.com",
  },
  {
    id: 31,
    name: "Seda Erdoğan",
    lastMessage: "Son mesaj içeriği",
    email: "seda@ors.com",
  },
  {
    id: 32,
    name: "Volkan Tunç",
    lastMessage: "Son mesaj içeriği",
    email: "volkan@ors.com",
  },
  {
    id: 33,
    name: "Derya Keskin",
    lastMessage: "Son mesaj içeriği",
    email: "derya@ors.com",
  },
  {
    id: 34,
    name: "Erkan Özcan",
    lastMessage: "Son mesaj içeriği",
    email: "erkan@ors.com",
  },
  {
    id: 35,
    name: "Gamze Duran",
    lastMessage: "Son mesaj içeriği",
    email: "gamze@ors.com",
  },
  {
    id: 36,
    name: "Hakan Avcı",
    lastMessage: "Son mesaj içeriği",
    email: "hakan@ors.com",
  },
  {
    id: 37,
    name: "İrem Güzel",
    lastMessage: "Son mesaj içeriği",
    email: "irem@ors.com",
  },
  {
    id: 38,
    name: "Levent Kılıç",
    lastMessage: "Son mesaj içeriği",
    email: "levent@ors.com",
  },
  {
    id: 39,
    name: "Nalan Yüksel",
    lastMessage: "Son mesaj içeriği",
    email: "nalan@ors.com",
  },
  {
    id: 40,
    name: "Özgür Çakır",
    lastMessage: "Son mesaj içeriği",
    email: "ozgur@ors.com",
  },
  {
    id: 41,
    name: "Rabia Tekin",
    lastMessage: "Son mesaj içeriği",
    email: "rabia@ors.com",
  },
  {
    id: 42,
    name: "Şahin Koca",
    lastMessage: "Son mesaj içeriği",
    email: "sahin@ors.com",
  },
  {
    id: 43,
    name: "Tuğçe Aydın",
    lastMessage: "Son mesaj içeriği",
    email: "tugce@ors.com",
  },
  {
    id: 44,
    name: "Uğur Çelik",
    lastMessage: "Son mesaj içeriği",
    email: "ugur@ors.com",
  },
  {
    id: 45,
    name: "Yasemin Demir",
    lastMessage: "Son mesaj içeriği",
    email: "yasemin@ors.com",
  },
  {
    id: 46,
    name: "Zafer Kaya",
    lastMessage: "Son mesaj içeriği",
    email: "zafer@ors.com",
  },
  {
    id: 47,
    name: "Aslı Yıldırım",
    lastMessage: "Son mesaj içeriği",
    email: "asli@ors.com",
  },
  {
    id: 48,
    name: "Berk Öztürk",
    lastMessage: "Son mesaj içeriği",
    email: "berk@ors.com",
  },
  {
    id: 49,
    name: "Cansu Sarı",
    lastMessage: "Son mesaj içeriği",
    email: "cansu@ors.com",
  },
  {
    id: 50,
    name: "Doruk Şimşek",
    lastMessage: "Son mesaj içeriği",
    email: "doruk@ors.com",
  },
]);
const chatList = ref([...friendList.value]);
const personNotFound = ref(false);

watch(
  () => search.value,
  (searchInput) => {
    if (searchInput.length >= 2) {
      const term = searchInput.toLowerCase();

      if (term === "") {
        chatList.value = [...activeChats.value];
        return;
      } else {
        const filteredFriendList = friendList.value.filter((person) =>
          person.name.toLowerCase().includes(term)
        );
        if (filteredFriendList.length > 0) {
          chatType.value = "Kişiler";
          personNotFound.value = false;
          chatList.value = filteredFriendList;
        } else {
          personNotFound.value = true;
        }
      }
    } else {
      chatList.value = [...friendList.value];
    }
  }
);

// const unreadMessage = ref(true);

// const selectedChat = ref({
//   id: 1234,
//   name: "Ufuk ÖZBAY",0
//   LastTime: "17:49",
//   messageIncoming: true,
//   messages: [
//     { sender: "Mary", text: "Merhaba Ufuk!" },
//     { sender: "You", text: "Merhaba, nasılsın?" },
//     { sender: "Mary", text: "İyiyim, teşekkürler. Sen nasılsın?" },
//   ],
// });

const windows = ref([
  {
    id: 1,
    name: "Mesajlar",
    type: "menü",
  },
]);

// const chats = ref([
//   {
//     id: 2,
//     name: "Ufuk Özbay",
//     lastMessage: "Son mesaj içeriği",
//     LastTime: "17:49",
//     messages: [
//       { sender: "other", text: "Merhaba Ufuk!" },
//       { sender: "You", text: "Merhaba, nasılsın?" },
//       { sender: "other", text: "İyiyim, teşekkürler. Sen nasılsın?" },
//     ],
//   },
//   {
//     id: 3,
//     name: "Deniz Yalçın",
//     lastMessage: "Son mesaj içeriği",
//     LastTime: "17:35",
//     messages: [
//       { sender: "other", text: "Proje hakkında konuşabilir miyiz?" },
//       { sender: "You", text: "Tabii ki, ne zaman uygun olur?" },
//     ],
//   },
// ]);

const createChatWindows = (chat) => {
  if (windows.value.findIndex((x) => x.id === chat.id) !== -1) {
    console.log("chat mevcut");
    return;
  }
  if (windows.value.length < 5) {
    windows.value.push({
      id: chat.id,
      name: chat.name,
      type: "chat",
      messages: [],
    });
    //
  } else {
    windows.value[4] = {
      id: chat.id,
      name: chat.name,
      type: "chat",
      messages: [],
    };
  }
};

// const closeChatWindows = (index) => {
//   windows.value.splice(index, 1);
// };

const createAvatarLetter = (name) => {
  return name[0];
};

// const result = ref();
// const searchFriends = () => {
//   let foundItem = chatList.value.find((item) =>
//     item.name.toLowerCase().includes(searchTerm.value.toLowerCase())
//   );

// if (!foundItem) {
//   foundItem = chatList2.value.find((item) =>
//     item.name.toLowerCase().includes(searchTerm.value.toLowerCase())
//   );
// }

// chatList.value = [foundItem];
// };
</script>

<style scoped>
.messages {
  height: calc(100vh - 200px);
  overflow: hidden;
  width: 100%;
}
.chat-view {
  display: flex;
  flex-direction: column;
  height: 400px;
  padding: 0;
  color: whitesmoke;
}

/* Diğer stil tanımlamaları buraya eklenebilir */
</style>
