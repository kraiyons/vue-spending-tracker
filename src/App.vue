<template>
  <div id="app">
    <h1>SimpleBudget</h1>
    <p>
      A tool you can use to keep track of your expenses.
      <br />This will make use of your browser's local storage to store the
      spending information.
    </p>
    <small>This is a POC written in Vue.</small>
    <br />
    <div>
      <input type="text" v-model="activity" placeholder="Activity" />
      <input type="number" v-model="amount" placeholder="Amount" />
      <button v-if="activeId === null" @click="addActivity">Add</button>
      <button v-else @click="commitEditActivity">Save</button>
    </div>
    <div class="activity__list">
      <div
        class="activity__item shadow"
        v-for="(entry, index) in activityList"
        :key="index"
      >
        <div class="activity__details">
          <div>
            {{ entry.activity }}
          </div>
          <div>
            {{ entry.amount }}
          </div>
        </div>
        <div class="activity__controls">
          <button @click="editActivity(index)">Edit</button>
          <button @click="deleteActivity(index)">Delete</button>
        </div>
      </div>
    </div>
    <div class="total__spend">Total Spend: {{ totalSpend }}</div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      activity: '',
      amount: '',
      activityList: [],
      activeId: null,
    };
  },
  mounted() {
    let spendArray = localStorage.getItem('spendArray');
    if (spendArray) {
      let parsedArray = JSON.parse(spendArray);
      this.activityList = parsedArray.activityList;
      this.totalSpend = parsedArray.totalSpend;
    }
  },
  computed: {
    totalSpend: function() {
      let total = 0;
      for (let i = 0; i < this.activityList.length; i++) {
        total += parseInt(this.activityList[i].amount);
      }
      return total;
    },
  },

  methods: {
    addActivity() {
      this.activityList.push({ activity: this.activity, amount: this.amount });
      this.activity = '';
      this.amount = '';
      this.saveToLocalStorage();
    },
    deleteActivity(index) {
      this.activityList.splice(index, 1);
      this.saveToLocalStorage();
    },
    editActivity(index) {
      const active = this.activityList[index];
      this.activity = active.activity;
      this.amount = active.amount;
      this.activeId = index;
    },
    commitEditActivity() {
      this.activityList[this.activeId] = {
        activity: this.activity,
        amount: this.amount,
      };

      this.activity = '';
      this.amount = '';

      this.activeId = null;

      this.saveToLocalStorage();
    },
    saveToLocalStorage() {
      localStorage.setItem(
        'spendArray',
        JSON.stringify({
          activityList: this.activityList,
          totalSpend: this.totalSpend,
        })
      );
    },
  },
};
</script>

<style>
html,
body {
  height: 100%;
  width: 100%;
  font-size: 18px;
  margin: 0;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  display: flex;
  flex-direction: column;
  align-content: center;
  justify-content: center;
  background-color: #b4ecb4;
  height: auto;
  min-height: 100%;
  width: 100%;
}
input {
  padding: 0.5rem;
  border: none;
  border-bottom: 1px solid #000;
  outline: none;
}
button {
  padding: 0.5rem;
  border: none;
  margin: 0 0.2rem;
  outline: none;
}
button:hover {
  cursor: pointer;
}
.activity__list {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  padding: 1rem;
}
.activity__item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 2rem;
  margin: 0.5rem 0;
  box-shadow: 5px 10px;
  background-color: #fff;
  border-radius: 0.5rem;
  max-width: 500px;
  width: 100%;
}
.activity__item:hover {
  background-color: #f0fff0;
}

.activity__details {
  display: flex;
  justify-content: space-between;
  width: 80%;
}
.activity__controls {
  display: flex;
}
.shadow {
  box-shadow: 0 1px 1px rgba(0, 0, 0, 0.11), 0 2px 2px rgba(0, 0, 0, 0.11),
    0 4px 4px rgba(0, 0, 0, 0.11), 0 6px 8px rgba(0, 0, 0, 0.11),
    0 8px 16px rgba(0, 0, 0, 0.11);
}
.total__spend {
  font-size: 1.2rem;
  font-weight: bold;
}
</style>
