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
          filled
          hint=""
        ></v-textarea>
      </v-container>
      
    <v-card-text style="height: 100px; position: absolute">
            <v-fab-transition>
              <v-btn
                color="pink"
                dark
                absolute
                top
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
      width="500"
    >
      <v-card>
        <v-container class="mt-5">
          <vue-word-cloud
            style="
              height: 150px;
              width: 100%;
            "
            :words="[['Trump', 19], ['said', 3], ['fake', 7], ['covid', 2]]"
            :color="([, weight]) => weight > 10 ? 'Black' : weight > 5 ? 'RoyalBlue' : 'Indigo'"
            font-family="Roboto"
          />
        </v-container>
        <v-card-text>
          Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.
        </v-card-text>
        <v-container>
          <v-row>
            <v-col>
              <v-row>
                <!-- Véracité -->
                <v-container>
                  <v-progress-circular
                    :rotate="90"
                    :size="110"
                    :width="5"
                    :value="80"
                    color="teal"
                  >
                    Véracité
                  </v-progress-circular>
                </v-container>
              </v-row>
              <v-row>
                <!-- Objectivité -->
                <v-container>
                  <v-progress-circular
                    :rotate="90"
                    :size="110"
                    :width="5"
                    :value="40"
                    color="red"
                  >
                    Objectivité
                  </v-progress-circular>
                </v-container>
              </v-row>
            </v-col>
              
            <v-col>
              
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
