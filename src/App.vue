<template>
  <div id="app">
    <input type="number" min="1" v-model="tickets" /> Ticket(s)
    <h2>Total: ${{ total }}</h2>
    <label>First Name:</label>
    <input type="text" v-model="firstname" />
    <br />
    <br />
    <label>Last Name:</label>
    <input type="text" v-model="lastname" />
    <br />
    <br />
    <label>Email:</label>
    <input name="email" type="email" v-model="email" />
    <br />
    <br />
    <div class="card" ref="card"></div>
    <br />
    <div v-show="showPurchase" class="flex space">
      <button @click="purchase">Purchase</button>
    </div>
    <div class="flex message space">{{message}}</div>
    <div v-show="message === 'Processing...'">
      <img class="spinner" src="@/assets/ring-spinner.gif" alt="Spinner" />
    </div>
    <p>If you do not receive an email confirmation, please check your spam/junk folder.</p>
    <p>
      Questions? Contact
      <a href="mailto:theatre@covenantchristian.org">theatre@covenantchristian.org</a>.
    </p>
    <img class="logo" src="@/assets/cchs-logo.svg" alt="CCHS Logo" />
  </div>
</template>

<script>
import axios from "axios";

// eslint-disable-next-line no-undef
let stripe = Stripe('pk_test_wgPmrDXSBM6AamX7WP2VqBjq00wuxOvppL')
// let stripe = Stripe("pk_live_fIxJlqxQbJRICEasZvxs2QK600EmFoZAiO");
let elements = stripe.elements();
let card = undefined;

export default {
  name: 'App',
  data: () => {
    return {
      tickets: 1,
      firstname: "",
      lastname: "",
      email: "",
      showPurchase: true,
      message: ""
    };
  },
  computed: {
    total() {
      return this.tickets * 10;
    }
  },
  methods: {
    purchase() {
      if (this.firstname === "" || this.lastname === "" || this.email === "")
        return;
      this.showPurchase = false;
      this.message = "Processing...";
      stripe.createPaymentMethod("card", card).then(result => {
        if (result.error) {
          this.message = result.error.message;
          this.showPurchase = true;
        } else {
          let data = {
            description: "Spring Spec 2020",
            tickets: this.tickets,
            paymentMethodId: result.paymentMethod.id
          };
          axios
            .post(
              "https://us-central1-my-covenant.cloudfunctions.net/payStripe",
              {
                data
              }
            )
            .then(result => {
              let _this = this;
              stripe
                .retrievePaymentIntent(result.data.clientSecret)
                .then(function(result) {
                  let paymentIntent = result.paymentIntent;
                  if (paymentIntent.id) {
                    let html = "<html><body>";
                    html += "<h1>Spring Spec Tickets</h1>";
                    html +=
                      "<p>Thank you for your order and supporting Covenant Fine Arts!</p>";
                    html += "<h2>Your tickets:</h2><ul>";
                    html += `<li>Friday, March 13 tickets: ${_this.tickets}</li>`;
                    html += "</ul>";
                    html +=
                      "<p>Performance at Chapel Rock Christian Church at 7pm.</p>";
                    html += "</body></html>";

                    data = {
                      firstname: _this.firstname,
                      lastname: _this.lastname,
                      email: _this.email,
                      tickets: _this.tickets,
                      transaction: paymentIntent.id,
                      amount: _this.total,
                      description: "Spring Spec Tickets",
                      html: html
                    };
                    axios
                      .post(
                        "https://us-central1-my-covenant.cloudfunctions.net/specTickets",
                        { data }
                      )
                      .then(result => {
                        _this.message = result.data.description;
                      })
                      .catch(() => {});
                  }
                });
            });
        }
      });
    }
  },
  mounted() {
    var style = {
      base: {
        color: "#32325d",
        fontFamily: '"Helvetica Neue", Helvetica, sans-serif',
        fontSmoothing: "antialiased",
        fontSize: "18px",
        "::placeholder": {
          color: "#aab7c4"
        }
      },
      invalid: {
        color: "#fa755a",
        iconColor: "#fa755a"
      }
    };
    card = elements.create("card", { style: style });
    card.mount(this.$refs.card);
  }
}
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
button {
  font-size: 1.5em;
}
img {
  width: 60vw;
}
input {
  font-size: 1.5em;
}
input[type="number"] {
  width: 100px;
}
.card {
  margin-left: 20vw;
  width: 60vw;
}
.flex {
  display: flex;
  justify-content: center;
}
.logo {
  width: 50vw;
}
.message {
  font-size: 1.5em;
  font-weight: bold;
}
.space {
  margin: 4vh 1vw;
}
.spinner {
  width: 20vw;
}
@media only screen and (max-width: 600px) {
  .card {
    margin-left: 0;
    width: auto;
  }
}
</style>
