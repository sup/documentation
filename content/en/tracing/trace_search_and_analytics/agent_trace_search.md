---
title: Agent trace Search Configuration
kind: Documentation
---

[Trace Search & Analytics][1] is used to filter APM data by user-defined tags such as `customer_id`, `error_type`, or `app_name` to help troubleshoot and filter your requests. To enable it, either:

* Configure your APM tracer to emit the relevant analytics from your services—either [automatically][2] or [manually][3].
* Configure the Datadog Agent to emit the relevant analytics from your services (instructions below).

**Note**: To enable trace search and analytics with the Agent, [services][1] must be already flowing into Datadog.

1. Once [your services are set up][4], navigate to the [Trace Search & Analytics docs page][5] to find a list of services and resource names available for use in Trace Search.
3. Select the `environment` and `services` from which to extract [APM events][6].
2. Update your Datadog Agent configuration (based on Agent version) with the information below:

{{< tabs >}}
{{% tab "Agent 6.3.0+" %}}
In `datadog.yaml`, add `analyzed_spans` under `apm_config`. For example:

```yaml
apm_config:
  analyzed_spans:
    <SERVICE_NAME_1>|<OPERATION_NAME_1>: 1
    <SERVICE_NAME_2>|<OPERATION_NAME_2>: 1
```

{{% /tab %}}
{{% tab "Agent 5.25.0+" %}}
In `datadog.conf`, add `[trace.analyzed_spans]`. For example:

```
[trace.analyzed_spans]
<SERVICE_NAME_1>|<OPERATION_NAME_1>: 1
<SERVICE_NAME_2>|<OPERATION_NAME_2>: 1
```

{{% /tab %}}
{{% tab "Docker" %}}
Add `DD_APM_ANALYZED_SPANS` to the Agent container environment (compatible with version 12.6.5250+). Format should be a comma-separated regular expressions without spaces. For example:

```
DD_APM_ANALYZED_SPANS="<SERVICE_NAME_1>|<OPERATION_NAME_1>=1,<SERVICE_NAME_2>|<OPERATION_NAME_2>=1"
```

```
`my-express-app|express.request=1,my-dotnet-app|aspnet_core_mvc.request=1`
```

{{% /tab %}}
{{< /tabs >}}

In Datadog, every automatically instrumented service has an `<OPERATION_NAME>`, which is used to set the type of request being traced. For example, if you're tracing a Python Flask application, you might have a `flask.request` as your operation name. In a Node application using Express, you would have `express.request` ask your operation name.

Replace both the `<SERVICE_NAME>` and `<OPERATION_NAME>` in your configuration with the service name and operation name of the traces you want to add to Trace Search.

For example, if you have a Python service named `python-api`, and it's running Flask (operation name `flask.request`), your `<SERVICE_NAME>` would be `python-api`, and your `<OPERATION_NAME>` would be `flask.request`.

[1]: https://app.datadoghq.com/apm/services
[2]: /tracing/trace_search_and_analytics/#automatic-configuration
[3]: /tracing/trace_search_and_analytics/#custom-instrumentation
[4]: /tracing/setup
[5]: https://app.datadoghq.com/apm/docs/trace-search
[6]: /tracing/trace_search_and_analytics/search/#apm-events
