<template>
  <title>Board Foot Calculator</title>
  <nav class="navbar">
    <div class="logo-name">
      <img src="/logo/logo.png" alt="Logo" class="logo">
      <span class="name">Board Foot Calculator</span>
    </div>
    <a href="https://github.com/allancoding/bfoot-calc" class="github-link">
      <Icon name="uil:github" style="color: white" class="github-icon" />
    </a>
  </nav>
  <div id="app">
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
              v-model="row.name"
            />
          </td>
          <td>
            <input
              type="number"
              v-model="row.length"
              @input="calculateBoardFeet"
            />
          </td>
          <td>
            <input
              type="number"
              v-model="row.width"
              @input="calculateBoardFeet"
            />
          </td>
          <td>
            <input
              type="number"
              v-model="row.thickness"
              @input="calculateBoardFeet"
            />
          </td>
          <td>
            <input
              type="number"
              v-model="row.quantity"
              @input="calculateBoardFeet"
            />
          </td>
          <td>
            <select>
              <option
                v-for="(woodtype, index) in woodtypes"
                :key="index"
                :value="woodtype.price"
              >
                {{ woodtype.name }}
              </option>
            </select>

          </td>
        </tr>
      </tbody>
    </table>
    <button @click="addRow">Add Row</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      rows: [
        {
          length: 0,
          width: 0,
          thickness: 0,
          quantity: 0,
          boardFeet: 0,
        },
      ],
      woodtypes: [
        { name: 'Pine', price: 2.5 },
        { name: 'Oak', price: 3.5 },
        { name: 'Maple', price: 4.5 },
        { name: 'Cherry', price: 5.5 },
        { name: 'Walnut', price: 6.5 },
      ],
    };
  },
  methods: {
    addRow() {
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
      });
    },
  },
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

.github-link {
  display: flex;
  align-items: center;
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

/* add css for table color white*/
table {
  width: 100%;
  border-collapse: collapse;
  color: white;
}

</style>