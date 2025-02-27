---
title: Modifier un monitor
type: apicontent
order: 26.03
external_redirect: /api/#modifier-un-monitor
---

## Modifier un monitor
##### ARGUMENTS
* **`query`** [*obligatoire*] :
    La requête de la métrique pour laquelle les alertes sont envoyées.
* **`name`** [*obligatoire*] :
    Le nom du monitor.
* **`message`** [*facultatif*, *défaut*=**dynamique, en fonction de la requête**] :
    Un message à inclure avec les notifications pour ce monitor. Les notifications par e-mail peuvent être envoyées à des utilisateurs spécifiques en utilisant la même notation « @nomutilisateur » que les événements.
* **`options`** [*facultatif*, *défaut*=**{}**] :
    Reportez-vous à la documentation relative à la création des monitors pour obtenir plus d'informations sur les options disponibles.
* **`tags`** [*facultatif*, *défaut*=**None**] :
    La liste des tags à associer à votre monitor. Cela peut vous aider à catégoriser et à filtrer les monitors.
