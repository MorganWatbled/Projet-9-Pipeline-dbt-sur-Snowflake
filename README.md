# Évolution des profils sociodémographiques — Pipeline dbt sur Snowflake

Pipeline de transformation de données publiques INSEE, construit avec dbt (Data Build Tool) sur un data warehouse Snowflake, pour analyser l'évolution de profils sociodémographiques.

---

## Contexte / besoin métier

L'objectif du projet est d'analyser l'évolution de profils sociodémographiques en valorisant des données publiques mises à disposition par l'INSEE. Plutôt qu'une analyse ponctuelle, le projet met en place un **pipeline de données structuré et réutilisable** : de l'ingestion des données brutes jusqu'à des tableaux de KPI exploitables, avec transformations tracées, testées et documentées.

## Données (source, qualité, limites)

**Sources :** données publiques de l'INSEE, complétées par des données de base propres au projet, chargées telles quelles dans une couche RAW du data warehouse Snowflake avant toute transformation.

**Qualité :** la qualité est prise en charge nativement par le pipeline dbt — un fichier `source.yml` déclare chaque source (nom, base de données, tables et colonnes avec leur description), et des tests dbt sont exécutés pour vérifier l'absence de valeurs nulles et de doublons sur les champs clés, garantissant la fiabilité des données avant leur passage en couches supérieures.

**Limites :**
- Le pipeline dépend de la mise à jour et de la stabilité de format des publications INSEE en amont ; toute évolution de leur structure nécessite une adaptation des modèles de staging.
- Les tests dbt couvrent les valeurs nulles et les doublons ; d'autres règles de qualité plus spécifiques au domaine sociodémographique pourraient être ajoutées si besoin.

## Démarche (choix, outils, étapes)

Le pipeline suit l'architecture dbt en couches : **Sources de données → Tables RAW → Modèles (staging, intermediate, marts) → Dashboards**.

1. **Sources de données** : identification et déclaration des sources (bases applicatives, fichiers INSEE) dans `source.yml`, avec documentation des tables et colonnes, et tests de qualité associés.
2. **RAW Layer** : chargement des données dans Snowflake sans modification, pour conserver une trace fidèle des données d'origine.
3. **Staging** : nettoyage et standardisation — renommage des colonnes, harmonisation des formats.
4. **Intermediate** : transformations plus complexes — jointures, préparation de colonnes, agrégations (somme, count...), filtrage.
5. **Data Marts** : construction des tables finales (faits et dimensions), prêtes pour l'analyse et le reporting.
6. **Tests et documentation** : ajout de tests de qualité dbt et génération automatique de la documentation du pipeline.
7. **Restitution** : création des graphiques de KPI dans Excel à partir des tables finales.

**Outil :** dbt (Data Build Tool) pour les transformations SQL, Snowflake comme data warehouse, Excel pour la restitution des KPI.

## Résultats + impact / recommandations

- Un pipeline de données structuré et documenté, organisé en couches (RAW → staging → intermediate → marts), permettant de tracer chaque transformation depuis la donnée source INSEE jusqu'au KPI final.
- Des sources déclarées et testées via `source.yml`, garantissant une détection précoce des valeurs nulles ou des doublons avant qu'ils ne se propagent dans les tables finales.
- Des tableaux de KPI sur les profils sociodémographiques, restitués dans Excel sous forme de graphiques.
- **Impact attendu :** un pipeline réutilisable et évolutif, où une mise à jour des données sources INSEE se propage automatiquement jusqu'aux tables finales via les transformations dbt, plutôt qu'une analyse à refaire manuellement à chaque nouvelle publication.

## Limites + prochaines pistes

- Le projet couvre la mise en place du pipeline et sa restitution dans Excel ; une connexion directe entre les data marts Snowflake et un outil de visualisation (Power BI, Tableau) permettrait de s'affranchir de l'étape manuelle Excel.
- L'automatisation de l'exécution du pipeline dbt (orchestration planifiée) n'est pas couverte par ce livrable et pourrait être une prochaine étape pour suivre l'évolution des profils sociodémographiques dans la durée.
- L'ajout de tests dbt plus spécifiques (cohérence des plages de valeurs, croisements entre indicateurs) renforcerait la fiabilité du pipeline au-delà des contrôles de nullité et de doublons déjà en place.

---

*Projet de pipeline de données réalisé avec dbt et Snowflake, à partir de données publiques de l'INSEE.*
