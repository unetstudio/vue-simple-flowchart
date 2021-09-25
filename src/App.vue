<template>
  <div id="app">
    <simple-flowchart :scene.sync="scene"
      @nodeClick="nodeClick"
      @nodeDelete="nodeDelete"
      @linkBreak="linkBreak"
      @linkAdded="linkAdded"
      @canvasClick="canvasClick"
      :height="650"
    />
  </div>
</template>

<script>
import SimpleFlowchart from './components/SimpleFlowchart.vue'
import { format } from 'sql-formatter';
import { Parser } from '@dbml/core';

export default {
  name: 'app',
  components: {
    SimpleFlowchart
  },
  data() {
    return {
      scene: {
        centerX: 1024,
        centerY: 140,
        scale: 1,
        nodes: [
          {
            id: 2,
            x: -700,
            y: -69,
            type: 'Users',
            label: `
    id int PK
    company_id -> users.id
    members varchar [array]
    telephone varchar
    email varchar
    fax varchar
    content text nullable
            `,
          },
          {
            id: 4,
            x: -357,
            y: 80,
            type: 'Company',
            label: `
    id int PK
    company_id -> users.id
    project_id -> projects.id
            `,
          },
        ],
        links: [
          
        ]
      },
      newNodeType: 0,
      newNodeLabel: '',
      nodeCategory:[
        'rule',
        'action',
        'script',
        'decision',
        'fork',
        'join',
      ],

    }
  },
  created() {
      this.fileTree();
  },
  methods: {
    fileTree() {
        let paths = [
            "About.vue",
            "Categories/Index.vue",
            "Categories/Demo.vue",
            "Categories/Flavors.vue",
            "Categories/Types/Index.vue",
            "Categories/Types/Other.vue"
        ];

        paths = [
            "App/Constants/%Model%.php",
            "App/Services/%Model%Service.php",
            "App/Http/Requests/Api/%Model%Request.php",
            "Routes/Route/%Model%.php",
            "Resources/lang/ja/%model%.php",
        ];

        var items = [
        {
          name: '.git',
        },
        {
          name: 'node_modules',
        },
        {
          name: 'public',
          children: [
            {
              name: 'static',
              children: [{
                name: 'logo.png',
                file: 'png',
              }],
            },
            {
              name: 'favicon.ico',
              file: 'png',
            },
            {
              name: 'index.html',
              file: 'html',
            },
          ],
        }];

        let result = [];
        let level = {result};

        paths.forEach(path => {
          path.split('/').reduce((r, name, i, a) => {
            if(!r[name]) {
              r[name] = {result: []};
              if (name.indexOf('.') > -1) {
                  const file = name.split('.')[1]
                  r.result.push({name, file, children: r[name].result})
              } else {
                  r.result.push({name, children: r[name].result})
              }
            }

            return r[name];
          }, level)
        });

        console.log(result);
        console.log(items)
    },
    sqlParser() {
        console.log(format('SELECT * FROM tbl'));

    var dbml = `
table users {
  id int pk
  username varchar
  role varchar
  created_at timestamp
}
`;
    const database = Parser.parse(dbml, 'dbml');
    console.log(database);
    },
    canvasClick(e) {
      console.log('canvas Click, event:', e)
    },
    addNode() {
      let maxID = Math.max(0, ...this.scene.nodes.map((link) => {
        return link.id
      }))
      this.scene.nodes.push({
        id: maxID + 1,
        x: -400,
        y: 50,
        type: this.nodeCategory[this.newNodeType],
        label: this.newNodeLabel ? this.newNodeLabel: `test${maxID + 1}`,
      })
    },
    nodeClick(id) {
      console.log('node click', id);
    },
    nodeDelete(id) {
      console.log('node delete', id);
    },
    linkBreak(id) {
      console.log('link break', id);
    },
    linkAdded(link) {
      console.log('new link added:', link);
    }
  }
}
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 0;
  overflow: hidden;
  height: 800px;
  .tool-wrapper {
    position: relative;
  }
}
</style>
