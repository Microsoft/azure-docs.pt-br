---
title: include file description: include file services: event-hubs author: spelluru ms.service: event-hubs ms.topic: include ms.date: 03/31/2021 ms.author: spelluru ms.custom: "include file","fasttrack-edit","iot","event-hubs"

---

A tabela a seguir fornece cotas e limites específicos para os [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/). Para saber mais sobre os preços dos Hubs de Eventos, veja os [preços dos Hubs de Eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="common-limits-for-all-tiers"></a>Limites comuns para todas as camadas
Os limites a seguir são comuns em todas as camadas. 

| Limite |  Observações | Valor |
| --- |  --- | --- |
| Número de namespaces de Hubs de Eventos do Azure por assinatura |- |100 |
| Número de hubs de eventos por namespace | As solicitações subsequentes para a criação de um hub de eventos são rejeitadas. |10 |
| Tamanho do nome de um hub de eventos |- | 256 caracteres |
| Tamanho de um nome de grupo de consumidores | O protocolo Kafka não requer a criação de um grupo de consumidores. | <p>Kafka: 256 caracteres</p><p>AMQP: 50 caracteres |
| Número de destinatários sem época por grupo de consumidores |- |5 |
| Número de regras de autorização por namespace | As solicitações subsequentes de criação de regra de autorização são rejeitadas.|12 |
| Número de chamadas para o método GetRuntimeInformation |  - | 50 por segundo | 
| Número de VNets (redes virtuais) | - | 128 | 
| Número de regras de configuração de IP | - | 128 | 

### <a name="basic-vs-standard-tiers"></a>Camadas Básica versus Standard
A tabela a seguir mostra os limites que podem ser diferentes para as camadas Basic e Standard. 

| Limite | Observações | Basic | Standard |
|---|---|--|---|
| Tamanho máximo de publicação do Hubs de Eventos| &nbsp; | 256 KB | 1 MB |
| Número de grupos de consumidores por hub de eventos | &nbsp; |1 |20 |
| Número de conexões AMQP por namespace | Solicitações subsequentes de conexões adicionais são rejeitadas e uma exceção é recebida pelo código de chamada. |100 |5\.000|
| Período de retenção máximo dos dados do evento | &nbsp; |1 dia |Um a sete dias |
| Unidades de produtividade máxima |Exceder esse limite vai causar a limitação dos seus dados e gerar uma [exceção de servidor ocupado](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Para solicitar um número maior de unidades de produtividade para um nível Standard, preencha uma [solicitação de suporte](../articles/azure-portal/supportability/how-to-create-azure-support-request.md). As [unidades de produtividade adicionais](../articles/event-hubs/event-hubs-auto-inflate.md) estão disponíveis em blocos de 20, em uma base de compra garantida. |20 | 20 | 
| O número de partições por hub de eventos | |32 | 32 | 

> [!NOTE]
>
> Você pode publicar eventos individualmente ou em lotes. 
> O limite de publicação (de acordo com o SKU) aplica-se independentemente de ser um único evento ou um lote. A publicação de eventos maiores que o limite máximo será rejeitada.

### <a name="dedicated-tier-vs-standard-tier"></a>Camada Dedicado versus camada Standard
A oferta de Hubs de Eventos Dedicados é cobrada a um preço mensal fixo, com um mínimo de quatro horas de uso. O nível Dedicado oferece todos os recursos do plano Standard, mas com capacidade em escala empresarial e limites para clientes com cargas de trabalho exigentes. 

Confira este [documento](../articles/event-hubs/event-hubs-dedicated-cluster-create-portal.md) sobre como criar um cluster de Hubs de Eventos dedicados usando o portal do Azure.

| Recurso | Standard | Dedicado |
| --- |:---|:---|
| Largura de banda | 20 TUs (até 40 TUs) | 20 CUs |
| Namespaces |  1 | 50 por CU |
| Hubs de Eventos |  10 por namespace | 1\.000 por namespace |
| Eventos de entrada | Pagamento por milhão de eventos | Incluso |
| Tamanho da mensagem | 1 milhão de bytes | 1 milhão de bytes |
| Partições | 32 por Hub de Eventos | 1\.024 por Hub de Eventos<br/>2\.000 por CU |
| Grupos de consumidores | 20 por Hub de Eventos | Nenhum limite por CU, 1000 por hub de eventos |
| Conexões orientadas | 1\.000 incluídos, 5.000 no máximo | 100 mil incluídos e máximo |
| Retenção de mensagem | Sete dias, 84 GB incluídos por CU | 90 dias, 10 TB incluídos por CU |
| Capturar | Pagamento por hora | Incluso |


### <a name="schema-registry-limitations"></a>Limitações do registro de esquema

#### <a name="limits-that-are-the-same-for-standard-and-dedicated-tiers"></a>Os limites são os mesmos para as camadas standard e dedicada 
| Recurso | Limite | 
|---|---|
| Comprimento máximo de um nome de grupo de esquemas | 50 |  
| Comprimento máximo de um nome de esquema | 100 |    
| Tamanho em bytes por esquema | 1 MB |   
| Número de propriedades por grupo de esquemas | 1024 |
| Tamanho em bytes por chave de propriedade de grupo | 256 | 
| Tamanho em bytes por valor da propriedade de grupo | 1024 | 


#### <a name="limits-that-are-different-for-standard-and-dedicated-tiers"></a>Os limites são os diferentes para as camadas standard e dedicada 

| Limite | Standard | Dedicado | 
|---|---|--|
| Tamanho do registro de esquema (namespace) em megabytes | 25 |  1024 |
| Número de grupos de esquema em um Registro ou namespace de esquema | 1 – excluindo o grupo padrão | 1000 |
| Número de versões de esquema em todos os grupos de esquema | 25 | 10000 |