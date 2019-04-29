---
title: Guia de desempenho do serviço do Azure SignalR
description: Uma visão geral do desempenho do serviço do Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: zhshang
ms.openlocfilehash: f7cc05c8c2a299d809c4386d119fef58fa2548d5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61269370"
---
# <a name="performance-guide-for-azure-signalr-service"></a>Guia de desempenho do serviço do Azure SignalR

Um dos principais benefícios de usar o serviço do Azure SignalR é a facilidade de dimensionar os aplicativos do SignalR. Em um cenário em larga escala, o desempenho é um fator importante. 

Neste guia, apresentaremos os fatores que afetam o desempenho de aplicativos do SignalR. Descreveremos desempenho típico em cenários de caso de uso diferentes. No fim, apresentaremos o ambiente e as ferramentas que você pode usar para gerar um relatório de desempenho.

## <a name="term-definitions"></a>Definições de termos

*Entrada*: A mensagem de entrada para o serviço do Azure SignalR.

*Saída*: A mensagem de saída do serviço do Azure SignalR.

*Largura de banda*: O tamanho total de todas as mensagens em 1 segundo.

*Modo padrão*: O modo de trabalho padrão quando uma instância de serviço do Azure SignalR foi criada. Azure SignalR Service espera que o servidor de aplicativo para estabelecer uma conexão com ele antes que ele aceite todas as conexões de cliente.

*Modo sem servidor*: Um modo no qual o serviço do Azure SignalR aceita apenas conexões de clientes. Nenhuma conexão de servidor é permitido.

## <a name="overview"></a>Visão geral

Azure SignalR Service define sete camadas Standard para capacidades de desempenho diferentes. Este guia responde às seguintes perguntas:

-   O que é o desempenho do serviço do Azure SignalR típico para cada camada?

-   Serviço do Azure SignalR atende às minhas necessidades de taxa de transferência de mensagem (por exemplo, envio 100.000 mensagens por segundo)?

-   Para o meu cenário específico, qual camada é adequada para mim? Ou, como selecionar a camada correta?

-   Que tipo de servidor de aplicativos (tamanho da VM) é adequado para mim? Quantas delas deverá implantar?

Para responder a essas perguntas, neste guia primeiro fornece uma explicação de alto nível dos fatores que afetam o desempenho. Ele ilustra, em seguida, as mensagens de entrada e saídas máximas para cada camada para casos de uso típico: **echo**, **difusão**, **enviar para o grupo**, e **enviar para conexão** (peer-to-peer bate-papo).

Este guia não é possível cobrir todos os cenários (e casos de uso diferentes, tamanhos de mensagem, padrões de envio da mensagem e assim por diante). Mas ele fornece alguns métodos para ajudá-lo:

- Avalie seu requisito aproximado para as mensagens de entrada ou saídas.
- Encontre os níveis apropriados, verificando a tabela de desempenho.

## <a name="performance-insight"></a>Análise de desempenho

Esta seção descreve as metodologias de avaliação de desempenho e, em seguida, lista todos os fatores que afetam o desempenho. No final, ele fornece métodos para ajudá-lo a avaliar os requisitos de desempenho.

### <a name="methodology"></a>Metodologia

*Taxa de transferência* e *latência* são dois aspectos típicos de verificação de desempenho. Serviço do Azure SignalR, cada camada SKU tem sua própria política de limitação de taxa de transferência. A diretiva define *o máximo permitido de taxa de transferência (largura de banda de entrada e saída)* conforme a máxima taxa de transferência obtida quando tiver de 99% das mensagens de latência que é menor que 1 segundo.

A latência é o período de tempo do que a conexão, enviando a mensagem para receber a mensagem de resposta do serviço do Azure SignalR. Vamos **echo** como exemplo. Cada conexão de cliente adiciona um carimbo de data / hora na mensagem. O hub do servidor de aplicativo envia a mensagem original para o cliente. Portanto, o atraso de propagação é calculado facilmente cada conexão de cliente. O carimbo de data / hora está anexado para cada mensagem no **difusão**, **envio ao grupo**, e **enviar para conexão**.

Para simular milhares de conexões clientes simultâneas, criação de várias VMs em uma rede virtual privada no Azure. Todas essas VMs se conectar à mesma instância de serviço do Azure SignalR.

No modo padrão do serviço do Azure SignalR, VMs do servidor de aplicativo são implantados na mesma rede privada virtual como VMs do cliente. Todas as VMs de cliente e as VMs do servidor de aplicativo são implantados na mesma rede da mesma região para evitar a latência entre regiões.

### <a name="performance-factors"></a>Fatores de desempenho

Teoricamente, a capacidade de serviço do Azure SignalR é limitada pelos recursos de computação: CPU, memória e rede. Por exemplo, mais conexões ao serviço do Azure SignalR fazer com que o serviço usar mais memória. Para maior tráfego de mensagens (por exemplo, cada mensagem é maior que 2.048 bytes), o serviço Azure SignalR precisa gastar mais ciclos de CPU para processar o tráfego. Enquanto isso, a largura de banda de rede do Azure também impõe um limite para o tráfego máximo.

O tipo de transporte é outro fator que afeta o desempenho. Os três tipos são [WebSocket](https://en.wikipedia.org/wiki/WebSocket), [evento enviado servidor](https://en.wikipedia.org/wiki/Server-sent_events), e [sondagem longa](https://en.wikipedia.org/wiki/Push_technology). 

WebSocket é um protocolo de comunicação full duplex e bidirecionais ao longo de uma única conexão de TCP. Evento enviado Server é um protocolo unidirecional para mensagens por push do servidor ao cliente. Sondagem longa requer que os clientes realizem chamada seletiva periodicamente informações do servidor por meio de uma solicitação HTTP. Para a mesma API nas mesmas condições, WebSocket tem o melhor desempenho, eventos enviados Server é mais lento e sondagem longa é o mais lento. Azure SignalR Service recomenda WebSocket por padrão.

O custo de roteamento de mensagem também limita o desempenho. Azure SignalR Service desempenha uma função como um roteador de mensagem, que roteia a mensagem de um conjunto de clientes ou servidores para outros clientes ou servidores. Um cenário diferente ou uma API exige uma política de roteamento diferente. 

Para **echo**, o cliente envia uma mensagem para si mesmo e o destino de roteamento também é em si. Esse padrão é o custo mais baixo de roteamento. Mas para **difusão**, **envio ao grupo**, e **enviar para conexão**, precisa de serviço do Azure SignalR pesquisar as conexões de destino por meio dos dados distribuídos internos estrutura. Esse processamento extra usa mais CPU, memória e largura de banda de rede. Como resultado, o desempenho é mais lento.

No modo padrão, o servidor de aplicativo também pode se tornar um gargalo para determinados cenários. O SDK do Azure SignalR deve invocar o hub, enquanto ele mantém uma conexão ao vivo com cada cliente por meio de sinais de pulsação.

No modo sem servidor, o cliente envia uma mensagem por HTTP post, que não é tão eficiente quanto o WebSocket.

Outro fator é o protocolo: JSON e [MessagePack](https://msgpack.org/index.html). MessagePack é menor em tamanho e entregue mais rapidamente do que o JSON. MessagePack pode não melhorar o desempenho, no entanto. O desempenho do serviço do Azure SignalR não é sensível a protocolos porque ele não decodificar o conteúdo da mensagem durante o encaminhamento de mensagens de clientes para servidores ou vice-versa.

Em resumo, os seguintes fatores afetam a capacidade de entrada e saída:

-   Camada de SKU (CPU/memória)

-   Número de conexões

-   Tamanho da mensagem

-   taxa de envio de mensagem

-   Tipo de transporte (WebSocket, evento enviado servidor ou sondagem longa)

-   Cenário de caso de uso (roteamento custo)

-   conexões de servidor e o serviço de aplicativo (no modo de servidor)


### <a name="finding-a-proper-sku"></a>Localizando um SKU adequado

Como você pode avaliar a capacidade de entrada/saída ou localizar qual camada é adequado para um caso de uso específico?

Suponha que o servidor de aplicativo é poderoso o suficiente e não é o gargalo de desempenho. Em seguida, verifique a largura de banda máxima de entrada e saída para cada camada.

#### <a name="quick-evaluation"></a>Avaliação rápida

Vamos simplificar a avaliação pela primeira vez, supondo que algumas configurações padrão: 

- O tipo de transporte é WebSocket.
- O tamanho da mensagem é de 2.048 bytes.
- Uma mensagem é enviada a cada segundo.
- Azure SignalR Service está no modo padrão.

Cada camada tem seu próprio máxima largura de banda de entrada e saída. Uma experiência do usuário uniforme não é garantida após a conexão de entrada ou saída excede o limite.

**Echo** proporciona a máxima largura de banda de entrada porque ela tem o custo mais baixo de roteamento. **Difusão** define a largura de banda máxima de mensagem de saída.

Fazer *não* exceder os valores realçados nas duas tabelas a seguir.

|       Echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões                       | 1.000 | 2.000 | 5.000 | 10.000 | 20.000 | 50.000 | 100.000 |
| **Largura de banda de entrada** | **2 MBps**    | **4 MBps**    | **10 MBps**   | **20 MBps**    | **40 MBps**    | **100 MBps**   | **200 MBps**    |
| Largura de banda de saída | 2 Mbps   | 4 Mbps   | 10 Mbps  | 20 MBps   | 40 MBps   | 100 MBps  | 200 MBps   |


|     Difusão             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| conexões               | 1.000 | 2.000 | 5.000  | 10.000 | 20.000 | 50.000  | 100.000 |
| Largura de banda de entrada  | 4 kBps   | 4 kBps   | 4 kBps    | 4 kBps    | 4 kBps    | 4 kBps     | 4 kBps    |
| **Largura de banda de saída** | **4 MBps**    | **8 MBps**    | **20 MBps**    | **40 MBps**    | **80 MBps**    | **200 MBps**    | **400 MBps**   |

*Largura de banda de entrada* e *largura de banda de saída* têm o tamanho total de mensagens por segundo.  Aqui estão as fórmulas para eles:
```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

- *inboundConnections*: O número de conexões de envio da mensagem.

- *outboundConnections*: O número de conexões de recebimento da mensagem.

- *messageSize*: O tamanho de uma única mensagem (valor médio). Uma mensagem pequena que menos de 1.024 bytes tem um impacto de desempenho que é semelhante a uma mensagem de 1.024 bytes.

- *sendInterval*: A hora de envio de uma mensagem. Geralmente, é 1 segundo por mensagem, o que significa que enviar uma mensagem por segundo. Um intervalo menor significa que o envio de mensagem mais em um período de tempo. Por exemplo, 0,5 segundos por mensagem significa enviar duas mensagens a cada segundo.

- *Conexões*: O confirmada limite máximo para o serviço do Azure SignalR para todas as camadas. Se o número de conexão for aumentado ainda mais, ele sofrerá da limitação de conexão.

#### <a name="evaluation-for-complex-use-cases"></a>Avaliação para casos de uso complexos

##### <a name="bigger-message-size-or-different-sending-rate"></a>Maior tamanho de mensagem ou a taxa de envio diferente

O caso de uso real é mais complicado. Ele pode enviar uma mensagem maior que 2.048 bytes ou a taxa de envio de mensagem é não uma mensagem por segundo. Vamos difusão do Unit100 como um exemplo para saber como avaliar seu desempenho.

A tabela a seguir mostra um caso de uso real de **difusão**. Mas o tamanho da mensagem, a contagem de conexão e a taxa de envio da mensagem são diferentes das, supomos na seção anterior. A pergunta é como podemos deduzir qualquer um desses itens (tamanho da mensagem, contagem de conexão ou taxa de envio de mensagem) se sabemos que somente dois deles.

| Difusão  | Tamanho da mensagem | Mensagens de entrada por segundo | conexões | Enviar intervalos |
|---|---------------------|--------------------------|-------------|-------------------------|
| 1 | 20 KB                | 1                        | 100.000     | 5 segundos                      |
| 2 | 256 KB               | 1                        | 8.000       | 5 segundos                      |

A fórmula a seguir é fácil inferir com base na fórmula anterior:

```
outboundConnections = outboundBandwidth * sendInterval / messageSize
```

Para Unit100, a largura de banda de saída máxima é de 400 MB da tabela anterior. Para um tamanho de mensagem de 20 KB, as conexões de saída máximas devem ser 400 MB \* 5 / 20 KB = 100.000, que corresponde ao valor real.

##### <a name="mixed-use-cases"></a>Casos de uso misto

O caso de uso real normalmente combina quatro casos de uso básico juntos: **echo**, **difusão**, **enviar para o grupo**, e **enviar para conexão**. A metodologia que você usa para avaliar a capacidade é:

1. Divida os casos de uso misto em quatro casos de uso básico.
1. Calcule a largura de banda máxima de mensagem de entrada e saída, usando as fórmulas anteriores separadamente.
1. Some os cálculos de largura de banda para obter a largura de banda de entrada/saída máxima total. 

Em seguida, pegue a camada correta das tabelas de entrada/saída de largura de banda máxima.

> [!NOTE]
> Para enviar uma mensagem para centenas ou milhares de pequenos grupos, ou para milhares de clientes que enviam uma mensagem uns aos outros, o custo de roteamento se tornará dominante. Levar esse impacto em consideração.

Para o caso de uso de envio de uma mensagem para os clientes, certifique-se de que o servidor de aplicativo está *não* o gargalo. A seção "Case study" a seguir fornece diretrizes sobre quantos servidores de aplicativo que você precisa e quantas conexões de servidor que você deve configurar.

## <a name="case-study"></a>Análise

As seções a seguir percorrem quatro casos de uso típico para o transporte de WebSocket: **echo**, **difusão**, **enviar para o grupo**, e **enviar para conexão**. Para cada cenário, a seção lista a capacidade atual de entrada e saída para o serviço do Azure SignalR. Ele também explica os principais fatores que afetam o desempenho.

No modo padrão, o servidor de aplicativo cria cinco conexões de servidor com o serviço do Azure SignalR. O servidor de aplicativo usa o SDK do serviço do Azure SignalR por padrão. Os seguintes resultados de teste de desempenho, conexões de servidor são aumentadas 15 (ou mais para difusão e enviar uma mensagem para um grande grupo).

Casos de uso diferentes têm requisitos diferentes para servidores de aplicativo. **Difusão** precisa pequeno número de servidores de aplicativo. **Echo** ou **enviar para conexão** precisa de muitos servidores de aplicativo.

Em todos os casos de uso, o tamanho da mensagem padrão é de 2.048 bytes e o intervalo de envio de mensagem é de 1 segundo.

### <a name="default-mode"></a>Modo padrão

Os clientes, servidores de aplicativo web e serviço do Azure SignalR estão envolvidos no modo padrão. Todos os clientes significa uma única conexão.

#### <a name="echo"></a>Echo

Primeiro, um aplicativo web se conecta ao serviço do Azure SignalR. Em segundo lugar, muitos clientes se conectar ao aplicativo web, que redireciona os clientes para o serviço do Azure SignalR com o token de acesso e o ponto de extremidade. Em seguida, os clientes estabelecem conexões WebSocket com o serviço do Azure SignalR.

Depois que todos os clientes estabelecem conexões, elas comecem a enviar uma mensagem que contém um carimbo de hora para o hub específico a cada segundo. O hub retorna a mensagem de volta ao cliente original. Todos os clientes calcula a latência quando ele recebe a mensagem de eco de volta.

O diagrama a seguir, 5 a 8 (tráfego realçado vermelho) estão em um loop. O loop é executado por uma duração padrão (5 minutos) e obtém a estatística de latência de todas as mensagens.

![Tráfego para o caso de uso de eco](./media/signalr-concept-performance/echo.png)

O comportamento de **echo** determina que a máxima largura de banda de entrada é igual a máxima largura de banda de saída. Para obter detalhes, consulte a tabela a seguir.

|       Echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões                       | 1.000 | 2.000 | 5.000 | 10.000 | 20.000 | 50.000 | 100.000 |
| Mensagens de entrada/saída por segundo | 1.000 | 2.000 | 5.000 | 10.000 | 20.000 | 50.000 | 100.000 |
| Largura de banda de entrada/saída | 2 Mbps   | 4 Mbps   | 10 Mbps  | 20 MBps   | 40 MBps   | 100 MBps  | 200 MBps   |

Nesse caso, cada cliente invoca o hub definido no servidor de aplicativo. O hub apenas chama o método definido no lado do cliente original. Esse hub é o hub mais leve para **echo**.

```
        public void Echo(IDictionary<string, object> data)
        {
            Clients.Client(Context.ConnectionId).SendAsync("RecordLatency", data);
        }
```

Mesmo para esse hub simple, a pressão de tráfego no servidor do aplicativo está em destaque como o **echo** aumentos de carga de mensagem de entrada. Essa pressão de tráfego exige vários servidores de aplicativo para as camadas SKU grandes. A tabela a seguir lista a contagem do servidor de aplicativo para cada camada.


|    Echo          | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões      | 1.000 | 2.000 | 5.000 | 10.000 | 20.000 | 50.000 | 100.000 |
| Contagem de servidor de aplicativo | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> O cliente conexão número, tamanho da mensagem, a taxa, camada de SKU e CPU/memória do servidor de aplicativo está enviando a mensagem afeta o desempenho geral do **echo**.

#### <a name="broadcast"></a>Difusão

Para **difusão**, quando o aplicativo web recebe a mensagem, ele transmite a todos os clientes. Mais de clientes são transmitir o tráfego de mensagens mais é necessário para todos os clientes. Consulte o diagrama a seguir.

![Tráfego para o caso de uso de difusão](./media/signalr-concept-performance/broadcast.png)

Um número pequeno de clientes estiver transmitindo. A largura de banda de mensagem de entrada for pequena, mas a largura de banda de saída é enorme. A largura de banda de saída de mensagem aumenta à medida que a conexão de cliente ou aumenta a taxa de difusão.

A tabela a seguir resume as conexões de cliente máximo, contagem de mensagens de entrada/saída e largura de banda.

|     Difusão             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| conexões               | 1.000 | 2.000 | 5.000  | 10.000 | 20.000 | 50.000  | 100.000 |
| Mensagens de entrada por segundo  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Mensagens de saída por segundo | 2.000 | 4.000 | 10.000 | 20.000 | 40.000 | 100.000 | 200.000 |
| Largura de banda de entrada  | 4 kBps   | 4 kBps   | 4 kBps    | 4 kBps    | 4 kBps    | 4 kBps     | 4 kBps     |
| Largura de banda de saída | 4 Mbps   | 8 Mbps   | 20 MBps   | 40 MBps   | 80 MBps   | 200 MBps   | 400 MBps   |

Os clientes de difusão que postagem mensagens são não mais do que quatro. Eles precisam de menos servidores de aplicativo em comparação com **echo** porque a quantidade de mensagem de entrada for pequena. Dois servidores de aplicativo são suficientes para considerações de desempenho e SLA. Mas você deve aumentar as conexões de servidor padrão para evitar o desequilíbrio, especialmente para Unit50 e Unit100.

|   Difusão      | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões      | 1.000 | 2.000 | 5.000 | 10.000 | 20.000 | 50.000 | 100.000 |
| Contagem de servidor de aplicativo | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Aumente as conexões de servidor padrão de 5 a 40 em cada servidor de aplicativo para evitar conexões de servidor de desbalanceada possíveis para o serviço do Azure SignalR.
>
> O número de conexão do cliente, o tamanho de mensagem, taxa de envio de mensagem e camada de SKU afetam o desempenho geral para **difusão**.

#### <a name="send-to-group"></a>Enviar para o grupo

O **envio ao grupo** caso de uso tem um padrão de tráfego semelhante aos **difusão**. A diferença é que depois que os clientes estabelecem conexões WebSocket com o serviço do Azure SignalR, eles devem adicionar grupos antes de enviar uma mensagem a um grupo específico. O diagrama a seguir ilustra o fluxo de tráfego.

![Tráfego para o caso de uso do grupo de envio](./media/signalr-concept-performance/sendtogroup.png)

Membro do grupo e a contagem de grupo são dois fatores que afetam o desempenho. Para simplificar a análise, podemos definir dois tipos de grupos:

- **Grupo pequeno**: Cada grupo tem 10 conexões. O número de grupo é igual a (contagem de conexão máximo) / 10. Por exemplo, para Unit1, se houver 1.000 contagens de conexão, em seguida, temos 1000 / 10 = 100 grupos.

- **Grupo grande**: O número de grupo é sempre 10. A contagem de membros do grupo é igual a (contagem de conexão máximo) / 10. Por exemplo, para Unit1, se houver 1.000 contagens de conexão, em seguida, cada grupo tem 1000 / 10 = 100 membros.

**Enviar para o grupo** traz um roteamento de custo para o serviço do Azure SignalR porque ele encontrou as conexões de destino por meio de uma estrutura de dados distribuídos. Como as conexões de envio aumentam, aumenta o custo.

##### <a name="small-group"></a>Grupo pequeno

O custo de roteamento é significativo para enviar mensagens para muitos pequenos grupos. Atualmente, a implementação de serviço do Azure SignalR atinge o limite de custo de roteamento em Unit50. Adição de mais CPU e memória não ajudar, assim, não é possível melhorar o Unit100 ainda mais por design. Se você precisar de entrada mais largura de banda, entre em contato com o atendimento ao cliente.

|   Enviar para um pequeno grupo     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50 | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| conexões               | 1.000 | 2.000 | 5.000  | 10.000 | 20.000 | 50.000 | 100.000
| Contagem de membros do grupo        | 10    | 10    | 10     | 10     | 10     | 10     | 10 
| Contagem de grupo               | 100   | 200   | 500    | 1.000  | 2.000  | 5.000  | 10.000 
| Mensagens de entrada por segundo  | 200   | 400   | 1.000  | 2.500  | 4.000  | 7.000  | 7.000   |
| Largura de banda de entrada  | 400 kBps  | 800 kBps  | 2 Mbps     | 5 Mbps     | 8 Mbps     | 14 MBps    | 14 MBps     |
| Mensagens de saída por segundo | 2.000 | 4.000 | 10.000 | 25.000 | 40.000 | 70,000 | 70,000  |
| Largura de banda de saída | 4 Mbps    | 8 Mbps    | 20 MBps    | 50 MBps     | 80 MBps    | 140 MBps   | 140 MBps    |

Muitas conexões de cliente estão chamando o hub, para que o número de servidor de aplicativo também é fundamental para o desempenho. A tabela a seguir lista as contagens de servidor de aplicativo sugeridas.

|  Enviar para um pequeno grupo   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões      | 1.000 | 2.000 | 5.000 | 10.000 | 20.000 | 50.000 | 100.000 |
| Contagem de servidor de aplicativo | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> O cliente conexão número, tamanho da mensagem, a taxa, custo de roteamento, camada de SKU e CPU/memória do servidor de aplicativo está enviando a mensagem afeta o desempenho geral do **enviar para um pequeno grupo**.

##### <a name="big-group"></a>Grupo grande

Para **enviar para um grande grupo**, a largura de banda de saída se torna o gargalo antes de atingir o roteamento de limite de custo. A tabela a seguir lista a saída largura de banda máxima, que é quase o mesmo que para **difusão**.

|    Enviar para um grupo grande      | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| conexões               | 1.000 | 2.000 | 5.000  | 10.000 | 20.000 | 50.000  | 100.000
| Contagem de membros do grupo        | 100   | 200   | 500    | 1.000  | 2.000  | 5.000   | 10.000 
| Contagem de grupo               | 10    | 10    | 10     | 10     | 10     | 10      | 10
| Mensagens de entrada por segundo  | 20    | 20    | 20     | 20     | 20     | 20      | 20      |
| Largura de banda de entrada  | 80 kBps   | 40 kBps   | 40 kBps    | 20 kBps    | 40 kBps    | 40 kBps     | 40 kBps     |
| Mensagens de saída por segundo | 2.000 | 4.000 | 10.000 | 20.000 | 40.000 | 100.000 | 200.000 |
| Largura de banda de saída | 8 Mbps    | 8 Mbps    | 20 MBps    | 40 MBps    | 80 MBps    | 200 MBps    | 400 MBps    |

A contagem de conexão de envio é não mais do que 40. A carga do servidor de aplicativo é pequena, portanto, o número sugerido de aplicativos web é pequeno.

|  Enviar para um grupo grande  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões      | 1.000 | 2.000 | 5.000 | 10.000 | 20.000 | 50.000 | 100.000 |
| Contagem de servidor de aplicativo | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Aumente as conexões de servidor padrão de 5 a 40 em cada servidor de aplicativo para evitar conexões de servidor de desbalanceada possíveis para o serviço do Azure SignalR.
> 
> O número de conexão do cliente, tamanho da mensagem, taxa de envio de mensagem, custo de roteamento e camada de SKU afetam o desempenho geral do **enviar para um grande grupo**.

#### <a name="send-to-connection"></a>Enviar para a conexão

No **enviar para conexão** caso de uso, quando os clientes estabelecem as conexões com o serviço do Azure SignalR, cada cliente chama um hub especial para obter sua própria ID de conexão. O parâmetro de comparação de desempenho coleta todas as IDs de conexão, mistura as-los e reatribui-las a todos os clientes como um destino de envio. Os clientes continuar enviando a mensagem para a conexão de destino até que o teste de desempenho seja concluído.

![Tráfego para o caso de uso do envio ao cliente](./media/signalr-concept-performance/sendtoclient.png)

O roteamento de custo para **enviar para a conexão** é semelhante ao custo para **enviar para um pequeno grupo**.

À medida que aumenta a contagem de conexão, o custo de roteamento limita o desempenho geral. Unit50 atingiu o limite. Como resultado, Unit100 não é possível aprimorar ainda mais.

A tabela a seguir está um resumo estatístico após a muitas rodadas de executar o **enviar para conexão** benchmark.

|   Enviar para a conexão   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50          | Unit100         |
|------------------------------------|-------|-------|-------|--------|--------|-----------------|-----------------|
| conexões                        | 1.000 | 2.000 | 5.000 | 10.000 | 20.000 | 50.000          | 100.000         |
| Mensagens de entrada/saída por segundo | 1.000 | 2.000 | 5.000 | 8.000  | 9.000  | 20.000 | 20.000 |
| Largura de banda de entrada/saída | 2 Mbps    | 4 Mbps    | 10 Mbps   | 16 MBps    | 18 MBps    | 40 MBps       | 40 MBps       |

Esse caso de uso requer alta carga no lado do servidor de aplicativo. Ver o servidor de aplicativo sugerido contagem na tabela a seguir.

|  Enviar para a conexão  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões      | 1.000 | 2.000 | 5.000 | 10.000 | 20.000 | 50.000 | 100.000 |
| Contagem de servidor de aplicativo | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> O cliente conexão número, tamanho da mensagem, a mensagem de envio de taxa, custo de roteamento, camada de SKU e CPU/memória para o servidor de aplicativo afeta o desempenho geral do **enviar para conexão**.

#### <a name="aspnet-signalr-echo-broadcast-and-send-to-small-group"></a>ASP.NET SignalR eco, difusão e enviar para um pequeno grupo

Azure SignalR Service fornece a mesma capacidade de desempenho para ASP.NET SignalR. 

O teste de desempenho usa Azure Web Apps do [Standard S3 de plano de serviço](https://azure.microsoft.com/pricing/details/app-service/windows/) para ASP.NET SignalR.

A tabela a seguir fornece a contagem de aplicativo web sugeridas para ASP.NET SignalR **echo**.

|   Echo           | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões      | 1.000 | 2.000 | 5.000 | 10.000 | 20.000 | 50.000 | 100.000 |
| Contagem de servidor de aplicativo | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

A tabela a seguir fornece a contagem de aplicativo web sugeridas para ASP.NET SignalR **difusão**.

|  Difusão       | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões      | 1.000 | 2.000 | 5.000 | 10.000 | 20.000 | 50.000 | 100.000 |
| Contagem de servidor de aplicativo | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

A tabela a seguir fornece a contagem de aplicativo web sugeridas para ASP.NET SignalR **enviar para um pequeno grupo**.

|  Enviar para um pequeno grupo     | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões      | 1.000 | 2.000 | 5.000 | 10.000 | 20.000 | 50.000 | 100.000 |
| Contagem de servidor de aplicativo | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

### <a name="serverless-mode"></a>Modo sem servidor

Os clientes e o serviço do Azure SignalR estão envolvidos no modo sem servidor. Todos os clientes significa uma única conexão. O cliente envia mensagens por meio da API REST para outro cliente ou difusão mensagens para todos.

Enviar mensagens de alta densidade por meio da API REST não é tão eficiente quanto usando WebSocket. Exige que você crie uma nova conexão de HTTP toda vez e que é um adicional de custo no modo sem servidor.

#### <a name="broadcast-through-rest-api"></a>Transmissão por meio da API REST
Todos os clientes estabelecem conexões WebSocket com o serviço do Azure SignalR. Em seguida, alguns clientes de inicio por meio da API REST. A mensagem de envio (entrada) é tudo através de HTTP Post, que não é eficiente em comparação com WebSocket.

|   Transmissão por meio da API REST     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| conexões               | 1.000 | 2.000 | 5.000  | 10.000 | 20.000 | 50.000  | 100.000 |
| Mensagens de entrada por segundo  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Mensagens de saída por segundo | 2.000 | 4.000 | 10.000 | 20.000 | 40.000 | 100.000 | 200.000 |
| Largura de banda de entrada  | 4 kBps    | 4 kBps    | 4 kBps     | 4 kBps     | 4 kBps     | 4 kBps      | 4 kBps      |
| Largura de banda de saída | 4 Mbps    | 8 Mbps    | 20 MBps    | 40 MBps    | 80 MBps    | 200 MBps    | 400 MBps    |

#### <a name="send-to-user-through-rest-api"></a>Enviar para o usuário por meio da API REST
O parâmetro de comparação atribui nomes de usuário para todos os clientes antes de começar a se conectar ao serviço do Azure SignalR. Depois que os clientes estabelecem conexões WebSocket com o serviço do Azure SignalR, comecem a enviar mensagens para outras pessoas por meio de HTTP Post.

|   Enviar para o usuário por meio da API REST | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| conexões               | 1.000 | 2.000 | 5.000  | 10.000 | 20.000 | 50.000  | 100.000 |
| Mensagens de entrada por segundo  | 300   | 600   | 900    | 1,300  | 2.000  | 10.000  | 18,000  |
| Mensagens de saída por segundo | 300   | 600   | 900    | 1,300  | 2.000  | 10.000  | 18,000 |
| Largura de banda de entrada  | 600 kBps  | 1.2 MBps  | 1.8 MBps   | 2.6 MBps   | 4 Mbps     | 10 Mbps     | 36 MBps    |
| Largura de banda de saída | 600 kBps  | 1.2 MBps  | 1.8 MBps   | 2.6 MBps   | 4 Mbps     | 10 Mbps     | 36 MBps    |

## <a name="performance-test-environments"></a>Ambientes de teste de desempenho

Para todos os casos listados anteriormente de uso, realizamos testes de desempenho em um ambiente do Azure. No máximo, nós usamos 50 VMs de cliente e o servidor de aplicativo 20 VMs. Aqui estão alguns detalhes:

- Tamanho VM do cliente: StandardDS2V2 (2 vCPU, memória de 7G)

- Tamanho da VM do servidor de aplicativos: StandardF4sV2 (4 vCPU, memória 8g)

- Conexões de servidor SignalR SDK do Azure: 15

## <a name="performance-tools"></a>Ferramentas de desempenho

Você pode encontrar as ferramentas de desempenho para o serviço Azure SignalR em [GitHub](https://github.com/Azure/azure-signalr-bench/tree/master/SignalRServiceBenchmarkPlugin).

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você obteve uma visão geral do desempenho do serviço do Azure SignalR em cenários de caso de uso típicos.

Para obter detalhes sobre as operações internas do serviço e colocação em escala para ele, leia os guias a seguir:

* [Componentes internos do SignalR Service do Azure](signalr-concept-internals.md)
* [Azure SignalR Service dimensionamento](signalr-howto-scale-multi-instances.md)
