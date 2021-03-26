<template>
    <v-card>
        <v-card-title class="headline mb-5">
        {{ this.titre }}
        </v-card-title>

        <v-card-subtitle>
            <p v-for="ligne in texte.split('\n')">
                {{ligne}}
            </p>
        
        </v-card-subtitle>
    </v-card>
</template>

<script>
  export default {
    data: () => ({
       titre : "Traitement des données :",
        texte : "Pour être le plus engagés éthiquement parlant nous avons choisi de ne stocker aucunes des informations que auxquelles vous nous donnez accès.\n Les articles que vous nous fournissez ne sont pas stockés, ils sont analyser en temps réelle.\n Nous avons choisi de ne pas mettre d’authentification pour n’avoir à stocker aucune donnée. Par conséquent vous ne pourrez pas avoir d’historique sur les articles précédemment analysés avec notre outils."
    }),
    methods: {
    },
  }
</script>