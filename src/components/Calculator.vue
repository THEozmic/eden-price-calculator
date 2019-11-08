<template>
  <div class="calculator">
    <!-- <div class="plan-name">{{this.planName}}</div> -->
    <form @submit.prevent class="card">
      <div class="card__header">
        <h1>Select &amp; Customize Services</h1>
        <div class="total">NGN {{computedTotal}}</div>
      </div>
      <div class="grid-container">
        <div v-for="option of options" :key="option.title" class="form-control">
          <div>
            <div class="form-control__name">{{option.title}}</div>

            <div>
              <!-- v-for="(input, index) in option.inputs" :key="index" -->
              <!-- <div>
                <label :for="multiplier.name" class="form-input__label">{{multiplier.display}}</label>
                <input
                  type="number"
                  :name="multiplier.name"
                  min="1"
                  class="form-input"
                  v-model="multiplier.value"
                  @keypress="(event) => calculateTotalFromMultiplier(String.fromCharCode(event.charCode), input)"
                />
              </div>-->
              <label :for="option.display" class="form-input__label">{{option.display}}</label>
              <div class="relative">
                <select
                  @change="(event) => {handleInputChange(event, option)}"
                  class="form-input"
                  required
                >
                  <optgroup label="Select an option">
                    <option value selected></option>
                    <option
                      v-for="(value, index) of option.inputs"
                      :key="index"
                      :value="index"
                    >{{value.title}}</option>
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

            <div v-if="option.subInputsType">
              <div v-if="option.type === 'number'">
                <label
                  :for="option.subInputDisplay"
                  class="form-input__label"
                >{{option.subInputDisplay}}</label>
                <input
                  type="number"
                  :name="option.subInputDisplay"
                  min="1"
                  class="form-input"
                  v-model="option.selectedSubInput.value"
                  @input="(event) => {handleSubInputChange(event, option)}"
                />
              </div>
              <div v-if="option.type === 'dropdown'">
                <label
                  :for="option.subInputDisplay"
                  class="form-input__label"
                >{{option.subInputDisplay}}</label>

                <div class="relative">
                  <select
                    @change="(event) => handleSubInputChange(event, option)"
                    class="form-input"
                    required
                    v-model="option.selectedSubInput.value"
                  >
                    <optgroup label="Select an option" v-if="option.selectedInput">
                      <option value selected></option>
                      <option
                        v-for="(subInput, index) of option.selectedInput.subInputs"
                        :key="index"
                        :value="index"
                      >{{subInput.title}}</option>
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
  data() {
    return {
      total: 0,
      options: [
        {
          title: "Laundry",
          type: "number",
          display: "Frequency",
          subInputDisplay: "Quantity",
          selectedInput: {},
          selectedSubInput: {},
          subInputsType: "multiplication",
          inputs: [
            {
              value: 35000,
              title: "Weekly"
            },
            {
              value: 23000,
              title: "Bi-weekly"
            },
            {
              value: 12967,
              title: "Monthly"
            }
          ]
        },
        {
          title: "Home Cleaning",
          type: "dropdown",
          subInputsType: "addition",
          display: "Frequency",
          subInputDisplay: "Bedrooms (to estimate home size)",
          selectedInput: {},
          selectedSubInput: {},
          inputs: [
            {
              value: 27000,
              title: "Weekly",
              subInputs: [
                { value: 0, updateWith: 0, title: "1" },
                { value: 1, updateWith: 0, title: "2" },
                { value: 2, updateWith: 4000, title: "3" },
                { value: 3, updateWith: 4000, title: "4" },
                { value: 4, updateWith: 8000, title: "5+" }
              ]
            },
            {
              value: 19000,
              title: "Bi-Weekly",
              subInputs: [
                { value: 0, updateWith: 0, title: "1" },
                { value: 1, updateWith: 0, title: "2" },
                { value: 2, updateWith: 2000, title: "3" },
                { value: 3, updateWith: 2000, title: "4" },
                { value: 4, updateWith: 4000, title: "5+" }
              ]
            },
            {
              value: 10967,
              title: "Monthly",
              subInputs: [
                { value: 0, updateWith: 0, title: "1" },
                { value: 1, updateWith: 0, title: "2" },
                { value: 2, updateWith: 1000, title: "3" },
                { value: 3, updateWith: 1000, title: "4" },
                { value: 4, updateWith: 2000, title: "5+" }
              ]
            }
          ]
        },
        {
          title: "Meals",
          type: "dropdown",
          display: "Frequency",
          selectedInput: {},
          inputs: [
            {
              value: 48200,
              title: "Weekly"
            },
            {
              value: 28234,
              title: "Bi-Weekly"
            },
            {
              value: 86000,
              title: "Daily"
            }
          ]
        }
      ]
    };
  },
  computed: {
    computedTotal() {
      return this.total.toLocaleString("en-US");
    }
  },
  methods: {
    handleInputChange(event, option) {
      option.selectedInput = option.inputs[event.target.value];
      if (!option.selectedInput) {
        option.value = 0;
        this.calculateTotal();
        return;
      }
      option.value = Number(option.selectedInput.value);

      if (option.subInputDisplay) {
        if (option.subInputsType === "addition") {
          option.selectedSubInput = option.selectedInput.subInputs[0];
          let subInputValue = option.selectedSubInput.updateWith;
          option.value += Number(subInputValue);
        }

        if (option.subInputsType === "multiplication") {
          if (!option.selectedSubInput.value) {
            option.selectedSubInput.value = 1;
          }
          option.value *= Number(option.selectedSubInput.value);
        }
      }

      this.calculateTotal();
    },
    handleSubInputChange(event, option) {
      if (option.subInputsType === "addition") {
        option.selectedSubInput =
          option.selectedInput.subInputs[Number(event.target.value)];
        option.value =
          Number(option.selectedSubInput.updateWith) +
          Number(option.selectedInput.value);
      }

      if (option.subInputsType === "multiplication") {
        option.value = option.selectedInput.value * event.target.value;
      }
      this.calculateTotal();
    },
    calculateTotal() {
      this.total = this.options.reduce((prev, curr) => ({
        value: (prev.value || 0) + (curr.value || 0)
      })).value;
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

.plan-name {
  font-weight: bold;
  color: rgba(23, 26, 25, 0.38);
  text-align: center;
  margin: 20px;
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
