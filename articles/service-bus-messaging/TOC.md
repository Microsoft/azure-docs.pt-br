# Visão geral
## [O que são as mensagens do Barramento de Serviço?](service-bus-messaging-overview.md)
## [Conceitos fundamentais do barramento de serviço](service-bus-fundamentals-hybrid-solutions.md)
## [Arquitetura do Barramento de Serviço](service-bus-architecture.md)
## [Perguntas frequentes](service-bus-faq.md)

# Introdução
## [Criar um namespace](service-bus-create-namespace-portal.md)
### [Filas, tópicos e assinaturas](service-bus-queues-topics-subscriptions.md)
## Usar as filas
### [.NET](service-bus-dotnet-get-started-with-queues.md)
### [Java](service-bus-java-how-to-use-queues.md)
### [Node.js](service-bus-nodejs-how-to-use-queues.md)
### [PHP](service-bus-php-how-to-use-queues.md)
### [Python](service-bus-python-how-to-use-queues.md)
### [Ruby](service-bus-ruby-how-to-use-queues.md)
### [REST](/rest/api/servicebus/queues)
## Usar tópicos e assinaturas
### [.NET](service-bus-dotnet-how-to-use-topics-subscriptions.md)
### [Java](service-bus-java-how-to-use-topics-subscriptions.md)
### [Node.js](service-bus-nodejs-how-to-use-topics-subscriptions.md)
### [PHP](service-bus-php-how-to-use-topics-subscriptions.md)
### [Python](service-bus-python-how-to-use-topics-subscriptions.md)
### [Ruby](service-bus-ruby-how-to-use-topics-subscriptions.md)
## [Criar um aplicativo de barramento de serviço de várias camadas](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)

# Como
## Planejar e projetar
### [Mensagens premium](service-bus-premium-messaging.md)
### [Comparar filas do Azure e filas do Barramento de Serviço](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
### [Otimizar desempenho](service-bus-performance-improvements.md)
### [Recuperação de desastres geográficos e replicação geográfica](service-bus-geo-dr.md)
### [Mensagens assíncronas e de alta disponibilidade](service-bus-async-messaging.md)
### [Manipulação de interrupções e desastres](service-bus-outages-disasters.md)

## Desenvolver
### Manipulação de mensagens
#### [Mensagens, conteúdos e serialização](service-bus-messages-payloads.md)
#### [Transferências de mensagem, bloqueios e liquidação](message-transfers-locks-settlement.md)
#### [Carimbos de data/hora e sequenciamento de mensagem](message-sequencing.md)
#### [Expiração da mensagem (vida útil)](message-expiration.md)
### [Autenticação e autorização](service-bus-authentication-and-authorization.md)
#### [Migrar do ACS para o SAS](service-bus-migrate-acs-sas.md)
#### [Autenticação com assinaturas de acesso compartilhado](service-bus-sas.md)
### [Ações e filtros de tópico](topic-filters.md)
### [Filas e tópicos particionados](service-bus-partitioning.md)
### [Sessões de mensagens](message-sessions.md)
### AMQP
#### [Visão geral do AMQP](service-bus-amqp-overview.md)
#### [.NET](service-bus-amqp-dotnet.md)
#### [Java](service-bus-amqp-java.md)
#### [AMQP e Serviço de Mensagens Java](service-bus-java-how-to-use-jms-api-amqp.md)
#### [Guia do protocolo AMQP](service-bus-amqp-protocol-guide.md)
#### [Operações baseadas em resposta de solicitação AMQP](service-bus-amqp-request-response.md)
### Recursos avançados
#### [Filas de mensagens mortas](service-bus-dead-letter-queues.md)
#### [Mensagens de pré-busca](service-bus-prefetch.md)
#### [Detecção de mensagens duplicadas](duplicate-detection.md)
#### [Contadores de mensagens](message-counters.md)
#### [Adiamento de mensagens](message-deferral.md)
#### [Procura de mensagens](message-browsing.md)
#### [Entidades em cadeia com encaminhamento automático](service-bus-auto-forwarding.md)
#### [Processamento de transação](service-bus-transactions.md)
#### [Implementação do namespace emparelhado](service-bus-paired-namespaces.md)
## Gerenciar
### [Bibliotecas de gerenciamento do Barramento de Serviço](service-bus-management-libraries.md)
### [Logs de diagnóstico](service-bus-diagnostic-logs.md)
### [Suspender e reativar as entidades de mensagens](entity-suspend.md)
### [Usar modelos do Azure Resource Manager](service-bus-resource-manager-overview.md)
#### [Criar um namespace](service-bus-resource-manager-namespace.md)
#### [Criar um namespace e uma fila](service-bus-resource-manager-namespace-queue.md)
#### [Criar um namespace com tópico e assinatura](service-bus-resource-manager-namespace-topic.md)
#### [Criar uma regra de autorização do namespace e da fila](service-bus-resource-manager-namespace-auth-rule.md)
#### [Criar um namespace com tópico, assinatura e regra](service-bus-resource-manager-namespace-topic-with-rule.md)
#### 
### [Usando o Azure PowerShell para provisionar entidades](service-bus-manage-with-ps.md)

# Referência
## .NET
### [Microsoft.ServiceBus.Messaging (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging)
### [Microsoft.Azure.ServiceBus (.NET Padrão)](/dotnet/api/microsoft.azure.servicebus)
## [Java](/java/api/overview/azure/servicebus)
## [PowerShell do Azure](/powershell/module/azurerm.servicebus)
## [REST](/rest/api/servicebus)
## [Exceções](service-bus-messaging-exceptions.md)
## [Cotas](service-bus-quotas.md)
## [Sintaxe SQLFilter](service-bus-messaging-sql-filter.md)
## [Sintaxe SQLRuleAction](service-bus-messaging-sql-rule-action.md)

# Recursos
## [Roteiro do Azure](https://azure.microsoft.com/roadmap/?category=enterprise-integration)
## [Blog](https://blogs.msdn.microsoft.com/servicebus/)
## [Fóruns do MSDN](https://social.msdn.microsoft.com/forums/home?forum=servbus)
## [Preços](https://azure.microsoft.com/pricing/details/service-bus/)
## [Calculadora de preço](https://azure.microsoft.com/pricing/calculator/)
## [Detalhes de preço](service-bus-pricing-billing.md)
## [Exemplos](service-bus-samples.md)
## [ServiceBus360](https://www.servicebus360.com/)
## [Explorer do Barramento de Serviço](https://github.com/paolosalvatori/ServiceBusExplorer)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?product=service-bus)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azureservicebus)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=service-bus)


