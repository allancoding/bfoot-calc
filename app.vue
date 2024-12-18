<template>
  <title>Board Foot Calculator</title>
  <nav class="navbar">
    <div class="logo-name">
      <img src="/logo/logo.png" alt="Logo" class="logo" />
      <span class="name">Board Foot Calculator</span>
    </div>
    <div class="navbar-right">
      <a href="#prices" @click="openPriceModal()" title="Change the prices of wood">Change Prices</a>
      <a href="#print" @click="openPrintModal()" title="Print the page">
        <Icon name="uil:print" class="icon" />
      </a>
      <a href="#settings" @click="openSettingsModal()" title="Settings">
        <Icon name="uil:setting" class="icon" />
      </a>
      <a href="https://github.com/allancoding/bfoot-calc" title="GitHub">
        <Icon name="uil:github" class="icon" />
      </a>
    </div>
  </nav>
  <div id="app">
    <div class="settings-row">
      <div class="left">
        <h3>{{ projects[selectedProject].name }}</h3>
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
        <label for="projectSelect">Project:</label>
        <select id="projectSelect" v-model="projects[selectedProject].name">
          <option
            v-for="(project, index) in projects"
            :key="index"
            :value="project.name"
          >
            {{ project.name }}
          </option>
        </select>
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
          <tr v-for="(row, index) in projects[selectedProject].rows" :key="index">
            <td class="delete-row">
              <Icon @click="projects[selectedProject].rows.splice(index, 1)" name="uil:trash" />
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
                  projects[selectedProject].rows
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
      <div class="scroll">
        <div
          v-for="(wood, index) in tempWoodtypes"
          :key="index"
          class="price-row"
        >
          <input
              type="text"
              class="list-input"
              v-model="wood.name"
            />
          <span class="input-padding">-</span>
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
            BF
          </div>
        </div>
      </div>
      <button @click="newWoodtype()">Add Wood Type</button>
      <button @click="closePriceModal(true)">Save Prices</button>
    </Modal>
    <Modal
      @close-modal="closeSettingsModal(false)"
      :class="[showSettingsModal ? 'showModal' : 'hideModal']"
    >
      <h2>Project Settings</h2>
      <p>You can import and export individual projects or just save all projects.</p>
      <div class="projects">
        <input type="text" v-model="projects[selectedProject].name" />
        <Icon @click="exportProject(projects[selectedProject])" name="uil:export" title="Export Project" class="icon"/>
        <Icon @click="importProject(projects[selectedProject])" name="uil:import" title="Import Project" class="icon"/>
        <Icon @click="projects[selectedProject].rows = []" name="uil:trash" title="Delete Project" class="icon"/>
      </div>
      <div class="new-project">
        <Icon @click="addProject()" name="uil:plus" title="Add Project" class="icon"/>
      </div>
      <button @click="closeSettingsModal();saveSettings()">Save Projects</button>
      <button @click="closeSettingsModal();loadSettings()">Load Projects</button>
    </Modal>
  </div>
</template>

<script>
import Modal from "./components/Modal.vue";
import "~/assets/app.css";
import "~/assets/scrollbar.css";

export default {
  components: {
    Modal,
  },
  data() {
    return {
      projects: [{
        name: "Default Project",
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
      }],
      woodtypes: [
        { name: "Red Oak", price: 4.15 },
        { name: "Cherry", price: 6.5 },
        { name: "African Mahogany", price: 7.95 },
        { name: "Rustic Cherry", price: 3.54 },
        { name: "Rustic Walnut", price: 7.29 },
        { name: "Ceder", price: 5.3 },
        { name: "Soft Maple", price: 4.98 },
        { name: "Hard Maple", price: 5.38 },
        { name: "Walnut FAS", price: 14.15 },
        { name: "Knotty Alder", price: 2.65 },
        { name: "Alder", price: 2.85 },
        { name: "Rustic Maple", price: 3.55 },
        { name: "Poplar", price: 3.65 },
        { name: "Pine", price: 3.2 },
        { name: "Sapele", price: 8.6 },
        { name: "White Oak", price: 8.72 },
        { name: "Plumb Sapele", price: 8.95 },
      ],
      selectedProject: 0,
      tempWoodtypes: [],
      showPriceModal: false,
      showSettingsModal: false,
      tableReady: false,
      defaultWood: "Alder",
    };
  },
  created() {
    this.projects[this.selectedProject].rows[0].pricePerBoardFoot = this.defaultWood;
  },
  watch: {
    projects: {
      handler(newRows) {
        this.onRowsChanged(newRows);
      },
      deep: true,
    },
  },
  methods: {
    async addRow() {
      this.projects[this.selectedProject].rows.push({
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
      this.projects[this.selectedProject].rows.forEach((row) => {
        row.boardFeet =
          (row.length * row.width * row.thickness * row.quantity) / 144;
        this.calculatePrice();
      });
    },
    calculatePrice() {
      this.projects[this.selectedProject].rows.forEach((row) => {
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
    openSettingsModal() {
      this.showSettingsModal = true;
    },
    closeSettingsModal() {
      this.showSettingsModal = false;
      if (window.location.hash === "#settings") {
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
      localStorage.setItem("project", JSON.stringify(newRows));
    },
    saveSettings() {
      const settings = {
        woodtypes: this.woodtypes,
        defaultWood: this.defaultWood,
        projects: this.projects,
      };
      const settingsBlob = new Blob([JSON.stringify(settings)], {
        type: "application/json",
      });
      const url = URL.createObjectURL(settingsBlob);
      const a = document.createElement("a");
      a.href = url;
      a.download = "Board Foot Projects.json";
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
          this.projects = settings.projects;
        };
        reader.readAsText(file);
      };
      input.click();
    },
    clear() {
      this.projects[this.selectedProject].rows = [
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
    newWoodtype() {
      this.tempWoodtypes.push({ name: "", price: 0 });
    },
    addProject() {
      this.projects.push({
        name: "New Project " + (this.projects.length + 1),
        rows: [
          {
            length: 0,
            width: 0,
            thickness: 0,
            quantity: 0,
            boardFeet: 0,
            pricePerBoardFoot: this.defaultWood,
            price: "$0.00",
          },
        ],
      });
      this.selectedProject = this.projects.length - 1;
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
    } else if (window.location.hash === "#settings") {
      this.tempWoodtypes = JSON.parse(JSON.stringify(this.woodtypes));
      this.showSettingsModal = true;
    }
    const savedDefaultWood = localStorage.getItem("defaultwood");
    if (savedDefaultWood) {
      this.defaultWood = savedDefaultWood;
    }
    const savedProjects = localStorage.getItem("project");
    if (savedProjects) {
      this.projects = JSON.parse(savedProjects);
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
