# Serverless Assignment 2

## Azure vs AWS vs GCP


| Topic                            | Azure service         | AWS service(s)                                                  | GCP service(s)                                                  |
| -------------------------------- | --------------------- | --------------------------------------------------------------- | --------------------------------------------------------------- |
| Functions (triggers & bindings)  | **Azure Functions**   | **AWS Lambda**                                                  | **Google Cloud Functions**                                      |
| Stateful orchestration           | **Durable Functions** | **AWS Step Functions**                                          | **Google Cloud Workflows**                                      |
| Low-code integration             | **Azure Logic Apps**  | **AWS Step Functions**, **Amazon EventBridge**, **AWS AppFlow** | **Google Cloud Workflows**, **Application Integration**         |
| Queues & pub/sub messaging       | **Azure Service Bus** | **Amazon SQS**, **Amazon SNS**                                  | **Google Cloud Pub/Sub**                                        |
| Event routing / cloud events     | **Azure Event Grid**  | **Amazon EventBridge**                                          | **Google Cloud Eventarc**                                       |
| High-throughput streaming ingest | **Azure Event Hubs**  | **Amazon Kinesis Data Streams**                                 | **Google Cloud Pub/Sub** (+ **Dataflow** for stream processing) |


### Function Comparison


| Service       | Azure (Azure Functions)                                                                  | AWS (Lambda)                                                                      | GCP (Cloud Functions)                                                      |
| ------------- | ---------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- | -------------------------------------------------------------------------- |
| **Core**      | HTTP, timers, queues, blobs, Event Grid, Cosmos, Service Bus; bindings reduce glue code. | API Gateway, S3, SQS, SNS, streams, EventBridge; layers; provisioned concurrency. | HTTP, Pub/Sub, GCS, Firestore; broader sources via Eventarc (CloudEvents). |
| **Integrate** | DevOps, GitHub Actions, APIM, ARM/Bicep/Terraform.                                       | API Gateway, Step Functions, SAM/CDK/Terraform, CodePipeline.                     | Cloud Build, Eventarc, Workflows, Terraform.                               |
| **Observe**   | Application Insights, Azure Monitor.                                                     | CloudWatch, X-Ray, Lambda Insights.                                               | Cloud Logging/Monitoring, Trace, OTel (gen 2).                             |
| **Price**     | Pay per run + GB-s; free tier; Premium/Dedicated optional.                               | Per request + GB-s; free tier; extra for provisioned concurrency.                 | Per invocation + GB-s/GHz-s; free tier.                                    |
| **+ / −**     | + Bindings, Azure fit. − Cold starts (Consumption), binding lock-in.                     | + Huge trigger set, mature. − No bindings, 15 min cap, package limits.            | + Simple, gen 2 on Cloud Run. − Fewer built-in triggers than Lambda.       |


### Orchestrating Functions Comparison


| Service       | Azure (Durable Functions)                                   | AWS (Step Functions)                                               | GCP (Workflows)                                        |
| ------------- | ----------------------------------------------------------- | ------------------------------------------------------------------ | ------------------------------------------------------ |
| **Core**      | Orchestrator/activity/entity; timers, retries, human tasks. | Map, parallel, Catch/Retry; many service integrations.             | Steps, branches, HTTP, connectors, sub-workflows.      |
| **Integrate** | Azure Functions + Storage (state); same CI/CD as Functions. | Lambda, ECS, many AWS services; SAM/CDK/Terraform.                 | Cloud Functions, Cloud Run, Pub/Sub; Terraform.        |
| **Observe**   | Durable Monitor, App Insights, history in Storage.          | Console graph, CloudWatch, X-Ray.                                  | Console history, Cloud Logging/Monitoring.             |
| **Price**     | Function executions + state storage.                        | Per state transition (Standard) or request+duration (Express).     | Per workflow step (internal vs external HTTP differs). |
| **+ / −**     | + Code-first patterns. − Replay rules, debugging.           | + Visual, huge catalog. − ASL verbosity, cost at scale (Standard). | + Simple YAML. − Less expressive than ASL/Durable.     |


### Logic App Comparison


| Service        | Azure (Logic Apps)                                                   | AWS (Step Functions + EventBridge + AppFlow)                                                        | GCP (Workflows + Application Integration)                                                        |
| -------------- | -------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
| **What it is** | **Logic Apps** — low-code workflows and 400+ connectors (SaaS, B2B). | **Step Functions + EventBridge** (+ **AppFlow** for SaaS data) — closer in pieces than one product. | **Workflows** + **Application Integration** — orchestration + visual connectors (Apigee family). |
| **Core**       | Designer, connectors, Standard vs Consumption, hybrid gateway.       | Rules, buses, partner events; state machines; AppFlow ETL to S3/Redshift.                           | YAML workflows + integration tasks/templates.                                                    |
| **Integrate**  | On-prem gateway, APIM, DevOps, IaC.                                  | Pipes, API destinations, CodePipeline, IaC.                                                         | Cloud Build, Pub/Sub, Cloud Run, Terraform.                                                      |
| **Observe**    | Run history, Monitor, App Insights.                                  | CloudWatch, X-Ray, EventBridge archive/replay.                                                      | Logging, Monitoring, integration dashboards.                                                     |
| **Price**      | Per action + connector tier; Standard = capacity-based.              | Step Functions + EventBridge + AppFlow each priced.                                                 | Per step + connector usage (varies).                                                             |
| **+ / −**      | + Best connector breadth. − Complex flows, connector cost.           | + Flexible for devs. − Not one Logic Apps–style designer.                                           | + GCP-native. − Split products, smaller catalog vs Logic Apps.                                   |


### Bus Comparison


| Service       | Azure (Service Bus)                                            | AWS (SQS + SNS)                                                    | GCP (Pub/Sub)                                                               |
| ------------- | -------------------------------------------------------------- | ------------------------------------------------------------------ | --------------------------------------------------------------------------- |
| **Core**      | Queues, topics, transactions, sessions, schedule, defer.       | SQS Standard/FIFO; SNS topics → many targets; DLQs.                | Filtering, DLQ topics, replay (seek), BigQuery subs.                        |
| **Integrate** | Functions, Logic Apps, AMQP/JMS, SDKs.                         | Lambda, EventBridge, SDKs.                                         | Functions, Cloud Run, Dataflow, gRPC/REST.                                  |
| **Observe**   | Azure Monitor, Service Bus Explorer.                           | CloudWatch, X-Ray.                                                 | Cloud Monitoring, Logging.                                                  |
| **Price**     | Tiered (Basic/Standard/Premium); ops + capacity units.         | Per SQS request + SNS publish/delivery; free tiers.                | Data volume (ingest/egress); min billable size per message.                 |
| **+ / −**     | + Enterprise patterns (sessions, dup detect). − Pricing/tiers. | + Simple, huge scale. − Two services for full pub/sub+queue story. | + Unified model, replay. − No classic queues/transactions like Service Bus. |


### Event Comparison


| Service       | Azure (Event Grid)                                                   | AWS (EventBridge)                                                 | GCP (Eventarc)                                                     |
| ------------- | -------------------------------------------------------------------- | ----------------------------------------------------------------- | ------------------------------------------------------------------ |
| **Core**      | Topics, domains, filters, retries, DLQ, CloudEvents.                 | Buses, rules, archive/replay, schema registry, Pipes.             | Audit log + custom sources, filtering, CloudEvents.                |
| **Integrate** | Native Azure sources, Functions, Logic Apps, IaC.                    | 200+ sources, Lambda/SQS/SNS/Step Functions, IaC.                 | GCP services, Pub/Sub transport, Terraform.                        |
| **Observe**   | Monitor metrics/logs, DLQ for failures.                              | CloudWatch, replay from archive.                                  | Logging, Monitoring, Pub/Sub metrics.                              |
| **Price**     | Per published event (+ free tier).                                   | Per event (+ free tier on default bus for AWS events).            | Charged as Pub/Sub + audit usage (no separate Eventarc line item). |
| **+ / −**     | + Azure-native, CloudEvents. − Fewer SaaS partners than EventBridge. | + Richest rules/partners, archive. − Cost vs Event Grid at scale. | + Cheap with GCP events. − Routing less general than EventBridge.  |


### Ingestion Comparison


| Service       | Azure (Event Hubs)                                                   | AWS (Kinesis Data Streams)                                           | GCP (Pub/Sub + Dataflow)                                                                         |
| ------------- | -------------------------------------------------------------------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
| **Core**      | Partitions, TUs/PUs, Capture, Kafka endpoint.                        | Shards, enhanced fan-out, on-demand option, retention.               | Global topics, retention, seek; Dataflow windows/aggregations.                                   |
| **Integrate** | Functions, Stream Analytics, Spark, Kafka clients.                   | Lambda, Firehose, Flink (Managed Service), SDKs.                     | Functions, Cloud Run, Dataflow, BigQuery subscription.                                           |
| **Observe**   | Monitor, lag, logs.                                                  | CloudWatch, shard metrics, iterator age.                             | Backlog metrics, Dataflow dashboards.                                                            |
| **Price**     | Throughput/processing units + ingress (+ Capture).                   | Per shard/stream or on-demand + data out.                            | Pub/Sub volume + Dataflow resources (+ Pub/Sub Lite option).                                     |
| **+ / −**     | + Kafka wire compatibility, Capture. − Capacity planning (Standard). | + Mature streaming on AWS. − Shards ops (provisioned), no Kafka API. | + Fully managed scale, BigQuery path. − Kafka needs a bridge; processing is separate (Dataflow). |


