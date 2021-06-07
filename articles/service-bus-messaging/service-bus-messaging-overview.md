---
title: Visão geral de sistema de mensagens do Barramento de Serviço do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral de alto nível do Barramento de Serviço do Azure, um agente de mensagens de integração empresarial totalmente gerenciado.
ms.topic: overview
ms.date: 02/16/2021
ms.openlocfilehash: 897729b9748d69ad3c6de507e800dbb3a1a3619c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100570468"
---
# <a name="what-is-azure-service-bus"></a>O que é o Barramento de Serviço do Azure?
O Barramento de Serviço do Microsoft Azure é um agente de mensagens empresarial totalmente gerenciado com filas de mensagens e tópicos de publicação/assinatura. O Barramento de Serviço é usado para separar aplicativos e serviços uns dos outros, proporcionando estes benefícios:

- Balanceamento da carga de trabalho entre funções de trabalho concorrentes
- Roteamento e transferência de dados e do controle, com segurança, entre limites de serviços e aplicativos
- Coordenação do trabalho transacional que requer um alto grau de confiabilidade 

## <a name="overview"></a>Visão geral
Os dados são transferidos entre diferentes aplicativos e serviços usando *mensagens*. Uma mensagem é um contêiner decorado com metadados e que contém dados. Os dados podem ser qualquer tipo de informação, incluindo dados estruturados codificados com os formatos comuns, como os seguintes: JSON, XML, Apache Avro, texto sem formatação.

Alguns cenários de sistema de mensagens comuns são:

* *Mensagens*. Transfira dados comerciais, como ordens de venda ou compra, diários ou movimentos de estoque.
* *Separar aplicativos*. Melhore a confiabilidade e a escalabilidade de aplicativos e serviços. O produtor e o consumidor não precisam estar online nem prontamente disponíveis ao mesmo tempo. A [carga é nivelada](/azure/architecture/patterns/queue-based-load-leveling) de maneira que picos de tráfego não sobrecarregam um serviço. 
* *Balanceamento de carga*. Permita que vários [consumidores concorrentes](/azure/architecture/patterns/competing-consumers) leiam de uma fila ao mesmo tempo, cada um deles assumindo com segurança a propriedade exclusiva de mensagens específicas. 
* *Tópicos e assinaturas*. Habilite relações de 1:*n* entre [publicadores e assinantes](/azure/architecture/patterns/publisher-subscriber), permitindo que os assinantes selecionem mensagens específicas de um fluxo de mensagens publicado.
* *Transações*. Permite que você execute diversas operações, todas elas no escopo de uma transação atômica. Por exemplo, as operações a seguir podem ser realizadas no escopo de uma transação.  

    1. Obter uma mensagem de uma fila.
    2. Postar os resultados do processamento em uma ou mais filas diferentes.
    3. Mover a mensagem de entrada da fila original. 
    
    Os resultados ficam visíveis para consumidores downstream somente após o êxito, incluindo a liquidação bem-sucedida da mensagem de entrada, permitindo a semântica de processamento único. Esse modelo de transação é uma base robusta para o padrão de [transações de compensação](/azure/architecture/patterns/compensating-transaction) no contexto maior da solução. 
* *Sessões de mensagem*. Implemente a coordenação em alta escala de fluxos de trabalho e transferências multiplexadas que exigem uma ordenação de mensagens estrita ou o adiamento de mensagens.

Se você estiver familiarizado com outros agentes de mensagens como o Apache ActiveMQ, os conceitos do Barramento de Serviço serão semelhantes aos que você conhece. Como o Barramento de Serviço é uma oferta de PaaS (plataforma como serviço), uma diferença importante é que você não precisa se preocupar com as ações a seguir. O Azure cuida dessas tarefas para você. 

- Criar logs e gerenciar o espaço em disco
- Cuidar de backups
- Manter os patches dos sistemas operacionais ou produtos
- Preocupar-se com falhas de hardware 
- Fazer failover para um computador de reserva

## <a name="compliance-with-standards-and-protocols"></a>Conformidade com padrões e protocolos

O protocolo de conexão primária do Barramento de Serviço é o [AMQP (Advanced Message Queuing Protocol) 1.0](service-bus-amqp-overview.md), um padrão ISO/IEC aberto. Ele permite que os clientes escrevam aplicativos que funcionam no Barramento de Serviço e em agentes locais, como o ActiveMQ ou o RabbitMQ. O [guia do protocolo AMQP](service-bus-amqp-protocol-guide.md) fornece informações detalhadas caso você queira criar uma abstração desse tipo.

O [Barramento de Serviço Premium](service-bus-premium-messaging.md) tem total conformidade com a API [JMS (Java Message Service) 2.0](how-to-use-java-message-service-20.md) do Java/Jakarta EE. E o Barramento de Serviço Standard dá suporte ao subconjunto JMS 1.1 focado em filas. O JMS é uma abstração comum para agentes de mensagens e integra-se a muitos aplicativos e estruturas, incluindo a popular estrutura Spring. Para alternar de outros agentes para o Barramento de Serviço do Azure, você só precisa recriar a topologia de filas e tópicos e alterar as dependências e a configuração do provedor do cliente. Para ver um exemplo, confira o [Guia de migração do ActiveMQ](migrate-jms-activemq-to-servicebus.md).

## <a name="concepts-and-terminology"></a>Conceitos e terminologia 
Esta seção aborda os conceitos e a terminologia do Barramento de Serviço.

### <a name="namespaces"></a>Namespaces
Um namespace é um contêiner para todos os componentes de mensagem. Vários tópicos e filas podem estar em um único namespace, e os namespaces geralmente servem como contêineres de aplicativos. 

Um namespace pode ser comparado a um "servidor" na terminologia de outros agentes, mas os conceitos não são exatamente equivalentes. Um namespace do Barramento de Serviço é a própria fatia de capacidade de um cluster grande composto por dezenas de máquinas virtuais totalmente ativas. Ele também pode incluir três [Zonas de Disponibilidade do Azure](../availability-zones/az-overview.md). Assim, você obtém todos os benefícios decorrentes da disponibilidade e da robustez de executar o agente de mensagens em uma escala enorme. E você não precisa se preocupar com as complexidades subjacentes. O Barramento de Serviço é um serviço de mensagens "sem servidor".

### <a name="queues"></a>Filas
As mensagens são enviadas e recebidas a partir de *filas*. As filas armazenam mensagens até que o aplicativo de recebimento esteja disponível para recebê-las e processá-las.

![Fila](./media/service-bus-messaging-overview/about-service-bus-queue.png)

As mensagens em filas são ordenadas e recebem carimbo de data/hora na chegada. Depois de aceita pelo agente, a mensagem sempre será mantida permanentemente em um armazenamento com redundância tripla, distribuído entre zonas de disponibilidade se o namespace estiver habilitado para zonas. O Barramento de Serviço nunca deixa mensagens na memória ou em um armazenamento volátil após elas serem relatadas ao cliente como aceitas.

As mensagens são entregues em modo *pull*, e somente quando solicitado. Diferentemente do modelo de sondagem de ocupação de algumas outras filas de nuvem, a operação de pull pode ter longa duração e ser concluída somente quando uma mensagem estiver disponível. 

### <a name="topics"></a>Tópicos

Também é possível usar *tópicos* para enviar e receber mensagens. Enquanto uma fila é frequentemente usada para comunicação ponto a ponto, os tópicos são úteis em cenários de publicação/assinatura.

![Tópico](./media/service-bus-messaging-overview/about-service-bus-topic.png)

Os tópicos podem ter várias assinaturas independentes, que se anexam ao tópico e, de modo geral, funcionam exatamente como filas do lado do destinatário. Um assinante de um tópico pode receber uma cópia de cada mensagem enviada para esse tópico. As assinaturas são entidades nomeadas. As assinaturas são duráveis por padrão, mas podem ser configuradas para expirarem e serem excluídas automaticamente. Por meio da API JMS, o Barramento de Serviço Premium também permite que você crie assinaturas voláteis que existem durante a conexão.

Você pode definir regras em uma assinatura. Uma regra de assinatura tem um *filtro* para definir uma condição para que a mensagem seja copiada na assinatura e uma *ação* opcional que pode modificar os metadados da mensagem. Para saber mais, confira [Filtros e ações de tópico](topic-filters.md). Esse recurso é útil nos seguintes cenários:

- Você não quer que uma assinatura receba todas as mensagens enviadas a um tópico.
- Você quer marcar as mensagens com metadados extras quando elas passarem por uma assinatura.

## <a name="advanced-features"></a>Recursos avançados

O Barramento de Serviço inclui recursos avançados que permitem resolver problemas de mensagens mais complexos. As seções a seguir descrevem vários desses recursos.

### <a name="message-sessions"></a>Sessões de mensagem

Para criar uma garantia PEPS (primeiro a entrar, primeiro a sair) no Barramento de Serviço, use as sessões. As sessões de mensagens permitem a manipulação ordenada e exclusiva de sequências não associadas de mensagens relacionadas. Para permitir a manipulação das sessões em sistemas de grande escala e alta disponibilidade, o recurso de sessão também permite o armazenamento de estado de sessão, que permite que as sessões se movimentem com segurança entre os manipuladores. Para saber mais, confira [Sessões de mensagem: PEPS (primeiro a entrar, primeiro a sair)](message-sessions.md).

### <a name="autoforwarding"></a>Encaminhamento automático

O recurso de encaminhamento automático encadeia uma fila ou uma assinatura em outra fila ou outro tópico no mesmo namespace. Quando você usa esse recurso, o Barramento de Serviço move automaticamente as mensagens de uma fila ou assinatura para uma fila ou tópico de destino. Todas essas movimentações são feitas de maneira transacional. Para saber mais, confira [Encadeamento das entidades do Barramento de Serviço com encaminhamento automático](service-bus-auto-forwarding.md).

### <a name="dead-letter-queue"></a>Fila de mensagens mortas

Todas as assinaturas de tópicos e filas do Barramento de Serviço têm uma DLQ (fila de mensagens mortas) associada. Uma DLQ contém mensagens que atendem a estes critérios: 

- Elas não podem ser entregues com êxito a nenhum receptor.
- Elas atingiram o tempo limite.
- Elas são inseridas explicitamente pelo aplicativo receptor. 

As mensagens na fila de mensagens mortas são anotadas com o motivo pelo qual estão lá. A fila de mensagens mortas tem um ponto de extremidade especial, mas de modo geral funciona como qualquer fila regular. Um aplicativo ou uma ferramenta pode procurar uma DLQ ou retirar-se dela. Você também pode configurar o encaminhamento automático para fora de uma fila de mensagens mortas. Para saber mais, confira [Visão geral das filas de mensagens mortas do Barramento de Serviço](service-bus-dead-letter-queues.md).

### <a name="scheduled-delivery"></a>Entrega agendada

Você pode enviar mensagens a uma fila ou um tópico para processamento atrasado, definindo a hora em que a mensagem ficará disponível para consumo. Mensagens agendadas também podem ser canceladas. Para saber mais, confira [Mensagens agendadas](message-sequencing.md#scheduled-messages).

### <a name="message-deferral"></a>Adiamento de mensagens

Um cliente de fila ou assinatura pode adiar a recuperação de uma mensagem recebida para mais tarde. A mensagem pode ter sido postada fora de uma ordem esperada e o cliente quer aguardar até receber outra mensagem. As mensagens adiadas permanecem na fila ou assinatura e precisam ser reativadas explicitamente usando o número de sequência atribuído pelo serviço. Para saber mais, confira [Adiamento de mensagem](message-deferral.md).

### <a name="batching"></a>Envio em lote

O envio em lote do lado do cliente permite que o cliente de uma fila ou tópico acumule um conjunto de mensagens e as transfira juntas. Muitas vezes, isso é feito para economizar largura de banda ou para aumentar a taxa de transferência. Para saber mais, confira [Envio em lote do lado do cliente](service-bus-performance-improvements.md#client-side-batching).

### <a name="transactions"></a>Transactions

Uma transação agrupa duas ou mais operações em um *escopo de execução*. O Barramento de Serviço permite que você agrupe operações em várias entidades de mensagens no escopo de uma só transação. Uma entidade de mensagem pode ser uma fila, um tópico ou uma assinatura. Para saber mais, confira [Visão geral do processamento de transações do Barramento de Serviço](service-bus-transactions.md).

### <a name="autodelete-on-idle"></a>Exclusão automática em tempo ocioso
A exclusão automática em tempo ocioso permite que você especifique um intervalo de tempo ocioso após o qual a assinatura de um tópico ou fila será excluída automaticamente. A duração mínima é de 5 minutos. 

### <a name="duplicate-detection"></a>Detecção de duplicidade
O recurso de detecção de duplicidade permite que o remetente reenvie a mesma mensagem e que o agente remova uma possível duplicidade. Para saber mais, confira [Detecção de duplicatas](duplicate-detection.md).

### <a name="geo-disaster-recovery"></a>Recuperação de desastre geográfico

Quando uma região do Azure passa por um tempo de inatividade, o recurso de recuperação de desastre permite que o processamento de dados continue operando em um datacenter ou região diferente. O recurso mantém um espelho estrutural de um namespace disponível na região secundária e permite que a identidade do namespace mude para o namespace secundário. As mensagens já postadas permanecem no namespace primário anterior para recuperação após o episódio de disponibilidade retroceder. Para mais informações consulte [Recuperação de desastre em área geográfica do Barramento de Serviço do Azure](service-bus-geo-dr.md).

### <a name="security"></a>Segurança

O Barramento de Serviço dá suporte aos protocolos padrão [AMQP 1.0](service-bus-amqp-overview.md) e [HTTP ou REST](/rest/api/servicebus/) e a seus respectivos recursos de segurança, incluindo o protocolo TLS. Os clientes podem ser autorizados a acessar usando a segurança baseada em funções de [Assinatura de Acesso Compartilhado](service-bus-sas.md) ou do [Azure Active Directory](service-bus-authentication-and-authorization.md). 

Para oferecer proteção contra tráfego indesejado, o Barramento de Serviço fornece [recursos de segurança](network-security.md) como o firewall IP e a integração a redes virtuais. 

## <a name="client-libraries"></a>Bibliotecas de cliente

As bibliotecas de cliente do Barramento de Serviço com suporte total estão disponíveis por meio do SDK do Azure.

- [Barramento de Serviço do Azure para .NET](/dotnet/api/overview/azure/service-bus?preserve-view=true)
- [Bibliotecas do Barramento de Serviço do Azure para Java](/java/api/overview/azure/servicebus?preserve-view=true)
- [Provedor do Barramento de Serviço do Azure para Java JMS 2.0](how-to-use-java-message-service-20.md)
- [Módulos do Barramento de Serviço do Azure para JavaScript e TypeScript](/javascript/api/overview/azure/service-bus?preserve-view=true)
- [Bibliotecas do Barramento de Serviço do Azure para Python](/python/api/overview/azure/servicebus?preserve-view=true)

[O protocolo primário do Barramento de Serviço do Azure é o AMQP 1.0](service-bus-amqp-overview.md), que pode ser usado de qualquer cliente de protocolo em conformidade com AMQP 1.0. Vários clientes AMQP de software livre têm amostras que demonstram explicitamente a interoperabilidade do Barramento de Serviço. Examine o [Guia do protocolo AMQP 1.0](service-bus-amqp-protocol-guide.md) para saber como usar os recursos do Barramento de Serviço diretamente com os clientes do AMQP 1.0.

[!INCLUDE [messaging-oss-amqp-stacks.md](../../includes/messaging-oss-amqp-stacks.md)]

## <a name="integration"></a>Integração

O Barramento de Serviço integra-se totalmente a muitos serviços da Microsoft e do Azure, por exemplo:

* [Grade de Eventos](service-bus-to-event-grid-integration-example.md)
* [Aplicativos Lógicos](../connectors/connectors-create-api-servicebus.md)
* [Azure Functions](../azure-functions/functions-bindings-service-bus.md)
* [Power Platform](../connectors/connectors-create-api-servicebus.md)
* [Dynamics 365](/dynamics365/fin-ops-core/dev-itpro/business-events/how-to/how-to-servicebus)
* [Azure Stream Analytics](../stream-analytics/stream-analytics-define-outputs.md)

## <a name="next-steps"></a>Próximas etapas

Para começar a usar o sistema de mensagens do Barramento de Serviço, consulte os artigos a seguir:

* Para comparar os serviços de mensagens do Azure, confira [Comparação de serviços](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json).
* Teste os inícios rápidos para [.NET](service-bus-dotnet-get-started-with-queues.md), [Java](service-bus-java-how-to-use-queues.md) ou [JMS](service-bus-java-how-to-use-jms-api-amqp.md).
* Para gerenciar recursos do Barramento de Serviço, confira [Explorador do Barramento de Serviço](https://github.com/paolosalvatori/ServiceBusExplorer/releases).
* Para saber mais sobre as camadas Standard e Premium e respectivos preços, confira [Preço do Barramento de Serviço](https://azure.microsoft.com/pricing/details/service-bus/).
* Para saber mais sobre o desempenho e a latência da camada Premium, confira [Mensagens Premium](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722).