<template>
  <div class="hello">
    <div class="row">
      <div class="col-md-6">
        <table class="table table-bordered">
          <tr>
              <th colspan="3"><h4>Resources : </h4></th>
          </tr>
          <tr>
              <th><div class="text-center">A</div></th>
              <th><div class="text-center">B</div></th>
              <th><div class="text-center">C</div></th>
          </tr>
          <tr>
              <td><div class="text-center">10</div></td>
              <td><div class="text-center">5</div></td>
              <td><div class="text-center">7</div></td>
          </tr>
        </table>
      </div>
      <div class="col-md-6">
        <h3 class="mb-4">Request Process {{ val_a }} {{ val_b }} {{ val_c }}</h3>
        <form @submit.prevent="requestProcess">
          <div class="row">
            <div class="col-md-3  d-flex">
              <select class="custom-select" name="request_index">
                <option selected>Process</option>
                <option v-for="(i, index) in allocation" :key="i.key" :value="index">
                  {{ index.toUpperCase() }}
                </option>
              </select>
            </div>
            <div class="col-md-3">
              <input type="number" max="9" class="form-control" placeholder="A" name="val_a" required>
            </div>
            <div class="col-md-3">
              <input type="number" max="9" class="form-control" placeholder="B" name="val_b" required>
            </div>
            <div class="col-md-3">
              <input type="number" max="9" class="form-control" placeholder="C" name="val_c" required>
            </div>
          </div>
          <button class="btn btn-success w-100 mt-3" type="submit">Add Available Resources</button>
        </form>

        
        
      </div>
    </div>

    <div class="row mx-0">
      <table class="table table-bordered col-md-4">
          <tr align="center">
            <th scope="col" rowspan="2">Process</th>
            <th scope="col" colspan="3">Allocation</th>
            <!-- <th scope="col" rowspan="7"></th>
            <th scope="col" colspan="3">Max</th>
            <th scope="col" rowspan="7"></th>
            <th scope="col" colspan="3">Available</th>
            <th scope="col" rowspan="7"></th>
            <th scope="col" colspan="3" v-on:click="submitNeed">Need</th>
            <th scope="col" rowspan="2">Status</th> -->
          </tr>

          <tr align="center">
            <th v-for="i in column_process" :key="i">{{ i }}</th>
            <!-- <th v-for="i in column_process" :key="i">{{ i }}</th>
            <th v-for="i in column_process" :key="i">{{ i }}</th>
            <th v-for="i in column_process" :key="i">{{ i }}</th> -->
          </tr>

          <tr v-for="(i, index) in allocation" :key="i.key" align="center">
            <td>{{ index.toUpperCase() }}</td>
            <td>{{ i.a }}</td>
            <td>{{ i.b }}</td>
            <td>{{ i.c }}</td>
          </tr>
      </table>

      <table class="table table-bordered col-md-2">
          <tr align="center">
            <th scope="col" colspan="3">Max</th>
          </tr>
          
          <tr align="center">
            <th v-for="i in column_process" :key="i">{{ i }}</th>
          </tr>

          <tr v-for="i in max" :key="i" align="center">
            <td>{{ i.a }}</td>
            <td>{{ i.b }}</td>
            <td>{{ i.c }}</td>
          </tr>

      </table>

      <table class="table table-bordered col-md-2">
          <tr align="center">
            <th scope="col" colspan="3">Available</th>
          </tr>
          
          <tr align="center">
            <th v-for="i in column_process" :key="i">{{ i }}</th>
          </tr>

          <tr v-for="i in available" :key="i" align="center">
            <th>{{ i.a }}</th>
            <th>{{ i.b }}</th>
            <th>{{ i.c }}</th>
          </tr>
      </table>

      <button v-if="!start_simulator" @click="showNeed" class="btn btn-primary col-md-4 mb-3">
        <h2>Mulai Simulasi</h2>
      </button>

      <table v-else class="table table-bordered col-md-4" v-on:click="submitNeed">
          <tr align="center">
            <th scope="col" colspan="3">Need</th>
            <th scope="col" rowspan="2">Status</th>
            <th scope="col" rowspan="7" class="col-btn-start">
              <button class="btn btn-primary btn-start">
                <span>Start Process</span>
              </button>
            </th>
          </tr>
          
          <tr align="center">
            <th v-for="i in column_process" :key="i">{{ i }}</th>
          </tr>

          <tr v-for="i in need" :key="i" align="center">
            
            <th>
              <span v-if="needsubmit"> {{ i.a }} </span>
              <span v-else> - </span>
            </th>
            <th>
              <span v-if="needsubmit"> {{ i.b }} </span>
              <span v-else> - </span>
            </th>
            <th>
              <span v-if="needsubmit"> {{ i.c }} </span>
              <span v-else> - </span>
            </th>

            <th>
              <span v-if="!needsubmit"> - </span>
              <div v-else>
                <span v-if="status.i" class="text-success">{{ success }}</span>
                <span v-else class="text-danger">{{ failed }}</span>
              </div>
            </th>
          </tr>
      </table>

    </div>

    <div v-if="needsubmit">
        <h2>Proses berjalan</h2>
        <div class="row">
            <div class="col-md-2">
                <h3>P0</h3>
                <h5>
                    <span v-for="i in need.p0" :key="i">{{ i }}</span>
                    &lt;
                    <span v-for="i in available.p0" :key="i">{{ i }}</span> 
                    <span class="text-danger"> {{ failed }}</span>                
                </h5>
            </div>
            <div class="col-md-2">
                <h3>P1</h3>

                <h5>
                    <span v-for="i in need.p1" :key="i">{{ i }}</span>
                    &lt;
                    <span v-for="i in available.p1" :key="i">{{ i }}</span> 
                    <span class="text-danger"> {{ failed }}</span>                
                </h5>
            </div>
            <div class="col-md-2">
                <h3>P2</h3>

                <h5>
                    <span v-for="i in need.p2" :key="i">{{ i }}</span>
                    &lt;
                    <span v-for="i in available.p2" :key="i">{{ i }}</span> 
                    <span class="text-danger"> {{ failed }}</span>                
                </h5>
            </div>
            <div class="col-md-2">
                <h3>P3</h3>

                <h5>
                    <span v-for="i in need.p3" :key="i">{{ i }}</span>
                    &lt;
                    <span v-for="i in available.p3" :key="i">{{ i }}</span> 
                    <span class="text-danger"> {{ failed }}</span>                
                </h5>
            </div>
            <div class="col-md-2">
                <h3>P4</h3>

                <h5>
                    <span v-for="i in need.p4" :key="i">{{ i }}</span>
                    &lt;
                    <span v-for="i in available.p4" :key="i">{{ i }}</span> 
                    <span class="text-danger"> {{ failed }}</span>                
                </h5>
            </div>
            <div class="col-md-2">
                <h3>P0</h3>

                <h5>
                    <span v-for="i in need.p0" :key="i">{{ i }}</span>
                    &lt;
                    <span v-for="i in available.p0" :key="i">{{ i }}</span> 
                    <span class="text-danger"> {{ failed }}</span>                
                </h5>
            </div>
        </div>
    </div>
    
  </div>
</template>

<script>
export default {
  name: 'TableDeadlock',
  methods: {
    submitNeed() {
      this.needsubmit = true
    },
    showNeed() {
      this.start_simulator = true
    },
    requestProcess() {

    }
  },
  data() {
    return {
      request_index: '',
      val_a: 0,
      val_b: 0,
      val_c: 0,
      success: "✓",
      failed: "✘",
      needsubmit: false,
      start_simulator: false,
      column_process: ["A", "B", "C"],
      request: {
        p0: {
          a: "", b: "", c: ""
        },
        p1: {
          a: "", b: "", c: ""
        },
        p2: {
          a: "", b: "", c: ""
        },
        p3: {
          a: "", b: "", c: ""
        },
        p4: {
          a: "", b: "", c: ""
        },
      },
      allocation: {
        p0: {
          a: "0", b: "1", c: "0"
        },
        p1: {
          a: "2", b: "0", c: "0"
        },
        p2: {
          a: "3", b: "0", c: "2"
        },
        p3: {
          a: "2", b: "1", c: "1"
        },
        p4: {
          a: "0", b: "0", c: "2"
        },
      },
      max: {
        p0: {
          a: "7", b: "5", c: "3"
        },
        p1: {
          a: "3", b: "2", c: "2"
        },
        p2: {
          a: "9", b: "0", c: "2"
        },
        p3: {
          a: "2", b: "2", c: "2"
        },
        p4: {
          a: "4", b: "3", c: "3"
        },
      },
      available: {
        p0: {
          a: "3", b: "3", c: "2"
        },
        p1: {
          a: "0", b: "0", c: "0"
        },
        p2: {
          a: "0", b: "0", c: "0"
        },
        p3: {
          a: "0", b: "0", c: "0"
        },
        p4: {
          a: "0", b: "0", c: "0"
        },
      },
      need: {
        p0: {
          a: "7", b: "4", c: "3"
        },
        p1: {
          a: "1", b: "2", c: "2"
        },
        p2: {
          a: "6", b: "0", c: "0"
        },
        p3: {
          a: "0", b: "1", c: "1"
        },
        p4: {
          a: "4", b: "3", c: "1"
        },
      },
      status: [
          false, false, false, false, true
      ]
    }
  }
}
</script>

<style scoped>
  .col-btn-start {
    padding: 0;
    margin: 0;
    width: 5px;
  }
  
  .btn-start {    
    width: 45px;
    text-transform: uppercase;
  }

  .btn-start span {
    writing-mode: vertical-rl;
    height: 320px;
    width: 20px;
  }
</style>