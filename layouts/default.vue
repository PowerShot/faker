<template>
  <v-app id="inspire">
    <v-app-bar
      app
      color="white"
      flat
    >
      <v-container class="py-0 fill-height">
        
        <v-responsive>
        <v-avatar
          class="mr-10"
          size="32"
        >
          <img src="ic_launcher.png" alt="logo">
        </v-avatar>


        <nuxt-link
          style="text-decoration: none; color: inherit;"
          to="/">
            <v-btn
              text
            >
              Accueil
            </v-btn>
        </nuxt-link>

        
        <nuxt-link
          style="text-decoration: none; color: inherit;"
          to="/about">
            <v-btn
              text
            >
              à propos
            </v-btn>
        </nuxt-link>

        <nuxt-link
          style="text-decoration: none; color: inherit;"
          to="/data">
            <v-btn
              text
            >
              vos données
            </v-btn>
        </nuxt-link>

        </v-responsive>



        
      </v-container>
    </v-app-bar>

    <v-main class="grey lighten-3">
      
      <v-container>
          <!--  -->
          <nuxt/>
      </v-container>
    </v-main>

    <v-footer
      color="primary"
      padless
    >
      <v-row
        justify="center"
        no-gutters
      >
      <v-dialog
        transition="dialog-bottom-transition"
        max-width="600"
      >
        <template v-slot:activator="{ on, attrs }">
          <v-btn
            color="white"
            text
            rounded
            class="my-2"
            v-bind="attrs"
            v-on="on"
          ><v-icon class="mx-2">mdi-email</v-icon>
            nous contacter
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
                  <v-text-field
                    v-model="name"
                    :rules="nameRules"
                    label="Nom (facultatif)"
                  ></v-text-field>

                  <v-text-field
                    v-model="email"
                    :rules="emailRules"
                    label="E-mail"
                    required
                  ></v-text-field>

                  <v-text-field
                    v-model="objet"
                    label="Objet"
                    required
                  ></v-text-field>

                  <v-textarea
                      v-model="message"
                      clearable
                      clear-icon="mdi-close-circle"
                      name="input-7-1"
                      label="Message"
                      auto-grow
                      filled
                      hint=""
                      required
                    ></v-textarea>
                  <v-btn
                    :disabled="!valid"
                    color="success"
                    class="mr-4"
                    @click="validate;if(valid){dialog.value = false;sendFeedback()}"
                  >
                    Envoyer
                  </v-btn>

                  <v-btn
                    color="error"
                    class="mr-4"
                    @click="reset"
                  >
                    Réinitailiser le formulaire
                  </v-btn>
                </v-form>
              </v-card-text>
              <v-card-actions class="justify-end">
                <v-btn
                  text
                  @click="dialog.value = false;$refs.form.reset()"
                >Close</v-btn>
              </v-card-actions>
            </v-card>
          </template>
        </v-dialog>

        <v-btn
          color="white"
          text
          rounded
          class="my-2"
          @click="openNewPage('https://github.com/Gosow/Faker')"
        >
          <v-icon class="mx-2">mdi-github</v-icon>
          code source serveur
        </v-btn>
        <v-btn
          color="white"
          text
          rounded
          class="my-2"
          @click="openNewPage('https://github.com/PowerShot/faker')"
        >
          <v-icon class="mx-2">mdi-github</v-icon>
          code source site
        </v-btn>

        <v-btn
          color="white"
          text
          rounded
          class="my-2"
          @click="openNewPage('https://github.com/Gosow/Faker/blob/main/Faker.ipynb')"
        >
          <v-icon class="mx-2">mdi-language-python</v-icon>
          code IA
        </v-btn>

        <v-col
          class="primary py-4 text-center white--text"
          cols="12"
        >
          {{ new Date().getFullYear() }} — <strong>Faker</strong>
        </v-col>
      </v-row>
    </v-footer>
    
  </v-app>
</template>

<script>
  export default {
    data: () => ({
      links: [
        'Accueil',
        'à propos',
        'vos données'
      ],
      valid: true,
      name: '',
      objet: '',
      message: '',
      email: '',
      emailRules: [
        v => !!v || "Un e-mail est requis",
        v => /.+@.+\..+/.test(v) || "L'e-mail doit être valide",
      ],
    }),
    methods: {
      openNewPage(link){
        window.open(link, "_blank")
      },
      validate () {
        this.$refs.form.validate()
      },
      reset () {
        this.$refs.form.reset()
      },
      sendFeedback: function () {
        let text = 'FORMULAIRE DE CONTACT\nnom :' + this.name + '\n' + 'email : ' + this.email + '\n' + 'objet :' + this.objet + '\n' + 'message :\n' + this.message
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
  }
</script>