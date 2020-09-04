<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <form @submit.prevent="sendDetail">
      <input type="hidden" name="token" />
      <div class="form-row">
        <label for="country">Country</label>
        <select id="country">
          <option value="US">United States</option>
          <option value="CA">Canada</option>
          <option value="GB">United Kingdom</option>
          <option value="DE">Germany</option>
          <option value="FR">France</option>
          <option value="ES">Spain</option>
        </select>
      </div>
      <div class="form-row">
        <label for="currency">Currency</label>
        <select id="currency">
          <option value="USD">US Dollar</option>
          <option value="CAD">Canadian Dollar</option>
          <option value="GBP">Pound</option>
          <option value="EUR">Euro</option>
        </select>
      </div>
      <div class="form-row">
        <label for="routing-number">Routing Number</label>
        <input type="text" id="routing-number" value="110000000XXXX" />
      </div>
      <div class="form-row">
        <label for="account-number">Account Number</label>
        <input type="text" id="account-number" value="000123456789XXXXX" />
      </div>
      <div class="form-row">
        <label for="account-holder-name">Account Holder Name</label>
        <input type="text" id="account-holder-name" value="Jane Austen XXXXXX" />
      </div>
      <div class="form-row">
        <label for="account-holder-type">Account Holder Type</label>
        <select id="account-holder-type">
          <option value="individual">Individual</option>
          <option value="company">Company</option>
        </select>
      </div>
      <button type="submit">Submit</button>
      <div class="outcome">
        <div class="error"></div>
        <div class="success">
          Success! Your Stripe token is
          <span class="token"></span>
        </div>
      </div>
    </form>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  data() {
    return {
      stripe: window.Stripe("ENTER YOUR PUBLIC STRIP KEY"),
    };
  },
  props: {
    msg: String,
  },
  methods: {
    setOutcome(result) {
      var successElement = document.querySelector(".success");
      var errorElement = document.querySelector(".error");
      successElement.classList.remove("visible");
      errorElement.classList.remove("visible");
      if (result.token) {
        // In this example, we're simply displaying the token
        successElement.querySelector(".token").textContent = result.token.id;
        successElement.classList.add("visible");
      } else {
        errorElement.textContent = result.error.message;
        errorElement.classList.add("visible");
      }
    },
    sendDetail() {
      var bankAccountParams = {
        country: document.getElementById("country").value,
        currency: document.getElementById("currency").value,
        account_number: document.getElementById("account-number").value,
        account_holder_name: document.getElementById("account-holder-name")
          .value,
        account_holder_type: document.getElementById("account-holder-type")
          .value,
      };
      if (document.getElementById("routing-number").value != "") {
        bankAccountParams["routing_number"] = document.getElementById(
          "routing-number"
        ).value;
      }
      this.stripe
        .createToken("bank_account", bankAccountParams)
        .then(this.setOutcome);
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.success,
.error {
  display: none;
}

.success.visible,
.error.visible {
  display: inline;
}
</style>
