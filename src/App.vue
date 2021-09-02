<template>
  <div id="app">
    <div>
      <input type="text" v-model="activity" placeholder="Activity" />
      <input type="number" v-model="amount" placeholder="Amount" />
      <button v-if="activeId === null" @click="addActivity">Add</button>
      <button v-else @click="commitEditActivity">Save</button>
    </div>
    <div class="activityList">
      <div
        class="activityItem"
        v-for="(entry, index) in activityList"
        :key="index"
      >
        <div>
          {{ entry.activity }}
        </div>
        <div>
          {{ entry.amount }}
        </div>
        <button @click="editActivity(index)">Edit</button>
        <button @click="deleteActivity(index)">Delete</button>
      </div>
    </div>
    <div>Total Spend: {{ totalSpend }}</div>
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
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  padding: 40px;
  display: flex;
  flex-direction: column;
  align-content: center;
  justify-content: center;
}
.activityList {
  margin: 0 auto;
}
.activityItem {
  display: flex;
}
.activityItem div {
  padding: 5px 10px;
}
</style>
