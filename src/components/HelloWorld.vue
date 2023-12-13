<template>
  <div class="terminal">
    <div class="terminal-header">
      <div class="terminal-header-buttons">
        <div class="circle close"></div>
        <div class="circle minimalize"></div>
        <div class="circle open"></div>
      </div>
      <div class="terminal-header-title">
        <p>jamie@MacBook-Pro-van-Jamie:~</p>
      </div>
      <div class="terminal-header-other">
        <img class="terminal-header-other-img" src="../assets/output-onlinepngtools.png">
        <p>1</p>
      </div>
    </div>
    <div class="terminal-subheader" v-if="subheaders.length > 0">
      <div v-for="(subheader, index) in subheaders" :key="index" class="terminal-subheader-item"
        @click="toggleSubheaderColor(index)" :class="{ 'active': activeSubheader === index }">
        {{ subheader }}
        <button class="delete-button" @click="deleteSubheader(index)">x</button>
      </div>
      <div class="terminal-subheader-item add-subheader" @click="addSubheader">
        +
      </div>
    </div>
    <div class="terminal-content">
      <span class="prompt">{{ prompt }}</span>
      <input type="text" v-model="currentCommand" @keyup.enter="executeCommand" ref="commandInput">
      <div class="output">{{ commandOutput }}</div>
    </div>

  </div>
</template>

<script>
export default {
  data() {
    return {
      activeSubheader: 1, // Set Subheader 2 as active by default (0-based index)
      subheaders: [], // Initialize subheaders as an empty array
      currentCommand: '',
      commandHistory: [],
      commandOutput: '',
      historyIndex: -1,
      prompt: 'jamie@MacBook-Pro-van-Jamie:~$',
    };
  },
  created() {
    const storedSubheaders = localStorage.getItem('subheaders');
    if (storedSubheaders) {
      this.subheaders = JSON.parse(storedSubheaders);
    } else {
      this.subheaders = ['Subheader 1']; // Initialize with a default subheader
    }

    const storedActiveSubheader = localStorage.getItem('activeSubheader');
    if (storedActiveSubheader !== null) {
      this.activeSubheader = JSON.parse(storedActiveSubheader);
    } else {
      this.activeSubheader = 0; // Set default active subheader to the first one
    }
  },


  mounted() {
    document.addEventListener('keydown', this.handleKeydown);
    this.$refs.commandInput.focus();
  },

  beforeDestroy() {
    document.removeEventListener('keydown', this.handleKeydown);
  },

  methods: {
    executeCommand() {
      // Add command to history and reset currentCommand
      this.commandHistory.push(this.currentCommand);
      this.historyIndex = this.commandHistory.length;

      // Simulate command output
      if (this.currentCommand.trim() === 'help') {
        this.commandOutput = 'On branch master\nYour branch is up to date with \'origin/master\'.';
      } else {
        this.commandOutput = 'Command not recognized.';
      }

      // Reset current command
      this.currentCommand = '';
    },

    toggleSubheaderColor(index) {
      if (this.activeSubheader !== index) {
        this.activeSubheader = index;
        localStorage.setItem('activeSubheader', JSON.stringify(this.activeSubheader));
      } else {
        this.activeSubheader = index;
      }
      localStorage.setItem('activeSubheader', JSON.stringify(this.activeSubheader));
    },

    addSubheader() {
      if (this.subheaders.length < 5) { // Check if the number of subheaders is less than 5
        // Add a new subheader when the '+' button is clicked
        const newSubheader = `Subheader ${this.subheaders.length + 1}`;
        this.subheaders.push(newSubheader);

        // Save updated subheaders to local storage
        localStorage.setItem('subheaders', JSON.stringify(this.subheaders));
      }
    },

    handleKeydown(e) {
      // Check for Ctrl + T on Windows/Linux or Cmd + T on Mac
      // if (e.key === 't') {
      //   e.preventDefault();  // Prevent the default browser action
      //   this.addSubheader();  // Add your custom action
      // }

      // if (e.key === 'w') {
      //   e.preventDefault();  // Prevent the default browser action
      //   this.deleteSubheader();  // Add you cx  r custom action
      // }

      // Handle command history navigation
      if (e.key === 'ArrowUp') {
        e.preventDefault();
        if (this.historyIndex > 0) {
          this.historyIndex--;
          this.currentCommand = this.commandHistory[this.historyIndex];
        }
      } else if (e.key === 'ArrowDown') {
        e.preventDefault();
        if (this.historyIndex < this.commandHistory.length - 1) {
          this.historyIndex++;
          this.currentCommand = this.commandHistory[this.historyIndex];
        } else {
          this.historyIndex = this.commandHistory.length;
          this.currentCommand = '';
        }
      }
    },

    deleteSubheader(index) {
      // Remove the subheader at the specified index
      this.subheaders.splice(index, 1);

      // Handle case when the last subheader is deleted
      if (this.subheaders.length === 0) {
        this.activeSubheader = -1;
      } else if (this.activeSubheader >= index) {
        // Adjust the active subheader index
        this.activeSubheader = Math.max(0, this.activeSubheader - 1);
      }

      // Update local storage
      localStorage.setItem('subheaders', JSON.stringify(this.subheaders));
      localStorage.setItem('activeSubheader', JSON.stringify(this.activeSubheader));
    },

  },
};
</script>

<style>
.terminal {
  width: 45rem;
  height: 30rem;
  background-color: black;
  border-radius: 12px;
  box-shadow: rgba(0, 0, 0, 0.35) 0px 5px 15px;
  font-family: monospace;
}

.terminal-header {
  background-color: #373838;
  height: 28px;
  width: 100%;
  border-top-left-radius: 12px;
  border-top-right-radius: 12px;
  padding: 0 10px;
  display: flex;
  justify-content: space-between;
}

.terminal-header-buttons {
  display: flex;
  align-items: center;
  height: 100%;
  gap: 8px;
}

.circle {
  width: 12px;
  height: 12px;
  border-radius: 50%;
}

.close {
  background-color: #fe5f58;
}

.minimalize {
  background-color: #ffbc30;
}

.open {
  background-color: #27c83f;
}

.terminal-header-title {
  color: #b3b5b4;
  font-weight: bold;
  display: flex;
  align-items: center;
  font-size: 100%;
}

.terminal-header-other {
  display: flex;
  color: rgb(117, 117, 117);
  align-items: center;
  gap: 2px;
}

.terminal-header-other-img {
  height: 10px;
}

.terminal-subheader {
  display: flex;
}

.terminal-subheader-item {
  width: calc(1000% - 1px);
  box-sizing: border-box;
  font-size: 80%;
  position: relative;
  cursor: pointer;
  height: 23px;
  background-color: #1c1c1c;
  display: flex;
  color: #515151;
  align-items: center;
  justify-content: center;
}

.add-subheader {
  width: 25px;
  background-color: #292a29;
  color: #5d5e5e;
  font-size: 140%;
  font-weight: 0;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
}

.terminal-subheader-item.active {
  background-color: #373838;
  color: white;
}

.delete-button {
  display: none;
  color: white;
  border: none;
  cursor: pointer;
  position: absolute;
  background-color: transparent;
  padding: 5px 10px;
  left: 0;
  border-radius: 4px;
  font-size: 14px;
  margin-left: 5px;
}

.terminal-subheader-item:hover .delete-button {
  display: inline-block;
}

.terminal-content {
  padding: 10px;
}

.terminal-content {
  padding: 10px;
  color: white;
}

.prompt {
  color: #0f0;
  padding-right: 5px;
  /* Green text for prompt */
}

input {
  background: transparent;
  border: none;
  outline: none;
  width: 60%;
  color: white;
  font-family: monospace;
}

.output {
  color: white;
  margin-top: 10px;
  white-space: pre;
  /* Keeps the formatting of the output */
}
</style>