---
assets:
  dashboards: {}
  monitors: {}
  service_checks: assets/service_checks.json
categories:
  - web
  - os & system
  - log collection
creates_events: false
ddtype: check
dependencies:
  - 'https://github.com/DataDog/integrations-core/blob/master/ibm_was/README.md'
display_name: IBM WAS
git_integration_title: ibm_was
guid: ba177bb7-1bad-4ea8-ac59-1bc8a016f4f7
integration_id: ibm-was
integration_title: IBM WAS
is_public: true
kind: integration
maintainer: help@datadoghq.com
manifest_version: 1.0.0
metric_prefix: ibm_was.
metric_to_check: ibm_was.can_connect
name: ibm_was
public_title: "Intégration Datadog/IBM\_WAS"
short_description: IBM Websphere Application Server est un framework utilisé pour héberger des applications Java.
support: core
supported_os:
  - linux
  - mac_os
  - windows
---
## Présentation

Ce check permet de surveiller [IBM Websphere Application Server (WAS)][1] avec l'Agent Datadog. Il est compatible avec IBM WAS versions >= 8.5.5.

## Implémentation

L'intégration Datadog/IBM WAS permet de recueillir les compteurs PMI activés depuis l'environnement WebSphere Application Server. Son implémentation nécessite d'activer le servlet PerfServlet, qui offre un moyen à Datadog de récupérer les données de performance issues de WAS.

Par défaut, ce check recueille les métriques associées à JDBC, au JVM, au pool de threads et au gestionnaire de sessions du servlet. Il est également possible de recueillir des métriques supplémentaires en les spécifiant dans la section « custom_queries ». Consultez le [fichier d'exemple de configuration du check][2] pour découvrir des exemples.

### Installation

Le check IBM WAS est inclus avec le paquet de l'[Agent Datadog][3].

#### Activer le servlet PerfServlet
Le fichier .ear du servlet (PerfServletApp.ear) est situé dans le répertoire `<BASE_HOME>/installableApps`, où `<BASE_HOME>` correspond au chemin d'installation de WebSphere Application Server.

Le servlet de performance se déploie de la même manière que tout autre servlet. Déployez-le sur une instance unique du serveur d'application au sein du domaine.

**Remarque** : depuis la version 6.1, vous devez activer la sécurité des applications pour faire fonctionner PerfServlet.

### Modifier l'ensemble de statistiques actuellement surveillé
Par défaut, votre serveur d'application est uniquement configuré pour la surveillance « Basic ». Pour bénéficier d'une visibilité totale sur votre JVM, vos connexions JDBC et vos connexions servlet, remplacez la valeur de l'ensemble de statistiques actuellement surveillé pour votre serveur d'application « Basic » par « All ».

Depuis la console d'administration de WebSphere, vous pouvez accéder à ce réglage depuis `Application servers > <VOTRE_SERVEUR_APP> > Performance Monitoring Infrastructure (PMI)`.

Une fois ce changement effectué, cliquez sur « Apply » pour enregistre la configuration et redémarrer votre serveur d'application. Les métriques JDBC, JVM et servlet supplémentaires apparaissent quelques instants plus tard dans Datadog.

### Configuration

1. Modifiez le fichier `ibm_was.d/conf.yaml` dans le dossier `conf.d/` à la racine du répertoire de configuration de votre Agent pour commencer à recueillir vos données de performance IBM WAS. Consultez le [fichier d'exemple ibm_was.d/conf.yaml][2] pour découvrir toutes les options de configuration disponibles.

2. [Redémarrez l'Agent][4].

#### Collecte de logs

La collecte de logs est désactivée par défaut dans l'Agent Datadog. Vous devez l'activer dans `datadog.yaml` :
```
    logs_enabled: true
```

Modifiez ensuite `ibm_was.d/conf.yaml` en supprimant la mise en commentaire des lignes `logs` en bas du fichier. Mettez à jour la ligne `path` en indiquant le bon chemin vers vos fichiers de log WAS.

```yaml
logs:
 - type: file
   path: /opt/IBM/WebSphere/AppServer/profiles/InfoSphere/logs/server1/*.log
   source: ibm_was
   service: websphere
```

### Validation

[Lancez la sous-commande status de l'Agent][5] et cherchez `ibm_was` dans la section Checks.

## Données collectées

### Métriques
{{< get-metrics-from-git "ibm_was" >}}


### Checks de service

**ibm_was.can_connect**
Renvoie `CRITICAL` si l'Agent n'est pas capable de se connecter au servlet PerfServlet pour une raison quelconque. Si ce n'est pas le cas, renvoie `OK`.

### Événements

IBM WAS n'inclut aucun événement.

## Dépannage

Besoin d'aide ? Contactez [l'assistance Datadog][7].

[1]: https://www.ibm.com/cloud/websphere-application-platform
[2]: https://github.com/DataDog/integrations-core/blob/master/ibm_was/datadog_checks/ibm_was/data/conf.yaml.example
[3]: https://app.datadoghq.com/account/settings#agent
[4]: https://docs.datadoghq.com/fr/agent/guide/agent-commands/?tab=agentv6#start-stop-and-restart-the-agent
[5]: https://docs.datadoghq.com/fr/agent/guide/agent-commands/?tab=agentv6#agent-status-and-information
[6]: https://github.com/DataDog/integrations-core/blob/master/ibm_was/metadata.csv
[7]: https://docs.datadoghq.com/fr/help


{{< get-dependencies >}}