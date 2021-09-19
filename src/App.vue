<template>
  <div id="app">
    <h1> simple flowchart</h1>
    <div class="tool-wrapper">
      <input id="test" min="6" max="16" v-model="value" step="2" @change="showVal()" type="range"/>
    </div>
    
    <div class="zoom" @wheel="handleWheel()" @wheel.prevent>
      <simple-flowchart :scene.sync="scene" 
      @nodeClick="nodeClick"
      @nodeDelete="nodeDelete"
      @linkBreak="linkBreak"
      @linkAdded="linkAdded"
      @canvasClick="canvasClick"
      :height="800"/>
    </div>
  </div>
</template>

<script>
import SimpleFlowchart from './components/SimpleFlowchart.vue'

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
      value: 10,
    }
  },
  methods: {
    canvasClick(e) {
      console.log('canvas Click, event:', e)
    },
    setZoom(zoom,el) {
          
          var transformOrigin = [0,0];
          var p = ["webkit", "moz", "ms", "o"],
                s = "scale(" + zoom + ")",
                oString = (transformOrigin[0] * 100) + "% " + (transformOrigin[1] * 100) + "%";

          for (var i = 0; i < p.length; i++) {
              el.style[p[i] + "Transform"] = s;
              el.style[p[i] + "TransformOrigin"] = oString;
          }

          el.style["transform"] = s;
          el.style["transformOrigin"] = oString;
          
    },
    showVal(){
      var zoomScale = Number(this.value)/10;
      this.setZoom(zoomScale,document.getElementsByClassName('flowchart-container')[0])
    },
    handleWheel() {
      const $this = this;
      window.addEventListener('wheel', function(event) {
          if (event.deltaY < 0) {
              $this.value++
              $this.showVal()
          } else if (event.deltaY > 0) {
              $this.value--
              $this.showVal()
          }
      })
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
