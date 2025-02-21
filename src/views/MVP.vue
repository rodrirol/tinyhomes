<template>
  <div class="container mt-5">
    <h2>The MVP Tiny Home</h2>
    <div class="row">
      <div class="col-md-8">
        <div
          class="card mb-4"
          v-for="(options, feature) in features"
          :key="feature"
        >
          <div class="card-header">
            <h5 class="mb-0">{{ formatFeatureName(feature) }}</h5>
          </div>
          <div class="card-body">
            <div class="row">
              <div
                class="col-md-4"
                v-for="option in options"
                :key="option.id"
              >
                <div class="form-check">
                  <input
                    class="form-check-input"
                    type="radio"
                    :name="feature"
                    :id="option.id"
                    :value="option"
                    v-model="selections[feature]"
                    @change="calculateTotal"
                  />
                  <label
                    class="form-check-label"
                    :for="option.id"
                  >
                    {{ option.name }}
                    <span
                      class="text-muted"
                      v-if="option.price > 0"
                    >
                      (+${{ option.price.toLocaleString() }})
                    </span>
                  </label>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div class="col-md-4">
        <div class="card">
          <div class="card-header">
            <h5 class="mb-0">Price Summary</h5>
          </div>
          <div class="card-body">
            <p class="lead">Base Price: $20,000</p>
            <p>Upgrades: ${{ upgradeTotal.toLocaleString() }}</p>
            <h4>Total: ${{ totalPrice.toLocaleString() }}</h4>
            <!-- <button
              class="btn btn-primary btn-lg w-100 mt-3"
              @click="proceedToCheckout"
            >
              Proceed to Checkout
            </button> -->

            <div
              v-if="showPaymentForm"
              class="mt-4"
            >
              <PaymentForm
                :amount="totalPrice"
                @payment-success="handlePaymentSuccess"
              />
            </div>

            <button
              v-else
              class="btn btn-primary btn-lg w-100 mt-3"
              @click="showPaymentForm = true"
            >
              Proceed to Payment
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import PaymentForm from "@/components/PaymentForm.vue";

  export default {
    components: {
      PaymentForm,
    },
    data() {
      return {
        // features: {},
        selections: {},
        upgradeTotal: 0,
        totalPrice: 20000,
        showPaymentForm: false,
        features: {
          roof: [
            { id: 1, name: "Asphalt Shingles", price: 0, level: "basic" },
            { id: 2, name: "Tin Roof", price: 1000, level: "medium" },
            { id: 3, name: "Ceramic Tile", price: 3000, level: "deluxe" },
          ],
          windows: [
            { id: 4, name: "Standard Double-Pane", price: 0, level: "basic" },
            { id: 5, name: "Energy Efficient", price: 1000, level: "medium" },
            {
              id: 6,
              name: "Triple-Pane Smart Tint",
              price: 3000,
              level: "deluxe",
            },
          ],
          kitchen: [
            { id: 7, name: "Basic Appliances", price: 0, level: "basic" },
            {
              id: 8,
              name: "Stainless Steel Package",
              price: 1000,
              level: "medium",
            },
            {
              id: 9,
              name: "Premium Smart Appliances",
              price: 3000,
              level: "deluxe",
            },
          ],
          bathroom: [
            { id: 10, name: "Standard Fixtures", price: 0, level: "basic" },
            { id: 11, name: "Modern Package", price: 1000, level: "medium" },
            {
              id: 12,
              name: "Luxury Spa Package",
              price: 3000,
              level: "deluxe",
            },
          ],
          frontDoor: [
            { id: 13, name: "Basic Steel Door", price: 0, level: "basic" },
            { id: 14, name: "Fiberglass Door", price: 1000, level: "medium" },
            { id: 15, name: "Custom Wood Door", price: 3000, level: "deluxe" },
          ],
          bedroomDoor: [
            { id: 16, name: "Hollow Core", price: 0, level: "basic" },
            { id: 17, name: "Solid Core", price: 1000, level: "medium" },
            { id: 18, name: "Custom Design", price: 3000, level: "deluxe" },
          ],
          exteriorPaint: [
            { id: 19, name: "Standard Paint", price: 0, level: "basic" },
            { id: 20, name: "Premium Paint", price: 1000, level: "medium" },
            { id: 21, name: "Designer Colors", price: 3000, level: "deluxe" },
          ],
          interiorPaint: [
            { id: 22, name: "Basic Paint", price: 0, level: "basic" },
            { id: 23, name: "Premium Paint", price: 1000, level: "medium" },
            { id: 24, name: "Custom Colors", price: 3000, level: "deluxe" },
          ],
          smartHome: [
            { id: 25, name: "No Smart Features", price: 0, level: "basic" },
            {
              id: 26,
              name: "Basic Smart Package",
              price: 1000,
              level: "medium",
            },
            {
              id: 27,
              name: "Full Smart Integration",
              price: 3000,
              level: "deluxe",
            },
          ],
          acHeater: [
            { id: 28, name: "Standard HVAC", price: 0, level: "basic" },
            {
              id: 29,
              name: "High Efficiency System",
              price: 1000,
              level: "medium",
            },
            {
              id: 30,
              name: "Smart Climate System",
              price: 3000,
              level: "deluxe",
            },
          ],
        },
      };
    },
    async created() {
      try {
        // Initialize selections with basic options
        Object.keys(this.features).forEach((feature) => {
          this.selections[feature] = this.features[feature][0];
        });
      } catch (error) {
        console.error("Error fetching features:", error);
      }
    },
    methods: {
      formatFeatureName(feature) {
        return feature
          .replace(/([A-Z])/g, " $1")
          .replace(/^./, (str) => str.toUpperCase());
      },
      calculateTotal() {
        this.upgradeTotal = Object.values(this.selections).reduce(
          (total, selection) => total + selection.price,
          0
        );
        this.totalPrice = 20000 + this.upgradeTotal;
      },
      handlePaymentSuccess(paymentIntent) {
        // Handle successful payment
        alert("Payment successful! Order ID: " + paymentIntent.id);
        // Here you would typically:
        // 1. Save the order to your database
        // 2. Send confirmation email
        // 3. Redirect to success page
        this.showPaymentForm = false;
      },
    },
  };
</script>
