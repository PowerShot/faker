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
                <v-text-field
                  v-model="lien"
                  label="Lien de l'article"
                  placeholder="Coller l'URL de l'article"
                  filled
                  outlined
                  clearable
                ></v-text-field>
              
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
          </v-container>
          

          <v-divider></v-divider>
          <v-alert
            class="mt-2 ml-2"
            color="blue-grey"
            dark
            icon="mdi-information-outline"
            border="top"
            outlined
          >
            Seul les articles en <b>anglais</b> sont géré par notre systèmes pour l'instant.
          </v-alert>
          
          <!-- Corps de l'article -->
          <div v-show="erreur_langue">
            <div class="red darken-4 text-center py-2">
              <span class="white--text">Désolé il nous est impossible pour l'instant d'analyser d'autre langues que l'anglais. (langue détecté : '{{ this.lang_detect }}')</span>
              
            </div>
            <v-progress-linear color="red lighten-2" value="0" height="5"></v-progress-linear>
          </div>
          <v-container>
            <v-textarea
              clearable
              clear-icon="mdi-close-circle"
              label="Corps de l'article"
              v-model="article_text"
              auto-grow
              filled
              append
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
                
                @click="articleAnalyseEach(article_text)"
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
            @click="e1 = 1"
          >
            <v-icon
              left
              dark
            >
              mdi-arrow-left
            </v-icon>
            retour à la saisie de l'article
          </v-btn>
          <v-spacer></v-spacer>


          <v-dialog
            transition="dialog-bottom-transition"
            max-width="600"
          >
            <template v-slot:activator="{ on, attrs }">
              <v-btn
                color="primary"
                text
                v-bind="attrs"
                v-on="on"
              >
                <v-icon
                  left
                  dark
                >
                  mdi-comment-question
                </v-icon>
                donnez votre avis
              </v-btn>
              </template>
              <template v-slot:default="dialog">
                <v-card>
                  <v-toolbar
                    color="primary"
                    dark
                  >Formulaire de contact</v-toolbar>
                  <v-card-text>
                    <v-form
                      ref="form"
                      v-model="valid"
                      lazy-validation
                    >
                      <v-subheader class="pl-0">
                        Quel note donneriez vous aux résultats ?
                      </v-subheader>
                      <v-slider
                        v-model="slider"
                        :thumb-size="24"
                        thumb-label="always"
                        thumb-color="grey lighten-3"
                        color="green"
                        track-color="red"
                        height="50px"
                        loader-height="30px"
                      >
                      

                        <template v-slot:thumb-label="{ value }">
                          {{ satisfactionEmojis[Math.min(Math.floor(value / 10), 9)] }}
                        </template>
                      </v-slider>

                      <v-text-field
                        label="Votre avis (facultatif)"
                        v-model="comment"
                      ></v-text-field>

                      <v-btn
                        color="success"
                        class="mr-4"
                        @click="sendFeedback();dialog.value = false;"
                      >
                        Envoyer
                      </v-btn>
                      <v-btn
                        class="mr-4"
                        @click="$refs.form.reset();dialog.value = false;"
                      >
                        Fermer
                      </v-btn>

                    </v-form>
                  </v-card-text>
                </v-card>
              </template>
            </v-dialog>
          
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
            
            <v-row>
              <v-card>
                <v-toolbar
                  flat
                  color="primary"
                  dark
                >
                  <v-toolbar-title>Voila les outils mis à votre dispositions</v-toolbar-title>
                </v-toolbar>
                <v-tabs :vertical="isVertical">
                  <v-tabs-slider></v-tabs-slider>
                  <small class="text-uppercase mt-5">synthèse</small>
                  <v-tab>
                    <v-icon left>
                      mdi-flask
                    </v-icon>
                    <small>
                      Analyse globale
                    </small> 
                  </v-tab>
                  <small class="text-uppercase mt-5">résultats avancées</small>
                  <v-tab>
                    <v-icon left>
                      mdi-pencil-outline
                    </v-icon>
                    <small>
                      Article analysé
                    </small> 
                  </v-tab>
                  <v-tab>
                    <v-icon left>
                      mdi-table-check
                    </v-icon>
                    <small>
                      Analyse par bloc
                    </small> 
                  </v-tab>
                  <v-tab disabled>
                    <v-icon left>
                      mdi-newspaper
                    </v-icon>
                    <small>
                      articles en lien
                    </small> 
                  </v-tab>

                  <v-tab-item>
                    <v-card flat>
                      <v-card-text>
                        <v-alert
                          class="mt-2 ml-2"
                          color="blue-grey"
                          dark
                          icon="mdi-information-outline"
                          border="top"
                          outlined
                        >
                          Il s'agit d'une <b>synthèse globale</b> de l'analyse faite par l'IA. Si vous souhaitez en savoir plus vous pouvez aller aux différents onglets en dessous de "<b>résultats avancées</b>.
                          Si vous avez des remarques ou un avis à donner, vous pouvez le faire sur le bouton "<b>donnez votre avis</b>".
                        </v-alert>
                        <v-row>
                          <v-col class="m-2" cols="4">
                          <!-- Véracité -->
                            <!-- Cercle -->
                            <v-progress-circular
                              :rotate="90"
                              :size="150"
                              :width="15"
                              :value="veracity"
                              :color="getColorResults(veracity)"
                            >
                              <b>Véracité</b> : {{ this.veracity }}%
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
                              :size="150"
                              :width="15"
                              :value="objectivity"
                              :color="getColorResults(objectivity)"
                            >
                              <b>Objectivité</b> : {{this.objectivity}}%
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
                        
                <!-- fin contenu -->
                      </v-card-text>
                    </v-card>
                  </v-tab-item>
                  <v-tab-item>
                    <v-alert
                      class="mt-2 ml-2"
                      color="blue-grey"
                      dark
                      icon="mdi-information-outline"
                      border="top"
                      outlined
                    >
                    Vous avez accès au <b>nuage de mots</b> les plus utilisées dans l'article. Vous pouvez cliquer sur le mot du nuage pour qu'il apparaisse en surbrillance dans l'article. Appuyez à nouveau pour retirer la surbrillance.
                    </v-alert>


                    <v-card flat>
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
                            <!-- <v-tooltip
                                v-model="show"
                                top
                              >
                                <template v-slot:activator="{ on }"> -->
                                    <div v-on="on" :title="weight" style="cursor: pointer;" @click="onWordClick(word)">
                                      {{ text }}
                                    </div>
                                <!-- </template>
                                <div><b>{{ text }}</b> est présent {{ weight }} fois dans l'article</div>
                              </v-tooltip> -->
                          </template>
                        </vue-word-cloud>

                        <v-alert
                          color="info"
                          dark
                          dense
                          icon="mdi-pencil-outline"
                          border="left"
                          class="ml-3 mb-n3 mt-6 text-center secondary rounded-tl-0 rounded-bl-0 rounded-br-0 rounded-tr-xl"
                        >
                          Article
                        </v-alert>
                        <v-card-text>
                        
                          <v-card class="mt-3 ml-3" height="320px" :key="key_article">
                            <v-responsive
                              max-height="100%"
                              class="overflow-y-auto pa-3">
                              <p class="text-caption" v-for="paragraphe in article_paragraphes_click">
                                <span class="mr-4"></span> <span v-html="paragraphe"></span>
                              </p>
                            </v-responsive>
                          </v-card>
                        
                      </v-card-text>
                    </v-card>
                  </v-tab-item>
                  <v-tab-item>
                    <v-card flat>
                      <v-card-text>
                        <v-alert
                          class="mt-2 ml-2"
                          color="blue-grey"
                          dark
                          icon="mdi-information-outline"
                          border="top"
                          outlined
                        >
                          Chaque paragraphe a été analysé indépendament afin d'avoir un aperçu des émotions que transmettait l'article.
                          Un pourcentage <b>négatif</b> équivaux à un sentiments négatif et inversement pour un pourcentage <b>positif</b>.
                        </v-alert>
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
                        </v-data-table>
                      </v-card-text>
                    </v-card>
                  </v-tab-item>
                  <v-tab-item>
                    <v-card flat>
                    </v-card>
                  </v-tab-item>
                </v-tabs>
              </v-card>
            </v-row>
          </v-container>
        </v-card>
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
        ],
        objectivity: 0,
        veracity: 0,
        timer_alerte: 0,
        loading_API: false,
        loading_extraction: false,
        erreur_extraction: false,
        erreur_langue: false,
        lang_detect: '',
        e1: 1,
        lien: '',
        dialog: false,
        article_text: '',
        comment: '',
        dialog_resultat: false,
        article_paragraphes: [],
        article_paragraphes_click: [],
        article_analyse_json: [],
        words_cloud: [],
        key_article: 0,
        isVertical: true,
        satisfactionEmojis: ['😭', '😢', '☹️', '🙁', '😐', '🙂', '😊', '😁', '😄', '😍'],
        slider: 50,
      }
    },
    methods: {
      onWordClick: function(word) {
        var input = word[0]
        var output = '<mark>' + word[0] + '</mark>'


        // Check si le mot a déjà été surligné
        for(var i = 0; i < this.article_paragraphes_click.length; i++) {
          if(this.article_paragraphes_click[i].includes(output)) {
            var temp = input
            input = output
            output = temp
            break
          }
        }

        

        for(var i = 0; i < this.article_paragraphes_click.length; i++) {
          this.article_paragraphes_click[i] = this.article_paragraphes_click[i].replaceAll(input, output)
        }
        this.key_article = this.key_article + 1
        

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

      getColorResults: function (val) {
        if (val > 75) return 'green lighten-1'
        else if (val > 50) return 'green lighten-4'
        else if (val > 25) return 'deep-orange lighten-4'
        else if (val > 0) return 'deep-orange lighten-1'
        else return 'red'
      },

      extractionArticle: function () {
        this.loading_extraction = true
        this.erreur_extraction = false
        this.erreur_langue = false

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
        if(global_analysis["language"] != 'en') throw global_analysis["language"]
        global_analysis["text"] = text

        // articles_list
        var tab = text.split('\n\n')
        var articles_list = []
        var s= document.getElementById("avancement");
        for(var i=0; i<tab.length; i++){
          
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
        
        this.erreur_langue = false
        this.erreur_extraction = false

        if(this.article_text.length < 100) {
          alert("Veuillez insérer un article avec au moins 100 caractères")
          return
        }
        this.dialog = true
        

        this.articleAnalyseAPI(results)
        .then(result => {
          


          this.words_cloud = []
          for(var [key, value] of Object.entries(result[1]["Mots"])){
            this.words_cloud.push([key, value])
          }

          this.article_paragraphes = result[1]["text"].split('\n\n')
          this.article_paragraphes_click = this.article_paragraphes

          this.objectivity = 100 - Math.abs(result[1]["polarity"])
          this.veracity = result[1]["proba_true"]

          this.article_analyse_json = result
          this.dialog = false
          this.dialog_resultat = true
          this.erreur_extraction = false
          this.e1 = 2
          console.log(result)
          
        }).catch(err => {
          console.log(err)
          this.erreur_langue = true
          this.lang_detect = err
          this.dialog = false
          this.dialog_resultat = true
          this.erreur_extraction = false
        })
        this.$nuxt.refresh()
        return
      },

      onResize() {
        if(window.innerWidth < 800) this.isVertical = false
        else this.isVertical = true
      },

      sendFeedback: function () {
        let text = 'AVIS ANALYSE\nNote de satisfaction :' + parseInt(this.slider) + '%\n' + 'Commentaire : ' + this.comment + '\n' + 'url :' + this.lien + '\n' + 'article :\n' + this.article_text
        // global analysis
        var rawResponse = fetch('https://corsefaker.herokuapp.com/https://fakernews.herokuapp.com/send', {
          method: 'POST',
          headers: {
            'Accept': 'application/json',
            'Content-Type': 'application/json'
          },
          body: JSON.stringify({'form': text})
        })
        this.$refs.form.reset()
      },

    },
    mounted: function() {
      this.onResize()
      this.$nextTick(() => {
      window.addEventListener('resize', this.onResize);
    })
    },
    beforeDestroy() { 
    window.removeEventListener('resize', this.onResize); 
  },

    watch: {
      dialog (val) {
        if (!val) return
      },
    },
  }
</script>