<template>
  <main class="d-flex align-items-center flex-column">
    <h1 class="mb-5">StarNavi: Test task</h1>

    <div class="wrapper d-flex gap-5">

      <section class="left">
        <div class="d-inline-flex gap-3 mb-3">
          <select 
            :disabled="isStarted"
            v-model="appMode"
            class="form-select" 
            aria-label="Select mode" 
          >
            <option disabled selected>Pick mode</option>
            <option
              v-for="item in myData"
              :key="item.id"
              :value="item.field"
            >
              {{ item.name }}
            </option>
          </select>

          <button 
              @click="start" 
              :disabled="isStarted" 
              class="btn text-uppercase" 
              :class="{'btn-primary' : !isStarted, 'btn-secondary' : isStarted}"
            >
            Start!
          </button>
        </div>

        <div v-if="field" class="squares" :style="{ '--app-grid': field }">
          <div
              v-for="(square, index) in squares"
              :key="index"
              :class="{ blue: square.isBlue, white: square.isWhite }"
              @mouseover="hover(index)"
            >
          </div>
        </div>
      </section>

      <section class="right">
        <h3 class="mb-3">Hover squares</h3>

        <ul class="hover-list">
          <transition-group name="list" mode="out-in">
            <li 
              v-for="(hoverItem, index) in hoverList"
              :key="index"
            >
              {{ hoverItem }}
            </li>
          </transition-group>
        </ul>
      </section>

    </div>
  </main>
</template>

<script>

export default {
  name: 'App',

  data() {
    return {
      myData: null,
      field: null,
      appMode: 'Pick mode',
      squares: [],
      hoverList: [],
      isStarted: true,
    }
  },

  mounted() {
    setTimeout(() => {
      fetch('https://60816d9073292b0017cdd833.mockapi.io/modes')
        .then(response => response.json())
        .then(data => {
          this.myData = data
        })
        .then(this.isStarted = false)
        .catch(error => {
          console.error(error);
        });
    }, 1500);
  },
  
  methods: {
    start() {
      this.field = Number.isInteger(this.appMode) ? this.appMode : null
      this.squares = []
      this.hoverList = []

      for (let i = 0; i < this.appMode; i++) {
        for (let j = 0; j < this.appMode; j++) {
          this.squares.push({
            name: `row ${i + 1} col ${j + 1}`,
            isBlue: false,
            isWhite: false
          });
        }
      }
    },

    hover(index) {
      const square = this.squares[index];
      square.isBlue = !square.isBlue;
      if (square.isBlue) {
        square.isWhite = false;
      }
      this.hoverList.push(square.name);
    },
  }
  
}
</script>

<style >
  .squares {
    display: grid;
    grid-template-columns: repeat(var(--app-grid), 50px);
    grid-template-rows: repeat(var(--app-grid), 50px);
    border: 1px solid rgb(78, 78, 78);
  }

  .squares div {
    width: 50px;
    height: 50px;
    border: 1px solid rgb(78, 78, 78);
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 10px;
    cursor: pointer;
    transition: all .25s;
  }

  .squares .blue {
    background-color: rgb(61, 166, 231);
  }

  .squares .white {
    background-color: white;
  }

  .hover-list {
    list-style-type: none;
    padding: 0;
    display: flex;
    flex-direction: column-reverse;
  }

  .hover-list li {
    padding: 10px;
    background-color: #fdf8b8;
    border: 1px solid #fbf169;
    border-radius: 5px;
    margin-bottom: 5px;
  }

  .list-enter-active,
  .list-leave-active {
    transition: all 0.5s ease;
  }

  .list-enter-from,
  .list-leave-to {
    opacity: 0;
    transform: translateX(30px);
  }
</style>
