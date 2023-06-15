<template>
  <el-card class="form">
    <json-editor ref="JsonEditor" :schema="schema" v-model="model" @input="input">
      <el-button @click="start" type="primary">Start</el-button>
      <el-button @click="share" type="primary">Share</el-button>
    </json-editor>
    <pre>{{ userInput }}</pre>
  </el-card>
</template>

<script>
import JsonEditor from 'vue-json-ui-editor';
import { render, checkInput } from './app.js';

const SCHEMA = {
  type: 'object',
  title: 'Playground',
  properties: {
    'algorithm': {
      title: 'Algorithm',
      type: 'string',
    },
    'data': {
      // title: 'Data',
      type: 'object',
      properties: {
        'graph': {
          title: 'Graph',
          type: 'string',
        },
        'start': {
          title: 'Start',
          type: 'string',
        }
      }
    }
  },
};

export default {
  name: 'App',
  components: {
    JsonEditor
  },
  data() {
    return {
      schema: SCHEMA,
      model: {
        'algorithm': 'bfs',
        'data': {
          'graph': 's--v, s--r, s--u, t--r, t--u, u--y, y--v, v--w, r--w, y--x, w--x, w--z, x--z',
          'start': 's'
        }
      },
      errorMsg: '',
    };
  },
  computed: {
    userInput() {
      const json = JSON.stringify(this.model);
      const obj = JSON.parse(json);
      obj.data.graph = obj.data.graph.split(',').map(s => s.trim());
      return obj;
    }
  },
  methods: {
    start() {
      render(this.userInput);
    },
    share() {
      navigator.clipboard.writeText(JSON.stringify(this.model)).then(() => {
        alert('json copy to clipboard')
      }, () => {
        alert('cannot copy to clipboard')
      })
    },
    input() {
      checkInput(this.userInput);
    }
  },
  mounted() {
    checkInput(this.userInput);
    JsonEditor.setComponent('form', 'el-form', ({ vm }) => {
      // vm is the JsonEditor VM

      const labelWidth = '120px';
      const model = vm.data;
      const rules = {};

      function parseField(fields) {
        Object.keys(fields).forEach(key => {
          if (key.indexOf('$') === 0 && key !== '$sub') return;
          const field = fields[key];
          if (field.$sub) {
            return parseField(field);
          }
          if (!field.name) return;
          rules[field.name] = [];
          // http://element.eleme.io/#/en-US/component/form#validation
          const type = field.schemaType === 'array' && field.type === 'radio' ? 'string' : field.schemaType;
          const required = field.required;
          const message = field.title;
          const trigger = ['radio', 'checkbox', 'select'].includes(field.type) ? 'change' : 'blur';
          rules[field.name].push({ type, required, message, trigger });

          if (field.minlength !== undefined || field.maxlength !== undefined) {
            const max = field.maxlength || 255;
            const min = field.minlength || 0;
            const msg = `Length must between ${min} and ${max}`;
            rules[field.name].push({ min, max, message: msg, trigger });
          }
        });
      }

      parseField(vm.fields);

      // returning the form props
      return { labelWidth, rules, model };
    });
    JsonEditor.setComponent('label', 'el-form-item', ({ field }) => ({ prop: field.name }));
    JsonEditor.setComponent('email', 'el-input');
    JsonEditor.setComponent('url', 'el-input');
    JsonEditor.setComponent('number', 'el-input-number');
    JsonEditor.setComponent('text', 'el-input');
    JsonEditor.setComponent('textarea', 'el-input');
    JsonEditor.setComponent('checkbox', 'el-checkbox');
    JsonEditor.setComponent('checkboxgroup', 'el-checkbox-group');
    JsonEditor.setComponent('radio', 'el-radio');
    JsonEditor.setComponent('select', 'el-select');
    JsonEditor.setComponent('switch', 'el-switch');
    JsonEditor.setComponent('color', 'el-color-picker');
    JsonEditor.setComponent('rate', 'el-rate');
    JsonEditor.setComponent('slider', 'el-slider');
    JsonEditor.setComponent('option', 'el-option');
    JsonEditor.setComponent('title', 'h2');
    JsonEditor.setComponent('description', 'small');
  }
}
</script>

<style scoped>
.form {
  text-align: left;
  width: 90%;
  margin: auto;
}

h2 {
  font-size: 1.7em;
  text-align: center;
  margin-top: 0;
  margin-bottom: 0.2em;
}

h2+small {
  display: block;
  text-align: center;
  margin-bottom: 1.2em;
}

small {
  line-height: 20px;
  display: block;
}

.el-alert {
  margin-bottom: 15px;
}

.el-form .sub {
  margin-left: 10%;
}

.output-msg {
  padding: 10px 20px;
  font-size: 20px;
  font-family: monospace;
}
</style>