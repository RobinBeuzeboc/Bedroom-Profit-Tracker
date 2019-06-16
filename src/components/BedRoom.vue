<template>
  <div class="container">
    <div class="columns is-multiline">
      <div class="column auto ">
        <div class="select">
          <select v-model="currency">
            <option>€</option>
            <option>£</option>
          </select>
        </div>
      </div>

      <div class="column is-2">
        <div class="control has-icons-right">
          <input class="input has-text-centered" type="text" placeholder="0,00" ref="balance" v-model="balance">
          <span class="icon is-small is-right">
            {{ currency }}
          </span>
        </div>
      </div>


      <div class="column is-2">
        <div class="control">
          <div class="select">
            <select v-model="type">
              <option value="cost">Cost</option>
              <option value="income">Income</option>
            </select>
          </div>
        </div>
        
      </div><div class="column is-6">
        <div class="control has-icons-right">
          <input class="input has-text-centered" type="text" placeholder="Description of the operation" ref="description" v-model="description">
          <span class="icon is-small is-right">

          </span>
        </div>
      </div>

      <div class="column is-1">
        <div class="control has-text-left">
          <button 
            class="button is-primary" 
            :disabled="regexBalance === false" 
            @click="editBalance">Add</button>
        </div>
      </div>

      <div class="column is-6 is-offset-3">
        <div class="columns">
          <div class="column is-half">
            <h4 class="title is-4">
              <span>Balance: <span v-html="totalFlow"/> {{ currency }}</span> 
            </h4>
          </div>
          <div class="column is-3" v-if="this.bedRoomCashFlow.length > 0">
            <button class="button is-link is-small tooltip is-tooltip-bottom" 
                    data-tooltip="Save into local storage." 
                    @click="saveBalance()">
              <i class="fa fa-save"></i>
              <span>Save</span>
            </button>
          </div>
          <div class="column is-3" v-if="this.bedRoomCashFlow.length > 0">
            <button class="button is-danger is-small tooltip is-tooltip-bottom" 
                    data-tooltip="Flush cash flow from everywhere!"
                    @click="flushBalance()">
              <i class="fa fa-trash"></i>
              <span>Flush</span>
            </button>
          </div>
        </div><br>
 
        <transition name="fade-balance">
          <hr class="divide" v-if="this.bedRoomCashFlow.length > 0">
        </transition>
      </div>
    

      <div class="column is-11 is-offset-1" v-if="this.bedRoomCashFlow.length > 0">
        <table class="table is-bordered is-striped is-narrow is-hoverable is-fullwidth">
          <thead>
            <th class="has-text-centered">Amount</th>
            <th class="has-text-centered">Type</th>
            <th class="has-text-centered">Description</th>
            <th class="has-text-centered">Actions</th>
          </thead>
          <tbody>
            <tr 
              is="Flow" 
              v-for="(flow, index) in bedRoomCashFlow" 
              :key="flow.id"
              :index="index"
              :flow="flow"
              :description="description"
              :currency="currency"
              :removeRow="removeRow"
            />
          </tbody>
        </table>
      </div>
    </div>

    <Notification
      v-if="saveNotification"
      message="Cash flow saved into local storage"
      :closeNotification="() => this.saveNotification = false"
    />

    <Notification 
      v-if="flushNotification"
      type="danger"
      message="Local storage has been flushed"
      :closeNotification="() => this.flushNotification = false"
    />
  </div>
</template>

<script>
import Flow from "./Flow";
import Notification from "./Notification";
//Import Flow for computing/storing and Notification for the popUp window 
export default {
  components: {
    Flow,
    Notification
  },
  data() {
    return {
      type: "income",
      description: "",
      balance: "",
      currency: '€',
      totalBalance: 0.0,
      bedRoomCashFlow: [],
      saveNotification: false,
      flushNotification: false,
    };
  },
  mounted() { //Save the actual balance locally
    const savedBalance = localStorage.getItem('vue-cash-flow-balance');
    if (savedBalance && savedBalance.length > 0) {
      this.bedRoomCashFlow = JSON.parse(savedBalance);
    }
  },
  methods: { //Methods to compute prices/save/delete balance

    editBalance() { //Edit balance at each new additions/deletions in the app
      this.bedRoomCashFlow.push({
        id: Date.now(),
        type: this.type,
        description: this.description,
        amount: parseFloat(this.balance.toString().replace(',', '.'))
      });
      this.balance = "";
      this.$refs.balance.focus();
    },
    removeRow(index) { //delete the cost row based on the index of it
      this.bedRoomCashFlow.splice(index, 1);
    },
    saveBalance() { //save the balance in JSON format locally
      localStorage.setItem('bedroom-balance', JSON.stringify(this.bedRoomCashFlow));
      this.flushNotification = false;
      this.saveNotification  = true;
    },
    flushBalance() { //flush local storage
      this.bedRoomCashFlow = [];
      localStorage.removeItem('bedroom-balance');
      this.saveNotification  = false;
      this.flushNotification = true;
    },
  },
  computed: {
    regexBalance() { //verifies if cost is a number
      return /^(\d*([.,])?\d{1,2})$/.test(this.balance);
    },
    totalFlow() { //compute total balance income/cost When field is completed
      let total = 0;
      this.bedRoomCashFlow.map((item) => {
        if (item.type === 'cost') {
          total -= item.amount;
        }
        else {
          total += item.amount;
        }
      });
      const className = total >= 0 ? "has-text-success" : "has-text-danger"; 
    
      return '<span class="' + className + '">' + total.toFixed(2).replace('.', ',') + '</span>' 
    }
  },
};
</script>


//styles
<style lang="scss" scoped>
  .divide {
    margin-top: 1.2rem;
    margin-bottom: 0.5rem;
  }
  .fade-balance {
    &-enter {
      opacity: 0;

      &-active {
        transition: opacity 1s;
      }
    }
  }
  .button {
    width: 100%;
  }
</style>