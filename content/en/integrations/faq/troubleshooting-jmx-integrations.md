---
title: Troubleshooting JMX Integrations
kind: faq
disable_toc: true
further_reading:
- link: "https://docs.datadoghq.com/integrations/java/"
  tag: "Documentation"
  text: "Java integration"
---

To verify you have access to JMX, test using JConsole or equivalent if possible. If you're unable to connect using JConsole [this article][1] may help to get you sorted. Also, if the metrics listed in your YAML aren't 1:1 with those listed in JConsole you'll need to correct this.

<div class="alert alert-warning">
For all versions of <strong>Agent v6</strong>, the <code>jmxterm</code> JAR is not shipped with the agent. To download and use <code>jmxterm</code>, see the <a href="https://github.com/jiaqi/jmxterm">upstream project</a>.
</div>

If you're able to connect using JConsole, run the following:

```
java -jar /opt/datadog-agent/agent/checks/libs/jmxterm-1.0-DATADOG-uber.jar -l localhost:<PORT> -u <USER> -p <PASSWORD>
```

If you're able to connect using the command above, run: `beans` and send to the [Datadog support team][2] a copy of the results from above along with the following information:


{{< tabs >}}
{{% tab "Agent v6" %}}

* Content of `/var/log/datadog/agent.log`
* Output of the [info command][1]
* Output of: `ps aux | grep jmxfetch`
* A copy of the YAML integration (send the file)


[1]: /agent/guide/agent-commands/#agent-status-and-information
{{% /tab %}}
{{% tab "Agent v5" %}}

* [Agent logs][1]
* Output of the [info command][2]
* Output of: `ps aux | grep jmxfetch`
* Content of `/var/log/datadog/jmxfetch.log`
* Output of: `sudo /etc/init.d/datadog-agent jmx list_everything`
* A copy of the YAML integration.

**Note**: if you're able to see some metrics (`jvm.heap_memory`, `jvm.non_heap_memory`, etc.) it is a sign that JMXFetch is properly running. If you're targeting another application and not seeing related metrics, the likely issue is a misconfiguration in your YAML.


[1]: /agent/faq/send-logs-and-configs-to-datadog-via-flare-command
[2]: /agent/guide/agent-commands/#agent-status-and-information
{{% /tab %}}
{{< /tabs >}}

## Agent troubleshooting

{{< tabs >}}
{{% tab "Agent >= v6.2" %}}

These commands are available since v6.2.0:

| Command                                                | Description                                                                                                                                                             |
| :----------------------------------------              | :---                                                                                                                                                                    |
| `sudo -u dd-agent datadog-agent jmx list matching`     | List attributes that match at least one of your instances configuration.                                                                                                |
| `sudo -u dd-agent datadog-agent jmx list limited`      | List attributes that do match one of your instances configuration but that are not being collected because it would exceed the number of metrics that can be collected. |
| `sudo -u dd-agent datadog-agent jmx list collected`    | List attributes that are actually collected by your current instances configuration.                                                                                    |
| `sudo -u dd-agent datadog-agent jmx list not-matching` | List attributes that don't match any of your instances configuration.                                                                                                   |
| `sudo -u dd-agent datadog-agent jmx list everything`   | List every attributes available that has a type supported by JMXFetch.                                                                                                  |
| `sudo -u dd-agent datadog-agent jmx collect`           | Start the collection of metrics based on your current configuration and display them in the console.                                                                    |

By default theses commands run on all the configured jmx checks. If you want to use them for specific checks, specify them using the `--checks` flag :

`sudo datadog-agent jmx list collected --checks tomcat`

{{% /tab %}}
{{% tab "Agent v6.0 and v6.1" %}}

The Agent 6 ships JMXFetch and supports all of its features, except those listed below.

The Agent doesn't have a full featured interface to JMXFetch, so you may have to run some commands manually to debug the list of beans collected, JVMs, etc. A typical manual call will take the following form:

```shell
/usr/bin/java -Xmx200m -Xms50m -classpath /usr/lib/jvm/java-8-oracle/lib/tools.jar:/opt/datadog-agent/bin/agent/dist/jmx/jmxfetch-0.18.2-jar-with-dependencies.jar org.datadog.jmxfetch.App --check <CHECK_LIST> --conf_directory /etc/datadog-agent/conf.d --log_level INFO --log_location /var/log/datadog/jmxfetch.log --reporter console <COMMAND>
```

where `<COMMAND>` is any of:

- `list_everything`
- `list_collected_attributes`
- `list_matching_attributes`
- `list_not_matching_attributes`
- `list_limited_attributes`
- `list_jvms`

and `<CHECK_LIST>` corresponds to a list of valid `yaml` configurations in
`/etc/datadog-agent/conf.d/`. For instance:

- `cassandra.d/conf.yaml`
- `kafka.d/conf.yaml`
- `jmx.d/conf.yaml`

Example:

```
/usr/bin/java -Xmx200m -Xms50m -classpath /usr/lib/jvm/java-8-oracle/lib/tools.jar:/opt/datadog-agent/bin/agent/dist/jmx/jmxfetch-0.18.2-jar-with-dependencies.jar org.datadog.jmxfetch.App --check cassandra.d/conf.yaml jmx.d/conf.yaml --conf_directory /etc/datadog-agent/conf.d --log_level INFO --log_location /var/log/datadog/jmxfetch.log --reporter console list_everything
```

Note: the location to the JRE tools.jar (`/usr/lib/jvm/java-8-oracle/lib/tools.jar` in the example) might reside elsewhere in your system. You should be able to easily find it with `sudo find / -type f -name 'tools.jar'`.

**Note**: you may wish to specify alternative JVM heap parameters `-Xmx`, `-Xms`, the values used in the example correspond to the JMXFetch defaults.

{{% /tab %}}
{{% tab "Agent v5" %}}

| Command                                                           | Description                                                                                                                                                             |
| :----------------------------------------                         | :---                                                                                                                                                                    |
| `sudo /etc/init.d/datadog-agent jmx list_matching_attributes`     | List attributes that match at least one of your instance configurations.                                                                                                |
| `sudo /etc/init.d/datadog-agent jmx list_limited_attributes`      | List attributes that do match one of your instance configurations but that are not being collected because it would exceed the number of metrics that can be collected. |
| `sudo /etc/init.d/datadog-agent jmx list_collected_attributes`    | List attributes that are actually collected by your current instance configurations.                                                                                    |
| `sudo /etc/init.d/datadog-agent jmx list_not_matching_attributes` | List attributes that don't match any of your instance configurations.                                                                                                   |
| `sudo /etc/init.d/datadog-agent jmx list_everything`              | List every attributes available that has a type supported by JMXFetch.                                                                                                  |
| `sudo /etc/init.d/datadog-agent jmx collect`                      | Start the collection of metrics based on your current configuration and display them in the console.                                                                    |

{{% /tab %}}
{{% tab "Docker Agent" %}}

To check whether Autodiscovery is loading JMX-based checks:

```
docker exec -it <AGENT_CONTAINER_NAME> datadog-agent configcheck
```

To see JMX-based checks status from the Agent:

```
$ docker exec -it <AGENT_CONTAINER_NAME> datadog-agent status
```

{{% /tab %}}
{{< /tabs >}}

## FAQs

### The 350 metric limit

Datadog accepts a maximum of 350 metrics.
A best practice is to limit your metrics to less than 350 by creating filters to refine those metrics collected, but if you need more than 350 metrics, contact [Datadog support][3].

### Java Path

The Agent does not come with a bundled JVM, but uses the one installed on your system. Therefore you must make sure that the Java home directory is present in the path of the user running the Agent.

Alternatively, you can specify the JVM path in the integration's configuration file with the `java_bin_path` parameter.


### Monitoring JBoss/WildFly applications

The following instructions work on Agent v5.6.0+.

JBoss/WildFly applications expose JMX over a specific protocol (Remoting JMX) that is not bundled by default with JMXFetch. To allow JMXFetch to connect to these applications, configure it as follows:

* Locate the `jboss-cli-client.jar` file on your JBoss/WildFly server (by default, its path should be `$JBOSS_HOME/bin/client/jboss-cli-client.jar`).
* If JMXFetch is running on a different host than the JBoss/WildFly application, copy `jboss-cli-client.jar` to a location on the host JMXFetch is running on.
* Add the path of the jar to the `init_config` section of your configuration:

```yaml
  # Datadog Agent >= 5.6.0

  init_config:
    custom_jar_paths:
      - /path/to/jboss-cli-client.jar
```

* Specify a custom URL that JMXFetch connects to, in the `instances` section of your configuration:

  ```yaml
  # Datadog Agent >= 5.6.0

  # The jmx_url may be different depending on the version of JBoss/WildFly you're using
  # and the way you've set up JMX on your server
  # Refer to the relevant documentation of JBoss/WildFly for more information
  instances:
    - jmx_url: "service:jmx:remote://localhost:4447"
      name: jboss-application  # Mandatory, but can be set to any value,
                               # is used to tag the metrics pulled from that instance
  ```

* [Restart the Agent][4].

### Monitoring Tomcat with JMX Remote Lifecycle Listener enabled

The following instructions work on Agent v5.6.0+.

If you're using Tomcat with JMX Remote Lifecycle Listener enabled (see the [Tomcat documentation][5] for more information), JMXFetch needs an additional setup to be able to connect to your Tomcat application.

* Locate the `catalina-jmx-remote.jar` file on your Tomcat server (by default, its path should be `$CATALINA_HOME/lib`).
* If JMXFetch is running on a different host than the Tomcat application, copy `catalina-jmx-remote.jar` to a location on the host JMXFetch is running on.
* Add the path of the jar to the `init_config` section of your configuration:

```yaml
# Datadog Agent >= 5.6.0

init_config:
  custom_jar_paths:
    - /path/to/catalina-jmx-remote.jar
```

* Specify a custom URL that JMXFetch connects to, in the `instances` section of your configuration:

```yaml
# Datadog Agent >= 5.6.0

# The jmx_url may be different depending on the way you've set up JMX on your Tomcat server
instances:
  - jmx_url: "service:jmx:rmi://:10002/jndi/rmi://:10001/jmxrmi"
    name: tomcat-application  # Mandatory, but can be set to any arbitrary value,
                              # is used to tag the metrics pulled from that instance
```

* [Restart the Agent][4].

### SSL troubleshooting

Once JMX is enabled and your Agent check is successfully sending metrics to Datadog, you can secure the remote connection over an SSL Socket.

**Note**: You cannot secure JMX over SSL without using the JMX remote user/password authentication files. If you are using system level permissions to run your application, add these files and run them at startup.

This example shows the Datadog configuration for the [Tomcat integration][6].

* Establish a certificate and key to apply to your [Java app keystore][7].
* Update your Datadog Tomcat `conf.yaml` file located in `conf.d/tomcat.d`:

```yaml
instances:
  - host: localhost
    port: 9000
    user: tomcat
    password: tomcat
    name: tomcat_webapp
    trust_store_path: <KEYSTORE_PATH>
    trust_store_password: <KEY_PASSWORD>
```

* [Restart the Agent][4].

## Further Reading

{{< partial name="whats-next/whats-next.html" >}}


[1]: https://docs.oracle.com/javase/8/docs/technotes/guides/management/faq.html
[2]: /help
[3]: 
[4]: /agent/guide/agent-commands/#restart-the-agent
[5]: https://tomcat.apache.org/tomcat-7.0-doc/config/listeners.html#JMX_Remote_Lifecycle_Listener_-_org.apache.catalina.mbeans.JmxRemoteLifecycleListener
[6]: https://tomcat.apache.org/tomcat-7.0-doc/ssl-howto.html#SSL_and_Tomcat
[7]: http://docs.oracle.com/javase/1.5.0/docs/guide/management/agent.html
