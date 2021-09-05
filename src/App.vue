<template>
  <div id="app">
    <div class="page_header">
      <h1>SimpleBudget</h1>
      <p>
        A tool you can use to keep track of your expenses.
        <br />This will make use of your browser's local storage to store the
        spending information.
      </p>
      <div class="disclaimer">This is a POC written in Vue and pure CSS.</div>
      <br />
      <br />
      <div>
        <input type="text" v-model="activity" placeholder="Activity" />
        <input type="number" v-model="amount" placeholder="Amount" />
        <button v-if="activeId === null" @click="addActivity">Add</button>
        <button v-else @click="commitEditActivity">Save</button>
      </div>
    </div>
    <div class="activity__list shadow" v-if="totalActivities > 0">
      <div
        class="activity__item "
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
      <div class="total__spend"><span>Total Spend</span> {{ totalSpend }}</div>
    </div>
  </div>
</template>

<script>
import './assets/css/reset.css';
import 'normalize.css';

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
    totalActivities: function() {
      return this.activityList.length;
    },
  },

  methods: {
    addActivity() {
      if (this.checkIfValid()) {
        this.showMessage(
          "Please fill up all fields properly. Both fields shouldn't be empty and amount should be a Number"
        );
        return;
      }
      this.activityList.push({ activity: this.activity, amount: this.amount });
      this.activity = '';
      this.amount = '';
    },
    deleteActivity(index) {
      this.activityList.splice(index, 1);
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
    },
    checkIfValid() {
      return (
        this.activity === '' ||
        this.amount === '' ||
        Number.isInteger(this.amount)
      );
    },
    showMessage(msg) {
      alert(msg);
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
  watch: {
    activityList() {
      this.saveToLocalStorage();
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
  background-color: #b4ecb4;
  min-height: 100%;
  width: 100%;
}
h1 {
  margin: 0;
  padding: 1rem;
  font-weight: bold;
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
.disclaimer {
  font-size: 0.8rem;
  font-style: italic;
  padding: 1rem;
}
.activity__list {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  background-color: #fff;
  border-radius: 1rem;
  padding: 0 1rem;
  max-width: 500px;
  width: 100%;
  overflow: hidden;
  margin: 1rem auto;
}
.activity__item {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  align-items: center;
  padding: 1rem;

  width: 100%;
  border-bottom: 1px solid #eee;
  transition: border 200ms ease-in;
}
.activity__item:hover {
  background-color: #f0fff0;
  border-color: #00ff00;
  border-width: 2px;
}

.activity__details {
  display: flex;
  justify-content: space-between;
  width: 50%;
}
.activity__controls {
  display: flex;
  flex-wrap: wrap;
  width: 50%;
  justify-content: flex-end;
}
.shadow {
  box-shadow: 0 1px 1px rgba(0, 0, 0, 0.11), 0 2px 2px rgba(0, 0, 0, 0.11),
    0 4px 4px rgba(0, 0, 0, 0.11), 0 6px 8px rgba(0, 0, 0, 0.11),
    0 8px 16px rgba(0, 0, 0, 0.11);
}
.total__spend {
  font-size: 1.2rem;
  font-weight: bold;
  padding: 1rem 0;
  text-align: right;
  width: 100%;
}
.total__spend > span {
  font-size: 0.6rem;
  font-weight: normal;
}
</style>
