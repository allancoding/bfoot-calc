<template>
  <title>Print - Board Foot Calculator</title>
  <header>
    <div class="right-left">
      <p class="left"> {{ username }} </p>
      <p class="right"> {{ topString }} </p>
    </div>
  </header>
  <main>
    <div class="project" v-if="printProject === 'all'" v-for="(project, index) in projects" :key="index">
      <div v-for="(chunk, chunkIndex) in getPaginatedRows(project.rows)" :key="chunkIndex">
        <h2 v-if="tableReady" style="text-align: center;">{{ project.name }}</h2>
        <div class="table-wrapper print-table">
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
              <tr v-for="(row, rowIndex) in chunk" :key="rowIndex">
                <td><p>{{ row.name }}</p></td>
                <td><p>{{ row.length }}</p></td>
                <td><p>{{ row.width }}</p></td>
                <td><p>{{ row.thickness }}</p></td>
                <td><p>{{ row.quantity }}</p></td>
                <td><p>{{ row.pricePerBoardFoot }}</p></td>
                <td><p>{{ row.price }}</p></td>
              </tr>
            </tbody>
            <tfoot v-if="tableReady && chunkIndex === getPaginatedRows(project.rows).length - 1">
              <tr>
                <td colspan="6"></td>
                <td>
                  <strong>
                    Total: ${{
                      project.rows
                        .reduce(
                          (acc, row) => acc + parseFloat(row.price.replace("$", "")),
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
        <p>Page {{ chunkIndex + 1 }} of {{ getPaginatedRows(project.rows).length }}</p>
        <div class="page-break" :class="{ 'no-page-break': chunkIndex === getPaginatedRows(project.rows).length - 1 }"> </div>
      </div>
      <div class="page-break" :class="{ 'no-page-break': index === projects.length - 1 }"> </div>
    </div>
    <div class="project" v-if="printProject !== 'all'">
      <div v-for="(chunk, chunkIndex) in getPaginatedRows(projects[printProject].rows)" :key="chunkIndex">
        <h2 v-if="tableReady" style="text-align: center;">{{ projects[printProject].name }}</h2>
        <div class="table-wrapper print-table">
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
              <tr v-for="(row, rowIndex) in chunk" :key="rowIndex">
                <td><p>{{ row.name }}</p></td>
                <td><p>{{ row.length }}</p></td>
                <td><p>{{ row.width }}</p></td>
                <td><p>{{ row.thickness }}</p></td>
                <td><p>{{ row.quantity }}</p></td>
                <td><p>{{ row.pricePerBoardFoot }}</p></td>
                <td><p>{{ row.price }}</p></td>
              </tr>
            </tbody>
            <tfoot v-if="tableReady && chunkIndex === getPaginatedRows(projects[printProject].rows).length - 1">
              <tr>
                <td colspan="6"></td>
                <td>
                  <strong>
                    Total: ${{
                      projects[printProject].rows
                        .reduce(
                          (acc, row) => acc + parseFloat(row.price.replace("$", "")),
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
        <p>Page {{ chunkIndex + 1 }} of {{ getPaginatedRows(projects[printProject].rows).length }}</p>
        <div class="page-break" :class="{ 'no-page-break': chunkIndex === getPaginatedRows(projects[printProject].rows).length - 1 }"> </div>
      </div>
      <div class="page-break" :class="{ 'no-page-break': index === projects.length - 1 }"> </div>
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
      rowsPerPage: 0,
    }
  },
  methods: {
    load() {
      this.username = localStorage.getItem('username');
      this.printProject = localStorage.getItem('printProject');
      this.printDate = localStorage.getItem('showDate') === 'true';
      this.projects = JSON.parse(localStorage.getItem('project'));
      this.rowsPerPage = parseInt(localStorage.getItem('rowsPerPage'));
      this.tableReady = true;
      if (this.printDate === true) {
        this.topString = new Date().toLocaleDateString();
      } else {
        this.topString = '';
      }
    },
    getPaginatedRows(rows) {
      const chunks = [];
      const rowsPerPage = Math.min(this.rowsPerPage, rows.length);
      for (let i = 0; i < rows.length; i += rowsPerPage) {
        chunks.push(rows.slice(i, i + rowsPerPage));
      }
      return chunks;
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