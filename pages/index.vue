<template>
  <v-container>
    <v-alert
      color="primary"
      dark
      icon="mdi-newspaper"
      border="left"
    >
    <b>Faker</b> est un système informatique doté d'une "intelligence" capable de vous aider à évaluer la pertinence des articles de presse et d'aider les utilisateurs à mieux choisir les ressources, vérifiées et fiables sur le net.
    </v-alert>
    <!-- <p class="ml-10">Pour utiliser notre outil, copier/coller les articles que vous voulez analyser ci-dessous (au format texte) ou via le lien :</p> -->
    
    

    <v-card>
      <v-card-title class="headline">
        Article
      </v-card-title>

      <v-card-subtitle>Pour utiliser notre outil, copier/coller les articles que vous voulez analyser ci-dessous (au format texte) ou l'extraire via le lien :</v-card-subtitle>
      <v-container>
        <v-row>
          <v-col
            cols="9">
            <v-text-field
              v-model="message4"
              label="Lien de l'article"
              placeholder="Coller l'URL de l'article"
              filled
              outlined
              clearable
            ></v-text-field>
          </v-col>
          <v-col>
            <v-card-actions class="justify-end">
              <v-btn
                class="rounded-lg"
                color="primary"
                dark>
                Extraire l'article
              </v-btn>
            </v-card-actions>
          </v-col>
        </v-row>
      </v-container>
      <v-divider></v-divider>
      
      <v-container>
        <v-textarea
          name="input-7-1"
          label="Corps de l'article"
          value=""
          auto-grow
          filled
          hint=""
        ></v-textarea>
      </v-container>
      
      <v-card-text>
        <v-fab-transition>
          <v-btn
            color="pink"
            dark
            absolute
            bottom
            right
            :disabled="dialog"
            :loading="dialog"
            @click="dialog = true"
          >
            <v-icon>mdi-text-box-search-outline</v-icon> Lancer l'analyse
          </v-btn>
        </v-fab-transition>
      </v-card-text>
      </v-card>

    <!-- Chargement -->
    <v-dialog
      v-model="dialog"
      hide-overlay
      persistent
      width="300"
    >
      <v-card
        color="primary"
        dark
      >
        <v-card-text>
          Analyse en cours. Veuillez patienter.
          <v-progress-linear
            indeterminate
            color="white"
            class="mb-0"
          ></v-progress-linear>
        </v-card-text>
      </v-card>
    </v-dialog>

    <!-- Résultats -->
    <v-dialog
      v-model="dialog_resultat"
      width="70%"
    >
      <v-card>

        <v-container>
          <v-row>
            <v-col class="ml-1">
              <v-row>
                <v-alert
                  color="info"
                  dark
                  dense
                  icon="mdi-cloud-outline"
                  border="left"
                  class="ml-3 mb-n3 mt-3 text-center secondary rounded-tl-0 rounded-bl-0 rounded-br-0 rounded-tr-xl"
                >
                  Nuages de mots
                </v-alert>
              </v-row>
              <v-row>
                <v-col>
                <v-banner elevation="1">
                  
                <vue-word-cloud
                  class="ma-4"
                  style="
                      height: 150px;
                      width: 100%;
                      "
                  :words="[['Trump', 19], ['said', 3], ['fake', 7], ['covid', 15],['Biden', 21], ['5G', 10], ['vaccin', 25], ['scam', 12]]"
                  :color="([, weight]) => weight > 10 ? 'black' : weight > 5 ? 'RoyalBlue' : 'Indigo'"
                  font-family="Roboto"
                  >
                  <template slot-scope="{text, weight, word}">
                    <v-tooltip
                        v-model="show"
                        top
                      >
                        <template v-slot:activator="{ on, attrs }">
                            <div v-on="on" :title="weight" style="cursor: pointer;" @click="onWordClick(word)">
                              {{ text }}
                            </div>
                        </template>
                        <div><b>{{ text }}</b> est présent {{ weight }} fois dans l'article</div>
                      </v-tooltip>
                  </template>
                </vue-word-cloud>

                </v-banner>
                </v-col>
              </v-row>
          </v-col>
          <v-col>
            test
          </v-col>
          </v-row>
        </v-container>



        <v-container>
          <v-row>
            <v-col>
              <v-row>

                <v-alert
                  color="info"
                  dark
                  dense
                  border="left"
                  class="ml-4 mt-4 mb-0 text-center secondary rounded-tl-0 rounded-bl-0 rounded-br-0 rounded-tr-xl"
                  icon="mdi-flask"
                >
                  Analyse globale 
                </v-alert>
              </v-row>
              <v-row>
              <v-banner class="ml-4" elevation="3">
                <v-row>
                  <v-col class="m-2" cols="4">
                  <!-- Véracité -->
                    <!-- Cercle -->
                    <v-progress-circular
                      :rotate="90"
                      :size="110"
                      :width="5"
                      :value="80"
                      color="teal"
                    >
                      Véracité
                    </v-progress-circular>
                  </v-col>
                  <v-col>
                    <!-- Info -->
                    <v-alert
                      border="left"
                      color="green"
                      icon="mdi-check"
                      outlined
                      text
                    >
                      Le véracité correspond aux proportion d'informations jugé véridique par rapport aux informations jugé fausse 
                    </v-alert>
                  </v-col>
                </v-row>

                <v-divider class="my-5"></v-divider>
                <v-row>
                  <v-col class="m-2" cols="4">
                  <!-- Objectivité -->
                    <!-- Cercle -->
                    <v-progress-circular
                      :rotate="90"
                      :size="110"
                      :width="5"
                      :value="40"
                      color="red"
                    >
                      Objectivité
                    </v-progress-circular>
                  </v-col>
                  <v-col>
                    <!-- Info -->
                    <v-alert
                      border="right"
                      color="blue"
                      icon="mdi-compass"
                      outlined
                      text
                    >
                      L'objectivité est un indice d'impartialité à propos de l'écriture de l'article
                    </v-alert>
                  </v-col>
                </v-row>
              </v-banner>
              </v-row>
            </v-col>
              
            <v-col>
                <v-banner elevation="1" style="height=100%;">
                  <v-list-item>
                    <v-list-item-icon>
                      <v-icon>mdi-newspaper</v-icon>
                    </v-list-item-icon>
                    <v-list-item-content>
                      <v-list-item-title class="title">
                        Titre
                      </v-list-item-title>
                      <v-list-item-subtitle>
                        article
                      </v-list-item-subtitle>
                    </v-list-item-content>
                  </v-list-item>
                  <v-divider></v-divider>
                </v-banner>
            </v-col>
          </v-row>
        </v-container>
        <v-divider></v-divider>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            color="primary"
            text
            @click="dialog_resultat = false"
          >
            Fermer
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>


    
  </v-container>
</template>

<script>
import VueWordCloud from 'vuewordcloud';
export default {
    components: {
      [VueWordCloud.name]: VueWordCloud,
    },
    data () {
      return {
        dialog: false,
        dialog_resultat: false,
        words: [['romance', 19], ['horror', 3], ['fantasy', 7], ['adventure', 3]]
      }
    },
    methods: {
      onWordClick: function(word) {
        
      }
    },
    watch: {
      dialog (val) {
        if (!val) return
        setTimeout(() => {
          this.dialog = false
          this.dialog_resultat = true
          }, 2000)
        
      },
    },
  }
</script>
