# Comparatif des approches - projet fraude (TP04)

Date de consultation de la documentation officielle : 19/10/2026.
Mesures locales : blocs A et B (F1 sur le jeu de test, découpage 75/25, graine 42).

| Approche | F1 test | Temps humain | Temps machine | Coût estimé (source, date) | Explicabilité | Export / réversibilité |
|---|---|---|---|---|---|---|
| Logistique (réf.) | 0.7407 | ... | 0.11 s | 0 (poste local) | Maximale | Totale |
| Manuel (TP2) | 0.8669 | ... | 2.67 s (un entraînement) | 0 (poste local) | Moyenne / bonne | Totale |
| FLAML 60 s | 0.8582 | ... | 60.3 s | 0 (poste local) | Moyenne (journal de 158 essais, importances selon le modèle) | Totale (bibliothèque Python locale) |
| Vertex AI AutoML | non mesuré | ... | non mesuré | Entraînement tabulaire : 21,252 $ par node-hour ; déploiement facturé en plus à l'heure (cloud.google.com/vertex-ai/pricing, 19/10/2026) | Bonne (pages « Feature attributions » et « View model architecture » dans la doc) | Partielle (une page « Export model » existe pour le tabulaire : lire ses conditions) |
| SageMaker Autopilot | non mesuré | ... | non mesuré | Heures d'instances SageMaker ; exemple AWS : build standard de 2 h sur ml.c5.18xlarge = 7,30 $ (annonce AWS de mars 2024, à revérifier sur la page de tarification) | Bonne (rapport d'importance des variables, rapport de performance, notebooks générés) | Moyenne (endpoint et artefacts du modèle ; notebooks de code modifiables) |
| Azure Automated ML | non mesuré | ... | non mesuré | Pas de supplément pour le service ; on paie le calcul et le stockage (azure.microsoft.com/pricing/details/machine-learning, 19/10/2026) | Bonne (explicabilité du meilleur modèle, métriques journalisées par run) | Bonne (conversion ONNX possible via le SDK ; modèle sérialisé .pkl) |

## Ce qu'il faut pour commencer (Bloc E, étape E1)

- Vertex AI : un projet Google Cloud (étape « Set up your project and environment » du tutoriel tabulaire).
- SageMaker Autopilot : un compte AWS ; l'interface est intégrée à SageMaker Canvas depuis le 30/11/2023, l'API AutoML reste disponible.
- Azure Automated ML : un espace de travail Azure Machine Learning ; la page consultée décrit le SDK v1, dont le support s'arrête le 30/06/2026, le SDK v2 est la version actuelle.

## Ce qui est journalisé

- Vertex AI : modèle et évaluation visibles dans la console, registre de modèles (à confirmer en visitant la console).
- SageMaker Autopilot : candidats classés, rapports d'explicabilité et de performance, notebooks d'exploration des données et de définition des candidats.
- Azure Automated ML : jobs et jobs enfants avec leurs métriques, modèle recommandé, option de jeu de test.
- FLAML : une ligne par essai dans le journal (famille, configuration, perte de validation, temps), lue par inspecter.py.

## Commentaire (5 lignes)

1. Logistique : à utiliser en premier, comme référence rapide et lisible ; dans notre cas elle plafonne à 0,74 de F1.
2. Réglage manuel : le meilleur score mesuré (0,8669) pour environ 3 s d'entraînement ; il se justifie quand on connaît déjà la bonne famille de modèles.
3. FLAML local : 0,8582 en 60 s sans choisir la famille ; le score se fige entre 60 s et 120 s, donc il se justifie pour explorer vite et gratuitement.
4. Plateformes cloud : utiles quand l'entreprise veut un service géré (déploiement, registre, gouvernance) et accepte un coût à l'heure ; à éviter pour un petit jeu de 5 000 lignes comme ici.
5. Dans tous les cas, aucune approche ne détecte une fuite de données comme la colonne num_dossier_litige : le contrôle des variables reste humain.

## Sources

- Vertex AI pricing : https://cloud.google.com/vertex-ai/pricing
- Vertex AI, tutoriel tabulaire : https://docs.cloud.google.com/vertex-ai/docs/tutorials/tabular-automl/dataset-train
- SageMaker Autopilot : https://docs.aws.amazon.com/sagemaker/latest/dg/autopilot-automate-model-development.html
- SageMaker Canvas, nouvelle tarification : https://aws.amazon.com/about-aws/whats-new/2024/03/amazon-sagemaker-canvas-pricing-tabular-models
- Azure Machine Learning, AutoML (v1) : https://learn.microsoft.com/en-us/previous-versions/azure/machine-learning/concept-automated-ml?view=azureml-api-1
- Azure Machine Learning, tarification : https://azure.microsoft.com/en-us/pricing/details/machine-learning-services/
