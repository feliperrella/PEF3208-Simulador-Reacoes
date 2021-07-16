<template>
  <div>
    <v-parallax src="https://imagens.usp.br/wp-content/uploads/Pra%C3%A7a_Rel%C3%B3gio_273-19_Foto-Cec%C3%ADlia-Bastos-05.jpg">
      <v-layout white--text align-center column justify-center>
        <img :src="require('@/assets/minerva.png')" height="350">
        <h3 class=" display-3 ma-2 text-xs-center">Projeto de PEF3208</h3>
        <h2 class="subheading">Simulação 2D - Grupo 6</h2>
        
      </v-layout>
    </v-parallax>

    <h1>Membros do grupo</h1>

          <v-container class="lighten-5">
            <v-row no-gutters>
              <template v-for="(n, index) in membros">
                <v-col :key="index">
                  <v-card class="card-member">
                    <img :src='n.img' style="width: 150px ;border-radius: 50%; margin-top: 5%"/>
                    
                    <h2 class="name-member">{{ n.name }}</h2>
                  </v-card>
                </v-col>
                <v-responsive
                  v-if="index === 2"
                  :key="`width-${n}`"
                  width="100%"
                ></v-responsive>
              </template>
            </v-row>
          </v-container>

<h1>Sobre nosso projeto</h1>

    <p style="padding-left: 50px; padding-right: 50px">
      O objetivo deste projeto é, por meio de técnicas de programação, criar simulações dinâmicas e precisas de exercicios da lista P1A.
      No projeto utilizamos Javascript como nossa linguagem base, Vue.js como framework para criacao de interfaces, Matter.js como biblioteca para simulaçäo de física e LaTeX para renderizar equações.
      
    </p>

    <h1 id="estrutura-simulada">Estruturas simuladas</h1>
    
          <v-row dense>
            <v-col v-for="s in simulations" :key="s.ex" >
              <v-card class="mx-auto card-simulation">
                <v-card-text>
                  <div>Simulação</div>
                  <p class="display-1 text--primary">{{ s.name }}</p>
                  <div class="text--primary">
                    Referente ao exercicio {{ s.ex }} da lista P1A
                  </div>
                </v-card-text>
                <v-card-actions>
                  <v-btn
                    text
                    style="color: rgba(0, 76, 165)"
                    @click.stop="
                      s.dialog = true;
                      renderKey++;
                    "
                  >
                    Abrir
                  </v-btn>
                </v-card-actions>
              </v-card>
            </v-col>

            <v-dialog
              v-for="s in simulations"
              :key="s.ex + 'dialog'"
              v-model="s.dialog"
              width="90vw"
            >
              <ReactionP1A1 :key="renderKey + 'p1a1'" v-if="s.ex === 1" />
              <ReactionP1A2 :key="renderKey + 'p1a2'" v-if="s.ex === 2" />
              <ReactionP1A3 :key="renderKey + 'p1a3'" v-if="s.ex === 3" />
              <ReactionP1A4 :key="renderKey + 'p1a4'" v-if="s.ex === 4" />
              <ReactionP1A5 :key="renderKey + 'p1a5'" v-if="s.ex === 5" />
            </v-dialog>
          </v-row>
  </div>
</template>

<script>
import ReactionP1A1 from "@/components/ReactionP1A_1";
import ReactionP1A2 from "@/components/ReactionP1A_2";
import ReactionP1A3 from "@/components/ReactionP1A_3";
import ReactionP1A4 from "@/components/ReactionP1A_4.vue";
import ReactionP1A5 from "@/components/ReactionP1A_5.vue";

export default {
  name: "HelloWorld",
  props: {
  },
  components: {
    ReactionP1A1,
    ReactionP1A2,
    ReactionP1A3,
    ReactionP1A4,
    ReactionP1A5
  },
  data() {
    return {
      renderKey: 0,
      model: null,
      membros: [
        { name: "Barbara Medeiros", img: require('@/assets/menospior_babi.jpeg'), nusp: '11871393' },
        { name: "Felipe Perrella", img: require('@/assets/tireiAgora_felip.jpeg'), nusp: '11871393' },
        { name: "Marina Ribeiro", img: require('@/assets/marina.jpg'), nusp: '11871393' },
        { name: "Matheus Rosa", img: require('@/assets/fotomatheusfoto.jpeg'), nusp: '11871393' },
        { name: "Thamiris Nascimento", img: require('@/assets/thami.jpg'), nusp: '11871393' },
      ],
      simulations: [
        { name: "Viga em balanço (Peso pontual)", ex: 1, dialog: false },
        { name: "Viga em balanço (Peso distribuido)", ex: 2, dialog: false },
        { name: "Viga triangular em balanço (Peso distribuido)", ex: 3, dialog: false },
        { name: "Viga bi-apoiada (Peso pontual)", ex: 4, dialog: false },
        { name: "Viga bi-apoiada (Peso distribuido)", ex: 5, dialog: false },
      ],
    };
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

h1 {
  margin-top: 3%;
  margin-bottom: 3%;
}

.card-member {
  margin: 10px;
}

.name-member {
  margin-top: 15px;
}

.nusp-member {
  color: rgba(0, 76, 165)
}

.card-simulation {
  margin-bottom: 5%;
}


</style>
