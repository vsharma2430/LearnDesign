| Category    | Scenario ID | Scenario                                                      | Importance | Open % |
| ----------- | ----------- | ------------------------------------------------------------- | ---------- | ------ |
| Storage     | SE-3        | What if database connection pool exhausts?                    | Medium     | 36.83% |
| Storage     | SE-4        | What if schema migration fails midway?                        | High       | 28.81% |
| Storage     | SE-6        | What if cache becomes inconsistent with DB?                   | Medium     | 83.21% |
| Storage     | SE-8        | What if leader election fails?                                | High       | 28.98% |
| Storage     | SE-9        | What if hot partition issue happens?                          | High       | 61.79% |
| Storage     | SE-15       | What if split-brain problem occurs?                           | Low        | 47.08% |
| Storage     | SE-17       | What if your primary DB is overloaded?                        | High       | 72.78% |
| Storage     | SE-19       | What if distributed lock never releases?                      | Low        | 50.89% |
| Storage     | SE-22       | What if one shard grows much larger than others?              | Medium     | 68.66% |
| Storage     | SE-28       | What if users see stale data?                                 | High       | 89.32% |
| Storage     | SE-37       | What if Redis crashes?                                        | High       | 36.76% |
| Storage     | SE-38       | What if DB replication lag increases?                         | Medium     | 61.14% |
| Storage     | SE-39       | What if two services update same data simultaneously?         | High       | 83.58% |
| Storage     | SE-45       | What if you must store and serve very large files?            | High       | 75.49% |
| Storage     | SE-47       | What if you must count likes or views at massive scale?       | High       | 69.41% |
| Storage     | SE-49       | What if search must scale to billions of records?             | High       | 28.14% |
| Storage     | SE-51       | What if you need globally unique IDs at scale?                | Medium     | 45.33% |
| API         | SE-10       | What if one API starts timing out?                            | High       | 66.39% |
| API         | SE-27       | What if duplicate payments happen?                            | High       | 73.74% |
| API         | SE-44       | What if you must change an API without breaking clients?      | Low        | 41.36% |
| API         | SE-50       | What if you must paginate a huge dataset?                     | High       | 85.49% |
| Real-time   | SE-2        | What if real-time analytics becomes delayed?                  | High       | 80.32% |
| Real-time   | SE-34       | What if millions of websocket connections drop together?      | Low        | 88.80% |
| Real-time   | SE-36       | What if millions join livestream at same time?                | Low        | 57.60% |
| Real-time   | SE-48       | What if a celebrity post triggers a fanout storm?             | Medium     | 82.64% |
| Scalability | SE-5        | What if AI inference latency increases?                       | Low        | 60.93% |
| Scalability | SE-11       | What if network partition happens?                            | High       | 89.80% |
| Scalability | SE-12       | What if 10M requests hit your service suddenly?               | Low        | 37.33% |
| Scalability | SE-16       | What if CDN fails globally?                                   | Low        | 86.58% |
| Scalability | SE-20       | What if Kubernetes node crashes?                              | High       | 32.96% |
| Scalability | SE-23       | What if load balancer becomes bottleneck?                     | Medium     | 72.84% |
| Scalability | SE-25       | What if retry mechanism causes cascading failures?            | Low        | 63.99% |
| Scalability | SE-29       | What if traffic spikes 100x during sale/event?                | Low        | 30.24% |
| Scalability | SE-30       | What if thundering herd problem happens?                      | Medium     | 39.45% |
| Scalability | SE-31       | What if one microservice becomes slow?                        | Medium     | 64.38% |
| Scalability | SE-35       | What if autoscaling doesn't scale fast enough?                | Medium     | 29.94% |
| Scalability | SE-40       | What if deployment breaks production?                         | Medium     | 75.53% |
| Scalability | SE-43       | What if system must scale from 1M to 1B users?                | High       | 51.43% |
| Scalability | SE-46       | What if users are spread across the globe?                    | Low        | 89.52% |
| Messaging   | SE-7        | What if notification service sends duplicates?                | Medium     | 55.73% |
| Messaging   | SE-14       | What if queue backlog increases continuously?                 | Medium     | 89.41% |
| Messaging   | SE-41       | What if Kafka goes down?                                      | High       | 73.17% |
| Reliability | SE-1        | What if one region completely fails?                          | Low        | 51.87% |
| Reliability | SE-21       | What if cloud provider region becomes unavailable?            | High       | 54.10% |
| Reliability | SE-24       | What if your database suddenly gets deleted?                  | High       | 80.02% |
| Reliability | SE-26       | What if backups are corrupted?                                | Medium     | 32.01% |
| Reliability | SE-33       | What if disaster recovery also fails?                         | High       | 79.90% |
| Reliability | SE-42       | What if data corruption happens silently?                     | High       | 41.65% |
| Reliability | SE-53       | What if you must choose between consistency and availability? | High       | 85.65% |
| Security    | SE-13       | What if authentication service goes down?                     | High       | 34.00% |
| Security    | SE-18       | What if secrets/API keys leak?                                | Medium     | 51.58% |
| Security    | SE-32       | What if malicious traffic/DDoS attack comes?                  | High       | 32.30% |
| Security    | SE-52       | What if you must rate limit a public API?                     | High       | 65.70% |
