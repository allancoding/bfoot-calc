<template>
  <title>Board Foot Calculator</title>
  <nav class="navbar">
    <div class="logo-name">
      <img src="/logo/logo.png" alt="Logo" class="logo" />
      <span class="name">Board Foot Calculator</span>
    </div>
    <div class="navbar-right">
      <a href="#prices" @click="openPriceModal()" title="Change the prices of wood">Change Prices</a>
      <a href="#print" title="Print the page">
        <Icon name="uil:print" class="github-icon" />
      </a>
      <a href="https://github.com/allancoding/bfoot-calc" title="GitHub">
        <Icon name="uil:github" class="github-icon" />
      </a>
    </div>
  </nav>
  <div id="app">
    <div class="settings-row">
      <div class="left">
        <label for="projectName">Project Name:</label>
        <input
          type="text"
          class="styled-input"
          id="projectName"
          placeholder="Woods 1"
        />
      </div>
      <div class="right">
        <label for="defaultWood">Default Wood:</label>
        <select id="defaultWood" v-model="defaultWood" @change="changeDefaultWood()">
          <option
            v-for="(woodtype, index) in woodtypes"
            :key="index"
            :value="woodtype.name"
          >
            {{ woodtype.name }}
          </option>
        </select>
        <button @click="saveSettings">Save</button>
        <button @click="loadSettings">Load</button>
      </div>
    </div>
    <div class="table-wapper">
      <table>
        <thead>
          <tr>
            <th class="delete-row"></th>
            <th>Board Name</th>
            <th>Length</th>
            <th>Width</th>
            <th>Thickness</th>
            <th>Quantity</th>
            <th>Type of Wood</th>
            <th>Board Feet with Price</th>
          </tr>
        </thead>
        <tbody v-if="tableReady">
          <tr v-for="(row, index) in rows" :key="index">
            <td class="delete-row">
              <Icon @click="rows.splice(index, 1)" name="uil:trash" />
            </td>
            <td>
              <input
                type="text"
                class="num"
                placeholder="Name"
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
                  :value="woodtype.name"
                >
                  {{ woodtype.name }}
                </option>
              </select>
            </td>
            <td>
              <input type="text" v-model="row.price" disabled />
            </td>
          </tr>
        </tbody>
        <tfoot v-if="tableReady">
          <tr>
            <td colspan="7"></td>
            <td>
              <strong>
                Total: ${{
                  rows
                    .reduce(
                      (acc, row) =>
                        acc + parseFloat(row.price.replace("$", "")),
                      0
                    )
                    .toFixed(2)
                }}
              </strong>
            </td>
          </tr>
        </tfoot>
      </table>
      <div style="text-align: center" v-if="!tableReady">
        <p>Loading...</p>
      </div>
    </div>
    <div class="buttons">
      <button @click="addRow">Add Row</button>
      <button @click="clear">Clear</button>
    </div>
    <Modal
      @close-modal="closePriceModal(false)"
      :class="[showPriceModal ? 'showModal' : 'hideModal']"
    >
      <h2>Change Prices</h2>
      <div
        v-for="(wood, index) in tempWoodtypes"
        :key="index"
        class="price-row"
      >
        <span>{{ wood.name }}</span>
        <div class="input-wrapper">
          <span class="dollar-symbol">$</span>
          <input
            min="0.01"
            step="0.01"
            type="number"
            v-model.number="wood.price"
            @blur="formatCurrency(index)"
            @input="formatOnStep(index, $event)"
          />
        </div>
      </div>
      <button @click="closePriceModal(true)">Save Prices</button>
    </Modal>
  </div>
</template>

<script>
import Modal from "./components/Modal.vue";
import "~/assets/app.css";

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
          pricePerBoardFoot: "",
          price: "$0.00",
        },
      ],
      woodtypes: [
        { name: "Pine", price: 2.5 },
        { name: "Oak", price: 3.5 },
        { name: "Maple", price: 4.5 },
        { name: "Cherry", price: 5.5 },
        { name: "Walnut", price: 6.5 },
      ],
      tempWoodtypes: [],
      showPriceModal: false,
      tableReady: false,
      defaultWood: "Oak",
    };
  },
  created() {
    this.rows[0].pricePerBoardFoot = this.defaultWood;
  },
  watch: {
    rows: {
      handler(newRows) {
        this.onRowsChanged(newRows);
      },
      deep: true,
    },
  },
  methods: {
    async addRow() {
      this.rows.push({
        length: 0,
        width: 0,
        thickness: 0,
        quantity: 0,
        boardFeet: 0,
        pricePerBoardFoot: this.defaultWood,
        price: "$0.00",
      });
    },
    changeDefaultWood() {
      localStorage.setItem("defaultwood", this.defaultWood);
    },
    calculateBoardFeet() {
      this.rows.forEach((row) => {
        row.boardFeet =
          (row.length * row.width * row.thickness * row.quantity) / 144;
        this.calculatePrice();
      });
    },
    calculatePrice() {
      this.rows.forEach((row) => {
        if (row.pricePerBoardFoot) {
          row.price =
            row.boardFeet *
            this.woodtypes.find((wood) => wood.name === row.pricePerBoardFoot)
              .price;
          row.price = "$" + row.price.toFixed(2);
        } else {
          row.price = "$0.00";
        }
      });
    },
    openPriceModal() {
      this.tempWoodtypes = JSON.parse(JSON.stringify(this.woodtypes));
      this.showPriceModal = true;
    },
    closePriceModal(save) {
      if (save) {
        this.savePrices();
      }
      this.showPriceModal = false;
      if (window.location.hash === "#prices") {
        history.replaceState(null, null, " ");
      }
    },
    savePrices() {
      this.woodtypes = JSON.parse(JSON.stringify(this.tempWoodtypes));
      localStorage.setItem("woodtypes", JSON.stringify(this.woodtypes));
      this.showModal = false;
      this.calculatePrice();
    },
    onRowsChanged(newRows) {
      localStorage.setItem("rows", JSON.stringify(newRows));
    },
    saveSettings() {
      const settings = {
        woodtypes: this.woodtypes,
        defaultWood: this.defaultWood,
        rows: this.rows,
      };
      const settingsBlob = new Blob([JSON.stringify(settings)], {
        type: "application/json",
      });
      const url = URL.createObjectURL(settingsBlob);
      const a = document.createElement("a");
      a.href = url;
      a.download = "Board Foot Settings.json";
      a.click();
      URL.revokeObjectURL(url);
    },
    loadSettings() {
      const input = document.createElement("input");
      input.type = "file";
      input.accept = "application/json";
      input.onchange = (event) => {
        const file = event.target.files[0];
        const reader = new FileReader();
        reader.onload = (event) => {
          const settings = JSON.parse(event.target.result);
          this.woodtypes = settings.woodtypes;
          this.defaultWood = settings.defaultWood;
          this.rows = settings.rows;
        };
        reader.readAsText(file);
      };
      input.click();
    },
    clear() {
      this.rows = [
        {
          length: 0,
          width: 0,
          thickness: 0,
          quantity: 0,
          boardFeet: 0,
          pricePerBoardFoot: this.defaultWood,
          price: "$0.00",
        },
      ];
    },
    formatCurrency(index) {
      const wood = this.tempWoodtypes[index];
      wood.price = parseFloat(wood.price || 0).toFixed(2);
    },
    formatOnStep(index, event) {
      const wood = this.tempWoodtypes[index];
      if (document.activeElement !== event.target) {
        this.formatCurrency(index);
      }
    },
  },
  mounted() {
    const savedWoodtypes = localStorage.getItem("woodtypes");
    if (savedWoodtypes) {
      this.woodtypes = JSON.parse(savedWoodtypes);
    }
    if (window.location.hash === "#prices") {
      this.tempWoodtypes = JSON.parse(JSON.stringify(this.woodtypes));
      this.showPriceModal = true;
    }
    const savedDefaultWood = localStorage.getItem("defaultwood");
    if (savedDefaultWood) {
      this.defaultWood = savedDefaultWood;
    }
    const savedRows = localStorage.getItem("rows");
    if (savedRows) {
      this.rows = JSON.parse(savedRows);
    }
    this.calculatePrice();
    this.tableReady = true;
    document.addEventListener("keydown", (event) => {
      if (
        (event.key === "Enter" || event.key === "Tab") &&
        event.target.nodeName === "INPUT"
      ) {
        var inputs = Array.from(document.querySelectorAll("td .num"));
        var index = inputs.indexOf(event.target);
        if (index === inputs.length - 1) {
          this.addRow().then(() => {
            inputs = Array.from(document.querySelectorAll("td .num"));
            index = inputs.indexOf(event.target);
            inputs[index + 1].focus();
          });
        } else if (index > -1 && index < inputs.length - 1) {
          if (inputs[index + 1].value === "0") {
            inputs[index + 1].select();
          }
          inputs[index + 1].focus();
        }
        event.preventDefault();
      }
    });
  },
};
</script>
