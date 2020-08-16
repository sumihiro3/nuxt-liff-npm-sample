<template lang="pug">
v-container
  v-card
    v-card-title.headline
      | Welcome to the LIFF sample application using npm module
    v-card-text
      p
        | This sample application provide LINE Login and show your LINE profile.
    v-card-text(v-if="profile")
      p.subtitle-1
        | Your LINE profile
      v-avatar(size="256")
        v-img(:src="profile.pictureUrl")
      v-simple-table
        thead
          tr
            th.text-left Attribute
            th.text-left Value
        tbody
          tr
            td.text-left ID
            td.text-left {{ profile.userId }}
          tr
            td.text-left Display name
            td.text-left {{ profile.displayName }}
    v-card-text(v-if="liffIniitalized === true")
      p.subtitle-1
        | LIFF information
      //- Show LIFF informations
      v-simple-table
        thead
          tr
            th.text-left Attribute
            th.text-left Value
        tbody
          tr
            td.text-left OS
            td.text-left {{ os }}
          tr
            td.text-left Language
            td.text-left {{ language }}
          tr
            td.text-left LIFF Version
            td.text-left {{ liffVersion }}
          tr
            td.text-left LINE Version
            td.text-left {{ lineVersion }}
          tr
            td.text-left Is In LINE App
            td.text-left {{ isInClient }}
    v-card-actions
      v-spacer
      v-btn(color="primary", @click="liffLogin()", v-if="!profile")
        | Login
      v-btn(color="secondary", @click="liffLogout()", v-else)
        | Logout
      v-spacer
</template>

<script>
let liff;
if (process.client) {
  // ブラウザでの実行時のみLIFF を利用する
  liff = require("@line/liff");
}
const liffId = process.env.LIFF_ID;

export default {
  data() {
    return {
      profile: null,
      liffIniitalized: false,
    };
  },
  async mounted() {
    console.log(`LIFF_ID: ${process.env.LIFF_ID}`);
    if (process.client) {
      try {
        // 1. LIFFの初期化
        await liff.init({ liffId });
        this.liffIniitalized = true;
      } catch (error) {
        console.error(`LIFF initialize failed ${error}`);
      }
      // Get Profile
      if (liff.isLoggedIn()) {
        const profile = await liff.getProfile();
        this.profile = profile;
      }
    }
  },
  computed: {
    os: function () {
      return liff.getOS();
    },
    language: function () {
      return liff.getLanguage();
    },
    liffVersion: function () {
      return liff.getVersion();
    },
    lineVersion: function () {
      return liff.getLineVersion();
    },
    isInClient: function () {
      return liff.isInClient();
    },
  },
  methods: {
    async liffLogin() {
      // ログイン画面にリダイレクト
      if (!liff.isLoggedIn()) {
        await liff.login();
        return;
      }
    },
    liffLogout() {
      // ログアウト
      liff.logout();
      this.profile = null;
    },
  },
};
</script>
