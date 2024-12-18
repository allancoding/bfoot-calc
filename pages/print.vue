<template>
  <title>Print - Board Foot Calculator</title>
  <header>
    <div class="right-left">
      <p class="left"> {{ username }} </p>
      <p class="right"> {{ topString }} </p>
    </div>
  </header>
  <main>
    <div class="table-wapper print-table">
      <table>
        <thead>
          <tr>
            <th>Name</th>
            <th>Length</th>
            <th>Width</th>
            <th>Thickness</th>
            <th>Quantity</th>
            <th>Wood</th>
            <th>Price</th>
          </tr>
        </thead>
        <tbody v-if="tableReady">
          <tr v-for="(row, index) in projects[0].rows" :key="index">
            <td>
              <p>{{ row.name }}</p>
            </td>
            <td>
              <p>{{ row.length }}</p>
            </td>
            <td>
              <p>{{ row.width }}</p>
            </td>
            <td>
              <p>{{ row.thickness }}</p>
            </td>
            <td>
              <p>{{ row.quantity }}</p>
            </td>
            <td>
              <p>{{ row.pricePerBoardFoot }}</p>
            </td>
            <td>
              <p>{{ row.price }}</p>
            </td>
          </tr>
        </tbody>
        <tfoot v-if="tableReady">
          <tr>
            <td colspan="6"></td>
            <td>
              <strong>
                Total: ${{
                  projects[0].rows
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
    </div>
  </main>
</template>
<style>
body,
html {
  margin: 0;
  padding: 0;
  background-color: #fff;
  color: #000;
}
</style>
<script>
import "~/assets/print.css"

export default {
  data() {
    return {
      username: 'Name',
      printProject: 'all',
      printDate: false,
      topString: '',
      projects: [],
      tableReady: false,
    }
  },
  methods: {
    load() {
      this.username = localStorage.getItem('username');
      this.printProject = localStorage.getItem('printProject');
      this.printDate = localStorage.getItem('showDate') === 'true';
      this.projects = JSON.parse(localStorage.getItem('project'));
      this.tableReady = true;
      if (this.printDate === true) {
        if (this.printProject === 'all') {
          this.topString = new Date().toLocaleDateString();
        } else {
          this.topString = this.projects[parseInt(this.printProject)].name + " - " + new Date().toLocaleDateString();
        }
      } else {
        this.topString = '';
      }
    },
    printPage() {
      setTimeout(() => {
        window.print();
      }, 100);
    }
  },
  mounted() {
    window.addEventListener('message', (event) => {
      if (event.origin !== window.location.origin) return;
      const message = event.data;
      if (message === 'load') {
        this.load();
      }
      if (message === 'print') {
        this.load();
        this.printPage();
      }
    });
  },
}
</script>