# Questions / réponses de soutenance — InfraSentinel-AI

## Repères pour répondre au jury

Le projet a été préparé par **Mehdi L'harti et Aya Benjilali**. Les réponses ci-dessous restent volontairement alignées sur les preuves disponibles : validation locale, tests automatisés, stress tests contrôlés, télémétrie locale réelle et scénarios de démonstration synthétiques. Lorsqu’un point n’a pas été testé sur une infrastructure réelle, ou lorsqu’il manque une vérité terrain, il faut le dire clairement puis expliquer comment il serait validé.

## Vision et problématique

### 1. Quel problème InfraSentinel-AI cherche-t-il à résoudre ?

Les outils de supervision classiques montrent souvent qu’un incident est déjà en cours. InfraSentinel-AI cherche à réunir les métriques d’infrastructure, la détection d’anomalies, une estimation de risque et des recommandations dans une même plateforme afin d’aider l’opérateur à intervenir plus tôt.

### 2. Quelle est la différence entre monitoring réactif et supervision proactive ?

Le monitoring réactif déclenche généralement une alerte après le franchissement d’un seuil. L’approche proactive analyse aussi l’évolution et le caractère inhabituel des métriques pour signaler une dégradation avant un état critique. Dans le projet, les deux approches sont complémentaires : les règles donnent des contrôles explicites et l’IA aide à repérer des comportements moins évidents.

### 3. Quelle est la valeur principale de la solution ?

La valeur principale est la centralisation du parcours opérationnel : collecter la télémétrie, la traiter, afficher l’état en temps réel, repérer une anomalie et présenter une information exploitable. La présentation ne revendique pas encore un retour sur investissement quantifié en production.

### 4. Qui a préparé le projet ?

Le projet et sa présentation ont été préparés conjointement par Mehdi L'harti et Aya Benjilali dans le cadre du stage d’observation.

## Architecture

### 5. Quels sont les principaux composants de l’architecture ?

L’architecture présentée comprend un agent Windows pour la collecte, une API backend Django, PostgreSQL pour la persistance, Redis et Celery pour les traitements asynchrones, un canal WebSocket pour les mises à jour en temps réel et une interface React pour la visualisation et l’exploitation.

### 6. Pourquoi utiliser un agent sur les machines supervisées ?

L’agent rapproche la collecte de la source. Il récupère la télémétrie disponible sur la machine puis l’envoie au backend selon le protocole défini par le projet. Cette séparation évite de mélanger la logique de collecte locale avec l’interface ou le moteur central.

### 7. Pourquoi Django et React ?

Django structure l’API, l’authentification, les règles métier et l’accès aux données côté serveur. React est adapté à une interface riche dont les composants se mettent à jour fréquemment. Le choix permet de séparer clairement les responsabilités du backend et du frontend.

### 8. Quel est le rôle de PostgreSQL ?

PostgreSQL assure la persistance structurée des informations gérées par la plateforme. Après correction du pool de connexions, le stress test local n’a observé aucune saturation PostgreSQL jusqu’à 100 agents accélérés et 250 agents au rythme réaliste. Cela reste une mesure locale de capacité, pas un dimensionnement de production à grande échelle.

### 9. Pourquoi Redis et Celery sont-ils nécessaires ?

Redis sert d’infrastructure rapide pour les échanges ou files utilisés par les traitements asynchrones, tandis que Celery exécute les tâches hors du cycle immédiat d’une requête web. Ce découplage est utile lorsque l’ingestion, l’analyse ou certaines actions ne doivent pas bloquer l’API.

### 10. Comment circule une mesure dans la plateforme ?

Une mesure part de l’agent, rejoint le backend, est validée et enregistrée, puis peut alimenter les traitements de règles ou de ML. Le résultat est ensuite restitué à l’interface, avec des mises à jour en temps réel lorsque le flux WebSocket est utilisé.

## Temps réel

### 11. Que signifie « temps réel » dans cette présentation ?

Il s’agit de la propagation rapide des nouveaux états vers l’interface grâce au pipeline applicatif et au WebSocket. Cela ne signifie pas « temps réel dur » au sens industriel, avec une latence maximale mathématiquement garantie.

### 12. Comment l’interface reçoit-elle les mises à jour ?

Le backend traite les nouvelles données et publie les événements nécessaires ; Redis, Celery et le canal WebSocket participent au découplage et à la diffusion. L’interface React peut ainsi refléter un changement sans recharger manuellement toute la page.

### 13. Avez-vous mesuré la latence maximale du pipeline ?

La validation locale confirme le fonctionnement du mécanisme WebSocket avec deux clients simultanés, diffusion, déconnexion/replay et rejet d’un ticket réutilisé. Les stress tests ont mesuré la latence de l’API d’ingestion, pas une borne de latence bout en bout ni un SLA WebSocket en production. Il ne faut donc annoncer aucun engagement de latence temps réel ; un benchmark WebSocket multi-client et une mesure de bout en bout restent nécessaires.

## Intelligence artificielle et Isolation Forest

### 14. Pourquoi avoir choisi Isolation Forest ?

Isolation Forest est un algorithme non supervisé adapté à la détection d’observations atypiques. Il est intéressant lorsqu’on dispose de télémétrie mais pas encore d’un grand jeu d’incidents réels correctement étiquetés. Son rôle ici est de compléter les règles, pas de remplacer l’expertise de l’opérateur.

### 15. Comment fonctionne Isolation Forest, simplement ?

Le modèle construit plusieurs arbres qui séparent aléatoirement les observations. Un point très différent des autres tend à être isolé en moins de séparations. L’ensemble de ces arbres produit alors un score permettant de signaler les observations les plus atypiques.

### 16. Le modèle est-il supervisé ?

Non. L’utilisation présentée d’Isolation Forest est non supervisée : le modèle cherche des comportements atypiques sans apprendre à partir d’une liste exhaustive d’incidents déjà étiquetés.

### 17. Sur quelles données le modèle a-t-il été validé ?

Deux niveaux de preuve existent. La démonstration de soutenance utilise des données synthétiques contrôlées. En complément, le modèle réel n°8 a été entraîné localement sur 529 fenêtres après exclusion de 486 métriques contrôlées, avec six caractéristiques système et l’utilisation GPU. La configuration utilise 200 estimateurs et une contamination de 2 %. Sans incidents réels étiquetés, cette validation ne permet toujours pas de calculer précision, rappel ou vrai taux de faux positifs.

### 18. Que signifie un taux de contamination de 2 % ?

Ce paramètre indique au modèle la proportion attendue d’observations atypiques dans le jeu analysé. Il influence le seuil de décision. Ce n’est pas la preuve que exactement 2 % des mesures d’une infrastructure réelle seront anormales ; ce paramètre devra être recalibré avec des données réelles.

### 19. Pourquoi 200 estimateurs ?

Il s’agit de la configuration utilisée dans la validation contrôlée. Plusieurs arbres permettent de stabiliser le score par rapport à un arbre unique. La présentation ne prétend pas que 200 est une valeur universellement optimale ; un réglage comparatif sur des données réelles serait nécessaire.

### 20. Quelles sont les six caractéristiques utilisées ?

Les six caractéristiques système obligatoires sont le CPU, la RAM, l’utilisation disque maximale par volume, le réseau entrant, le réseau sortant et la latence. Le schéma 2.0 peut ajouter des caractéristiques GPU optionnelles lorsqu’elles disposent d’une couverture historique suffisante ; le modèle réel n°8 a ajouté `system.gpu.utilization`. L’absence de GPU est distinguée d’une utilisation réelle à 0 %.

### 21. Quelle est la précision du modèle ?

La présentation ne revendique pas de précision, de rappel ou de F1-score sur des incidents réels. Sans vérité terrain étiquetée, ces métriques ne seraient pas honnêtes. L’étape suivante serait de constituer un corpus représentatif, faire annoter les événements puis mesurer les faux positifs et faux négatifs.

### 22. Comment gérez-vous les faux positifs ?

Dans l’état actuel, le ML doit être considéré comme une aide au tri. Le moteur exige trois fenêtres anormales parmi les cinq dernières, avec la plus récente anormale, puis trois fenêtres normales consécutives pour résoudre l’alerte. Cette hystérésis a permis de fermer les alertes historiques lors de la validation corrective. Elle réduit le bruit instantané sans prouver un taux de faux positifs ; une boucle de retour opérateur et des labels réels restent nécessaires.

### 23. Quelle différence faites-vous entre une règle et l’IA ?

Une règle est déterministe et facile à expliquer, par exemple le franchissement d’un seuil connu. Le modèle cherche plutôt une observation inhabituelle selon les données apprises. Les règles couvrent les conditions connues ; le ML peut attirer l’attention sur des formes moins attendues. Leur combinaison est plus robuste que l’usage exclusif de l’un ou de l’autre.

### 24. Le modèle remplace-t-il l’administrateur système ?

Non. Il fournit un signal supplémentaire et peut contribuer à la priorisation. La décision opérationnelle reste humaine, surtout tant que le modèle n’a pas été validé sur des données de production étiquetées.

### 25. Comment interpréter le scénario prédictif présenté ?

Le scénario affiché dans la présentation est contrôlé : CPU à 74 %, progression de 4 points par heure, risque 70 et dépassement estimé à environ 3 h 45 selon les critères du prototype. C’est une démonstration du mécanisme d’anticipation, pas une garantie de date d’incident. Une autre campagne sur télémétrie locale réelle a également produit une tendance et une échéance avec une confiance `LOW` ; elle doit, elle aussi, être présentée comme une estimation.

## Sécurité et séparation des données

### 26. Comment les accès sont-ils contrôlés ?

Le projet utilise un contrôle d’accès fondé sur les rôles, ou RBAC. L’utilisateur ne doit accéder qu’aux actions et données autorisées par son rôle. Les contrôles concernés font partie de la validation locale annoncée.

### 27. Comment gérez-vous le multi-tenant ?

La plateforme prévoit une séparation logique des données par tenant et applique cette portée dans les contrôles d’accès. La validation locale couvre ce comportement. Une mise en production devrait compléter cette preuve par des tests d’isolation adverses, des audits et une configuration d’exploitation adaptée.

### 28. Le multi-tenant est-il uniquement une séparation visuelle dans l’interface ?

Non, il ne doit pas dépendre seulement du frontend. La portée du tenant et les autorisations doivent être vérifiées côté backend pour chaque opération sensible. C’est le backend qui constitue la barrière de sécurité principale.

### 29. Peut-on considérer la plateforme comme totalement sécurisée ?

Aucun logiciel sérieux ne peut être déclaré « totalement sécurisé » sur la seule base de tests locaux. L’audit initial du 24 août 2026, dont les suites ont été révalidées le 26 août après remédiation, rapporte 0 risque critique, 0 élevé, 3 moyens et 4 faibles résiduels. Les contrôles RBAC, multi-tenant, IDOR, CSRF, JWT, throttling et en-têtes ont été validés localement, mais l’audit n’était pas un test d’intrusion sur une infrastructure de production. MFA/anti-bot, reverse proxy/WAF et contrôle d’egress restent notamment à traiter dans l’environnement cible.

## Tests et niveau de preuve

### 30. Quels tests ont été exécutés ?

Les résultats disponibles sont : 203 tests backend Django, dont 6 ignorés et 0 échec ; 64 tests frontend réussis sur 64 ; et 26 tests de l’agent Windows réussis sur 26.

### 31. Pourquoi certains tests backend sont-ils ignorés ?

Le résultat courant agrégé indique six tests ignorés. L’audit de sécurité relie les exclusions d’intégration aux services externes réellement absents du laboratoire — notamment SMTP, Redis/broker selon le mode de test, VMware et Hyper-V. Comme les rapports historiques n’affichent pas tous le même nombre de tests découverts, il vaut mieux ne pas attribuer une cause précise à chacun des six skips sans relire la sortie de la campagne du 10 septembre. Le point vérifiable est qu’aucun test exécuté n’a échoué.

### 32. Ces tests prouvent-ils que la solution est prête pour la production ?

Non. Ils apportent une preuve importante de cohérence fonctionnelle et technique dans l’environnement local. Le dépôt contient aussi des campagnes locales de charge, résilience, sécurité et sauvegarde/restauration, mais leur périmètre contrôlé ne remplace pas une qualification de production. Il reste à valider la durée, la montée en charge, la panne réelle des dépendances externes, le déploiement cible, le test d’intrusion et les procédures d’exploitation.

### 33. Qu’avez-vous réellement validé sur Windows ?

Le runtime Python de l’agent a été validé, ainsi que les 26 tests automatisés annoncés. En revanche, le cycle complet sous forme de service Windows — installation, démarrage automatique et reprise après redémarrage — n’a pas été testé.

### 34. Avez-vous réalisé des tests de charge ?

Oui, sur le poste local et l’API loopback. Après remédiation du pool PostgreSQL, 250 agents au rythme réaliste de 30 secondes ont été validés sans erreur, avec un p95 de 98,1 ms. Le profil artificiel à une requête par seconde plafonne autour de 45,2 requêtes/s ; à 100 agents, le p95 atteint 2,60 s et déclenche le gate de latence. Le verdict reste donc `PARTIAL` pour la capacité : 500 agents, la durée prolongée et l’infrastructure de production ne sont pas validés.

## VMware, Hyper-V et limites

### 35. Avez-vous connecté InfraSentinel-AI à un VMware réel ?

Non. L’intégration VMware réelle n’a pas été testée. Les validations disponibles utilisent des mocks et fixtures. La prochaine étape serait une connexion contrôlée à un vCenter de test avec un compte aux permissions minimales.

### 36. Avez-vous testé Hyper-V sur un hôte réel ?

Non. Les mocks et le collecteur ont été testés, mais pas l’intégration complète sur un hôte Hyper-V réel, notamment à cause des permissions nécessaires. Cette limite doit rester explicite dans la soutenance.

### 37. Pourquoi montrer VMware et Hyper-V si les connexions réelles ne sont pas validées ?

Ils représentent des cibles prévues par l’architecture et des interfaces préparées au niveau du prototype. Les montrer permet d’expliquer la direction technique, à condition de distinguer clairement « pris en compte dans le code ou les tests simulés » de « validé sur une infrastructure réelle ».

### 38. Quelle serait votre méthode de validation sur une infrastructure réelle ?

Commencer par un environnement de laboratoire isolé, créer des comptes à privilèges minimaux, vérifier la collecte nominale, simuler des indisponibilités, mesurer les délais et comparer les métriques à la source. Il faudrait ensuite tester la reprise, la sécurité, le volume et la compatibilité des versions avant toute généralisation.

## Exploitation et évolution

### 39. Quelle est la principale limite actuelle ?

La principale limite est l’écart entre un laboratoire local contrôlé et un parc de production. VMware, Hyper-V et le service Windows installé avec redémarrage hôte restent non validés. Les tests locaux couvrent 250 agents au rythme réaliste, mais pas 500 agents ni la durée de production. Le ML exploite désormais de la télémétrie locale réelle, toutefois sans incidents étiquetés permettant de mesurer précision, rappel et faux positifs.

### 40. Quelle serait la prochaine étape prioritaire ?

Valider d’abord la collecte de bout en bout sur une petite infrastructure VMware/Hyper-V réelle et installer l’agent comme Windows Service avec test après redémarrage. En parallèle, conserver et faire annoter la télémétrie pour évaluer le ML, prolonger les tests de charge au-delà du laboratoire et réaliser un test d’intrusion dans l’architecture de déploiement cible.

### 41. Les recommandations sont-elles exécutées automatiquement ?

La présentation montre des alertes et recommandations destinées à aider l’opérateur. Elle ne fournit pas de preuve d’une remédiation autonome validée en production. Toute automatisation future devrait être progressive, réversible, journalisée et soumise à des autorisations strictes.

### 42. Que faites-vous si le pipeline ML est indisponible ?

L’architecture doit continuer à valoriser les fonctions déterministes : collecte, historique, règles et affichage. Le ML est une couche d’assistance et ne doit pas devenir un point unique empêchant toute supervision. La résilience générale des services a été exercée localement, mais un scénario spécifique de perte du worker ou de l’artefact ML, avec mesure de l’impact utilisateur, reste pertinent avant industrialisation.

### 43. Comment amélioreriez-vous l’explicabilité ?

Il faudrait afficher les métriques ayant accompagné le signal, le score, la période de référence et le contexte de la machine, puis permettre à l’opérateur de confirmer ou rejeter l’alerte. Pour le prototype actuel, il faut rester prudent et parler d’indice d’anomalie plutôt que de diagnostic certain.

### 44. Pourquoi ce projet est-il pertinent pour un stage d’observation ?

Il permet d’observer plusieurs dimensions d’un système d’information moderne : supervision, agents, API, base de données, traitement asynchrone, temps réel, sécurité, tests et IA. La soutenance doit toutefois bien distinguer l’apprentissage et le prototype validé localement d’un produit déjà industrialisé.

## Formulations à privilégier

- « La fonctionnalité a été validée localement. »
- « La démonstration ML utilise des données synthétiques contrôlées ; le dépôt contient aussi une validation locale sur 529 fenêtres réelles, sans vérité terrain. »
- « Le test local valide 250 agents au rythme réaliste ; la capacité de production reste à qualifier. »
- « L’intégration réelle VMware reste à tester. »
- « Hyper-V a été couvert par des mocks et le collecteur, pas par une validation sur hôte réel. »
- « Le modèle complète les règles et l’analyse humaine. »
- « Les tests automatisés n’équivalent pas à une qualification de production. »

## Formulations à éviter

- « L’IA prédit tous les incidents. »
- « Le modèle est précis à 98 % » ou toute autre métrique non mesurée.
- « VMware et Hyper-V sont entièrement opérationnels » sans test réel.
- « La plateforme est totalement sécurisée. »
- « Le temps réel garantit une latence nulle. »
- « Le système remplace l’administrateur. »

## Réponse courte de conclusion

> InfraSentinel-AI réunit une chaîne cohérente de collecte, traitement, visualisation, sécurité et détection d’anomalies. Elle est étayée par des tests automatisés, des stress tests locaux jusqu’à 250 agents au rythme réaliste, une validation ML contrôlée et un modèle local entraîné sur 529 fenêtres réelles. Notre contribution est de présenter ces preuves sans masquer leurs limites : VMware et Hyper-V réels, Windows Service avec redémarrage, capacité de production prolongée et évaluation ML sur des incidents étiquetés restent à valider.
