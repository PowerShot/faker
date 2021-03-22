<template>
  <v-container>
    <!-- Info Faker -->
    <v-alert
      color="primary"
      dark
      icon="mdi-newspaper"
      border="left"
    >
    <b>Faker</b> est un système informatique doté d'une "intelligence" capable de vous aider à évaluer la pertinence des articles de presse et d'aider les utilisateurs à mieux choisir les ressources, vérifiées et fiables sur le net.
    </v-alert>

    <v-stepper v-model="e1">
    <v-stepper-header>
      <v-stepper-step
        :complete="e1 > 1"
        step="1"
      >
        Article
        <small>Saisie de l'article</small>
      </v-stepper-step>

      <v-divider></v-divider>

      <v-stepper-step
        :complete="e1 > 2"
        step="2"
      >
        Analyse
        <small>Résultats</small>
      </v-stepper-step>

      <v-divider></v-divider>

    </v-stepper-header>

    <v-stepper-items>
      <v-stepper-content step="1">
        <v-card>
          <v-card-title class="headline">
            Article
          </v-card-title>

          <v-card-subtitle>
            Pour utiliser notre outil, copier/coller les articles que vous voulez analyser ci-dessous (au format texte) ou l'extraire via le lien :
          </v-card-subtitle>
          <div v-show="erreur_extraction">
            <div class="red darken-4 text-center py-2">
              <span class="white--text">Il nous est impossible d'extraire l'article via le lien URL que vous avez entré, veuillez le copier/coller dans le champs 'Corps de l'article'</span>
              
            </div>
            <v-progress-linear color="red lighten-2" value="0" height="5"></v-progress-linear>
          </div>

          <!-- URL -->
          <v-container>
            <v-row>
              <v-col
                cols="10">
                <v-text-field
                  v-model="lien"
                  label="Lien de l'article"
                  placeholder="Coller l'URL de l'article"
                  filled
                  outlined
                  clearable
                ></v-text-field>
              </v-col>
              
              <v-col
                cols="2">
                <v-card-actions class="justify-end">
                  <v-btn
                    class="rounded-lg"
                    color="primary"
                    :loading="loading_extraction"
                    v-on:click="extractionArticle()"
                    dark
                    >
                    Extraire l'article
                  </v-btn>
                </v-card-actions>
              </v-col>
            </v-row>
          </v-container>
          

          <v-divider></v-divider>
          
          <!-- Corps de l'article -->
          <v-container>
            <v-textarea
              clearable
              clear-icon="mdi-close-circle"
              name="input-7-1"
              label="Corps de l'article"
              :value="article_text"
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
                
                @click="articleAnalyseEach(article_text);dialog = true"
              >
                <v-icon>mdi-text-box-search-outline</v-icon> Lancer l'analyse
              </v-btn>
            </v-fab-transition>
          </v-card-text>
        </v-card>
      </v-stepper-content>

      <v-stepper-content step="2">
        <!-- Card -->
      <v-card class="rounded-tl-xl rounded-tr-0 rounded-br-0">
        <!-- Bouton fermer -->
        <v-card-actions>
          <v-btn
            color="primary"
            text
            @click="e1 = 1; articleAnalyseEach()"
          >
            <v-icon
              left
              dark
            >
              mdi-arrow-left
            </v-icon>
            retour à la saisie de l'article
          </v-btn>
        </v-card-actions>
        <v-container>

          <!-- Info resultats -->
          <v-row>
            <v-card
              class="rounded-tl-xl rounded-b-0 rounded-r-0"
              width="100%"
              color="#385F73"
              dark
            >
              <v-card-title class="headline">
                Résultat de l'analyse
              </v-card-title>

              <v-card-subtitle class="ml-4 mt-2">
                Le résultat donné a été effectué par notre IA entraîné, il se peux que par des facteurs aléatoire le résultat soit erroné.
              </v-card-subtitle>

              </v-card>

            </v-row>
            
            <!-- Ligne 1 -->
            <v-row>

              <!-- Nuage de mots -->
              <v-col class="ml-1">
                <!-- Tag -->
                <v-row>
                  <v-alert
                    color="info"
                    dark
                    dense
                    icon="mdi-cloud-outline"
                    border="left"
                    class="ml-3 mb-n3 mt-6 text-center secondary rounded-tl-0 rounded-bl-0 rounded-br-0 rounded-tr-xl"
                  >
                    Nuage de mots
                  </v-alert>
                </v-row>
                
                <!-- Contenu -->
                <v-row>
                  <v-col>
                    <v-banner elevation="1">
                      
                      <vue-word-cloud
                        class="ma-4"
                        style="
                            height: 255px;
                            width: 90%;
                            "
                        :animation-duration="2000"
                        :rotation="4"
                        :spacing="1"
                        rotation-unit="deg"
                        :words="words_cloud"
                        :color="([, weight]) => weight > 10 ? 'black' : weight > 5 ? 'RoyalBlue' : 'Indigo'"
                        font-family="Roboto"
                        >
                        <template slot-scope="{text, weight, word}">
                          <v-tooltip
                              v-model="show"
                              top
                            >
                              <template v-slot:activator="{ on }">
                                  <div v-on="on" :title="weight" style="cursor: pointer;" @click="onWordClick(word)">
                                    {{ text }}
                                  </div>
                              </template>
                              <div><b>{{ text }}</b> est présent {{ weight }} fois dans l'article</div>
                            </v-tooltip>
                        </template>
                      </vue-word-cloud>

                    </v-banner>
                  </v-col>
                </v-row>
              </v-col>

              <!-- Article analysé -->
              <v-col>

                <!-- Tag -->
                <v-row>
                    <v-alert
                      color="info"
                      dark
                      dense
                      icon="mdi-pencil-outline"
                      border="left"
                      class="ml-3 mb-n3 mt-6 text-center secondary rounded-tl-0 rounded-bl-0 rounded-br-0 rounded-tr-xl"
                    >
                      Article analysé
                    </v-alert>
                </v-row>
                
                <!-- Contenu -->
                <v-row>
                  <v-card class="mt-3 ml-3" height="320px">
                    <v-responsive
                      max-height="100%"
                      class="overflow-y-auto pa-3">
                      <p class="text-caption" v-for="paragraphe in article_paragraphes">
                        <span class="mr-4"></span> {{ paragraphe }}
                        </p>
                    </v-responsive>
                  </v-card>
                </v-row>

              </v-col>
            
            </v-row>
          </v-container>

          <!-- 2e ligne -->
          <v-container>

            <!-- Analyse globale -->
            <v-row>
              <v-col>

                <!-- Tag -->
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

                <!-- Contenu -->
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
                <!-- fin contenu -->


                </v-row>
              </v-col>
              

              <!-- Articles en lien -->
              <v-col>

                <!-- Tag -->
                <v-row>
                  <v-alert
                    color="info"
                    dark
                    dense
                    border="left"
                    class="ml-6 mb-0 mt-4 text-center secondary rounded-tl-0 rounded-bl-0 rounded-br-0 rounded-tr-xl"
                    icon="mdi-newspaper"
                  >
                    Articles en lien
                  </v-alert>
                </v-row>
              
                <!-- Contenu -->
                <v-row class="mt-0 ml-auto">
                  <v-container>
                    
                    <v-card elevation="1" style="height=100%;">
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
                    </v-card></v-container>
                </v-row>
              </v-col>
            </v-row>
          </v-container>

          
          

        </v-card>
        <v-data-table
            :headers="headers"
            :items="article_analyse_json[0]"
            class="elevation-1"
          >
            <template v-slot:item.polarity="{ item }">
              <v-chip
                :color="getColorPolarity(item.polarity)"
                dark
              >
                {{ item.polarity }} %
              </v-chip>
            </template>
            <template v-slot:item.subjectivity="{ item }">
              <v-chip
                :color="getColorSubjectivity(item.subjectivity)"
                dark
              >
                {{ item.subjectivity }} %
              </v-chip>
            </template>
          </v-data-table>
      </v-stepper-content>

      <v-stepper-content step="3">
        <v-card
          class="mb-12"
          color="grey lighten-1"
          height="200px"
        ></v-card>

        <v-btn
          color="primary"
          @click="e1 = 1"
        >
          Continue
        </v-btn>

        <v-btn text>
          Cancel
        </v-btn>
      </v-stepper-content>
    </v-stepper-items>
  </v-stepper>

    

    <!-- Entrée -->
    
    <client-only placeholder="loading...">
      <!-- Chargement analyse -->
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
            <p id="avancement">Chargement des paragraphes...</p>
            <v-progress-linear
              indeterminate
              color="white"
              class="mb-0"
            ></v-progress-linear>
          </v-card-text>
        </v-card>
      </v-dialog>
    </client-only>



    
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
        headers: [
          {
            text: 'Paragraphes analysés',
            align: 'start',
            sortable: false,
            value: 'text',
          },
          { text: 'Sentiments', value: 'polarity' },
          { text: 'Subjectivité', value: 'subjectivity' },
        ],
        timer_alerte: 0,
        loading_API: false,
        loading_extraction: false,
        erreur_extraction: false,
        e1: 1,
        lien: '',
        dialog: false,
        article_text: '',
        dialog_resultat: false,
        article_paragraphes: [],
        article_analyse_json: [],
        words_cloud: []
      }
    },
    methods: {
      onWordClick: function(word) {
      },
      getParagraphs: function(htmlString) {
        const div = document.createElement("div");
        div.insertAdjacentHTML("beforeend", htmlString);
        
        return Array.from(div.querySelectorAll("p"))
                    .filter(p => p.textContent !== "") // because of the lonely </p> at the end - optional
                    .map(p => p.outerHTML);
      },

      getColorPolarity: function (val) {
        if (val > 75) return 'green lighten-1'
        else if (val > 50) return 'green lighten-2'
        else if (val > 25) return 'green lighten-3'
        else if (val > 0) return 'green lighten-4'
        else if (val == 0) return 'blue-grey lighten-5'
        else if (val > -25) return 'deep-orange lighten-3'
        else if (val > -75) return 'deep-orange lighten-2'
        else return 'deep-orange lighten-1'
      },

      getColorSubjectivity: function (val) {
        if (val > 75) return 'deep-orange lighten-1'
        else if (val > 50) return 'deep-orange lighten-2'
        else if (val > 25) return 'deep-orange lighten-3'
        else if (val > 0) return 'deep-orange lighten-4'
        else return 'blue-grey lighten-5'
      },

      extractionArticle: function () {
        this.loading_extraction = true
        this.erreur_extraction = false

        this.extractionArticleURL(this.lien)
          .then(result => {
            if(result.length > 100) {
              this.article_text = result
              
              
            } else {
              this.erreur_extraction = true
              this.timer_alerte = 0
              this.article_text = ''
            }
            this.loading_extraction = false
            
            })
        
        
          
      },
      extractionArticleURL: async (url) => {
        try {
          const response = await fetch('https://corsefaker.herokuapp.com/' + url);
          const text = await response.text();
          var test = text.match(/<article[^<>]*>([\s\S]*?)<\/article>/);
          var htmlString = (test == null ? text : test[1]) 
          const div = document.createElement("div");
          div.insertAdjacentHTML("beforeend", htmlString);
          
          var paragraphes = Array.from(div.querySelectorAll("p"))
                      .filter(p => p.textContent !== "")
                      .map(p => p.outerHTML);
          
          for(var i=0; i<paragraphes.length; i++){
            // Retrait des balises
            paragraphes[i] = paragraphes[i].replace(/(<([^>]+)>)/ig, "")
            
            // Retrait des caractères d'espacement
            paragraphes[i] = paragraphes[i].replace(/&nbsp;/g, ' ')
          }

          // On choisis seulement les paragraphe de plus de 100 caractères
          paragraphes = Array.from(paragraphes)
                          .filter(p => p.length > 100)

          //this.article_paragraphes = paragraphes
          
          return paragraphes.join("\n\n")
        }
        catch
        {
          console.log("erreur extraction")
          return ''
        }
      },

      // Résultats de l'API
      articleAnalyseAPI: async (text) =>  {

        // global analysis
        var rawResponse = await fetch('https://corsefaker.herokuapp.com/https://fakernews.herokuapp.com/predictapi', {
          method: 'POST',
          headers: {
            'Accept': 'application/json',
            'Content-Type': 'application/json'
          },
          body: JSON.stringify({'article': text})
        });
        var global_analysis = await rawResponse.json();
        global_analysis["text"] = text

        // articles_list
        var tab = text.split('\n\n')
        var articles_list = []
        var s= document.getElementById("avancement");
        for(var i=0; i<tab.length; i++){
          console.log(i,"/",tab.length)
          
          s.innerText = i + "/" + tab.length + " paragraphe(s) analysé(s)";
          var rawResponse = await fetch('https://corsefaker.herokuapp.com/https://fakernews.herokuapp.com/predictapi', {
            method: 'POST',
            headers: {
              'Accept': 'application/json',
              'Content-Type': 'application/json'
            },
            body: JSON.stringify({'article': tab[i]})
          });
          var content = await rawResponse.json();
          content["text"] = tab[i]
          articles_list.push(content)
          
        }
        s.innerText = "chargement terminé";
        return [articles_list, global_analysis];
      },

      // Analyser paragraphe par paragraphe
      articleAnalyseEach: function (results) {
        this.articleAnalyseAPI(results)
        .then(result => {
          this.article_analyse_json = result
          this.dialog = false
          this.dialog_resultat = true
          this.erreur_extraction = false
          this.e1 = 2


          this.words_cloud = []
          for(var [key, value] of Object.entries(result[1]["Mots"])){
            this.words_cloud.push([key, value])
          }

          this.article_paragraphes = result[1]["text"].split('\n\n')
          
          console.log(result)
          
        }).catch(err => console.log(err))
        this.$nuxt.refresh()
        return
      },

      clickLoading: function () {
        
      }

    },
    mounted: function() {
    },

    watch: {
      dialog (val) {
        if (!val) return
      },
    },
  }
</script>