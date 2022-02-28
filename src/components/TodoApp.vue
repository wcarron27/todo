<template>
  <div class="todoApp">
    <div>
      <div class="todo-side">
        <ul id="TodoList">
          <li v-for="(item, index) in items" :key="item.text" class="todo-item">
            <div class="priority">
              <h3>{{ item.priority }}</h3>
            </div>

            <div class="text-box">
              <h4>{{ item.text }}</h4>
            </div>

            <div class="complete-box">
              <button @click="deleteItem(index)">Complete Item</button>
            </div>
          </li>
        </ul>
      </div>
    </div>

    <hr />
    <div class="form-input">
      <label for="Todo Text">Text:</label>
      <input type="text" v-model="newItemText" minlength="0" maxlength="140" placeholder="What needs to be done?"/>
      <label for="priority">Priority:</label>
      <input v-model="priority" type="number" id="priority" name="priority" min="1">
      <button @click="createItem">Add Item</button>
    </div>


    <div v-if="message">
      {{ message }}
    </div>
  </div>
</template>

<script>
export default {
  name: 'todoApp',
  props: {
    msg: String
  },

  data() {
    return {
      items: [],
      newItemText: '',
      priority: 1,
      message: ''
    }
  },

  computed: {
    currentlyAllocatedPriorities() {
      return this.items.map(item => {
        return item.priority
      })
    },

    lowestPriority() {
      return this.currentlyAllocatedPriorities.reduce((a, b) => Math.max(a, b))
    },

    missingPriorities() {
      let all = Array.from(Array(this.lowestPriority).keys())
      let difference = all.filter(x => !this.currentlyAllocatedPriorities.includes(x));
      difference.shift() // remove 0th index containing a zero)
      return difference
    },

    lowestMissingPriority() {
      if (this.missingPriorities && this.missingPriorities.length) {
        return this.missingPriorities.reduce((a, b) => Math.max(a, b))
      } else {
        return 1
      }
    }
  },

  methods: {
    createItem() {
      let item = {
        text: this.newItemText,
        priority: parseInt(this.priority, 10)
      }
      if (this.checkValidity(item)) {
        this.items.push(item)
        this.resetForm();
      } else {
        this.clearMessage(5000)
      }
    },

    deleteItem(index) {
      this.items.splice(index, 1)

    },

    resetForm() {
      this.newItemText = ''
      this.priority = this.lowestMissingPriority
      this.clearMessage(5000)
    },

    clearMessage(timeout) {
      setTimeout(() => {
        this.message = ''
      }, timeout)
    },

    checkValidity(item) {
      if (item.priority == 0 || this.currentlyAllocatedPriorities.indexOf(item.priority) >= 0) {
        this.message = 'Priority must be positive integer and cannot be a duplicate of prior tasks'
        return false
      } else if (item.text.length > 140 || item.text.length <= 0) {
        this.message = 'Text must be between 1 and 140 characters in length'
        return false
      } else {
        return true
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}

.form-input,
.todo-item {
  width: 100%;
  display: flex;
  justify-content: center;
  align-content: center;
  align-items: center;
}

.form-input {
  flex-flow: column;
}

.todo-item {
  flex-flow: row nowrap;
}
</style>
