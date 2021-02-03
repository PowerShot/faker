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

    <!-- Entrée -->
    <v-card>
      <v-card-title class="headline">
        Article
      </v-card-title>

      <v-card-subtitle>
        Pour utiliser notre outil, copier/coller les articles que vous voulez analyser ci-dessous (au format texte) ou l'extraire via le lien :
      </v-card-subtitle>

      <!-- URL -->
      <v-container>
        <v-row>
          <v-col
            cols="9">
            <v-text-field
              v-model="lien"
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
                v-on:click="extractionArticle()"
                dark>
                Extraire l'article
              </v-btn>
            </v-card-actions>
          </v-col>
        </v-row>
      </v-container>

      <v-divider></v-divider>
      
      <!-- Corpds de l'article -->
      <v-container>
        <v-textarea
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
            @click="dialog = true"
          >
            <v-icon>mdi-text-box-search-outline</v-icon> Lancer l'analyse
          </v-btn>
        </v-fab-transition>
      </v-card-text>
    </v-card>

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
      <!-- Card -->
      <v-card class="rounded-l-xl rounded-tr-0 rounded-br-0">
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
                      animation-duration="2000"
                      rotation="4"
                      spacing="1"
                      rotation-unit="deg"
                      :words="[['Trump', 19], ['said', 3], ['fake', 7], ['covid', 15],['Biden', 21], ['5G', 10], ['vaccin', 25], ['scam', 12]]"
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
                  <p class="text-caption">
                    <span class="mr-4"></span> Auctor. Nullam ultrices elementum. <mark>Trump</mark> ipsum malesuada, neque felis, placerat commodo adipiscing a. Pede netus primis turpis molestie. Pharetra parturient magna sem sociis urna. Consequat. Integer curabitur erat platea aenean, interdum habitasse eget pretium torquent. Suspendisse, libero. Curabitur sodales ultrices iaculis mauris per, nec Nisi nec commodo cursus. Cras eu sem, conubia varius fringilla aenean venenatis amet a consectetuer phasellus laoreet accumsan imperdiet phasellus adipiscing facilisis elit dui. Habitasse elementum mattis eu elementum morbi vehicula vulputate suscipit pellentesque ac habitant dolor vivamus pellentesque fringilla hymenaeos risus pretium nullam aliquet. Sapien taciti. Phasellus nam, taciti nulla semper consectetuer nisl eget nulla placerat fames magnis etiam non. Sociosqu congue conubia nascetur leo vulputate sollicitudin. Consectetuer nam ornare, lacinia facilisi. Aliquam iaculis. Maecenas ipsum ipsum risus libero sociis purus lacus luctus nonummy massa duis class nunc, aliquet hendrerit vulputate pede ultricies sociis placerat, nulla. Varius integer. Natoque velit vitae mus non erat imperdiet a, hymenaeos praesent luctus nisi consectetuer quisque mus urna dis vulputate est natoque ad purus. Imperdiet lectus nonummy ultrices fringilla elementum sem semper. Augue inceptos aliquam, nisi. Pellentesque rhoncus. Nullam inceptos et rutrum class tortor tempus fermentum sem, orci, ante montes donec consectetuer nascetur. Laoreet. Penatibus torquent amet mollis tortor hendrerit parturient. Est facilisi senectus sapien Taciti commodo magna velit. Malesuada etiam ridiculus magnis rhoncus dictum ipsum ad commodo mauris. Placerat ut. Magnis mollis arcu at netus dui mattis rutrum risus nostra interdum. Malesuada eget Auctor euismod imperdiet consectetuer ut class est tempor pellentesque amet fusce amet eu sem, vivamus libero fusce, egestas erat potenti sit habitasse, at, nulla vitae facilisis mattis sapien pharetra nascetur nulla purus. Purus faucibus sagittis donec sodales dictum maecenas dis nisi turpis ultrices pharetra purus aptent convallis non mattis sagittis dignissim ullamcorper aptent ligula fringilla nullam integer etiam fringilla nisl. Aliquet natoque viverra ligula potenti lorem morbi praesent laoreet viverra ipsum sed sociis etiam. Magnis id tristique praesent magnis purus laoreet. Accumsan nec. Phasellus porta pulvinar ante et feugiat tellus nisi pellentesque interdum. Orci ad id. Vel habitant litora. Eros taciti hymenaeos rhoncus. Auctor risus Dapibus cras In Aenean natoque adipiscing venenatis sed eu fermentum, tempor elementum accumsan cum accumsan. Feugiat id class. In vel tellus. Cras ultrices viverra. Non hendrerit mattis suspendisse duis cubilia ultrices sollicitudin a penatibus fusce fringilla neque vulputate nisi in eleifend tincidunt cubilia quam feugiat hendrerit. Eleifend nunc maecenas molestie. Turpis orci primis quam sapien suscipit pulvinar id. Nec class fringilla nonummy sed varius suscipit mus dictumst nascetur odio egestas praesent gravida proin tristique. Justo. Aptent orci nulla nisl nibh fusce massa placerat ac. Scelerisque egestas leo sed nam primis. Diam sociis pretium facilisis orci hendrerit. Eget. Fames hymenaeos ultrices hymenaeos justo sapien. Cursus Ultricies tempus volutpat. Sem diam vestibulum cum massa faucibus pretium diam, rhoncus venenatis tempus velit. Tortor nostra commodo ultrices cubilia a ut etiam mattis, integer conubia, dis neque mi sapien ultricies. Scelerisque potenti. Ut, enim commodo Neque morbi, cursus habitasse posuere. A porta. Nonummy. Interdum dis non. Dis. Eleifend pede gravida egestas et pede bibendum tortor porta vivamus tortor inceptos viverra urna inceptos vitae lacinia, etiam suspendisse per molestie potenti praesent fringilla dictum viverra platea turpis vitae aliquet praesent donec odio hymenaeos imperdiet habitasse conubia. Volutpat mattis ad vivamus lacinia vestibulum lectus iaculis. Nascetur luctus quisque sem dictumst, sapien placerat lacinia hendrerit vestibulum tempus, egestas. Risus fames ipsum massa penatibus mus augue tristique ultrices inceptos ad torquent vitae phasellus lectus a nunc urna blandit ac dolor justo ac porttitor ultricies ultrices primis fringilla leo facilisi venenatis pretium purus, hymenaeos neque. Justo pretium taciti sodales felis per vulputate nostra risus dis consectetuer facilisis, placerat at laoreet primis donec curabitur. Praesent purus. Faucibus facilisis vestibulum cubilia rhoncus rutrum semper adipiscing lacus molestie pharetra, sed. Risus dapibus ipsum. Fusce viverra quis morbi primis. Convallis pretium Nulla hendrerit elit faucibus eu dis. Lacinia donec placerat leo Praesent adipiscing consectetuer consequat, consectetuer montes nec elit turpis, tristique ac fringilla faucibus sollicitudin mauris neque lacinia natoque ridiculus, eget torquent orci cras. Facilisi potenti auctor quis tellus vulputate. Habitant risus. Vitae sollicitudin ac netus erat. Ultricies class neque velit fusce penatibus sociosqu augue. Vestibulum, amet integer vivamus. Penatibus malesuada. Nisl aptent.
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

        <!-- Ligne -->
        <v-divider class="mt-4"></v-divider>

        <!-- Bouton fermer -->
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
        lien: '',
        dialog: false,
        article_text: '',
        dialog_resultat: false,
        words: [['romance', 19], ['horror', 3], ['fantasy', 7], ['adventure', 3]]
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
      extractionArticle: function () {
        this.extractionArticleURL(this.lien)
          .then(result => this.article_text = result)
      },
      extractionArticleURL: async (url) => {
        try {
          const response = await fetch('https://cors-anywhere.herokuapp.com/' + url);
          const text = await response.text();
          var test = text.match(/<article[^<>]*>([\s\S]*?)<\/article>/);
          
          var htmlString = test[1]
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


          return paragraphes.join("\n\n")
        }
        catch
        {
          return ''
        }
      }
    },
    mounted: function() {
      
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