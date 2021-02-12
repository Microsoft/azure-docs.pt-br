---
title: Perguntas frequentes - Hubs de Eventos | Microsoft Docs
description: Este artigo fornece uma lista de perguntas frequentes (FAQ) para os Hubs de Eventos do Azure e suas respostas.
ms.topic: article
ms.date: 01/20/2021
ms.openlocfilehash: e6fd4814e771d03827e51f1cd5ee182c9e432cc5
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696101"
---
# <a name="event-hubs-frequently-asked-questions"></a>Perguntas frequentes sobre os Hubs de Eventos

## <a name="general"></a>Geral

### <a name="what-is-an-event-hubs-namespace"></a>O que é um namespace dos Hubs de Eventos?
Um namespace é um contêiner de escopo do Hub de Eventos/Tópicos do Kafka. Ele fornece a você um [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) único. Um namespace serve como um contêiner de aplicativo que pode hospedar vários Hub de Eventos/Tópicos do Kafka. 

### <a name="when-do-i-create-a-new-namespace-vs-use-an-existing-namespace"></a>Quando devo criar um novo namespace em vez de usar um namespace existente?
As alocações de capacidade [(TUs, unidade de produtividade](#throughput-units)) são cobradas no nível do namespace. Um namespace também está associado a uma região.

Talvez você queira criar um novo namespace em vez de usar um existente em um dos seguintes cenários: 

- Você precisa de um hub de eventos associado a uma nova região.
- Você precisa de um hub de eventos associado a uma assinatura diferente.
- Você precisa de um hub de eventos com uma alocação de capacidade distinta (ou seja, a necessidade de capacidade para o namespace com o hub de eventos adicionado excederia o limite de 40 TUs, e você não quer optar pelo cluster dedicado)  

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Qual é a diferença entre os níveis Basic e Standard dos Hubs de Eventos?

A camada Standard dos Hubs de Evento do Azure fornece recursos além daqueles disponíveis na camada Básica. Os recursos abaixo estão incluídos na camada Standard:

* Maior retenção de evento
* Conexões agenciadas adicionais, com uma taxa excedente para além do número incluído
* Mais de um único [grupo de consumidores](event-hubs-features.md#consumer-groups)
* [Captura](event-hubs-capture-overview.md)
* [Integração com Kafka](event-hubs-for-kafka-ecosystem-overview.md)

Para obter mais informações sobre tipos de preço, incluindo Hubs de Eventos Dedicados, veja os [Detalhes de preços dos Hubs de Eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="where-is-azure-event-hubs-available"></a>Onde os Hubs de Eventos do Azure estão disponíveis?

Os Hubs de Eventos do Azure está disponível em todas as regiões do Azure com suporte. Para obter uma lista, visite a página [Regiões do Azure](https://azure.microsoft.com/regions/).  

### <a name="can-i-use-a-single-advanced-message-queuing-protocol-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Posso usar uma única conexão de Advanced Message Queuing Protocol (AMQP) para enviar e receber de vários hubs de eventos?

Sim, contanto que todos os Hubs de Eventos estejam no mesmo namespace.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Qual é o período de retenção máximo para eventos?

Atualmente, o tipo Standard dos Hubs de Eventos dá suporte a um período de retenção máximo de sete dias. Os Hubs de Eventos não servem como um armazenamento de dados permanente. Os períodos de retenção maiores que 24 horas se destinam a cenários em que é conveniente reproduzir um fluxo de eventos nos mesmos sistemas. Por exemplo, para treinar ou verificar um novo modelo de aprendizado de máquina nos dados existentes. Se você precisar de retenção de mensagens por mais de sete dias, a habilitação da [Captura de Hubs de Eventos](event-hubs-capture-overview.md) efetuará pull dos dados do hub de eventos para a conta de armazenamento ou conta de serviço do Azure Data Lake de sua escolha. A habilitação da Captura acarretará uma cobrança com base nas unidades de produtividade compradas.

Você pode configurar o período de retenção para os dados capturados em sua conta de armazenamento. O recurso de **gerenciamento do ciclo de vida** do Armazenamento do Microsoft Azure oferece uma política avançada baseada em regras para contas de armazenamento de Blobs V2 e de uso geral. Use a política para fazer a transição de seus dados para as camadas de acesso apropriadas ou expirá-los ao final do ciclo de vida dos dados. Para obter mais informações, confira [Gerenciar o ciclo de vida do armazenamento de Blobs do Azure](../storage/blobs/storage-lifecycle-management-concepts.md). 

### <a name="how-do-i-monitor-my-event-hubs"></a>Como monitorar meus Hubs de Eventos?
Os Hubs de Eventos emitem métricas exaustivas que fornecem o estado de seus recursos ao [Azure Monitor](../azure-monitor/overview.md). Eles também permitem a avaliação da integridade geral do serviço do Hubs de Eventos, não apenas no nível do namespace, mas também no nível da entidade. Saiba qual monitoramento é oferecido para os [Hubs de Eventos do Azure](event-hubs-metrics-azure-monitor.md).

### <a name="where-does-azure-event-hubs-store-data"></a><a name="in-region-data-residency"></a>Onde os hubs de eventos do Azure armazenam dados?
As camadas padrão e os hubs de eventos do Azure armazenam metadados e dados em regiões que você seleciona. Quando a recuperação de desastre geográfico é configurada para um namespace de hubs de eventos do Azure, os metadados são copiados para a região secundária que você selecionar. Portanto, esse serviço atende automaticamente aos requisitos de residência de dados de região, incluindo aqueles especificados na [central de confiabilidade](https://azuredatacentermap.azurewebsites.net/).

[!INCLUDE [event-hubs-connectivity](../../includes/event-hubs-connectivity.md)]

## <a name="apache-kafka-integration"></a>Integração do Apache Kafka

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>Como integrar o meu aplicativo existente do Kafka com os Hubs de Eventos?
Os Hubs de Eventos fornecem um ponto de extremidade Kafka que pode ser usado por seus aplicativos baseados em Apache Kafka existentes. Basta uma alteração de configuração para obter a experiência de PaaS Kafka. Ela fornece uma alternativa à execução de seu próprio cluster Kafka. Os Hubs de Eventos são compatíveis com o Apache Kafka 1.0 e com versões mais recentes do cliente e funcionam com seus aplicativos, ferramentas e estruturas existentes do Kafka. Para saber mais, confira [Hubs de Eventos para repositório Kafka](https://github.com/Azure/azure-event-hubs-for-kafka).

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>Quais alterações de configuração precisam ser feitas para que o meu aplicativo existente se comunique com os Hubs de Eventos?
Para se conectar a um Hub de Eventos, você precisará atualizar as configurações de cliente do Kafka. Isso é feito por meio da criação de um namespace dos Hubs de Eventos e obtenção da [cadeia de conexão](event-hubs-get-connection-string.md). Altere o bootstrap.servers para apontar o FQDN de Hubs de Eventos e a porta para 9093. Atualize o sasl.jaas.config para direcionar o cliente do Kafka ao seu ponto de extremidade de Hubs de Eventos (que é a cadeia de conexão que você obteve), com a autenticação correta, conforme mostrado abaixo:

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

Exemplo:

```properties
bootstrap.servers=dummynamespace.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXX";
```
Observação: se sasl.jaas.config não for uma configuração com suporte em sua estrutura, localize as configurações que são usadas para definir o nome de usuário e a senha SASL e use-as em vez disso. Defina o nome de usuário como $ConnectionString e a senha para sua cadeia de conexão de Hubs de Eventos.

### <a name="what-is-the-messageevent-size-for-event-hubs"></a>Qual é o tamanho de mensagem/evento para Hubs de Eventos?
O tamanho máximo de mensagem permitido para os Hubs de Eventos é de 1 MB.

## <a name="throughput-units"></a>Unidades de transferência

### <a name="what-are-event-hubs-throughput-units"></a>O que são unidades de produtividade dos Hubs de Eventos?
A taxa de transferência nos Hubs de Eventos define a quantidade de dados em megabytes ou o número (em milhares) de eventos de 1 KB que entram e saem por meio dos Hubs de Eventos. Essa taxa de transferência é medida em unidades de produtividade (TUs). Compre TUs antes de começar a usar o serviço Hubs de Eventos. Seleciona explicitamente as TUs dos Hubs de Eventos usando o portal do Azure ou de modelos do Resource Manager dos Hubs de Eventos. 


### <a name="do-throughput-units-apply-to-all-event-hubs-in-a-namespace"></a>As unidades de produtividade se aplicam a todos os hubs de eventos em um namespace?
Sim, as TUs (unidades de produtividade) se aplicam a todos os hubs de eventos em um namespace dos Hubs de Eventos. Isso significa que você compra TUs no nível do namespace e elas são compartilhadas entre os hubs de eventos no namespace. Cada TU proporciona ao namespace os seguintes recursos:

- Até 1 MB por segundo de eventos de entrada (eventos enviados para um Hub de Eventos), mas não mais de 1.000 eventos de entrada, operações de gerenciamento ou chamadas por segundo à API de controle.
- Até 2 MB por segundo de eventos de saída (eventos consumidos de um Hub de Eventos), mas não mais do que 4096 eventos de saída.
- Até 84 GB de armazenamento de eventos (suficiente para o período de retenção padrão de 24 horas).

### <a name="how-are-throughput-units-billed"></a>Como é a cobrança das unidades de produtividade?
As TUs (unidades de produtividade) são cobradas por hora. A cobrança é baseada no número máximo de unidades selecionado durante a hora determinada. 

### <a name="how-can-i-optimize-the-usage-on-my-throughput-units"></a>Como posso otimizar o uso em minhas unidades de produtividade?
Você pode começar com uma TU (unidade de produtividade) e ativar a [inflação automática](event-hubs-auto-inflate.md). O recurso de inflação automática permite que você aumente suas TUs à medida que seu conteúdo/tráfego aumenta. Você também pode definir um limite superior de TUs.

### <a name="how-does-auto-inflate-feature-of-event-hubs-work"></a>Como funciona o recurso Inflação Automática dos Hubs de Eventos?
O recurso inflação automática permite que você escale verticalmente suas TUs (unidades de produtividade). Isso significa que você pode começar comprando poucas TUs e a inflação automática escala verticalmente suas TUs à medida que aumenta a entrada. Isso proporciona uma opção econômica e controle total do número de TUs para gerenciamento. Esse recurso **serve apenas para escalar verticalmente**, e você pode controlar completamente a redução do número de TUs atualizando-o. 

Convém começar com poucas TUs (unidades de produtividade), por exemplo 2 TUs. Se você prever que o tráfego pode aumentar para 15 TUs, ative o recurso inflação automática em seu namespace e defina o limite máximo como 15 TUs. Agora, você pode aumentar suas TUs automaticamente conforme seu tráfego aumenta.

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>Há um custo associado à ativação do recurso inflação automática?
**Não há custo** associado a esse recurso. 

### <a name="how-are-throughput-limits-enforced"></a>Como os limites de taxa de transferência são impostos?
Se a produtividade de **entrada** total ou a taxa de eventos de entrada total em todos os Hubs de Eventos em um namespace exceder os limites de unidades de produtividade totais agregados, os remetentes serão limitados e receberão erros indicando que a cota de entrada foi excedida.

Se a produtividade de **saída** total ou a taxa de eventos de saída total em todos os Hubs de Eventos em um namespace exceder os limites de unidades de produtividade agregados, os receptores serão limitados, mas nenhum erro de limitação será gerado. 

As cotas de entrada e saída são aplicadas separadamente, para que nenhum remetente possa fazer com que o consumo de eventos se torne lento e para que um receptor não possa evitar que eventos sejam enviados para um Hub de Eventos.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-that-can-be-reservedselected"></a>Há um limite no número de unidades de produtividade que podem ser reservadas/selecionadas?

Ao criar um namespace de camada básica ou Standard no portal do Azure, você pode selecionar até 20 TUs para o namespace. Para prometê-lo a **exatamente** 40 Tus, envie uma  [solicitação de suporte](../azure-portal/supportability/how-to-create-azure-support-request.md).  

1. Na página **namespace do barramento de evento** , selecione **nova solicitação de suporte** no menu à esquerda. 
1. Na página **nova solicitação de suporte** , siga estas etapas:
    1. Para **Resumo**, descreva o problema em algumas palavras. 
    1. Para **Tipo de problema**, selecione **Cota**. 
    1. Para **subtipo de problema**, selecione **solicitação de aumento ou diminuição da unidade de produtividade**. 
    
        :::image type="content" source="./media/event-hubs-faq/support-request-throughput-units.png" alt-text="Página de Solicitação de suporte":::

Além dos 40 TUs, os Hubs de Eventos oferecem o modelo baseado em recursos/capacidade chamado de clusters de Hubs de Eventos Dedicados. Clusters dedicados são vendidos em Unidades de Capacidade (CUs). Para obter mais informações, consulte [hubs de eventos dedicados-visão geral](event-hubs-dedicated-overview.md).

## <a name="dedicated-clusters"></a>Clusters dedicados

### <a name="what-are-event-hubs-dedicated-clusters"></a>O que são clusters de Hubs de Eventos Dedicados?
Os clusters de Hubs de Eventos Dedicados oferecem implantações de locatário único para clientes com exigências mais rígidas. Essa oferta cria um cluster baseado em capacidade que não é limitado por unidades de produtividade. Isso significa que você pode usar o cluster para ingerir e transmitir seus dados, conforme determinado pelo uso de CPU e memória do cluster. Para saber mais, confira [Clusters dos Hubs de Eventos Dedicados](event-hubs-dedicated-overview.md).

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>Como criar um cluster dedicado de Hubs de Eventos?
Para obter instruções passo a passo e mais informações sobre como configurar um cluster de hubs de eventos dedicado, consulte o guia de [início rápido: criar um cluster de hubs de eventos dedicado usando o portal do Azure](event-hubs-dedicated-cluster-create-portal.md). 


[!INCLUDE [event-hubs-dedicated-clusters-faq](../../includes/event-hubs-dedicated-clusters-faq.md)]


## <a name="partitions"></a>Partições

### <a name="how-many-partitions-do-i-need"></a>De quantas partições preciso?
O número de partições é especificado na criação do Hub de Eventos e precisa estar entre 1 e 32. A contagem de partições não pode ser alterada em todas as camadas, exceto na [camada dedicada](event-hubs-dedicated-overview.md), portanto, você deve considerar a escala de longo prazo ao definir a contagem de partições. As partições são um mecanismo de organização de dados relacionados ao paralelismo de downstream necessário no consumo de aplicativos. O número de partições em um hub de eventos está diretamente relacionado ao número de leitores simultâneos que você espera ter. Para obter mais informações sobre partições, confira [Partições](event-hubs-features.md#partitions).

Talvez você queira defini-la como o maior valor possível, que é 32, no momento da criação. Lembre-se de que ter mais de uma partição resultará em eventos enviados a várias partições sem reter a ordem, a menos que você configure que os remetentes enviem somente para uma única partição fora das 32, deixando as 31 restantes de partições redundantes. No primeiro caso, você terá que ler eventos em todas as partições 32. No último caso, não há nenhum custo adicional óbvio gerado pela configuração extra que você precisa fazer no host do processador de eventos.

Hubs de Eventos são projetados para permitir um único leitor de partição por grupo de consumidores. Na maioria dos casos de uso, a configuração padrão de quatro partições é suficiente. Se você estiver buscando dimensionar o processamento de eventos, convém considerar a inclusão de partições adicionais. Não há nenhum limite específico de taxa de transferência em uma partição, no entanto, a taxa de transferência agregada em seu namespace é limitada pelo número de unidades de produtividade. Conforme você aumenta o número de unidades de taxa de transferência no seu namespace, talvez você deseje partições adicionais para permitir que cada um dos leitores simultâneos alcance sua taxa de transferência máxima.

No entanto, se você tiver um modelo no qual seu aplicativo tem uma afinidade com uma determinada partição, aumentar o número de partições pode não ser útil pra você. Para saber mais, confira [disponibilidade e consistência](event-hubs-availability-and-consistency.md).

### <a name="increase-partitions"></a>Aumentar partições
Você pode solicitar que a contagem de partições seja aumentada para 40 (exato) enviando uma solicitação de suporte. 

1. Na página **namespace do barramento de evento** , selecione **nova solicitação de suporte** no menu à esquerda. 
1. Na página **nova solicitação de suporte** , siga estas etapas:
    1. Para **Resumo**, descreva o problema em algumas palavras. 
    1. Para **Tipo de problema**, selecione **Cota**. 
    1. Para **subtipo de problema**, selecione **solicitação de alteração de partição**. 
    
        :::image type="content" source="./media/event-hubs-faq/support-request-increase-partitions.png" alt-text="Aumentar contagem de partições":::

A contagem de partições pode ser aumentada para exatamente 40. Nesse caso, o número de TUs também precisa ser aumentado para 40. Se você decidir mais tarde para reduzir o limite da TU para <= 20, o limite máximo de partição também será reduzido para 32. 

A redução nas partições não afeta os hubs de eventos existentes porque as partições são aplicadas no nível do hub de eventos e são imutáveis após a criação do Hub. 

## <a name="pricing"></a>Preços

### <a name="where-can-i-find-more-pricing-information"></a>Onde posso encontrar mais informações sobre preços?

Para obter todas as informações sobre os preços dos Hubs de Eventos, consulte os [detalhes de preços dos Hubs de Eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Há um custo para a retenção de eventos de Hubs de Eventos por mais de 24 horas?

A camada Standard dos Hubs de Eventos permite a retenção de mensagens por períodos superiores a 24 horas, por um máximo de sete dias. Se o número total de eventos armazenados exceder o limite de armazenamento para o número de unidades de produtividade selecionadas (84 GB por unidade de produtividade), o tamanho que exceder o limite será cobrado com base na taxa de armazenamento de Blobs do Azure publicada. O limite de armazenamento em cada unidade de produtividade cobre todos os custos de armazenamento para períodos de retenção de 24 horas (o padrão) mesmo que a unidade de produtividade seja usada até o limite máximo de entrada.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Como o tamanho do armazenamento de Hubs de Eventos é calculado e cobrado?

O tamanho total de todos os eventos armazenados, incluindo eventuais sobrecargas internas para cabeçalhos de eventos ou estruturas de armazenamento em disco em todos os Hubs de Eventos, é medido durante todo o dia. No final do dia, o tamanho do armazenamento de pico é calculado. O limite de armazenamento diário é calculado com base no número mínimo de unidades de produtividade que foram selecionadas durante o dia (cada unidade de produtividade fornece um limite de 84 GB). Se o tamanho total exceder o limite de armazenamento diário calculado, o armazenamento em excesso será cobrado usando as taxas de armazenamento de Blob do Azure (com base na taxa de **armazenamento com redundância local** ).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Como os eventos de entrada de Hubs de Eventos são calculados?

Cada evento enviado a um Hub de Eventos conta como uma mensagem faturável. Um *evento de entrada* é definido como uma unidade de dados menor ou igual a 64 KB. Qualquer evento menor ou igual a 64 KB de tamanho é considerado um evento faturável. Se o evento for maior que 64 KB, o número de eventos passíveis de cobrança será calculado de acordo com o tamanho do evento, em múltiplos de 64 KB. Por exemplo, um evento de 8 KB enviado ao Hub de Eventos é cobrado como um evento, mas uma mensagem de 96 KB enviada ao Hub de Eventos é cobrada como dois eventos.

Os eventos consumidos de um hub de eventos, operações de gerenciamento e chamadas de controle, como pontos de verificação, não são contados como eventos de entrada faturáveis, mas acumulam até a concessão de unidade de produtividade.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Cobranças por conexões agenciadas são aplicadas aos Hubs de Eventos?

Cobranças de conexão são aplicadas somente quando o protocolo AMQP é usado. Não há cobranças de conexão para enviar eventos usando HTTP, independentemente do número de sistemas ou dispositivos remetentes. Se você pretende usar o AMQP (por exemplo, para obter transmissões de eventos mais eficientes ou para habilitar a comunicação bidirecional em cenários de comando e controle da IoT), consulte a página [Informações sobre preços dos Hubs de Eventos](https://azure.microsoft.com/pricing/details/event-hubs/) para obter detalhes sobre quantas conexões estão incluídas em cada camada de serviço.

### <a name="how-is-event-hubs-capture-billed"></a>Como a Captura de Hubs de Eventos é cobrada?

A Captura de Hubs de Eventos será habilitada quando qualquer Hub de Eventos no namespace tiver o recurso Captura habilitado. A Captura de Hubs de Eventos é cobrada por hora de acordo com a unidade de taxa de transferência comprada. À medida que a contagem das unidades de taxa de transferência aumentar ou diminuir, a cobrança da Captura de Hubs de Eventos refletirá essas mudanças em incrementos de hora cheia. Para obter mais informações sobre a cobrança da Captura de Hubs de Eventos, confira [informações de preços de Hubs de Eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="do-i-get-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Serei cobrado pela conta de armazenamento selecionada para a Captura de Hubs de Eventos?

A Captura usa uma conta de armazenamento fornecida quando habilitado em um Hub de Eventos. Como essa é sua conta de armazenamento, todas as alterações nessa configuração serão cobradas em sua assinatura do Azure.

## <a name="quotas"></a>Cotas

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Existe alguma cota associada aos Hubs de Eventos?

Para obter uma lista de todas as cotas de Hubs de Eventos, consulte [cotas](event-hubs-quotas.md).

## <a name="troubleshooting"></a>Solução de problemas

### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Por que não eu consigo criar um namespace após excluí-lo de outra assinatura? 
Quando você exclui um namespace de uma assinatura, aguarde até 4 horas antes de recriá-lo com o mesmo nome em outra assinatura. Caso contrário, a seguinte mensagem de erro será exibida: `Namespace already exists`. 

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Quais são algumas das exceções geradas pelos Hubs de Eventos e suas ações sugeridas?

Para obter uma lista de exceções possíveis dos Hubs de Eventos, confira [Visão geral das exceções](event-hubs-messaging-exceptions.md).

### <a name="diagnostic-logs"></a>Logs de diagnóstico

Os Hubs de Eventos dão suporte a dois tipos de [logs de diagnóstico](event-hubs-diagnostic-logs.md) – logs de erro de Captura e logs operacionais – ambos os quais são representados em json e podem ser ativados por meio do Portal do Azure.

### <a name="support-and-sla"></a>Suporte e SLA

O suporte técnico para Hubs de Eventos está disponível por meio da [Página de perguntas e respostas da Microsoft para o Barramento de Serviço do Azure.](/answers/topics/azure-service-bus.html) O suporte para gerenciamento de assinaturas e cobranças é fornecido sem custo adicional.

Para saber mais sobre nosso SLA, veja a página [Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/) .

## <a name="azure-stack-hub"></a>Azure Stack Hub

### <a name="how-can-i-target-a-specific-version-of-azure-storage-sdk-when-using-azure-blob-storage-as-a-checkpoint-store"></a>Como é possível direcionar uma versão específica do SDK do armazenamento do Azure ao usar o armazenamento de BLOBs do Azure como um armazenamento de ponto de verificação?
Se você executar esse código no Hub Azure Stack, ocorrerá erros de tempo de execução, a menos que você direcione uma versão de API de armazenamento específica. Isso ocorre porque o SDK dos Hubs de Eventos usa a API do Armazenamento do Microsoft Azure mais recente disponível no Azure, que pode não estar disponível em sua plataforma do Azure Stack Hub. O Hub de Azure Stack pode dar suporte a uma versão diferente do SDK do blob de armazenamento que normalmente está disponível no Azure. Se você estiver usando o armazenamento de blog do Azure como um armazenamento de ponto de verificação, verifique a [versão da API de armazenamento do Azure com suporte para a compilação do hub de Azure Stack](/azure-stack/user/azure-stack-acs-differences?#api-version) e direcione essa versão em seu código. 

Por exemplo, se você estiver executando o Azure Stack Hub versão 2005, a versão mais alta disponível para o serviço de armazenamento é a versão 2019-02-02. Por padrão, a biblioteca de clientes do SDK dos Hubs de Eventos usa a versão mais alta disponível no Azure (2019-07-07 no momento do lançamento do SDK). Nesse caso, além das etapas a seguir nesta seção, você também precisará adicionar o código para direcionar a versão 2019-02-02 da API do serviço de armazenamento. Para obter um exemplo de como direcionar uma versão de API de armazenamento específica, consulte os exemplos a seguir para C#, Java, Python e JavaScript/TypeScript.  

Para obter um exemplo de como direcionar uma versão de API de armazenamento específica do seu código, consulte os seguintes exemplos no GitHub: 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/)
- [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java)
- Python – [síncrono](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py), [assíncrono](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py)
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) e [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts)

## <a name="next-steps"></a>Próximas etapas

Você pode saber mais sobre Hubs de Eventos visitando os links abaixo:

* [Visão geral de Hubs de Eventos](./event-hubs-about.md)
* [Criar um Hub de Eventos](event-hubs-create.md)
* [Inflar automaticamente de Hubs de Eventos](event-hubs-auto-inflate.md)
