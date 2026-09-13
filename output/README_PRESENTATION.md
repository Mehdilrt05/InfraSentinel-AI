# Présentation de soutenance — InfraSentinel-AI

## Informations essentielles

- **Projet :** InfraSentinel-AI
- **Nature :** projet présenté dans le cadre d’un stage d’observation
- **Préparé par :** Mehdi L'harti et Aya Benjilali
- **Format :** PowerPoint 16:9, 22 diapositives
- **Langue :** français
- **Durée visée :** 15 à 20 minutes, hors questions du jury
- **Style :** interface sombre orientée infrastructure, IA et cybersécurité, avec accents vert et cyan

Le support présente InfraSentinel-AI comme une plateforme de supervision proactive : elle centralise la télémétrie d’infrastructure, détecte des comportements anormaux, produit des estimations de risque et aide l’opérateur à intervenir avant qu’un incident ne devienne critique.

## Fichiers livrés

- `Soutenance_InfraSentinel_AI_Mehdi_Lharti_Aya_Benjilali.pptx` : version PowerPoint éditable avec notes orateur
- `Soutenance_InfraSentinel_AI_Mehdi_Lharti_Aya_Benjilali.pdf` : version PDF destinée au partage et à l’impression
- `README_PRESENTATION.md` : description du support, des sources et des conditions d’utilisation
- `PRESENTATION_QA.md` : préparation aux questions possibles du jury

## Structure des 22 diapositives

| Nº | Titre / intention | Message principal |
|---:|---|---|
| 1 | Couverture | Présenter InfraSentinel-AI et les deux auteurs du projet. |
| 2 | Superviser | Poser la supervision comme point de départ de la démarche. |
| 3 | Anticiper | Faire évoluer le discours de l’observation vers la prévention. |
| 4 | Problématique | Expliquer pourquoi une alerte tardive ne suffit pas. |
| 5 | Monitoring réactif vs approche proactive | Comparer deux modes de gestion des incidents. |
| 6 | La solution InfraSentinel-AI | Résumer la proposition de valeur et les fonctions principales. |
| 7 | Architecture globale | Montrer les principaux composants et leurs échanges. |
| 8 | Zoom sur l’agent Windows | Expliquer la collecte locale de télémétrie. |
| 9 | Pipeline de données | Suivre le trajet d’une mesure depuis la machine jusqu’à l’interface. |
| 10 | Pipeline IA | Situer la préparation des données, la détection et la restitution. |
| 11 | Détection d’anomalies | Illustrer l’utilisation contrôlée d’Isolation Forest. |
| 12 | Prédiction de risque | Présenter un scénario expérimental d’anticipation. |
| 13 | Tableau de bord réel | Montrer l’état général de la plateforme à partir d’une capture réelle. |
| 14 | Détail d’une machine | Passer de la vue globale au diagnostic ciblé. |
| 15 | Alertes et recommandations | Relier détection, priorisation et assistance à l’opérateur. |
| 16 | Temps réel | Expliquer la circulation des mises à jour via Redis, Celery et WebSocket. |
| 17 | Sécurité et multi-tenant | Présenter RBAC, séparation logique et contrôles d’accès. |
| 18 | Environnements et niveau de validation | Distinguer clairement ce qui a été validé localement de ce qui reste à tester. |
| 19 | Tests | Donner les résultats automatisés disponibles pour le backend, le frontend et l’agent. |
| 20 | Avant / après | Synthétiser le passage d’une supervision fragmentée à une vision proactive. |
| 21 | Feuille de route | Proposer les étapes de validation et d’industrialisation suivantes. |
| 22 | Conclusion et questions | Résumer la valeur du projet et ouvrir l’échange avec le jury. |

## Captures et éléments visuels

Le support réutilise des captures réelles issues de la validation locale du projet, notamment :

- la liste et l’état des agents ;
- le tableau de bord principal ;
- la liste des machines ;
- le détail d’une machine ;
- la vue des anomalies ;
- la gestion ou la visualisation des modèles ML ;
- la vue des prédictions ;
- les alertes et recommandations.

Les captures servent de preuve visuelle du fonctionnement de l’interface observé. Certaines vues de démonstration affichent des données PFE contrôlées ou synthétiques ; une capture réelle de l’application ne transforme pas ces données en télémétrie de production. Elles ne doivent pas être interprétées comme une validation d’un déploiement VMware ou Hyper-V réel.

La couverture utilise également un **fond original généré avec ImageGen** pour cette présentation. Direction artistique : environnement d’infrastructure numérique sombre, accents vert et cyan, ambiance entreprise/IA/cybersécurité, sans texte, sans logo, sans personne et avec un espace négatif permettant la composition du titre. L’asset de travail se trouve dans `.codex-pptx-build/assets/cover-background.png`.

## Transitions et animation

La narration est conçue autour de transitions **Morph PowerPoint** entre plusieurs vues liées. Les principales séquences sont :

- diapositives 2 → 3 : transformation de « Superviser » vers « Anticiper » ;
- diapositives 7 → 8 : zoom de l’architecture globale vers l’agent Windows et l’API Django ;
- diapositives 13 → 14 : passage du tableau de bord global au détail d’une machine.

Les autres transitions restent discrètes afin de préserver la lisibilité. Pour bénéficier du rendu Morph natif, ouvrir de préférence le fichier `.pptx` avec une version récente de Microsoft PowerPoint. Le PDF conserve la mise en page, mais pas les animations.

## Sources utilisées

Le contenu de la présentation est fondé sur les fichiers et rapports présents dans le dépôt, en particulier :

- `README.md` ;
- `docs/README.md` ;
- `docs/LOCAL_FINAL_VALIDATION_REPORT.md` ;
- `docs/LOCAL_ML_VALIDATION.md` ;
- `docs/ML.md` et `docs/ML_EVALUATION.md` ;
- `docs/HEAVY_LOCAL_STRESS_TEST_REPORT.md` et `docs/STRESS_REMEDIATION_REPORT.md` ;
- `docs/SECURITY_AUDIT.md` ;
- la documentation d’architecture et de temps réel du projet ;
- les captures produites pendant la validation locale ;
- le rapport de stage d’observation associé au projet.

Les rapports sont datés et certains conservent un état historique avant remédiation. Pour les chiffres courants, la mise à jour de remédiation du 30 août, `docs/ML.md` et l’état vérifié au 10 septembre dans `docs/README.md` priment sur les sections historiques. Les diapositives privilégient des formulations vérifiables et évitent de transformer une preuve locale ou simulée en promesse de production.

## État de validation communiqué

Les résultats automatisés disponibles sont :

- **backend Django :** 203 tests exécutés, dont 6 ignorés, avec 0 échec ;
- **frontend :** 64 tests réussis sur 64 ;
- **agent Windows :** 26 tests réussis sur 26.

La validation locale couvre notamment PostgreSQL, Redis, Celery, les mises à jour WebSocket, le RBAC, la séparation multi-tenant et les contrôles de sécurité décrits dans le projet. L’audit initial du 24 août 2026, dont les suites ont été révalidées le 26 août après remédiation, rapporte 0 risque critique, 0 élevé, 3 moyens et 4 faibles résiduels ; il s’agit d’une revue de code et de configuration, pas d’un test d’intrusion en production.

Le runtime Python de l’agent Windows a été validé dans l’environnement local. En revanche, l’installation complète sous forme de service Windows ainsi que le comportement après redémarrage n’ont pas été validés.

## Cadre de validation du machine learning

La démonstration de soutenance utilise **Isolation Forest** dans un contexte contrôlé :

- données synthétiques contrôlées ;
- 6 caractéristiques système obligatoires : CPU, RAM, disque maximal par volume, réseau entrant/sortant et latence ;
- 200 estimateurs ;
- taux de contamination configuré à 2 %.

Cette démonstration montre le fonctionnement technique du pipeline. Le dépôt contient également une validation locale plus récente : le modèle réel n°8 a été entraîné sur **529 fenêtres réelles** après exclusion de 486 métriques contrôlées, avec les six caractéristiques système et `system.gpu.utilization`. Le split temporel est 60/20/20 et une alerte exige trois fenêtres anormales parmi les cinq dernières, puis trois fenêtres normales pour la récupération.

Ce résultat reste non supervisé et sans vérité terrain d’incidents. Le taux stable observé sur le holdout est 4,90 %, mais ce n’est pas un taux de faux positifs prouvé. La présentation ne revendique donc ni précision, ni rappel, ni F1-score, ni taux de faux positifs en production.

Le scénario prédictif présenté — CPU à 74 %, progression de 4 points par heure, risque 70 et dépassement estimé à environ 3 h 45 — est une **illustration contrôlée**. Il ne doit pas être présenté comme une garantie opérationnelle.

## Limites à annoncer explicitement

- **VMware réel : non testé.** Les éléments disponibles reposent sur des mocks et fixtures ; ils ne prouvent pas une connexion à un vCenter réel.
- **Hyper-V réel : non testé.** Les mocks et le collecteur ont été testés, mais la validation sur un hôte réel nécessitant les permissions adéquates reste à effectuer.
- **Agent Windows en service : validation incomplète.** Le runtime Python est validé, pas le cycle complet d’installation, de démarrage automatique et de reprise après redémarrage.
- **ML : validation réelle mais non labellisée.** Un modèle local a été entraîné sur 529 fenêtres réelles et l’utilisation GPU a été intégrée, mais l’anomalie GPU reste `PARTIAL` et l’absence de labels empêche de mesurer précision, rappel ou vrai taux de faux positifs.
- **Échelle de production : non revendiquée.** Le stress test local valide 250 agents au rythme réaliste de 30 secondes, sans erreur et avec un p95 de 98,1 ms. Le profil accéléré plafonne autour de 45,2 requêtes/s et atteint 2,60 s de p95 à 100 agents ; 500 agents, la durée prolongée et l’infrastructure de production restent non testés.

Ces limites sont volontaires : elles permettent de distinguer une preuve de fonctionnement locale d’une validation en environnement de production.

## Conseils de présentation

- Consacrer environ 30 à 60 secondes à chaque diapositive, avec davantage de temps pour l’architecture, l’IA, la sécurité et les tests.
- Ne pas lire les captures écran ligne par ligne : montrer d’abord le parcours global, puis deux ou trois informations significatives.
- Employer « validé localement », « scénario contrôlé » ou « à tester sur environnement réel » selon le niveau de preuve.
- Pour l’IA, expliquer d’abord le besoin métier, puis le principe d’Isolation Forest ; éviter de présenter le modèle comme infaillible.
- Terminer sur la complémentarité entre règles déterministes, détection ML et décision humaine.

## Message de conclusion recommandé

InfraSentinel-AI démontre qu’une chaîne complète — collecte, centralisation, temps réel, sécurité, détection d’anomalies et aide à la décision — peut être assemblée et validée localement. La suite logique consiste à confronter cette base à des infrastructures VMware et Hyper-V réelles, à un agent installé comme Windows Service, à des incidents étiquetés et à des tests de charge prolongés dans l’architecture cible, sans masquer les limites actuelles.
