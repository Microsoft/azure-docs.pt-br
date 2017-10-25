A tabela a seguir relaciona as cotas e limites específicos para os [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/). Para saber mais sobre os preços dos Hubs de Eventos, veja os [preços dos Hubs de Eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

| Limite | Escopo | Tipo | Comportamento quando excedido | Valor |
| --- | --- | --- | --- | --- |
| Número de hubs de eventos por namespace |Namespace |estático |As solicitações seguintes para a criação de um novo hub de eventos serão rejeitadas. |10 |
| O número de partições por hub de eventos |Entidade |Estático |- |32 |
| Número de grupos de consumidores por hub de eventos |Entidade |Estático |- |20 |
| Número de conexões AMQP por namespace |Namespace |Estático |As solicitações subsequentes de conexões adicionais serão rejeitadas e uma exceção é recebida pelo código de chamada. |5.000 |
| Tamanho máximo de eventos de Hubs de Eventos|Todo o sistema |Estático |- |256 KB |
| Tamanho máximo do nome de um hub de eventos |Entidade |Estático |- |50 caracteres |
| Número de destinatários sem época por grupo de consumidores |Entidade |Estático |- |5 |
| Período de retenção máximo dos dados do evento |Entidade |Estático |- |Um a sete dias |
| Unidades de produtividade máxima |Namespace |Estático |Exceder o limite de unidades de produtividade causará a limitação dos seus dados e gerará um **[ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)**. Você pode solicitar um número maior de unidades de produtividade para uma camada Standard ao preencher uma [solicitação de suporte](/azure/azure-supportability/how-to-create-azure-support-request). As [unidades de produtividade adicionais](../articles/event-hubs/event-hubs-auto-inflate.md) estão disponíveis em blocos de 20, em uma base de compra garantida. |20 |
| Número de regras de autorização por namespace |Namespace|estático |As solicitações subsequentes de criação de regra de autorização serão rejeitadas.|12 |
