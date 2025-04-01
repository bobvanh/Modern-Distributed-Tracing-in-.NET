# Modern distrubuted tracing in dotnet

Personal notes / summaries.

### Why Open Telemetry

The goal of observability in distributed systems is to gain insight in the
behavior of the system as whole. If all you have are application logs on the
component level, retracing a bug somewhere in an end-to-end operation will be
cumbersome. You'll have to aggregate the logs and correlate log lines. On top
of that the available information and structure may vary. 

Various application moninoring platforms already exist to offer obervability
of structured logs. Elastic APM is a notable example.

OpenTelemetry defines standards that allows for writing instrumentation in a
unified way, leveraging the Open Telemetry Line Protocol (OTLP). This allows
for a variety of backends to consume the telemetry that's emitted from your
subsystems. 

The benefit is that your instrumentation code can cater to a variety of
observability backends. There is no vendor lock-in. Some backend include:

- [Elastic.APM](https://www.elastic.co/observability/application-performance-monitoring)
- [Jaeger](https://www.jaegertracing.io/)
- [Prometheus](https://prometheus.io/)
- [Honeycomb](https://honeycomb.io)
- [Grafana](https://grafana.com/)


### Open Telemetry Signals

Open Telemetry defines standards for the following signals:

- Traces composed of Spans
- Logs
- Metrics

#### Traces and Spans

A Trace aims to describe a logical operation from end to end, composed of
traces describing the constituent interactions over various subsystems.

A Span describes a network interaction, with the following features:

- SpanId
- ParentSpanId : this can refer to a trace or a parent span
- Name : in descriptive terms to person investigating the span
- StartTime
- Duration
- Status : Succes, Failure, Unknown
- Span kind : makes the distinction between client / server / internal call,
  or producer / consumer in async scenarios
- Atributes : property bag die de operatie in voldoende mate van detail
  beschrijven voo analyse

#### Logs and Events

Logs describe events in a system. Typically meant to be human readable, but
they can also be structured to facilitate treating them as documents that you
can index and query. You're well adviced to use a log management system. For
that purpose the [ELK stack](https://www.elastic.co/elastic-stack/) is a good
example.

Events would be logs that are structured adhering to a specific schema.

[Logging in C# and .NET](https://learn.microsoft.com/en-us/dotnet/core/extensions/logging)

#### Metrics and counters

Metrics are used to measure an aspect of the application behavior over time.
For instance the average duration of handling an HTTP request over time.
Dimensions of a metric enable aggregating over certai properties of the
behavior. For the HTTP request that could be the endpoint, status code,
server instance etc.

Metrics are good for for dashboards and generating alerts for values that
indicate a problem. They don't show causation.

A counter is metric without additional dimensions, like CPU usage.

### Open Telemetry Collector

https://opentelemetry.io/docs/collector/

OTLP signals can be consumed directly by a backend. The Collector can serve as a hub for a variety of backends. Design goals (from the docs):

> __Usability__: Reasonable default configuration, supports popular protocols,
  runs and collects out of the box.
> __Performance__: Highly stable and performant under varying loads and
  configurations.
> __Observability__: An exemplar of an observable service.
> __Extensibility__: Customizable without touching the core code.
> __Unification__: Single codebase, deployable as an agent or collector with
  support for traces, metrics, and logs. When to

## Open Telemetry en Elastic APM

https://www.elastic.co/guide/en/observability/current/apm-open-telemetry.html

Elastic heeft een eigen OTEL Sdk wrapper. Deze stuurt signalen in OTEL formaat, maar heeft bepaald geopinieerde defaults. *TODO : Wat zijn de voordelen hiervan?*

De Elastic APM Agent vertaalt generieke OTEL naar Elastic APM. Als je
applicatie OTEL instrumentatie heeft dan kan je sowieso Elastic APM
koppelen.