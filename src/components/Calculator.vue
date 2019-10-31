<template>
  <div class="calculator">
    <form @submit.prevent class="card">
      <div class="card__header">
        <h1>Select &amp; Customize Services</h1>
        <div class="total">NGN {{computedValue}}</div>
      </div>
      <div class="grid-container">
        <div v-for="option of options" :key="option.name" class="form-control">
          <div>
            <div class="form-control__name">{{option.name}}</div>
            <div v-for="(input, index) in option.inputs" :key="index">
              <div v-if="input.hasMultiplier">
                <label :for="multiplier.name" class="form-input__label">{{multiplier.display}}</label>
                <input
                  type="number"
                  :name="multiplier.name"
                  min="1"
                  class="form-input"
                  v-model="multiplier.value"
                  @keypress="(event) => calculateTotalFromMultiplier(String.fromCharCode(event.charCode), input)"
                />
              </div>
              <label :for="input.name" class="form-input__label">{{input.display}}</label>
              <div class="relative">
                <select
                  @change="(event) => handleSelectChange(event, input)"
                  class="form-input"
                  required
                  v-model="input.value"
                >
                  <optgroup label="Select an option">
                    <option value disabled selected></option>
                    <option
                      v-for="(value, index) of input.values"
                      :key="index"
                      :value="value"
                    >{{value}}</option>
                  </optgroup>
                </select>
                <div class="form-input__inset">
                  <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20">
                    <path
                      d="M9.293 12.95l.707.707L15.657 8l-1.414-1.414L10 10.828 5.757 6.586 4.343 8z"
                    />
                  </svg>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="card__footer">
        <button>
          <span>Get Started</span>
          <span>
            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24">
              <path
                d="M9.3 8.7a1 1 0 0 1 1.4-1.4l4 4a1 1 0 0 1 0 1.4l-4 4a1 1 0 0 1-1.4-1.4l3.29-3.3-3.3-3.3z"
              />
            </svg>
          </span>
        </button>
      </div>
    </form>
  </div>
</template>

<script>
export default {
  name: "Calculator",
  props: {
    pricing: Object
  },
  data() {
    return {
      value: 0,
      plan: {},
      multiplier: {
        code: "L",
        name: "quantity",
        display: "Quantity"
      },
      options: [
        {
          name: "laundry",
          inputs: [
            {
              name: "frequency",
              values: ["Weekly", "Bi-weekly", "Monthly"],
              display: "Frequency",
              code: "L",
              hasMultiplier: true
            }
          ]
        },
        {
          name: "home cleaning",
          inputs: [
            {
              name: "quantity",
              values: ["1", "2", "3", "4", "5+"],
              display: "Bedrooms (to estimate home size)",
              code: "R"
            },
            {
              name: "frequency",
              values: ["Weekly", "Bi-weekly", "Monthly"],
              display: "Frequency",
              code: "C"
            }
          ]
        },
        {
          name: "meals",
          inputs: [
            {
              name: "frequency",
              values: ["Daily", "Weekly", "Bi-weekly"],
              display: "Frequency",
              code: "M"
            }
          ]
        }
      ]
    };
  },
  computed: {
    computedValue() {
      return this.value.toLocaleString("en-US");
    }
  },
  methods: {
    calculateTotalFromPlan(plan) {
      /*
       * I've created a code system to easily retrieve the plans.
       * Each drop down selection carries a code as it's value.
       * When an option is selected, the code is inserted into the plan object.
       * In order to figure out what the amount is, I reduce the plan object
       * to a simple string like "CM" which stands for "Cleaning Monthly".
       * Then I find that code, "CM" within the pricing object and fetch the amount.
       */

      let result =
        Object.keys(plan).length == 0
          ? "No plan"
          : Object.keys(plan)
              .map(key => plan[key])
              .reduce((prev, curr) => prev + curr);

      let pricingKeys = Object.keys(this.pricing);
      this.value = 0;

      for (let i = 0; i < pricingKeys.length; i++) {
        let sortedElement = pricingKeys[i]
          .split("")
          .sort()
          .join("");
        let sortedResult = result
          .split("")
          .sort()
          .join("");
        if (sortedElement === sortedResult) {
          this.value = this.pricing[pricingKeys[i]].amount;
          return;
        }
      }
    },
    handleSelectChange(event, input) {
      if (!event.target.value) {
        event.preventDefault();
        return false;
      }
      let optionValue = "";

      optionValue = input.code + event.target.value[0];
      this.plan = {
        ...this.plan,
        [input.code]: optionValue
      };

      if (input.multiplier) {
        this.calculateTotalFromMultiplier(input.multiplier.value, input);
      } else if (this.multiplier) {
        this.calculateTotalFromMultiplier(this.multiplier.value, {
          code: this.multiplier.code
        });
      } else {
        this.calculateTotalFromPlan(this.plan);
      }
    },
    calculateTotalFromMultiplier(multiplier, input) {
      // This function is to accommodate the "Laundry Quantity" selection.
      // I've done my best to make it reusable by not making it coupled with the
      // "Laundry Quantity" input

      // It works by subtracting the base amount (if any) from the current amount,
      // Multiplying the subtracted value by the multiplier and then adding the
      // result back into the current amount.

      if (!parseInt(multiplier)) return;

      if (!this.pricing[this.plan[input.code]]) {
        this.handleSelectChange(
          {
            target: {
              value: input.values[0]
            }
          },
          input
        );

        input.value = input.values[0];
      }
      this.calculateTotalFromPlan(this.plan);
      let currentSelectionAmount = parseInt(
        this.pricing[this.plan[input.code]].amount
      );
      let amountWithoutCurrentSelection = this.value - currentSelectionAmount;
      let newCurrentSelectionAmount =
        currentSelectionAmount * parseInt(multiplier);
      let newValue = amountWithoutCurrentSelection + newCurrentSelectionAmount;
      this.value = newValue;
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1 {
  font-size: 1.6em;
}

.calculator {
  font-size: 12px;
}

.card {
  box-shadow: 0 15px 35px 0 rgba(42, 51, 83, 0.12),
    0 5px 15px rgba(0, 0, 0, 0.06);
  border-radius: 4px;
  background-color: #fff;
}

.card__header {
  display: inline-flex;
  justify-content: space-between;
  align-items: center;
  width: 100%;
  margin-bottom: 30px;
  padding: 1.4em;
}

.total {
  background-color: var(--secondary);
  color: var(--primary);
  font-size: 1.5em;
  font-weight: bold;
  padding: 0.5em;
  border-radius: 4px;
  font-family: monospace;
}

.form-control__name {
  text-transform: uppercase;
  color: var(--primary);
  font-weight: bold;
  font-size: 1em;
  margin-bottom: 20px;
  letter-spacing: 0.5px;
}

.grid-container {
  display: inline-grid;
  width: 100%;
  grid-gap: 20px 100px;
  padding: 1.4em;
}

.form-input__label {
  display: block;
  padding: 10px 0;
}

.form-input {
  border-radius: 4px;
  border: 1px solid var(--black);
  padding: 10px;
  display: block;
  width: 100%;
  box-sizing: border-box;
  margin-bottom: 20px;
  -webkit-appearance: none !important;
  -moz-appearance: none !important;
  appearance: none !important;
  border: 1px solid var(--light-grey);
}

.relative {
  position: relative;
}

.form-input__inset svg {
  width: 1rem;
}

.form-input__inset {
  right: 0;
  top: 0;
  bottom: 0;
  position: absolute;
  pointer-events: none;
  padding-left: 0.5rem;
  padding-right: 0.5rem;
  align-items: center;
  display: flex;
  color: var(--light-grey);
  background-color: var(--lighter-grey);
  border-radius: 0 4px 4px 0;
  border: 1px solid;
}

.card__footer {
  padding: 1.4em;
  text-align: right;
  box-shadow: 0px -15px 35px 0 rgba(42, 51, 83, 0.12),
    0px -6px 15px rgba(0, 0, 0, 0.06);
}

button {
  padding: 0.5em 0.8em;
  padding-left: 1.2em;
  border: 1px solid;
  background-color: var(--primary);
  color: #fff;
  font-size: 1rem;
  border-radius: 4px;
  font-weight: bold;
  letter-spacing: 0.5px;
  cursor: pointer;
  display: inline-flex;
  justify-content: space-between;
  align-items: center;
}

button svg {
  fill: #fff;
  width: 2rem;
  display: flex;
}

/* small screen */
@media (min-width: 640px) {
  .calculator {
    font-size: 16px;
  }

  .grid-container {
    grid-template-columns: repeat(2, 1fr);
  }
}

/* medium screen */
@media (min-width: 768px) {
  .calculator {
    font-size: 20px;
  }

  .grid-container {
    grid-template-columns: repeat(3, 1fr);
  }
}
</style>
