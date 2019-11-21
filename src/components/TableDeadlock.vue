<template>
  <div class="hello">
    <div class="row">
      <div class="col-md-6">
        <table class="table table-bordered">
          <tr>
            <th colspan="3">
              <h4>Resources :</h4>
            </th>
          </tr>
          <tr>
            <th>
              <div class="text-center">A</div>
            </th>
            <th>
              <div class="text-center">B</div>
            </th>
            <th>
              <div class="text-center">C</div>
            </th>
          </tr>
          <tr>
            <td>
              <div class="text-center">10</div>
            </td>
            <td>
              <div class="text-center">5</div>
            </td>
            <td>
              <div class="text-center">7</div>
            </td>
          </tr>
        </table>
      </div>
      <div class="col-md-6">
        <h3 class="mb-4">Request Process</h3>
        <form @submit.prevent="requestProcess">
          <div class="row">
            <div class="col-md-3 d-flex">
              <select class="custom-select" v-model="request_index">
                <option selected>Process</option>
                <option
                  v-for="(i, index) in allocation"
                  :key="i.key"
                  :value="index">
                    P{{ index }}
                </option>
              </select>
            </div>
            <div class="col-md-3">
              <input
                type="number"
                max="9"
                class="form-control"
                placeholder="A"
                v-model="val_request.a"
                required
              />
            </div>
            <div class="col-md-3">
              <input
                type="number"
                max="9"
                class="form-control"
                placeholder="B"
                v-model="val_request.b"
                required
              />
            </div>
            <div class="col-md-3">
              <input
                type="number"
                max="9"
                class="form-control"
                placeholder="C"
                v-model="val_request.c"
                required
              />
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
        </tr>

        <tr align="center">
          <th v-for="i in column_process" :key="i">{{ i }}</th>
        </tr>

        <tr v-for="(i, index) in allocation" :key="i.key" align="center">
          <td>P{{ index }}</td>
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

      <table v-else class="table table-bordered col-md-4" @click="statusShow">
        <tr align="center">
          <th scope="col" colspan="3">Need</th>
          <th scope="col" rowspan="2">Status</th>
          <th scope="col" rowspan="7" class="col-btn-start">
            <button class="btn btn-info btn-start" @click="reStart()">
              <span class="btn-refresh">Refresh</span>
            </button>

            <button v-if="!start_process" class="btn btn-primary btn-start" @click="startProcess()">
              <span>Start Process</span>
            </button>

            <button v-else class="btn btn-primary btn-start" @click="startProcess()">
              <span>Next Process</span>
            </button>
          </th>
        </tr>

        <tr align="center">
          <th v-for="i in column_process" :key="i">{{ i }}</th>
        </tr>

        <tr v-for="(i, index) in need" :key="i" align="center">
          <th>{{ i.a }}</th>
          <th>{{ i.b }}</th>
          <th>{{ i.c }}</th>

          <th>
            <span v-if="status[index] == success" class="text-success">{{ status[index] }}</span>
            <span v-else class="text-danger">{{ status[index] }}</span>
          </th>
        </tr>
      </table>
    </div>

    <!-- <div v-if="start_process">
      <h2>Proses berjalan</h2>
      <div class="row">
        <div class="col-md-2">
          <h3>P0</h3>
        </div>
        <div class="col-md-2">
          <h3>P1</h3>
        </div>
        <div class="col-md-2">
          <h3>P2</h3>
        </div>
        <div class="col-md-2">
          <h3>P3</h3>
        </div>
        <div class="col-md-2">
          <h3>P4</h3>
        </div>
      </div>
    </div> -->
  </div>
</template>

<script>
export default {
  name: 'TableDeadlock',
  watch: {
    
  },
  methods: {
    checkSuccess() {
      const values = Object.values(this.status).every(n => { 
        return n === "✓" 
      })

      if (values == true) {
        this.$swal({
          icon: 'success',
          title: 'Success...',
          text: 'Aman, proses telah selesai.',
        })
        this.reStart()
      }
      // else {
      //   this.$swal({
      //     icon: 'failed',
      //     title: 'Oops...',
      //     text: 'Proses mengalami Deadlock',
      //   })
      // }
    },
    showSuccess() {
      this.$swal({
        icon: 'success',
        title: 'Success...',
        text: 'Aman, proses telah selesai.',
      })
    },
    reStart() {
      this.allocation =  {
        0: { a: 0, b: 1, c: 0 },
        1: { a: 2, b: 0, c: 0 },
        2: { a: 3, b: 0, c: 2 },
        3: { a: 2, b: 1, c: 1 },
        4: { a: 0, b: 0, c: 2 },
      }
      this.available = {
        0: { a: 3, b: 3, c: 2 },
        1: { a: 0, b: 0, c: 0 },
        2: { a: 0, b: 0, c: 0 },
        3: { a: 0, b: 0, c: 0 },
        4: { a: 0, b: 0, c: 0 },
      }
      this.need = {
        0: { a: 7, b: 4, c: 3 },
        1: { a: 1, b: 2, c: 2 },
        2: { a: 6, b: 0, c: 0 },
        3: { a: 0, b: 1, c: 1 },
        4: { a: 4, b: 3, c: 1 },
      }
      this.process = { a: 0, b: 0, c: 0 }
      this.status = { 
        0: '-', 1: '-', 2: '-', 3: '-', 4: '-'
      }
    },  
    startProcess() {
      let need       = this.need
      let allocation = this.allocation 
      var available  = this.available
      let a = this.process.a

      if (this.process.a == 1 && this.process.c == 5) {
        this.process.a++
      }

      if (this.operation(available[this.process.b], need[this.process.a], false)){
        available[this.process.c] = this.operation(available[this.process.b], allocation[this.process.a], true)
        this.process.b++
        this.status[this.process.a] = this.success

        if ((this.status[a-=2]) == this.status[this.process.a]) {
          this.process.c++
        }
      }
      else {
        if (this.status[this.process.a] == this.success) {
          this.process.b++
        }
        this.process.c++
        this.status[this.process.a] = this.failed
      }

      this.process.a++

      // if (this,process.b == 4) {
      //   this.process.b = 0
      // }

      if (this.process.a == 5) {
        this.process.a = 0
        if (this.status[this.process.a] == this.failed) {
          this.process.c++
        }
      }

      if (this.status[this.process.a] == this.success) {
        this.process.c++
      }

      this.start_process = true

      this.checkSuccess()
      
    },

    statusShow() {
      this.status_show = true
    },

    showNeed() {
      this.start_simulator = true
    },

    requestProcess() {
      let need       = this.need
      let allocation = this.allocation 
      var available  = this.available
      let process    = this.request_index
      var request    = {}

      if (process == 'Process') {
        this.$swal({
          icon: 'error',
          title: 'Oops...',
          text: 'Proses yang anda masukkan salah',
        })
      }

      request[process] = {
        a: parseInt(this.val_request.a), 
        b: parseInt(this.val_request.b), 
        c: parseInt(this.val_request.c)
      }
      
      if (
        this.operation(available[0], request[process], false) && 
        this.operation(need[process], request[process], false) 
      ) {
        available[0]        = this.operation(available[0], request[process], false)
        need[process]       = this.operation(need[process], request[process], false)
        allocation[process] = this.operation(allocation[process], request[process], true)
      }
      else {
        this.$swal.fire({
          icon: 'error',
          title: 'Oops...',
          text: 'Request tidak dapat diproses karena telah melebihi resource',
        })
      }
      
      this.request_index = 'Process'
      this.val_request.a = null
      this.val_request.b = null
      this.val_request.c = null
    },

    operation(val1,val2,isAdd = true){
      let retval = {}
      if( isAdd ) {
        retval.a = val1.a + val2.a
        retval.b = val1.b + val2.b
        retval.c = val1.c + val2.c
        return retval
      } else {
        if (val1.a >= val2.a && val1.b >= val2.b && val1.c >= val2.c) {
          retval.a = val1.a - val2.a
          retval.b = val1.b - val2.b
          retval.c = val1.c - val2.c
          return retval
        } else {
          return false
        }
      }
    }
    
  },
  data() {
    return {
      process: { a: 0, b: 0, c: 0 },
      start_process: false,
      request_index: 'Process',
      val_request: {
        a: null,
        b: null,
        c: null
      },
      success: "✓",
      failed: "✘",
      status_show: false,
      start_simulator: false,
      column_process: ["A", "B", "C"],
      allocation: {
        0: { a: 0, b: 1, c: 0 },
        1: { a: 2, b: 0, c: 0 },
        2: { a: 3, b: 0, c: 2 },
        3: { a: 2, b: 1, c: 1 },
        4: { a: 0, b: 0, c: 2 },
      },
      max: {
        0: { a: 7, b: 5, c: 3 },
        1: { a: 3, b: 2, c: 2 },
        2: { a: 9, b: 0, c: 2 },
        3: { a: 2, b: 2, c: 2 },
        4: { a: 4, b: 3, c: 3 },
      },
      available: {
        0: { a: 3, b: 3, c: 2 },
        1: { a: 0, b: 0, c: 0 },
        2: { a: 0, b: 0, c: 0 },
        3: { a: 0, b: 0, c: 0 },
        4: { a: 0, b: 0, c: 0 },
      },
      need: {
        0: { a: 7, b: 4, c: 3 },
        1: { a: 1, b: 2, c: 2 },
        2: { a: 6, b: 0, c: 0 },
        3: { a: 0, b: 1, c: 1 },
        4: { a: 4, b: 3, c: 1 },
      },
      status: { 
        0: '-', 1: '-', 2: '-', 3: '-', 4: '-'
      }
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
  height: 200px;
  width: 20px;
}

.btn-refresh {
  height: 100px !important;
}
</style>