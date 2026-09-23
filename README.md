# PROJET-FIL-ROUGE-Gestion-des-r-servations-de-salles-avec-Laravel

PROJET FIL ROUGE
Gestion des réservations de salles avec Laravel
Cahier de projet technique fondé sur une démarche incrémentale

Paramètre
Cadre proposé
Niveau
Intermédiaire
Prérequis
PHP POO, SOLID, SQL, Git et notions Docker
Modalité
Travail individuel avec revues de jalons
Charge indicative
Trente-six à quarante-deux heures selon l’environnement de déploiement
Socle
PHP 8.3 ou 8.4, Laravel, MySQL, Composer, Docker, GitLab CI CD
Livraison
Quinze versions intermédiaires puis une release 1.0.0

Principe directeur  Chaque incrément produit une application exécutable, une version Git identifiable, une évolution maîtrisée de l’environnement, une preuve de pipeline et un DevLog. Les notions apparaissent lorsqu’un problème concret les rend nécessaires.

Compétence finale visée
À l’issue du projet, l’étudiant doit pouvoir concevoir, tester, conteneuriser et livrer une application Laravel de niveau intermédiaire, expliquer ses choix d’architecture et distinguer le déploiement du code du déploiement d’une image immuable.

A  Description du projet
1  Contexte
L’université utilise ses salles pour les cours, soutenances, réunions, travaux pratiques et événements étudiants. Les demandes par courriel créent des doublons et ne permettent ni contrôle fiable de disponibilité ni traçabilité.
L’application à construire centralise le catalogue des salles et leur réservation. La première version est rendue localement, puis industrialisée progressivement jusqu’à deux modes de livraison automatisée.
2  Objectifs pédagogiques
    • Comprendre le cycle d’une requête Laravel depuis la route jusqu’à la réponse.
    • Modéliser les données avec migrations, Eloquent, relations, factories et seeders.
    • Valider et transformer les données HTTP avant leur utilisation.
    • Séparer orchestration HTTP, règles métier, accès aux données et présentation.
    • Utiliser l’injection de dépendances et le conteneur de services Laravel.
    • Composer les requêtes Eloquent avec des scopes et maîtriser les effets de bord avec Observers, Events et Listeners.
    • Écrire des tests unitaires, fonctionnels et d’intégration.
    • Traiter les conflits de réservation et le risque de concurrence.
    • Construire une image Docker reproductible et exécuter l’application avec Compose.
    • Mettre en place une chaîne GitLab de qualité, publication et déploiement.
    • Documenter une progression technique par versions, tags, releases et DevLogs.
3  Socle technique
Besoin
Choix
Framework
Laravel sur une version stable compatible avec PHP 8.3 ou 8.4
Persistance
MySQL et Eloquent ORM
Interface
Blade et CSS léger
Validation
Form Requests et règles Laravel
Tests
Pest ou PHPUnit, choix unique justifié
Qualité
Laravel Pint et Larastan ou PHPStan
Conteneurs
Dockerfile multi stage et Docker Compose
Forge
GitLab Issues, Merge Requests, Registry, Environments et Releases
Automatisation
GitLab CI CD

Version de Laravel  La version exacte est figée dans composer.lock. Toute montée de version en cours de projet est une décision documentée, testée et livrée dans un incrément dédié.


4  Fonctionnalités attendues
Gestion des salles
    • Lister les salles et consulter une fiche détaillée.
    • Créer et modifier une salle.
    • Activer ou désactiver une salle sans supprimer son historique.
    • Filtrer par bâtiment, type et état lorsque le socle est terminé.
Salle  id, nom, bâtiment, capacité, type, active, created_at, updated_at
Types autorisés  cours, informatique, laboratoire, amphithéâtre, réunion
Ressources multimédias des salles
    • Ajouter plusieurs photos ou vidéos à une salle.
    • Afficher une galerie ordonnée sur la fiche de la salle.
    • Supprimer une ressource sans laisser de fichier orphelin.
    • Conserver les métadonnées dans MySQL et les fichiers dans MinIO.
RessourceSalle  id, salle_id, type, nom_original, mime_type, taille, cle_stockage, ordre, metadonnees, created_at, updated_at
Types autorisés  photo, vidéo
Gestion des réservations
    • Lister toutes les réservations et filtrer par salle.
    • Consulter une réservation.
    • Créer une réservation.
    • Annuler une réservation sans la supprimer.
Réservation  id, salle_id, responsable, email, motif, date_debut, date_fin, statut, created_at, updated_at
Statuts autorisés  confirmée, annulée
5  Règles métier
    1. La salle existe.
    2. La salle est active.
    3. Le responsable est renseigné.
    4. L’adresse électronique est valide.
    5. Le motif contient entre cinq et deux cent cinquante cinq caractères.
    6. La date de début précède la date de fin.
    7. La durée ne dépasse pas quatre heures.
    8. La réservation commence dans le futur.
    9. Aucune réservation confirmée ne chevauche la période.
Chevauchement  Il y a conflit lorsque nouveau début est strictement inférieur à la fin existante et que nouvelle fin est strictement supérieure au début existant. Deux créneaux adjacents sont autorisés. Une réservation annulée ne bloque plus la salle.

6  Exigences non fonctionnelles
    • Les erreurs de validation sont compréhensibles et les données saisies sont conservées.
    • Aucun secret ne figure dans le dépôt, l’image ou les journaux.
    • Les dates sont stockées selon une convention unique et affichées dans le fuseau documenté.
    • La création concurrente de deux réservations incompatibles ne doit pas produire un double succès.
    • Les ressources privées sont exposées par des URLs temporaires et leur catalogue mis en cache est invalidé lors des mutations.

7  Contraintes architecturales
    • Les contrôleurs restent minces et ne portent pas les règles de disponibilité.
    • Les Form Requests assurent la validation de forme et l’autorisation HTTP.
    • Les règles métier sont regroupées dans des services ou actions applicatives.
    • Les dépendances sont reçues par constructeur et résolues par le conteneur Laravel.
    • L’accès direct au conteneur depuis le domaine applicatif est interdit.
    • Les vues Blade ne contiennent ni requête Eloquent ni règle métier.
    • La détection de conflit repose sur une requête explicite et testable.
    • La création d’une réservation critique utilise une transaction et une stratégie de concurrence justifiée.
    • Les environnements local, test, staging et production sont séparés par configuration.
    • Les changements de schéma sont exclusivement réalisés par migrations.
8  Organisation attendue
L’arborescence Laravel standard est conservée. Les ajouts ci-dessous expriment les responsabilités sans transformer le framework en architecture artificielle.
Zone
Responsabilité
app Http Controllers
Orchestration HTTP et réponses
app Http Requests
Validation et autorisation
app Models
Modèles Eloquent, relations et casts
app Data
Objets de transfert immuables si leur utilité est démontrée
app Services ou Actions
Cas d’usage et règles métier
app Contracts
Ports nécessaires à une dépendance interchangeable
app Repositories ou Queries
Requêtes complexes seulement si elles apportent une valeur
app Exceptions
Échecs métier explicites
app Media
Téléversement, suppression, URLs temporaires et cache des ressources
app Observers Events Listeners
Réactions au cycle de vie, événements explicites et traitements découplés
database migrations factories seeders
Schéma et données contrôlées
resources views
Présentation Blade
tests Unit Feature
Tests isolés et tests applicatifs
docker et compose
Construction et orchestration locale
docs devlogs
Journal par incrément et décisions

9  Modèle de données
L’étudiant produit un diagramme de classes et un schéma relationnel avant de coder les relations. Il justifie les types SQL, index, clés étrangères, règles de suppression et contraintes d’unicité. Le schéma évolue uniquement par migrations.
Décision attendue  L’usage d’un Repository n’est pas automatique. L’étudiant doit comparer Eloquent direct, Query Object et Repository puis retenir la solution la plus simple qui protège réellement les règles et les tests.


Incrément 0  Initialisation du dépôt GitLab
Version cible
Résultat observable
Condition de passage
v0.0.0
Un dépôt vide mais gouverné, clonable et auditable.
La Merge Request est fusionnée, le pipeline est vert et le tag v0.0.0 pointe sur main.

Travail demandé
    • Créer le projet GitLab et un README présentant contexte, périmètre et prérequis.
    • Créer les labels type, priorité et statut ainsi que les Issues des trois premiers incréments.
    • Protéger main et interdire le push direct.
    • Ajouter des modèles de Merge Request, Issue et DevLog.
Contraintes techniques
    • main représente toujours un état livrable.
    • Toute évolution passe par une branche issue incrément sujet.
    • Les secrets et fichiers locaux sont exclus dès le premier commit.
Questions de découverte
    10. Pourquoi protéger main avant d’écrire du code.
    11. Quelle différence entre commit, tag, release et environnement.
    12. Quels éléments rendent un commit atomique.
GitLab imposé
    • Branche  chore 0 initialisation.
    • Commits suggérés  chore gitlab initialise project governance  puis  docs readme describe project.
    • Merge Request obligatoire, squash autorisé si l’historique de travail reste lisible.
    • Tag annoté v0.0.0 après fusion.
Docker évolutif
    • Créer seulement docs docker strategy.md avec les futures cibles local, ci et production.
    • Aucun conteneur n’est exigé à ce stade.
Pipeline et déploiement
    • Créer un pipeline minimal lint markdown ou vérification de structure.
    • Le pipeline doit s’exécuter sur la Merge Request et main.
DevLog obligatoire
    • Créer docs devlogs v0.0.0.md.
    • Expliquer la convention de branches, la protection de main et la première difficulté rencontrée.
    • Ajouter les liens vers l’Issue, la Merge Request, le pipeline et le tag.
Validation de l’incrément  La Merge Request est fusionnée, le pipeline est vert et le tag v0.0.0 pointe sur main.


Incrément 1  Socle Laravel exécutable
Version cible
Résultat observable
Condition de passage
v0.1.0
La page d’accueil Laravel répond localement et les tests du framework passent.
Un clone propre peut installer, démarrer et tester le socle avec les commandes documentées.

Travail demandé
    • Créer le projet Laravel avec Composer.
    • Configurer APP NAME et une page d’accueil de projet.
    • Identifier les répertoires clés et décrire le cycle route contrôleur réponse.
    • Ajouter Pint et l’outil d’analyse statique choisi.
Contraintes techniques
    • composer.lock est versionné.
    • .env n’est jamais versionné et .env.example ne contient aucun secret.
    • La version PHP et les extensions requises sont documentées.
Questions de découverte
    13. Quel fichier reçoit réellement la requête HTTP.
    14. À quoi servent Artisan, Composer et les Service Providers.
    15. Pourquoi figer les dépendances directes et transitives.
GitLab imposé
    • Branche  feat 1 bootstrap laravel.
    • Commits suggérés  build composer create laravel application  puis  feat home expose project entry page.
    • Tag annoté v0.1.0 après revue.
Docker évolutif
    • Ajouter un Dockerfile dev simple exécutant PHP et Composer.
    • Le bind mount autorise le rechargement du code local.
    • Documenter la différence entre image, conteneur, volume et port.
Pipeline et déploiement
    • Étapes  composer validate, composer install, Pint check, analyse statique et tests.
    • Mettre en cache les dépendances sans mettre vendor dans le dépôt.
DevLog obligatoire
    • Décrire l’amorçage Laravel, la place de public index.php et le rôle du lockfile.
    • Comparer une exécution locale native et conteneurisée.
Validation de l’incrément  Un clone propre peut installer, démarrer et tester le socle avec les commandes documentées.


Incrément 2  Environnement applicatif et MySQL
Version cible
Résultat observable
Condition de passage
v0.2.0
Laravel communique avec MySQL dans un environnement local reproductible.
Le pipeline établit une connexion MySQL et les commandes de diagnostic réussissent dans un clone propre.

Travail demandé
    • Ajouter MySQL à Docker Compose et configurer la connexion par variables d’environnement.
    • Ajouter un healthcheck MySQL et une commande de diagnostic.
    • Créer un environnement de test distinct.
Contraintes techniques
    • Aucune adresse de service n’est codée en dur dans PHP.
    • Les données MySQL persistent dans un volume nommé.
    • La base de test est isolée de la base de développement.
Questions de découverte
    16. Pourquoi depends on ne garantit pas que MySQL accepte déjà des connexions.
    17. Quelle différence entre configuration construite et configuration injectée.
    18. Pourquoi config cache peut provoquer un comportement surprenant.
GitLab imposé
    • Branche  feat 2 mysql environment.
    • Commits suggérés  build docker compose add mysql  puis  config database isolate environments.
    • Tag v0.2.0.
Docker évolutif
    • Services  app et db, réseau privé, volume nommé et healthcheck.
    • Ajouter une cible Makefile ou scripts Composer pour up, down, logs et shell.
Pipeline et déploiement
    • Ajouter un service MySQL au job d’intégration ou utiliser une image de service.
    • Le job attend explicitement la disponibilité de la base.
DevLog obligatoire
    • Présenter le flux de configuration du fichier env jusqu’à PDO.
    • Documenter un incident de démarrage ou de connexion et son diagnostic.
Validation de l’incrément  Le pipeline établit une connexion MySQL et les commandes de diagnostic réussissent dans un clone propre.


Incrément 3  Schéma et modèles Eloquent
Version cible
Résultat observable
Condition de passage
v0.3.0
Le schéma, les modèles et leurs relations représentent correctement le domaine.
Les migrations montent sur une base vide, redescendent si prévu, puis remontent dans le pipeline.

Travail demandé
    • Créer les migrations rooms et reservations.
    • Créer les modèles Room et Reservation avec relations inverses.
    • Définir casts, fillable ou guarded et conventions de dates.
    • Produire le diagramme de classes et le schéma relationnel.
Contraintes techniques
    • Clés étrangères, index et contraintes sont justifiés.
    • Une migration existante fusionnée n’est pas réécrite pour corriger un environnement partagé.
    • Les états métier sont modélisés par enum PHP si le choix est compatible avec la stratégie SQL.
Questions de découverte
    19. Pourquoi une relation Eloquent n’est pas la même chose qu’une clé étrangère.
    20. Quels champs doivent être indexés pour la recherche de chevauchement.
    21. Quel compromis entre enum SQL, chaîne contrainte et enum PHP.
GitLab imposé
    • Branche  feat 3 room reservation schema.
    • Commits séparés pour migrations, modèles relations et documentation du modèle.
    • Tag v0.3.0.
Docker évolutif
    • Ajouter une commande reproductible migrate fresh.
    • Ne pas embarquer de données MySQL dans l’image.
Pipeline et déploiement
    • Exécuter migrate fresh dans le job d’intégration.
    • Publier le journal de migration en artefact seulement en cas d’échec.
DevLog obligatoire
    • Justifier types, index, relation un à plusieurs, casts et stratégie de suppression.
    • Inclure les diagrammes versionnés.
Validation de l’incrément  Les migrations montent sur une base vide, redescendent si prévu, puis remontent dans le pipeline.


Incrément 4  Jeu de données reproductible
Version cible
Résultat observable
Condition de passage
v0.4.0
Une base réaliste est générée sans saisie manuelle.
Une seule commande reconstruit une base cohérente et les relations se vérifient par tests.

Travail demandé
    • Créer factories et seeders pour salles actives et inactives.
    • Créer des réservations confirmées et annulées couvrant plusieurs cas.
    • Ajouter un compte ou jeu de démonstration uniquement si nécessaire.
Contraintes techniques
    • Les données de test sont déterministes lorsque le scénario l’exige.
    • Aucun secret ni donnée personnelle réelle.
    • Factories et seeders gardent des responsabilités distinctes.
Questions de découverte
    22. Pourquoi les factories sont utiles aux tests au-delà du seeding.
    23. Quand rendre un jeu aléatoire déterministe.
    24. Comment éviter des scénarios impossibles produits par Faker.
GitLab imposé
    • Branche  feat 4 seed demo data.
    • Commits  test factories model valid states  et  feat seed development catalog.
    • Tag v0.4.0.
Docker évolutif
    • La commande de bootstrap local enchaîne migration et seed explicitement.
    • Le seed de développement n’est jamais lancé automatiquement en production.
Pipeline et déploiement
    • Le pipeline fabrique ses propres données avec factories.
    • Aucun artefact de base de données n’est réutilisé entre pipelines.
DevLog obligatoire
    • Expliquer factory, state, seeder et idempotence.
    • Présenter deux cas limites rendus testables grâce aux données générées.
Validation de l’incrément  Une seule commande reconstruit une base cohérente et les relations se vérifient par tests.


Incrément 5  Validation des entrées HTTP
Version cible
Résultat observable
Condition de passage
v0.5.0
Les formulaires invalides sont refusés avec des messages utiles.
Les cas invalides prévus retournent les erreurs attendues et aucun enregistrement n’est créé.

Travail demandé
    • Créer les Form Requests pour salles et réservations.
    • Valider champs, formats, ensembles autorisés et cohérence simple des dates.
    • Personnaliser les messages utiles à l’utilisateur.
    • Conserver les anciennes valeurs et afficher les erreurs dans Blade.
Contraintes techniques
    • La validation de forme ne décide pas de la disponibilité d’une salle.
    • Le contrôleur n’accède qu’aux données validées.
    • L’autorisation et la validation sont distinguées.
Questions de découverte
    25. Pourquoi toutes les règles ne doivent pas être placées dans un Form Request.
    26. Quelle différence entre validation syntaxique et invariant métier.
    27. Quand utiliser Rule enum, after validation ou une règle personnalisée.
GitLab imposé
    • Branche  feat 5 validate room reservation inputs.
    • Commits séparés pour demandes, messages et tests de validation.
    • Tag v0.5.0.
Docker évolutif
    • Aucune modification structurelle obligatoire.
    • Vérifier que les erreurs sont visibles derrière le serveur conteneurisé.
Pipeline et déploiement
    • Ajouter les Feature tests de validation à la Quality Gate.
    • Le pipeline échoue si une route accepte une valeur interdite.
DevLog obligatoire
    • Classer les règles entre forme, autorisation et métier.
    • Documenter un choix de règle Laravel et une alternative rejetée.
Validation de l’incrément  Les cas invalides prévus retournent les erreurs attendues et aucun enregistrement n’est créé.


Incrément 6  Objets de données et frontière applicative
Version cible
Résultat observable
Condition de passage
v0.6.0
Le cas d’usage reçoit des données typées indépendantes de la requête HTTP.
Le service peut être testé sans construire de requête HTTP et rejette les objets incohérents.

Travail demandé
    • Créer CreateReservationData et les objets nécessaires à la création d’une salle.
    • Transformer les données validées en objets typés.
    • Définir les exceptions métier attendues.
Contraintes techniques
    • Aucun Request n’entre dans un service métier.
    • Les objets de données sont immuables.
    • Les dates sont converties une seule fois selon une convention documentée.
Questions de découverte
    28. Quel couplage apparaît lorsqu’un service reçoit Illuminate Http Request.
    29. Quand un DTO est utile et quand il ajoute seulement du bruit.
    30. Pourquoi une exception métier ne doit pas dépendre du texte affiché à l’utilisateur.
GitLab imposé
    • Branche  refactor 6 application data boundary.
    • Commits  feat data add typed reservation input  puis  feat domain add business exceptions.
    • Tag v0.6.0.
Docker évolutif
    • Aucune évolution requise.
    • Vérifier que le fuseau du conteneur est cohérent avec la convention applicative.
Pipeline et déploiement
    • Tests unitaires de construction et de conversion des objets de données.
    • Conserver les mêmes Quality Gates.
DevLog obligatoire
    • Illustrer le passage HTTP vers données validées puis objet applicatif.
    • Expliquer immutabilité, typage et gestion du fuseau.
Validation de l’incrément  Le service peut être testé sans construire de requête HTTP et rejette les objets incohérents.


Incrément 7  Accès aux données et injection
Version cible
Résultat observable
Condition de passage
v0.7.0
Les requêtes utiles au cas d’usage sont explicites et injectées.
Les tests distinguent chevauchement, créneau adjacent, réservation annulée et salle différente.

Travail demandé
    • Créer une requête de recherche des réservations confirmées qui chevauchent un créneau.
    • Choisir Eloquent direct, Query Object ou Repository et documenter le choix.
    • Définir un Contract seulement si plusieurs implémentations ou une frontière de test le justifient.
    • Configurer les bindings nécessaires dans un Service Provider.
Contraintes techniques
    • Pas de façade globale dans le service métier.
    • Pas de Service Locator ni d’appel app dans les classes applicatives.
    • La requête de chevauchement reste lisible et couverte par un test d’intégration.
Questions de découverte
    31. Comment traduire exactement l’inégalité de chevauchement en SQL.
    32. Pourquoi masquer Eloquent derrière un Repository peut parfois être une abstraction trompeuse.
    33. Que réalise réellement le conteneur lors de l’autowiring.
GitLab imposé
    • Branche  feat 7 reservation availability query.
    • Commits  feat query detect overlaps  puis  config container bind availability contract.
    • Tag v0.7.0.
Docker évolutif
    • Aucune évolution requise.
    • Utiliser MySQL pour le test d’intégration qui dépend du comportement SQL réel.
Pipeline et déploiement
    • Ajouter un job integration avec MySQL.
    • Les tests unitaires peuvent rester sans base.
DevLog obligatoire
    • Comparer les trois stratégies d’accès aux données.
    • Expliquer Dependency Inversion, binding et autowiring sur le cas réel.
Validation de l’incrément  Les tests distinguent chevauchement, créneau adjacent, réservation annulée et salle différente.


Incrément 8  Service de réservation
Version cible
Résultat observable
Condition de passage
v0.8.0
Toutes les règles métier gouvernent la création d’une réservation.
Les neuf règles métier et l’annulation sont couvertes par des tests verts.

Travail demandé
    • Créer le service ou l’action CreateReservation.
    • Vérifier salle existante et active, dates, durée, futur et absence de conflit.
    • Créer une réservation confirmée ou lever une exception métier explicite.
    • Créer l’action d’annulation idempotente ou documenter son comportement.
Contraintes techniques
    • Le contrôleur n’exécute aucune requête de disponibilité.
    • L’ordre des contrôles est justifié.
    • Une réservation annulée est exclue de la recherche de conflit.
Questions de découverte
    34. Quel principe SOLID est visible dans la séparation du cas d’usage.
    35. Pourquoi une suite de règles correcte en mémoire peut échouer sous concurrence.
    36. Quel résultat retourner au contrôleur sans lui exposer les détails internes.
GitLab imposé
    • Branche  feat 8 enforce reservation rules.
    • Un commit par groupe cohérent  disponibilité, création, annulation, tests.
    • Tag v0.8.0.
Docker évolutif
    • Aucune évolution requise.
    • Les tests métier doivent pouvoir tourner dans le conteneur app.
Pipeline et déploiement
    • Séparer les suites unit et integration si leur coût diffère.
    • Publier un rapport JUnit exploitable par GitLab.
DevLog obligatoire
    • Associer chaque règle à un test et à un résultat.
    • Expliquer au moins une responsabilité déplacée hors du contrôleur.
Validation de l’incrément  Les neuf règles métier et l’annulation sont couvertes par des tests verts.


Incrément 9  Interface web complète
Version cible
Résultat observable
Condition de passage
v0.9.0
Les fonctionnalités de salles et réservations sont utilisables par navigateur.
Tous les parcours fonctionnels peuvent être démontrés et les réponses 404 et 405 sont correctes.

Travail demandé
    • Déclarer des routes nommées et appliquer le route model binding.
    • Créer contrôleurs minces et vues Blade avec layout.
    • Implémenter listes, détails, formulaires, modification, activation et annulation.
    • Gérer 404, 403 si nécessaire et conflits métier avec un retour utilisateur adapté.
Contraintes techniques
    • Aucune requête Eloquent dans Blade.
    • Protection CSRF sur toute mutation.
    • Les verbes HTTP correspondent à l’intention.
    • Les messages flash ne révèlent pas d’information sensible.
Questions de découverte
    37. Quelle différence entre erreur de validation, conflit métier et ressource absente.
    38. Pourquoi utiliser des routes nommées et le route model binding.
    39. Quand une redirection POST Redirect GET est utile.
GitLab imposé
    • Branche  feat 9 web booking workflow.
    • Commits par verticale fonctionnelle plutôt que par type de fichier.
    • Tag v0.9.0.
Docker évolutif
    • Ajouter un service web dédié si l’image app utilise PHP FPM.
    • Documenter ports, réseau interne et exposition publique.
Pipeline et déploiement
    • Ajouter des Feature tests HTTP à la Quality Gate.
    • Conserver captures ou traces de scénarios en artefacts de courte durée si utile.
DevLog obligatoire
    • Tracer une requête complète depuis la route jusqu’à la vue.
    • Expliquer la gestion des statuts HTTP et du CSRF.
Validation de l’incrément  Tous les parcours fonctionnels peuvent être démontrés et les réponses 404 et 405 sont correctes.


Incrément 10  Stratégie de tests et qualité
Version cible
Résultat observable
Condition de passage
v0.10.0
La non régression est automatisée et mesurable.
Les Quality Gates sont obligatoires et les scénarios critiques sont automatisés.

Travail demandé
    • Compléter les tests Unit et Feature selon une pyramide justifiée.
    • Couvrir les scénarios d’acceptation et les erreurs métier.
    • Configurer couverture, Pint et analyse statique.
    • Définir un seuil de couverture raisonnable et progressif.
Contraintes techniques
    • Les tests sont indépendants, reproductibles et lisibles.
    • Pas de test qui dépend de l’ordre d’exécution.
    • Les données sont créées par factories ou builders.
Questions de découverte
    40. Qu’est ce qui rend un test unitaire plutôt qu’un test d’intégration.
    41. Pourquoi cent pour cent de couverture ne garantit pas la qualité.
    42. Quels tests apportent la meilleure protection contre une régression métier.
GitLab imposé
    • Branche  test 10 complete quality strategy.
    • Commits par famille de scénarios et configuration qualité.
    • Tag v0.10.0.
Docker évolutif
    • Créer une cible test identique pour poste local et pipeline.
    • Éviter qu’un bind mount masque les dépendances installées dans l’image CI.
Pipeline et déploiement
    • Stages  validate, quality, test et integration.
    • Rapports JUnit et couverture exposés dans GitLab.
    • La Merge Request est bloquée si un job requis échoue.
DevLog obligatoire
    • Présenter la pyramide retenue et les doublures utilisées.
    • Analyser une régression que la suite aurait détectée.
Validation de l’incrément  Les Quality Gates sont obligatoires et les scénarios critiques sont automatisés.


Incrément 11  Transaction et concurrence
Version cible
Résultat observable
Condition de passage
v0.11.0
Deux créations concurrentes incompatibles ne peuvent pas être confirmées.
Un scénario simultané démontre qu’au plus une réservation conflictuelle est confirmée.

Travail demandé
    • Identifier la fenêtre de course entre contrôle et insertion.
    • Encapsuler l’opération critique dans une transaction.
    • Mettre en œuvre une stratégie de verrouillage ou de sérialisation compatible avec MySQL.
    • Créer un test ou scénario reproductible de concurrence.
Contraintes techniques
    • La solution ne repose pas uniquement sur une requête exists avant insert.
    • Les limites du verrouillage retenu sont documentées.
    • Le traitement des deadlocks ou retries est explicite.
Questions de découverte
    43. Pourquoi une transaction seule ne supprime pas nécessairement la course.
    44. Quelle donnée peut servir de point de verrouillage stable.
    45. Quel compromis entre pessimistic locking, optimistic locking et contrainte de base.
GitLab imposé
    • Branche  fix 11 prevent concurrent double booking.
    • Commits  test reproduce booking race  puis  fix transaction lock booking creation.
    • Tag v0.11.0.
Docker évolutif
    • Configurer MySQL avec le moteur et le niveau d’isolation documentés.
    • Le scénario concurrent doit tourner avec la même famille de base qu’en production.
Pipeline et déploiement
    • Ajouter un job de test de concurrence, éventuellement non parallèle avec lui même.
    • Conserver les journaux en artefact en cas d’échec.
DevLog obligatoire
    • Décrire la course avec une chronologie.
    • Expliquer transaction, isolation, verrou, deadlock et stratégie de reprise.
Validation de l’incrément  Un scénario simultané démontre qu’au plus une réservation conflictuelle est confirmée.


Incrément 12  Ressources multimédias et stratégies de représentation
Version cible
Résultat observable
Condition de passage
v0.12.0
Les salles gèrent des photos et vidéos stockées dans MinIO et exposées au choix en Blade ou en JSON.
Le même cas d’usage rend la galerie en Blade ou en JSON selon la configuration, les objets sont stockés dans MinIO, les scopes sont testés et le cache Redis reste cohérent grâce aux réactions après commit.

Travail demandé
    • Faire évoluer le modèle afin qu’une salle possède zéro à plusieurs ressources multimédias.
    • Créer la migration room_resources avec salle, type média, nom original, type MIME, taille, clé de stockage, ordre, métadonnées et timestamps.
    • Comparer trois stockages  disque local public, contenu binaire MySQL et stockage objet compatible S3 avec MinIO.
    • Retenir MinIO et configurer un disque Laravel dédié.
    • Ajouter et supprimer plusieurs photos ou vidéos avec validation du type MIME réel, de la taille et du nombre de fichiers.
    • Créer des scopes Eloquent locaux pour les salles actives, les réservations confirmées, les ressources ordonnées, les photos et les vidéos.
    • Mettre en place un RoomResourceObserver exécuté après commit afin de transformer les événements Eloquent created, updated et deleted en un événement applicatif RoomMediaChanged.
    • Créer InvalidateRoomMediaCache et WriteRoomMediaAuditLog comme Listeners de RoomMediaChanged, chacun avec une seule responsabilité.
    • Créer deux stratégies de représentation  une réponse Blade pour l’interface web et une réponse JSON pour les consommateurs applicatifs.
    • Sélectionner la stratégie par MEDIA_VIEW_DRIVER dans .env.example, relayé par config media.php avec les valeurs blade ou json.
    • Mettre en cache le catalogue des ressources d’une salle avec Redis et invalider ce cache après toute mutation.
Contraintes techniques
    • Le contrôleur n’appelle jamais env directement  il lit uniquement config media.view_driver.
    • Les deux stratégies implémentent un contrat commun et retournent une réponse HTTP compatible avec Laravel.
    • La valeur de configuration inconnue provoque une erreur explicite au démarrage ou un repli documenté vers Blade.
    • Le fichier binaire reste hors de MySQL  la base conserve les métadonnées et la clé objet.
    • MinIO n’est pas un répertoire public anonyme  les fichiers sont servis par URL temporaire ou flux autorisé.
    • Le cache contient le catalogue et les métadonnées, jamais une URL signée au-delà de sa durée de validité.
    • Observer, Event et Listener n’hébergent pas les règles métier principales du téléversement.
    • RoomResourceObserver ne réalise pas lui-même l’invalidation  il publie RoomMediaChanged après commit et les Listeners exécutent les réactions.
    • Les Listeners sont idempotents et une mutation ne doit pas provoquer deux invalidations concurrentes du même catalogue.
    • Les scopes restent composables, sans effet de bord et sans masquer une règle critique.
Questions de découverte
    46. Quels avantages et limites présentent disque local, BLOB MySQL et MinIO pour la sauvegarde, la montée en charge et le déploiement.
    47. Quelle différence existe entre un scope local, un scope global et une méthode de relation Eloquent.
    48. Quand choisir un Observer plutôt qu’un Event et un Listener explicites.
    49. Pourquoi un événement envoyé avant la validation de la transaction peut-il annoncer une opération finalement annulée.
    50. Quel Design Pattern permet de remplacer la représentation Blade par JSON sans multiplier les conditions dans le contrôleur.
    51. Pourquoi la valeur du .env doit-elle être copiée dans config media.php avant d’être utilisée par l’application.
    52. Quelles limites présente une sélection globale Blade ou JSON par environnement par rapport à la négociation HTTP avec l’en-tête Accept.
    53. Pourquoi un cache sans invalidation explicite produit-il une galerie obsolète.
    54. Comment coordonner MySQL et MinIO alors que les deux systèmes ne partagent pas la même transaction.
    55. Comment diffuser une vidéo volumineuse sans la charger entièrement en mémoire et quel rôle joue HTTP Range.
GitLab imposé
    • Branche  feat 12 room media presentation strategies.
    • Commits suggérés  feat media add resource model and scopes  puis  build minio add object storage  puis  feat media upload validated objects  puis  feat media add blade json strategies  puis  feat media dispatch lifecycle events  puis  perf media cache catalog.
    • La Merge Request contient les deux matrices de décision, les tests, le DevLog et les preuves Blade, JSON, MinIO et Redis.
    • Tag annoté v0.12.0 après fusion sur main.
Docker évolutif
    • Ajouter MinIO et Redis à Docker Compose avec volumes nommés, healthchecks et réseau privé.
    • Ajouter une commande reproductible créant le bucket et sa politique sans intervention manuelle.
    • Configurer endpoint, région, bucket, identifiants de développement et option path style de MinIO.
    • Distinguer l’endpoint interne utilisé entre conteneurs de l’URL publique résoluble par le navigateur ou utiliser une route Laravel de diffusion.
    • Tester successivement MEDIA_VIEW_DRIVER égal à blade puis json sans reconstruire l’image.
    • Conserver les médias hors de l’image applicative et du dépôt Git.
Pipeline et déploiement
    • Démarrer MySQL, MinIO et Redis dans le job d’intégration avec vérification de santé.
    • Créer un bucket de test isolé par pipeline et le nettoyer même après échec.
    • Exécuter une matrice de jobs avec MEDIA_VIEW_DRIVER égal à blade puis json.
    • Tester les scopes, le téléversement, la suppression, la publication unique de RoomMediaChanged, les Listeners, le cache hit et l’invalidation.
    • Vérifier qu’aucune clé MinIO n’apparaît dans les logs ou les artefacts.
DevLog obligatoire
    • Comparer les trois stockages selon coût, portabilité, sécurité, sauvegarde, performance et montée en charge.
    • Comparer Blade et JSON selon consommateur, format, statut HTTP, testabilité et couplage.
    • Tracer la résolution depuis MEDIA_VIEW_DRIVER jusqu’au contrat de stratégie sélectionné.
    • Expliquer scopes locaux et globaux, Observer, Event, Listener, dispatch après commit, Redis, TTL et invalidation.
    • Décrire un échec partiel MySQL MinIO et la compensation retenue.
    • Fournir les preuves des deux représentations, du cache hit et de l’invalidation après mutation.
Validation de l’incrément  Le même cas d’usage rend la galerie en Blade ou en JSON selon la configuration, les objets sont stockés dans MinIO, les scopes sont testés et le cache Redis reste cohérent grâce aux réactions après commit.


Incrément 13  Déploiement continu du code
Version cible
Résultat observable
Condition de passage
v0.13.0
Le commit validé est déployé automatiquement en staging depuis le code source.
Un commit fusionné est déployé en staging, testé puis restaurable par procédure documentée.

Travail demandé
    • Préparer un serveur de staging avec runtime, dépendances et configuration sécurisée.
    • Déployer l’archive ou le checkout du commit validé.
    • Installer les dépendances de production, exécuter migrations et caches Laravel.
    • Ajouter healthcheck, journal de déploiement et procédure de rollback.
Contraintes techniques
    • Le déploiement utilise l’identifiant exact du commit.
    • Les secrets sont des variables GitLab protégées et masquées.
    • Le job de production futur restera manuel et protégé.
    • Les migrations sont compatibles avec un déploiement sans perte.
Questions de découverte
    56. Pourquoi composer install est utilisé et non composer update.
    57. Quelle différence entre artefact de pipeline et workspace du runner.
    58. Dans quel ordre exécuter maintenance, migrations, caches et redémarrage.
GitLab imposé
    • Branche  ci 13 deploy source to staging.
    • Commits séparés pour pipeline, script de déploiement et runbook.
    • Tag v0.13.0 et Environment GitLab staging.
Docker évolutif
    • L’environnement local reste inchangé.
    • Documenter le décalage possible entre runtime serveur et runtime conteneurisé.
Pipeline et déploiement
    • Pipeline  validate, quality, test, package, deploy staging, smoke.
    • Déploiement seulement depuis main après succès des Quality Gates.
    • Job rollback manuel et smoke test HTTP obligatoire.
DevLog obligatoire
    • Insérer le graphe du pipeline, la preuve d’environnement et le temps de reprise.
    • Analyser la sécurité des variables et une panne simulée.
Validation de l’incrément  Un commit fusionné est déployé en staging, testé puis restaurable par procédure documentée.


Incrément 14  Image immuable et registre GitLab
Version cible
Résultat observable
Condition de passage
v0.14.0
Une image de production testée est publiée puis déployée sans reconstruction.
Le digest testé est celui du conteneur déployé et un rollback vers le digest précédent est démontré.

Travail demandé
    • Créer un Dockerfile multi stage avec dépendances de production.
    • Exécuter le processus applicatif avec un utilisateur non root.
    • Publier l’image dans GitLab Container Registry avec SHA et version.
    • Déployer en staging exactement le digest validé puis exécuter un smoke test.
Contraintes techniques
    • Aucun secret dans les layers.
    • Image minimale, healthcheck et politique de logs documentés.
    • latest n’est jamais l’unique référence de déploiement.
    • Build once deploy many.
Questions de découverte
    59. Quelle différence entre tag d’image mutable et digest immuable.
    60. Pourquoi copier composer.lock avant le code améliore le cache.
    61. Que doit contenir une image et que doit fournir l’environnement.
GitLab imposé
    • Branche  ci 14 publish immutable image.
    • Commits  build docker add production stages  puis  ci registry publish and deploy digest.
    • Tag v0.14.0.
Docker évolutif
    • Stages Composer, assets si nécessaires, runtime PHP et serveur.
    • Ajouter .dockerignore, utilisateur dédié et permissions minimales.
    • Fournir un fichier Compose de production ou un manifeste équivalent.
Pipeline et déploiement
    • Jobs  image build, image scan si disponible, image push, deploy staging et smoke.
    • Promotion en production par job manuel protégé utilisant le même digest.
    • Rollback par redéploiement du digest précédent.
DevLog obligatoire
    • Comparer déploiement du code et déploiement de l’image.
    • Documenter taille, layers, cache, sécurité, digest et rollback.
Validation de l’incrément  Le digest testé est celui du conteneur déployé et un rollback vers le digest précédent est démontré.


Incrément 15  Release finale
Version cible
Résultat observable
Condition de passage
v1.0.0
La version stable est documentée, traçable, déployable et démontrable.
La release v1.0.0 est reproductible depuis un clone et toutes les preuves demandées sont disponibles.

Travail demandé
    • Exécuter tous les scénarios d’acceptation.
    • Finaliser README, architecture, exploitation, sécurité et décisions.
    • Consolider CHANGELOG et publier la GitLab Release.
    • Réaliser une démonstration de la fonctionnalité, du pipeline et des deux modes de déploiement.
Contraintes techniques
    • Aucun correctif direct sur main.
    • Le tag final est créé depuis le commit validé.
    • Les preuves sont accessibles sans exposer de secret.
Questions de découverte
    62. Quels choix seraient conservés ou modifiés pour une deuxième version.
    63. Quelle dette technique demeure et quel risque porte t elle.
    64. Comment prouver qu’un binaire déployé correspond à une source auditée.
GitLab imposé
    • Branche  release 1.0.0.
    • Commits finaux limités à documentation, corrections bloquantes et version.
    • Tag annoté v1.0.0 et Release avec notes générées puis relues.
Docker évolutif
    • Figer les images par digest et conserver les versions utiles.
    • Documenter sauvegarde, restauration et rotation des journaux.
Pipeline et déploiement
    • Pipeline du tag  Quality Gates, image, publication, déploiement staging et approbation production.
    • Production manuelle et protégée, avec smoke test et rollback.
DevLog obligatoire
    • Rédiger une synthèse finale reliant les quinze apprentissages.
    • Inclure métriques, incidents, arbitrages, dette et plan de suite.
Validation de l’incrément  La release v1.0.0 est reproductible depuis un clone et toutes les preuves demandées sont disponibles.


C  Stratégie GitLab imposée
Branches
Élément
Convention
Branche stable
main protégée, sans push direct
Branche de travail
feat numéro sujet, fix numéro sujet, ci numéro sujet, docs numéro sujet
Merge Request
Liée à une Issue, petite, relue et pipeline vert
Fusion
Squash autorisé selon convention d’équipe, suppression de la branche
Hotfix
Branche fix depuis main, même Quality Gate, nouveau patch tag

Commits
    • Format Conventional Commits  type portée description.
    • Un commit correspond à une intention testable et réversible.
    • Les commits de formatage massif sont séparés des changements fonctionnels.
    • Les messages tels que update, fix bug ou travail sont refusés.
Tags et releases
Chaque incrément fusionné reçoit un tag annoté. Les versions 0.x matérialisent la progression pédagogique. La version 1.0.0 correspond au périmètre accepté. Toute correction après release incrémente le patch.
Definition of Done de toute Merge Request
    • Issue et critères d’acceptation renseignés.
    • Code, tests et documentation synchronisés.
    • Pipeline vert et absence de secret.
    • DevLog de l’incrément présent.
    • Review checklist complétée.
    • Tag créé uniquement après fusion sur main.

D  Stratégie Docker évolutive
Jalon
Évolution
Intention pédagogique
v0.1.0
Image PHP Composer de développement
Comprendre image, conteneur et bind mount
v0.2.0
Compose app MySQL, réseau, volume, healthcheck
Rendre l’environnement reproductible
v0.3.0
Migrations et modèles
Faire évoluer le schéma sans état manuel
v0.4.0 à v0.10.0
Commandes seed, test et qualité
Aligner poste local et CI
v0.11.0
Configuration MySQL réaliste pour concurrence
Tester le comportement transactionnel
v0.12.0
MinIO, Redis, bucket et matrice Blade JSON
Ajouter stockage objet, cache et représentations configurables
v0.13.0
Le déploiement du code reste volontairement distinct
Observer les limites du runtime serveur
v0.14.0
Dockerfile multi stage de production et Registry
Construire un artefact immuable
v1.0.0
Promotion par digest, rollback et exploitation
Garantir la traçabilité

Règles permanentes
    • Aucun secret n’est copié dans l’image.
    • Les données sont externes aux conteneurs applicatifs.
    • Le processus de production n’est pas root.
    • Les images ont un tag SHA et, pour les releases, un tag sémantique.
    • Les commandes de développement et de CI utilisent les mêmes scripts de projet.
    • Le healthcheck vérifie un état utile et non la simple existence d’un processus.

E  Pipeline et déploiement
Stage
Responsabilité
Déclenchement
validate
Composer validate, contrôles de structure et sécurité basique
Toutes branches et Merge Requests
quality
Pint check et analyse statique
Toutes Merge Requests
test
Tests unitaires et Feature avec rapports
Toutes Merge Requests
integration
MySQL puis MinIO et Redis à partir de v0.12.0
Toutes Merge Requests
package
Archive de code puis image OCI selon le jalon
main et tags
deploy staging
Déployer automatiquement l’artefact validé
main
smoke
Vérifier santé et parcours minimal
Après déploiement
deploy production
Promouvoir l’artefact déjà testé
Tag stable et approbation manuelle
rollback
Redéployer la version précédente
Action manuelle protégée

Deux stratégies obligatoires
Déploiement du code
Le pipeline transfère le code ou un artefact source vers le serveur, installe les dépendances de production, prépare les caches Laravel, applique les migrations et redémarre le service. Cette stratégie met en évidence la dépendance au runtime du serveur.
Déploiement par image
Le pipeline construit une image une seule fois, la teste, la publie dans le registre puis déploie son digest. La production promeut exactement le même artefact que le staging. La configuration et les secrets restent externes.
Règle de promotion  Une étape de production ne reconstruit jamais l’application. Elle promeut un artefact déjà testé.


F  DevLog obligatoire
Emplacement et nom
Un fichier Markdown par version dans docs devlogs, nommé avec le tag, par exemple v0.8.0.md.
Structure minimale
Rubrique
Contenu attendu
Objectif
Problème traité et résultat observable
Concepts
Notions rencontrées et définition dans le contexte du projet
Choix
Décision, alternatives, avantages et limites
Implémentation
Éléments essentiels sans recopier tout le code
Problèmes
Symptôme, hypothèses, diagnostic, correction et preuve
Tests
Scénarios, résultats et limites
GitLab
Issue, Merge Request, pipeline, commits et tag
Docker
Évolution de l’environnement et commandes de vérification
Déploiement
Artefact, environnement, smoke test et rollback si applicable
Bilan
Dette, amélioration et apprentissage personnel

Critères de qualité
    • Le DevLog explique les décisions plutôt que de décrire une liste de fichiers.
    • Les captures sont accompagnées d’une interprétation.
    • Un échec réel et sa résolution sont décrits honnêtement.
    • Les liens et identifiants rendent les preuves vérifiables.
    • Le vocabulaire technique est utilisé avec précision.

G  Scénarios d’acceptation
Numéro
Scénario
Résultat attendu
1
Réservation valide dans une salle active
Création confirmée
2
Créneau qui chevauche une réservation confirmée
Refus pour conflit
3
Créneau adjacent à une réservation
Création autorisée
4
Réservation dans une salle inactive
Refus métier
5
Durée supérieure à quatre heures
Refus
6
Formulaire avec email ou motif invalide
Erreurs affichées sans insertion
7
Réservation annulée sur le même créneau
Elle ne bloque pas
8
Identifiant inexistant
Réponse 404
9
Verbe HTTP non autorisé
Réponse 405 avec méthodes permises
10
Deux requêtes concurrentes incompatibles
Au plus une confirmation
11
Déploiement staging par code
Smoke test vert et version traçable
12
Déploiement staging par digest
Digest testé égal au digest exécuté
13
Rollback
Retour à la version précédente vérifié
14
Ajout d’une photo et d’une vidéo valides
Objets MinIO et métadonnées créés
15
Fichier interdit ou trop volumineux
Refus sans objet orphelin
16
Second affichage de la galerie
Catalogue servi depuis le cache
17
Ajout ou suppression après mise en cache
Cache invalidé et galerie actualisée
18
MEDIA_VIEW_DRIVER égal à blade
Galerie rendue avec une vue Blade
19
MEDIA_VIEW_DRIVER égal à json
Même ressource rendue en JSON structuré
20
Mutation validée après transaction
Observer et Listener exécutés une seule fois après commit

Preuve attendue
Chaque scénario est associé à un test automatisé lorsque cela est pertinent. Les scénarios de déploiement sont prouvés par les jobs GitLab, l’état de l’environnement, un smoke test et le DevLog.

H  Livrables
    • Dépôt GitLab complet avec Issues, Merge Requests, branches supprimées après fusion et tags annotés.
    • Code source Laravel, composer.lock et fichiers de configuration non sensibles.
    • Migrations, factories, seeders et diagrammes.
    • Service MinIO, bucket reproductible, cache Redis et tests des ressources multimédias.
    • Tests et rapports de qualité.
    • Dockerfile de développement puis Dockerfile multi stage de production.
    • Fichiers Compose et scripts d’exploitation.
    • Pipeline GitLab CI CD fonctionnel.
    • Environnement de staging et procédure de production.
    • DevLog de chaque incrément.
    • README, ARCHITECTURE.md, DEPLOYMENT.md, SECURITY.md et CHANGELOG.md.
    • Release GitLab v1.0.0 et image publiée dans le Container Registry.
Documents d’architecture à produire
    • Cycle d’une requête Laravel.
    • Diagramme de classes et schéma relationnel.
    • Décisions d’accès aux données et d’injection.
    • Matrices de décision sur le stockage et l’affichage des ressources.
    • Diagramme expliquant scopes, Observer, Events, Listeners et sélection Blade JSON par configuration.
    • Séquence de réservation et mécanisme de concurrence.
    • Architecture des conteneurs.
    • Graphe du pipeline et flux de promotion des artefacts.

I  Évaluation
Axe
Poids
Indicateurs
Fonctionnel et règles métier
25 pour cent
Réservations, ressources, erreurs et annulation
Architecture Laravel
20 pour cent
Responsabilités, injection et lisibilité
Données et concurrence
15 pour cent
Migrations, requêtes, transaction et verrouillage
Tests et qualité
15 pour cent
Pertinence, reproductibilité et Quality Gates
GitLab et versionnement
10 pour cent
Commits, MR, tags, releases et traçabilité
Docker et CI CD
10 pour cent
Évolution, image, déploiements et rollback
DevLogs et justification
5 pour cent
Analyse, précision et preuves

Conditions éliminatoires techniques
    • Secrets versionnés ou inclus dans une image.
    • Règles de disponibilité uniquement dans un contrôleur ou une vue.
    • Push direct sur main pour contourner une Merge Request.
    • Tags ne pointant pas sur les commits annoncés.
    • Pipeline ou déploiement simulé sans preuve reproductible.
Bonus après v1.0.0
    • Authentification et rôles.
    • API JSON versionnée et documentation OpenAPI.
    • Notifications et rappels asynchrones avec queue.
    • Observabilité avec logs structurés, métriques et traces.
    • Déploiement blue green ou canary.
    • Analyse de vulnérabilités, SBOM et signature d’image.

J  Questions de synthèse
    65. Décrire le trajet complet d’une demande de réservation.
    66. Distinguer validation HTTP, invariant métier et contrainte de base.
    67. Expliquer pourquoi le contrôleur ne doit pas décider de la disponibilité.
    68. Justifier l’usage ou l’absence d’un Repository avec Eloquent.
    69. Comparer stockage local, BLOB MySQL et stockage objet MinIO pour les ressources d’une salle.
    70. Expliquer le cycle de vie du cache d’une galerie et la différence entre TTL et invalidation explicite.
    71. Distinguer scope local, scope global, Observer, Event et Listener sur des exemples du projet.
    72. Expliquer comment MEDIA_VIEW_DRIVER traverse .env et config media.php pour sélectionner Blade ou JSON.
    73. Expliquer comment le conteneur Laravel applique l’inversion de contrôle.
    74. Démontrer la formule de chevauchement sur quatre exemples.
    75. Expliquer pourquoi une transaction seule peut être insuffisante.
    76. Comparer test unitaire, Feature test et test d’intégration MySQL.
    77. Comparer déploiement du code et déploiement d’une image.
    78. Prouver la correspondance entre commit, tag, image et version déployée.
    79. Présenter une dette technique et le prochain incrément qui la réduirait.
Résultat attendu  Un étudiant ayant terminé le parcours doit être capable de reprendre une application Laravel existante, de localiser chaque responsabilité, d’ajouter un cas d’usage testé et de le livrer par une chaîne automatisée sans dégrader la traçabilité.
