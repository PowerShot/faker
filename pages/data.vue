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
        titre : "A propos :",
        texte : "Nous sommes trois étudiants en Master Informatique.\n Plusieurs sujet nous ont été proposés et nous avons choisi celui sur le thème « IA et éthique ».\n Les objectifs de ce projet sont de développer un détecteur de Fake news et de faire de la recherche sur l’éthique dans l’informatique.\n Pour répondre à cela nous avons choisi de faire ce site web de traitement d’article de presse.\n\n Nous avions comme but de mettre en avant le plus possible le coté éthique de notre outils. Nous voulons faire en sorte d’avertir et d’informer l’utilisateur pour qu’il ne soit pas piéger par les informations qu’il trouve et qu’il sache les analyser par lui même dans le futur.\n\n\n Nous avons choisi de traiter deux axes pour guider l’utilisateur.\n\n Le premier est la fiabilité des informations énumérées. \n Nous vérifions grâce à notre algorithme la fréquence d’apparition de certains mots clés dans des articles préalablement classés par rapport à un échantillon d’articles jugés Faux ou Vrais. Cela nous permet de déterminer, à l’aide d’un pourcentage, la possibilité au niveau statistique que l’article que vous nous fournirez soit fiable.\n\n Le deuxième axe repose sur l’analyse de sentiment. \n Dans un texte on peut amener les informations de manière négative, neutre ou positive. Cela peut jouer sur nos émotions et avoir un impact différent sur nous ainsi que notre compréhension. Nous avons également choisi de mettre à votre disposition cet indicateur afin de vous aider à faire le tri dans vos lectures, en fonction de votre humeur par exemple. Cet indicateur à de multiples applications.\n\n Nous avons choisi de vous présenter ces informations en pourcentage pour ne pas influencer votre jugement, comme cela aurait pu être le cas si nous ne vous avions donné qu’une réponse binaire (vrai/faux).\n Dans le premier onglet vous trouverez la synthèse simplifiée de toute l’analyse et dans les suivants le détail de cette dernière.\n\n Dans la partie plus détaillée vous aurez accès à une analyse paragraphe par paragraphe sous forme de tableau.\n Vous aurez également à disposition un nuage de mots regroupant l’ensembles des mots qui ont permis à notre algorithme de déterminer ce pourcentage. En passant votre souris du ces mots vous pourrez connaitre leur fréquence d’apparition dans le texte.\n\n En bas de la page de résultats nous vous laissons la possibilité de nous donner votre avis sur les informations que nous vous avons fournis. Nous vous encourageons à utiliser ce formulaire afin de nous permettre d’améliorer notre outils.\n\n Notre algorithme d’évaluation de fiabilité s’appuie sur divers thèmes d’articles pour qu’il soit le plus polyvalent possible. \n Si vous souhaitez en savoir plus voici le lien de son code que nous avons choisi de laisser open source : ",
        lien : ""
    }),
    methods: {
    },
  }
</script>