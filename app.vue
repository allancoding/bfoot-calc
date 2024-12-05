<template>
  <title>Board Foot Calculator</title>
  <nav class="navbar">
    <div class="logo-name">
      <img src="/logo/logo.png" alt="Logo" class="logo">
      <span class="name">Board Foot Calculator</span>
    </div>
    <div class="navbar-right">
      <a href="#prices" @click="openPriceModal()">Change Prices</a>
      <a href="https://github.com/allancoding/bfoot-calc">
        <Icon name="uil:github" class="github-icon" />
      </a>
    </div>
  </nav>
  <div id="app">
    <div class="table-wapper table-responsive">
      <table>
        <thead>
          <tr>
            <th>Board Name</th>
            <th>Length</th>
            <th>Width</th>
            <th>Thickness</th>
            <th>Quantity</th>
            <th>Type of Wood</th>
            <th>Board Feet with Price</th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="(row, index) in rows"
            :key="index"
          >
            <td>
              <input
                type="text"
                class="num"
                v-model="row.name"
              />
            </td>
            <td>
              <input
                type="number"
                class="num"
                v-model="row.length"
                @input="calculateBoardFeet"
              />
            </td>
            <td>
              <input
                type="number"
                class="num"
                v-model="row.width"
                @input="calculateBoardFeet"
              />
            </td>
            <td>
              <input
                type="number"
                class="num"
                v-model="row.thickness"
                @input="calculateBoardFeet"
              />
            </td>
            <td>
              <input
                type="number"
                class="num"
                v-model="row.quantity"
                @input="calculateBoardFeet"
              />
            </td>
            <td>
              <select v-model="row.pricePerBoardFoot" @change="calculatePrice">
                <option
                  v-for="(woodtype, index) in woodtypes"
                  :key="index"
                  :value="woodtype.price"
                >
                  {{ woodtype.name }}
                </option>
              </select>
            </td>
            <td>
              <input
                type="text"
                v-model="row.boardFeet"
                disabled
              />
            </td>
          </tr>
        </tbody>
      </table>
    </div>
    <button @click="addRow">Add Row</button>
    <Modal @close-modal="closePriceModal(false)" :class="[showPriceModal ? 'showModal': 'hideModal']">
      <h2>Change Prices</h2>
      <div v-for="(wood, index) in tempWoodtypes" :key="index" class="price-row">
        <span>{{ wood.name }}</span>
        <input type="number" v-model.number="wood.price" />
      </div>
      <button @click="closePriceModal(true)">Save Prices</button>
    </Modal>
  </div>
</template>

<script>
import Modal from './components/Modal.vue';

export default {
  components: {
    Modal,
  },
  data() {
    return {
      rows: [
        {
          length: 0,
          width: 0,
          thickness: 0,
          quantity: 0,
          boardFeet: 0,
          pricePerBoardFoot: 0,
          price: 0,
        },
      ],
      woodtypes: [
        { name: 'Pine', price: 2.5 },
        { name: 'Oak', price: 3.5 },
        { name: 'Maple', price: 4.5 },
        { name: 'Cherry', price: 5.5 },
        { name: 'Walnut', price: 6.5 },
      ],
      tempWoodtypes: [],
      showPriceModal: false,
    };
  },
  methods: {
    async addRow() {
      this.rows.push({
        length: 0,
        width: 0,
        thickness: 0,
        quantity: 0,
        boardFeet: 0,
      });
    },
    calculateBoardFeet() {
      this.rows.forEach((row) => {
        row.boardFeet = row.length * row.width * row.thickness * row.quantity / 144;
        this.calculatePrice();
      });
    },
      calculatePrice() {
      this.rows.forEach((row) => {
        row.price = row.boardFeet * row.pricePerBoardFoot;
      });
    },
    openPriceModal() {
      this.tempWoodtypes = JSON.parse(JSON.stringify(this.woodtypes));
      this.showPriceModal = true;
    },
    closePriceModal(save) {
      console.log('Wood types saved:', this.woodtypes);
      if (save) {
        this.savePrices();
      }
      this.showPriceModal = false;
      if (window.location.hash === '#prices') {
        history.replaceState(null, null, ' ');
      }
    },
    savePrices() {
      this.woodtypes = JSON.parse(JSON.stringify(this.tempWoodtypes));
      console.log('Wood types saved:', this.woodtypes);
      localStorage.setItem('woodtypes', JSON.stringify(this.woodtypes));
      this.showModal = false;
    }
  },
  mounted() {
    const savedWoodtypes = localStorage.getItem('woodtypes');
    if (savedWoodtypes) {
      this.woodtypes = JSON.parse(savedWoodtypes);
    }
    if (window.location.hash === '#prices') {
      this.tempWoodtypes = JSON.parse(JSON.stringify(this.woodtypes));
      this.showPriceModal = true;
    }
    document.addEventListener("keydown", (event) => {
      if ((event.key === "Enter" || event.key === "Tab") && event.target.nodeName === "INPUT") {
        var inputs = Array.from(document.querySelectorAll('td .num'));
        var index = inputs.indexOf(event.target);
        if (index === inputs.length - 1) {
          this.addRow().then(() => {
            inputs = Array.from(document.querySelectorAll('td .num'));
            index = inputs.indexOf(event.target);
            inputs[index + 1].focus();
          });
        } else if (index > -1 && index < inputs.length - 1) {
          if (inputs[index + 1].value === '0') {
            inputs[index + 1].select();
          }
          inputs[index + 1].focus();
        }
        event.preventDefault();
      }
    });
  }
};
</script>

<style>
body, html {
  margin: 0;
  padding: 0;
  background-color: #000;
}

.navbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px;
  background-color: #000;
  color: #fff;
  box-shadow: 0 0 30px rgba(255, 255, 255, 0.5);
}

.navbar-right {
  display: flex;
  align-items: center;
  padding: auto;
}

.navbar-right a {
  color: white;
  text-decoration: none;
  margin-right: 10px;
}

.navbar-right a:hover {
  color: #ccc;
}

.github-icon {
  width: 24px;
  height: 24px;
}

.logo-name {
  display: flex;
  align-items: center;
}

.logo {
  width: 40px;
  height: 40px;
  margin-right: 10px;
}

.name {
  font-size: 1.5em;
}

#app {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px;
}

.table-wapper {
  overflow: hidden;
  border-radius: 10px;
  border: 2px solid white;
  width: 100%;
}

table {
  width: 100%;
  color: white;
  border-collapse: collapse;
}

.table-responsive {
  width: 100%;
  overflow-x: auto;
}

.table-responsive table {
  min-width: 600px;
}

th, td {
  border-right: 2px solid white;
  text-align: center;
}

thead tr {
  border-bottom: 2px solid white;
}

th:last-child, td:last-child {
  border-right: none;
}

input, select {
  padding: 5px;
  margin: 5px;
  border: none;
  background-color: #444;
  color: white;
  border-radius: 5px;
  width: max-content;
}

input[type='number']::-webkit-inner-spin-button, 
input[type='number']::-webkit-outer-spin-button { 
  opacity: 0;
  margin-left: -14px;
  transition: opacity 0.5s;
}

input[type='number']::-webkit-inner-spin-button:hover, 
input[type='number']::-webkit-outer-spin-button:hover { 
  opacity: 0.75;
  margin-left: -14px;
}

.num {
  text-align: center;
}

.price-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 7px;
  margin-left: 15px;
  margin-right: 15px;
}

.price-row span {
  color: white;
  font-size: 1.2em;
}

.price-row input {
  width: min-content;
  padding: 5px;
  border: none;
  background-color: #444;
  color: white;
  border-radius: 5px;
}

button {
  background-color: #784220;
  border: none;
  color: white;
  padding: 8px 16px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  margin: 8px 4px;
  cursor: pointer;
  border-radius: 4px;
}

button:hover {
  background-color: #643318;
}
</style>