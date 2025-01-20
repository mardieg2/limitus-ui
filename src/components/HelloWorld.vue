<template>
  <div class="offset-container">
    <!-- Existing Card -->
    <v-card
      class="mx-auto"
      prepend-icon="mdi-robot-outline"
      subtitle="Browser Agent: An agent to register my account into any website"
      width="600"
    >
      <v-card-text>
        <h2> Please provide the information below so the agent can register you: </h2>
      </v-card-text>

      <v-container class="pa-4">
        <v-form ref="form" v-model="valid">
          <!-- Website Field -->
          <v-text-field
            dense
            v-model="formData.website"
            label="Website"
            :rules="[rules.required]"
            required
            class="short"
          ></v-text-field>

          <!-- Action Button -->
          <v-btn
            class="mt-4"
            :disabled="!valid"
            color="primary"
            @click="submitForm"
          >
            Submit
          </v-btn>
        </v-form>

        <!-- Display Results -->
        <v-card-text v-if="responseMessage">
          <h3>Results:</h3>
          <pre>{{ responseMessage }}</pre>
        </v-card-text>
      </v-container>
    </v-card>

    <!-- New Card for MetaMask Balance -->
    <v-card
      class="mx-auto mt-6"
      prepend-icon="mdi-wallet-outline"
      subtitle="Check Your MetaMask Wallet Balance"
      width="600"
    >
      <v-card-text>
        <h2>Connect to local wallet endpoint and check balance:</h2>
      </v-card-text>

      <v-container class="pa-4">
        <!-- Wallet Connect Button -->
        <v-btn
          class="mt-4"
          color="success"
          @click="getWalletBalance"
        >
          Connect Wallet
        </v-btn>

        <!-- Display Wallet Balance -->
        <v-card-text>
          <p><strong>Balance:</strong> {{ walletBalance }}</p>
        </v-card-text>
      </v-container>
    </v-card>
  </div>
</template>

<style>
.offset-container {
  display: flex;
  flex-direction: column;
  justify-content: flex-start; /* Aligns content to the top */
  align-items: center; /* Centers horizontally */
  height: 100vh; /* Full viewport height */
  padding-top: 50px; /* Adds space from the top */
}

.short {
  width: 250px;
}
</style>

<script>
import axios from "axios";

export default {
  data() {
    return {
      valid: false,
      formData: {
        website: "",
      },
      responseMessage: "",     // For the register-agent results
      walletBalance: 0,        // Holds the balance from local /wallet endpoint
      rules: {
        required: (value) => !!value || "This field is required",
      },
    };
  },
  methods: {
    // Form submission
    async submitForm() {
      if (this.$refs.form.validate()) {
        try {
          const endpoint = `http://127.0.0.1:8000/register-agent?url=${encodeURIComponent(
            this.formData.website
          )}`;
          const response = await axios.get(endpoint);
          this.responseMessage = response.data.data;
        } catch (error) {
          console.error("Error submitting form:", error);
          this.responseMessage =
            "There was an error fetching the data. Please check the console for details.";
        }
      }
    },

    // Fetch the local wallet balance from FastAPI
    async getWalletBalance() {
      try {
        const response = await axios.get("http://127.0.0.1:8000/wallet");
        // The endpoint returns { "balance": <some_value> }
        this.walletBalance = response.data.balance;
      } catch (error) {
        console.error("Error fetching wallet balance:", error);
        alert("Failed to fetch wallet balance. Check console for details.");
      }
    },
  },
};
</script>
