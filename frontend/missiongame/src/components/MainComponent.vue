<template>
  <div class="mainComponent">
    <p></p>
    <h1>
      <img :src="getUserImage" />
      <b>{{ getUserId }} {{ msg }}</b>
    </h1>
    <img alt="자네 지금 뭐하나?" src="../assets/main1.jpg" />
    <p></p>
    <p><b>각자 임무를 뽑으세요.</b></p>
    <p><b>각자의 임무를 맞추세요.</b></p>
    <p><b>자기의 임무를 들키지 마세요.</b></p>
    <p><b>오후 10시에 미션이 공개됨.</b></p>
    <h3></h3>
    <button class="btn btn-primary" @click="goPick()">시작하기</button>
    <button v-if="getLoginState" class="btn btn-danger" @click="logout()">
      로그아웃
    </button>
  </div>
</template>

<script>
import { mapGetters } from "vuex";
import { mapMutations } from "vuex";
import http from "../util/http-connect";

export default {
  name: "MainComponent",
  props: {
    msg: String,
  },
  computed: {
    ...mapGetters(["getLoginState", "getUserId", "getUserImage"]),
  },
  created() {
    let jwtFakeTokenInLocalStorage = localStorage.getItem("jwtFake");
    if (jwtFakeTokenInLocalStorage !== null) {
      this.getKakaoUserInfo(jwtFakeTokenInLocalStorage);
      this.createNewUserInBoard(jwtFakeTokenInLocalStorage);
    }
    let now = new Date();
    let hours = now.getHours();
    if (hours >= 22) this.SET_TIMEOUT_STATE_TRUE();
    if (hours < 1) {
      this.SET_TIMEOUT_STATE_FALSE();
      localStorage.removeItem("jwtFake");
    }
  },
  data() {
    return {};
  },
  methods: {
    ...mapMutations([
      "SET_LOGIN_STATE",
      "SET_LOGOUT_STATE",
      "SET_TIMEOUT_STATE_TRUE",
      "SET_TIMEOUT_STATE_FALSE",
    ]),
    goPick() {
      this.$router.push("/choice");
    },
    getKakaoUserInfo(jwtFakeToken) {
      let params = { jwtFakeToken: jwtFakeToken };
      http
        .get(`/kakaoAPI/userinfo`, { params: params })
        .then(({ data }) => {
          // console.log(data);
          this.SET_LOGIN_STATE({
            userId: data.nickname,
            userImage: data.profile_image,
            memberNo: data.memberNo,
          });
        })
        .catch((err) => {
          console.log(err);
        });
    },
    createNewUserInBoard(jwtFakeToken) {
      // console.log(jwtFakeToken);
      let params = { jwtFakeToken: jwtFakeToken };
      http
        .post(`/board/newboard`, params)
        .then(({ data }) => {
          console.log(data);
        })
        .catch((err) => {
          console.log(err);
        });
    },
    logout() {
      localStorage.removeItem("jwtFake");
      this.SET_LOGOUT_STATE();
      alert("로그아웃.");
      this.$router.go();
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
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
  color: #cd7228;
}

.mainComponent {
  width: 700px;
  height: 800px;
  position: absolute;
  left: 50%;
  margin-left: -350px;
  text-align: center;
  background-color: rgba(255, 255, 255, 1);
}
</style>
