<template>
  <div class="main">
    <div id="capture">
      <img width="600px" alt="Ticket" :src="img" />
      <h2 v-show="showCode" class="bosco">#{{ code }}</h2>
      <br />
      <p v-show="false" class="bosco-details">
        {{ event.tableFor }} -
        <span
          ><strong>{{ event.name.toUpperCase() }}</strong></span
        >
      </p>
    </div>
    <h1>GENERATE E-TICKET</h1>
    <form @submit.prevent="createEvent">
      <BaseInput
        v-model="event.name"
        type="text"
        placeholder="Name"
        class="field"
        :class="{ error: $v.event.name.$error }"
        @blur="$v.event.name.$touch()"
      />
      <BaseSelect
        :options="tableList"
        v-model="event.tableFor"
        :class="{ error: $v.event.tableFor.$error }"
        @blur="$v.event.tableFor.$touch()"
      />
      <BaseButton
        type="submit"
        buttonClass="-fill-gradient"
        :disabled="$v.$anyError"
        >Generate</BaseButton
      >
      <p v-if="$v.$anyError" class="errorMessage">
        Please fill out the required field(s).
      </p>
    </form>

    <img src="" id="ticket" style="width: 100%" />
  </div>
</template>

<script>
import ticketCode from "ticket-code";
import { required } from "vuelidate/lib/validators";
import BaseInput from "../components/BaseInput.vue";
import BaseButton from "../components/BaseButton.vue";
import BaseSelect from "../components/BaseSelect.vue";
import html2canvas from "html2canvas";
export default {
  name: "Home",
  data() {
    return {
      codeLength: 8,
      checksumSeed: 5,
      checksumIdx: 7,
      code: this.generateCode(),
      showCode: false,
      img: require("@/assets/dellusso.jpg"),
      tableList: [
        "REGULAR TABLE LADIES",
        "REGULAR TABLE MEN",
        "VIP TABLE FOR #THREE",
        "VIP TABLE FOR #FIVE",
        "VIP TABLE FOR #TEN",
        "VVIP",
      ],
      event: { name: "", tableFor: "" },
    };
  },
  validations: {
    event: {
      name: { required },
      tableFor: { required },
    },
  },
  watch: {
    "event.tableFor": function (table) {
      if (table === "VIP TABLE FOR #TEN") {
        this.img = require("@/assets/ten.jpg");
        this.showCode = true;
        return;
      }
      if (table === "VIP TABLE FOR #FIVE") {
        this.img = require("@/assets/five.jpg");
        this.showCode = true;
        return;
      }
      if (table === "VIP TABLE FOR #THREE") {
        this.img = require("@/assets/three.jpg");
        this.showCode = true;
        return;
      }
      if (table === "VVIP") {
        this.img = require("@/assets/vvip.jpg");
        this.showCode = true;
        return;
      }
      if (table === "REGULAR TABLE MEN") {
        this.img = require("@/assets/300.jpg");
        this.showCode = true;
        return;
      }
      if (table === "REGULAR TABLE LADIES") {
        this.img = require("@/assets/200.jpg");
        this.showCode = true;
        return;
      }
      this.showCode = false;
      return;
    },
  },
  methods: {
    generateCode() {
      return ticketCode.generate(
        this.checksumSeed,
        this.codeLength,
        this.checksumIdx
      );
    },

    createEvent() {
      this.generateCode();
      this.$v.$touch();
      if (!this.$v.$invalid) {
        html2canvas(document.querySelector("#capture")).then((canvas) => {
          // document.querySelector("#ticket")
          // document.querySelector("#ticket").appendChild(canvas);
          // var DOM_img = document.createElement("img");
          document.querySelector("#ticket").src = canvas.toDataURL();
          // document.appendChild(DOM_img);
        });
        this.code = this.generateCode();
        this.resetFormData();
      }
    },
    resetFormData() {
      this.event = { name: "", tableFor: "" };
    },
  },
  components: {
    BaseInput,
    BaseSelect,
    BaseButton,
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.main {
  width: 600px;
}
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
  color: #42b983;
}
.bosco {
  padding: 7px;
  position: relative;
  font-size: 18px;
  bottom: 350px;
  color: white;
  font-weight: 900;
  background-color: #c00;
  border-radius: 10px;
  width: 140px;
  float: right;
  right: 200px;
}
.bosco-details {
  margin: 0 auto;
  padding: 10px;
  position: absolute;
  font-size: 14px;
  color: #fff;
  top: 376px;
  font-weight: bold;
  float: left;
}
#ticket {
  margin-top: 100px;
}
</style>
