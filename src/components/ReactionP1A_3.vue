<template>

  <v-row no-gutters align="stretch">

    <v-col cols="8">

      <div class="card-render" id="render-p3">
        <div ref="label-holder-3" class="label-holder">
        </div>
      </div>

    </v-col>

    <v-col cols="4">

      <v-card class="mx-auto card-info" outlined>
        <script
          type="application/javascript"
          src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.2/MathJax.js?config=TeX-AMS_HTML"
        ></script>
        <h1>Viga triangular em balanço (Peso distribuido)</h1>
        <div v-if="devTools">
          <h2>Mouse position: {{ absolute }}</h2>
          <h2>inRange: {{ inRange }}</h2>
          <h2>Largura da Barra: {{ size }} centimetros</h2>

          <div v-if="inRange">
            <h2>X Clicado: {{ clickX }}</h2>
            <h2>Y Clicado: {{ clickY }}</h2>
            <h2>l: {{ l }}</h2>
            <h2>Forca: {{ f }} N</h2>
            <h2>Ya: {{ f }} N</h2>
            <h2>Ma: {{ ma }} N</h2>
          </div>
        </div>
        <div v-if="!devTools">
          <VueMathjax :formula="info"></VueMathjax>
          <VueMathjax :formula="sumFx"></VueMathjax>
          <VueMathjax :formula="sumFy"></VueMathjax>
          <VueMathjax v-if="f != 0" :formula="fya"></VueMathjax>
          <VueMathjax :formula="sumMa"></VueMathjax>
          <VueMathjax v-if="f != 0" :formula="fma"></VueMathjax>
        </div>
      </v-card>

    </v-col>

  </v-row>

</template>

<script>
import Matter from "matter-js";
import { VueMathjax } from "vue-mathjax";

export default {
  name: "App",
  components: {
    VueMathjax,
  },
  computed: {
    fma() {
      //f \cdot da - rb \cdot l
      return (
        "$$ {M}_A = \\frac{F \\cdot l^{2}}{6} \\rightarrow \\frac{" + Math.round(this.f * 100) / 100 + "\\cdot" + Math.round((this.sizeAvailable / 100) * 100) / 100 +  "^{2}}{6}"  + " =" + Math.round((this.ma / 100) * 100) + "$$"
      );
    },
    fya() {
      return (
        "$$ {Y}_A = \\frac{F \\cdot l}{2} \\rightarrow \\frac{" +
        Math.round(this.f * 100) / 100 +
        " \\cdot" +
        Math.round((this.sizeAvailable / 100) * 100) / 100 +
        "}{2} =" +
        Math.round(this.ya * 100) / 100 +
        "$$"
      );
    },
    info() {
      return (
        "$$ F = " +
        Math.round(this.f * 100) / 100 +
        " N ,\\ l = " +
        Math.round(this.sizeAvailable * 100) / 100 +
        "cm $$"
      );
    },
  },
  data() {
    return {
      clickX: "",
      clickY: "",
      absolute: "",
      inRange: true,
      l: 0,
      size: 0,
      sizeAvailable: 0,
      ma: 0,
      ya: 0,
      rb: 0,
      f: 0,
      devTools: false,
      sumFx: "$$\\sum {F}_x = 0 \\rightarrow {X}_A = 0 $$",
      sumFy: "$$\\sum {F}_y = 0 \\rightarrow {Y}_A = \\frac{F \\cdot l}{2} $$",
      sumMa: "$$\\sum {M}_a = 0 \\rightarrow {M}_A = \\frac{F \\cdot l^{2}}{6} $$",
      bodiesDom: [],
      bodies: [],
      bodiesPositions: [

      ],
      width: 0,
      height: 0,
    };
  },
  async mounted() {

    await new Promise((resolve) => {
      setTimeout(resolve, 1000)
    })

    this.height = document.getElementById("render-p3").clientHeight;
    this.width = document.getElementById("render-p3").clientWidth;
    let engine = Matter.Engine.create();
    let render = Matter.Render.create({
      element: document.querySelector("#render-p3"),
      engine: engine,
      options: {
        height: this.height,
        width: this.width,
        showDebug: this.devTools,
      },
    });

    let ground = Matter.Bodies.rectangle(
      this.width / 2,
      this.height - this.height / 40,
      this.width,
      this.height / 20,
      { isStatic: true }
    );
    let wall = Matter.Bodies.rectangle(
      this.width / 40,
      this.height / 2,
      this.width / 20,
      this.height,
      { isStatic: true }
    );
    this.size = this.width * 0.6;
    /*
    let boxA = Matter.Bodies.fromVertices(
      this.width * 0.3 + this.width / 20,
      this.height / 2,
      this.width * 0.6,
      this.height / 20,
      {
        isStatic: true,
        label: "boxA",
        render: {
          visible: true,
        },
      }
    );
    */
   var vertices = [{x: this.width / 20, y: this.height / 2 + this.height/35 }, {x: this.width / 20, y: this.height / 2 - this.height/35},  {x: 500, y: this.height / 2 + this.height/35}]
   let boxA = Matter.Body.create( 
         {
             position:  Matter.Vertices.centre(vertices),
             vertices: vertices, 
             isStatic: true,
             label: 'boxA'
    });
    this.sizeAvailable = this.width * 0.6;
    let mouse = Matter.Mouse.create(render.canvas);
    this.absolute = mouse.absolute;

    let mouseConstraint = Matter.MouseConstraint.create(engine, {
      mouse: mouse,
      constraint: {
        render: { visible: true },
      },
    });

    Matter.Events.on(mouseConstraint, "startdrag", (event) => {
      if (event.body.label == "boxA") {
        let position = event.mouse.absolute;
        //console.log(position);
        this.calculateSizes(position.x, position.y, this.width);
      }
    });

    Matter.Events.on(mouseConstraint, "enddrag", (event) => {
      console.log(event);
      if (event.body.label == "boxA") {
        let position = event.mouse.absolute;
        //console.log(position);
        this.calculateReactions(position.y);
      }
    });

    render.mouse = mouse;
    Matter.World.add(engine.world, [
      boxA,
      ground,
      wall,
      mouseConstraint,
    ]);
    console.log('w:',this.width, 'h:', this.height)
    //A
    this.createLabel(this.width/20,this.height/20, 0,this.height / 2 - this.height / 40, "A_point", "A")
    //Viga
    this.createLabel(this.width/20,this.height/20, this.width*0.64 + this.width/20,this.height / 2 - this.height / 40, "viga", "Viga")

    this.bodiesDom = document.querySelectorAll(".label3");
    this.bodies = [];
    for (var i = 0, l = this.bodiesDom.length; i < l; i++) {
      var body = Matter.Bodies.rectangle(0, 0, 0, 0);
      this.bodiesDom[i].id = body.id;
      this.bodies.push(body);
    }
    Matter.World.add(engine.world, this.bodies);
    window.requestAnimationFrame(this.update);
    window.onresize = this.onResize();
    //box.render();
    //Matter.Engine.update(engine);
    //requestAnimationFrame(rerender);
    Matter.Runner.run(engine);
    Matter.Render.run(render);
  },
  methods: {
    calculateSizes(x, y, w) {
      this.clickX = x;
      this.clickY = y;
      this.l = x - w / 20;
      //this.da = x - p1;
      //this.db = this.sizeAvailable - this.da;
    },
    calculateReactions(y) {
      if (this.inRange) {
        this.f = y - this.clickY;
        this.ma = (this.f * Math.pow(this.sizeAvailable / 100, 2))/6;
        this.ya = (this.f * (this.sizeAvailable / 100)) / 2;

        //this.rb = (this.f * this.da) / this.sizeAvailable;
        //this.ra = this.f - this.rb;
      }
    },
    update() {
      for (var i = 0, l = this.bodiesDom.length; i < l; i++) {
        var bodyDom = this.bodiesDom[i];
        var body = null;
        for (var j = 0, k = this.bodies.length; j < k; j++) {
          if (this.bodies[j].id == bodyDom.id) {
            body = this.bodies[j];
            break;
          }
        }

        if (body === null) continue;
        //console.log(this.bodiesPositions)
        bodyDom.style.transform =
          "translate( " +
          this?.bodiesPositions[i]?.x +
          "px, " +
          this?.bodiesPositions[i]?.y +
          "px )";
        //bodyDom.style.transform += "rotate( " + body.angle + "rad )";
      }
      window.requestAnimationFrame(this.update);
    },
    createLabel(width, height,x,y, id, text) {
      var label = document.createElement("div");
      label.id = id;
      label.className = "label3";
      label.style.cssText = "height:" + height + "px; width:" + width + "px;"
      var content = document.createTextNode(text);
      label.appendChild(content); //adiciona o nó de texto à nova div criada
      this.bodiesPositions.push({x:x, y:y})
      // document.getElementById("label-holder-1").appendChild(label);
      this.$refs['label-holder-3'].appendChild(label)
    },
    onResize(){
      console.log("resized")
    }
  },
};
</script>

<style>
#left {
  float: left;
  width: 60%;
  height: 100vh;
}
#right {
  float: right;
  width: 40%;
  height: 100vh;
}

#MathJax_Message {
  display: none;
}

.label-holder {
  position: absolute;
  z-index: 1;
}

.label3 {
  justify-content: center;
  align-items: center;
  display: flex;
  color: white !important;
  z-index: 2;
  position: absolute;
}

.card-info {
  /* position: absolute; */
  /* top: 50%; */
  /* left: 50%; */
  /* transform: translate(-50%, -50%); */
  width: 100%;
  height: 100%;
}

.card-render {
  /* position: absolute; */
  width: 100%;
  height: 100%;

 
  min-height: 85vh;
  /* top: 50%; */
  /* left: 50%; */
  /* transform: translate(-50%, -50%); */
}
</style>
