<template>

  <v-row no-gutters align="stretch">

    <v-col cols="8">

      <div class="card-render" id="render-p4">
        <div id="label-holder-4" class="label-holder">
        </div>
      </div>

    </v-col>

    <v-col cols="4">

      <v-card class="mx-auto card-info" outlined>
        <script
          type="application/javascript"
          src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.2/MathJax.js?config=TeX-AMS_HTML"
        ></script>
        <h1>Viga bi-apoiada (Peso pontual)</h1>
        <div v-if="devTools">
          <h2>Coordenadas mouse: {{ absolute }}</h2>
          <h2>Dentro do range: {{ inRange }}</h2>
          <h2>Largura da Barra: {{ size }} centimetros</h2>
          <h2>Largura Disponivel para calculo: {{ sizeAvailable }} centimetros</h2>

          <div v-if="inRange">
            <h2>X Clicado: {{ clickX }}</h2>
            <h2>Y Clicado: {{ clickY }}</h2>
            <h2>Da: {{ da }}, Db: {{ db }}</h2>
            <h2>Força: {{ f }} N</h2>
            <h2>Reacao em A: {{ ra }} N</h2>
            <h2>Reacao em B: {{ rb }} N</h2>
          </div>
        </div>
        <div v-if="!devTools">
          <VueMathjax :formula="info"></VueMathjax>
          <VueMathjax v-if="da != 0" :formula="distances"></VueMathjax>
          <VueMathjax :formula="sumMa"></VueMathjax>
          <VueMathjax v-if="f != 0" :formula="f1"></VueMathjax>
          <VueMathjax v-if="f != 0" :formula="f2"></VueMathjax>
          <VueMathjax :formula="sumFy"></VueMathjax>
          <VueMathjax v-if="f" :formula="f3"></VueMathjax>
          <VueMathjax v-if="f" :formula="f4"></VueMathjax>
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
  data() {
    return {
      clickX: "",
      clickY: "",
      absolute: "",
      inRange: false,
      da: 0,
      db: 0,
      size: 0,
      sizeAvailable: 0,
      ra: 0,
      rb: 0,
      f: 0,
      sumMa: "$$\\sum {M}_a = 0$$",
      sumFy: "$$\\sum {F}_y = 0$$",
      devTools: false,
      bodiesDom: [],
      bodies: [],
      bodiesPositions: [

      ],
      width: 0,
      height: 0,
      //f2: '$$' + this.f + '$$',
      //f3: '$$\\sum {M}_a = 0$$',
    };
  },
  computed: {
    f1() {
      //f \cdot da - rb \cdot l
      return (
        "$$" +
        Math.round(this.f * 100) / 100 +
        "\\cdot" +
        Math.round(this.da * 100) / 100 +
        "- {R}_b \\cdot" +
        Math.round(this.sizeAvailable * 100) / 100 +
        " = 0 $$"
      );
    },
    f2() {
      return (
        "$$ {R}_b = \\frac{ " +
        Math.round(this.da * 100) / 100 +
        " \\cdot " +
        Math.round(this.f * 100) / 100 +
        " }{ " +
        Math.round(this.sizeAvailable * 100) / 100 +
        " } \\rightarrow {R}_b = " +
        Math.round(this.rb * 100) / 100 +
        " $$"
      );
    },
    f3() {
      return "$${R}_a -" + Math.round(this.f * 100) / 100 + " + {R}_b = 0$$";
    },
    f4() {
      return (
        "$${R}_a =" +
        Math.round(this.f * 100) / 100 +
        " - " +
        Math.round(this.rb * 100) / 100 +
        " \\rightarrow {R}_a =" +
        Math.round(this.ra * 100) / 100 +
        "$$"
      );
    },
    distances(){
      return(
        "$${D}_a = " + Math.round(this.da * 100) / 100 + " \\ {D}_b = " + Math.round(this.db * 100) / 100 + "$$"
      )
    },
    info(){
      return(
        "$$ F = " + Math.round(this.f * 100) / 100 + ",\\ l = " + Math.round(this.sizeAvailable * 100) / 100 + "$$"
      )
    }
  },
  async mounted() {
    //console.log(this.bodiesPositions)
    // console.log(document.querySelector("#render").clientWidth)

    await new Promise((resolve) => {
      setTimeout(resolve, 1000)
    })
    console.log(document.querySelector("#render-p4").offsetWidth)

    this.height = document.getElementById("render-p4").clientHeight;
    this.width = document.getElementById("render-p4").clientWidth;
    let engine = Matter.Engine.create();
    let render = Matter.Render.create({
      element: document.querySelector("#render-p4"),
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
    this.size = this.width * 0.8;
    let boxA = Matter.Bodies.rectangle(
      this.width / 2,
      this.height / 1.4,
      this.width * 0.8,
      this.height / 20,
      { isStatic: true,
        label: "boxA", }
    );
    let constraint1 = Matter.Constraint.create({
      bodyA: ground,
      pointA: { x: (-this.width / 8), y: 0 },
      bodyB: boxA,
      pointB: { x: (-this.width / 4) , y: 0 },
    });
    let constraint2 = Matter.Constraint.create({
      bodyA: ground,
      pointA: { x: (-3*this.width/8), y: 0 },
      bodyB: boxA,
      pointB: { x: (-this.width / 4), y: 0 },
    });

    let constraint3 = Matter.Constraint.create({
      bodyA: ground,
      pointA: { x: (this.width / 8), y: 0 },
      bodyB: boxA,
      pointB: { x: (this.width / 4), y: 0 },
    });
    let constraint4 = Matter.Constraint.create({
      bodyA: ground,
      pointA: { x: (3*this.width / 8), y: 0 },
      bodyB: boxA,
      pointB: { x: (this.width / 4), y: 0 },
    });
    let mouse = Matter.Mouse.create(render.canvas);
    this.absolute = mouse.absolute;

    let mouseConstraint = Matter.MouseConstraint.create(engine, {
      mouse: mouse,
      constraint: {
        render: { visible: true },
      },
    });
    this.sizeAvailable = (this.width / 4) * 2;
    Matter.Events.on(mouseConstraint, "startdrag", (event) => {
      if (event.body.label == "boxA") {
        let position = event.mouse.absolute;
        this.calculateSizes(position.x, position.y, this.width / 4, (this.width / 4) * 3);
      }
      
    });

    Matter.Events.on(mouseConstraint, "enddrag", (event) => {
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
      mouseConstraint,
      constraint1,
      constraint2,
      constraint3,
      constraint4,
    ]);

    this.createLabel("auto", "auto", ((this.width) / 8) * 2 ,this.height - this.height/10, "Ra", "A")
    this.createLabel("auto", "auto", ((this.width) / 8) * 6 ,this.height - this.height/10, "Rb", "B")

    //this.bodiesDom = []
    this.bodiesDom = document.querySelectorAll(".label-p4");
    //console.log(this.bodiesDom)
    this.bodies = [];
    for (var i = 0, l = this.bodiesDom.length; i < l; i++) {
      var body = Matter.Bodies.rectangle(0, 0, 0, 0);
      this.bodiesDom[i].id = body.id;
      this.bodies.push(body);
    }
    Matter.World.add(engine.world, this.bodies);
    window.requestAnimationFrame(this.update);

    Matter.Runner.run(engine);
    Matter.Render.run(render);
  },
  methods: {
    calculateSizes(x, y, p1, p2) {
      this.clickX = x;
      this.clickY = y;
      if (x >= p1 && x <= p2) {
        this.inRange = true;
        this.da = x - p1;
        this.db = this.sizeAvailable - this.da;
      } else {
        this.inRange = false;
        this.ra = 0;
        this.rb = 0;
        this.f = 0;
      }
    },
    calculateReactions(y) {
      if (this.inRange) {
        this.f = y - this.clickY;
        this.rb = (this.f * this.da) / this.sizeAvailable;
        this.ra = this.f - this.rb;
      }
    },
    update() {
      for (var i = 0, l = this.bodiesPositions.length; i < l; i++) {
        var bodyDom = this.bodiesDom[i];
        var body = null;
        for (var j = 0, k = this.bodies.length; j < k; j++) {
          if (this.bodies[j].id == bodyDom.id) {
            body = this.bodies[j];
            break;
          }
        }

        if (body === null) continue;
        //  console.log(this.bodiesPositions[i])

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
      //  console.log(x,y)
      var label = document.createElement("div");
      label.id = id;
      label.className = "label-p4";
      label.style.cssText = "height:" + height + "px; width:" + width + "px;"
      var content = document.createTextNode(text);
      label.appendChild(content); //adiciona o nó de texto à nova div criada
      this.bodiesPositions.push({x:x, y:y})
      document.getElementById("label-holder-4").appendChild(label);
    },
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

.label-p4 {
  justify-content: center;
  align-items: center;
  display: flex;
  color: white;
  z-index: 1;
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

  min-width: 700px;
  min-height: 85vh;
  /* top: 50%; */
  /* left: 50%; */
  /* transform: translate(-50%, -50%); */
}
</style>
