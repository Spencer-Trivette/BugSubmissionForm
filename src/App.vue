<template>
  <div class="bug-reporter">
    <h2>QA Bug Reporter</h2>
    
    <div class="form-section">
      <label>Bug Title:</label>
      <input 
        v-model="newBug.title" 
        placeholder="Brief description"
      />
      <small>Preview: {{ newBug.title || 'Type something above!' }}</small>
    </div>

    <div class="form-section">
      <label>Severity:</label>
      <select v-model="newBug.severity">
        <option disabled value="">Select severity</option>
        <option 
          v-for="level in severityLevels" 
          :key="level"
          :value="level"
        >
          {{ level }}
        </option>
      </select>
    </div>

    <button 
      @click="addBug"
      :disabled="!canSubmit"
      class="submit-btn"
    >
      Submit Bug ({{ bugs.length }} total)
    </button>

    <div class="stats">
      <p>Critical Bugs: {{ criticalBugCount }} (computed)</p>
      <p>Open Bugs: {{ getOpenBugCount() }} (method)</p>
      <p>Character count: {{ characterCount }}/100</p>
    </div>

    <div class="bug-list">
      <h3>Bug List</h3>
      <div 
        v-for="(bug, index) in filteredBugs" 
        :key="bug.id"
        class="bug-item"
        :class="{ resolved: bug.resolved }"
      >
        <span v-if="bug.severity === 'Critical'" class="critical">🔴</span>
        <span v-show="bug.resolved" class="resolved-badge">✓</span>
        
        <strong>{{ bug.title }}</strong>
        <small>({{ formatDate(bug.createdAt) }})</small>
        
        <button @click="toggleResolved(bug.id)">
          {{ bug.resolved ? 'Reopen' : 'Resolve' }}
        </button>
      </div>
    </div>

    <div v-if="showWarning" class="warning">
      ⚠️ You've added {{ bugs.length }} bugs today!
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newBug: {
        title: '',
        severity: ''
      },
      bugs: [],
      severityLevels: ['Unsure', 'Low', 'Medium', 'Critical'],
      showWarning: false,
      nextId: 1
    }
  },
  
  computed: {
    criticalBugCount() {
      console.log('Computing critical bugs...')
      return this.bugs.filter(bug => 
        bug.severity === 'Critical' && !bug.resolved
      ).length
    },
    
    canSubmit() {
      return this.newBug.title.trim() && this.newBug.severity
    },
    
    characterCount() {
      return this.newBug.title.length
    },
    
    filteredBugs() {
      return [...this.bugs].reverse()
    }
  },
  
  methods: {
    addBug() {
      if (!this.canSubmit) return
      
      this.bugs.push({
        id: this.nextId++,
        title: this.newBug.title,
        severity: this.newBug.severity,
        resolved: false,
        createdAt: new Date()
      })
      
      this.newBug.title = ''
      this.newBug.severity = ''
    },
    
    getOpenBugCount() {
      console.log('Calculating open bugs...')
      return this.bugs.filter(bug => !bug.resolved).length
    },
    
    toggleResolved(bugId) {
      const bug = this.bugs.find(b => b.id === bugId)
      if (bug) {
        bug.resolved = !bug.resolved
      }
    },
    
    formatDate(date) {
      return date.toLocaleTimeString()
    }
  },
  
  watch: {
    bugs: {
      handler(newBugs) {
        this.showWarning = newBugs.length >= 5
      },
      deep: true
    },
    
    'newBug.title'(newTitle) {
      if (newTitle.length > 100) {
        this.newBug.title = newTitle.substring(0, 100)
      }
    }
  },
  
  created() {
    console.log('Component created! Data is ready but DOM is not')
  },
  
  mounted() {
    console.log('Component mounted! DOM is ready')
  }
}
</script>

<style scoped>
.bug-reporter {
  max-width: 600px;
  margin: 20px;
  font-family: Arial, sans-serif;
}

.form-section {
  margin-bottom: 15px;
}

label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
}

input, select {
  width: 100%;
  padding: 8px;
  border: 1px solid #ddd;
  border-radius: 4px;
}

.submit-btn {
  background: #42b983;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 4px;
  cursor: pointer;
}

.submit-btn:disabled {
  background: #ccc;
  cursor: not-allowed;
}

.bug-item {
  padding: 10px;
  margin: 10px 0;
  border: 1px solid #ddd;
  border-radius: 4px;
}

.bug-item.resolved {
  opacity: 0.6;
  background: #f0f0f0;
}

.critical {
  margin-right: 5px;
}

.warning {
  background: #fff3cd;
  border: 1px solid #ffeeba;
  color: #856404;
  padding: 10px;
  margin-top: 10px;
  border-radius: 4px;
}

.stats {
  background: #f5f5f5;
  padding: 10px;
  margin: 15px 0;
  border-radius: 4px;
}
</style>