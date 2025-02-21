<template>
    <div class="payment-form-container">
      <div class="card">
        <div class="card-body">
          <h3 class="card-title mb-4">Payment Details</h3>
          
          <div class="alert alert-danger" v-if="error">
            {{ error }}
          </div>
  
          <form @submit.prevent="handleSubmit">
            <div class="mb-3">
              <label for="card-element" class="form-label">Credit or debit card</label>
              <div id="card-element" class="form-control"></div>
              <div id="card-errors" class="text-danger mt-2" role="alert"></div>
            </div>
  
            <button 
              type="submit" 
              class="btn btn-primary w-100"
              :disabled="loading"
            >
              <span v-if="loading" class="spinner-border spinner-border-sm me-2"></span>
              Pay ${{ amount.toLocaleString() }}
            </button>
          </form>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  import { loadStripe } from '@stripe/stripe-js';
  
  export default {
    name: 'PaymentForm',
    props: {
      amount: {
        type: Number,
        required: true
      }
    },
    data() {
      return {
        stripe: null,
        card: null,
        loading: false,
        error: null
      }
    },
    async mounted() {
      // Initialize Stripe
      this.stripe = await loadStripe(import.meta.env.VITE_STRIPE_PUBLISHABLE_KEY);
      const elements = this.stripe.elements();
  
      // Create card element
      this.card = elements.create('card');
      this.card.mount('#card-element');
  
      // Handle validation errors
      this.card.addEventListener('change', (event) => {
        const displayError = document.getElementById('card-errors');
        if (event.error) {
          displayError.textContent = event.error.message;
        } else {
          displayError.textContent = '';
        }
      });
    },
    methods: {
      async handleSubmit() {
        this.loading = true;
        this.error = null;
  
        try {
          // Create payment intent
          console.log(import.meta.env.VITE_API_URL);
          const response = await fetch(`${import.meta.env.VITE_API_URL}/create-payment-intent`, {
            method: 'POST',
            headers: {
              'Content-Type': 'application/json'
            },
            body: JSON.stringify({
              amount: this.amount
            })
          });
  
          const data = await response.json();
  
          // Confirm payment
          const result = await this.stripe.confirmCardPayment(data.clientSecret, {
            payment_method: {
              card: this.card
            }
          });
  
          if (result.error) {
            this.error = result.error.message;
          } else {
            // Payment successful
            this.$emit('payment-success', result.paymentIntent);
          }
        } catch (error) {
          this.error = 'An error occurred while processing your payment. Please try again.';
          console.error('Payment error:', error);
        } finally {
          this.loading = false;
        }
      }
    },
    beforeUnmount() {
      if (this.card) {
        this.card.unmount();
      }
    }
  }
  </script>