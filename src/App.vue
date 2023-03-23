<template>
  <div>
    <h1>create-liff-app</h1>
    <p v-if="message">{{ message }}</p>
    <p v-if="error">
      <code>{{ error }}</code>
    </p>
    <a href="https://developers.line.biz/ja/docs/liff/" target="_blank" rel="noreferrer">
      LIFF Documentation
    </a>
  </div>
</template>

<script>
import liff from "@line/liff";

export default {
  data() {
    return {
      message: "",
      error: ""
    };
  },
  mounted() {
    this.liffInit()
  },
  methods: {
    randStr(length) {
      let result = '';
      const characters = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789';
      const charactersLength = characters.length;
      let counter = 0;
      while (counter < length) {
        result += characters.charAt(Math.floor(Math.random() * charactersLength));
        counter += 1;
      }
      return result;
    },
    async liffInit() {
      let params = new URLSearchParams(window.location.search)
      if (params.get("error") === "ACCESS_DENIED") {
        console.log('not allow')
        console.log('not allow')
        console.log('not allow')
        console.log('not allow')
        console.log('not allow')
        console.log('not allow')
        console.log('not allow')
        console.log('not allow')
        this.message = "no init LIFF";
        this.error = `ACCESS_DENIED`;
        // window.location.href = import.meta.env.VITE_FLOW_FRIEND_URL
      } else {
        try {
          await liff.init({ liffId: import.meta.env.VITE_LIFF_ID })
          this.message = "LIFF init succeeded.";
  
          let rdUrl = `${window.location.origin}${window.location.pathname}`
          if (params.get("addFriendFlow") === "true") {
            console.log("go to authorize")
            // remove addFriendFlow for stop loop
            params.delete("addFriendFlow")
            rdUrl = `${rdUrl}?${params.toString()}`
            const authorizeUrl = `https://access.line.me/oauth2/v2.1/authorize?response_type=code&client_id=${import.meta.env.VITE_CLIEND_ID}&redirect_uri=${encodeURIComponent(rdUrl)}&state=${this.randStr(10)}&scope=profile&prompt=consent&bot_prompt=aggressive`
            
            console.log(authorizeUrl)
            window.location.href = authorizeUrl
          } else {
            // login
            console.log("Login")
            if(!liff.isLoggedIn()){
              await liff.login({ redirectUri: rdUrl })
            } else {
              // get friendship
              console.log("getFriendship")
              const token = liff.getAccessToken()
              const fsp = await liff.getFriendship()
              console.log('token', token, 'friend', fsp)

              // check friendship changed to stop loop add friend (if true = continue / false = go to add friend)
              console.log('fs_changed', params.get('friendship_status_changed'))
              console.log('fs_changed check', params.get('friendship_status_changed') !== 'true')
              if (params.get('friendship_status_changed') !== 'true') {
                if (!fsp.friendFlag) {
                  console.log('no friend')
                  console.log('no friend')
                  console.log('no friend')
                  console.log('no friend')
                  console.log('no friend')
                  console.log('no friend')
                  console.log('no friend')
                  window.location.href = import.meta.env.VITE_FLOW_FRIEND_URL
                }
              }

              const profile = await liff.getProfile()
              this.message = profile.displayName
            }
          }
        } catch (e) {
          this.message = "LIFF init failed.";
          this.error = `${e}`;
        }
      }
    }
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
