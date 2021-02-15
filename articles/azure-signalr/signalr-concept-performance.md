---
title: Guia de desempenho para Serviço do Azure SignalR
description: Uma visão geral do desempenho e do parâmetro de comparação do serviço de Signaler do Azure. Métricas-chave a considerar ao planejar a capacidade.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 68cad32be177fa20794399157fca89e87c2f8f59
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "74157675"
---
# <a name="performance-guide-for-azure-signalr-service"></a>Guia de desempenho para Serviço do Azure SignalR

Um dos principais benefícios do uso do serviço de Signaler do Azure é a facilidade de dimensionar os aplicativos do Signalr. Em um cenário de grande escala, o desempenho é um fator importante. 

Neste guia, apresentaremos os fatores que afetam o desempenho do aplicativo Signalr. Descreveremos o desempenho típico em diferentes cenários de caso de uso. No final, apresentaremos o ambiente e as ferramentas que você pode usar para gerar um relatório de desempenho.

## <a name="term-definitions"></a>Definições de termos

*Entrada*: a mensagem de entrada para o serviço de Signaler do Azure.

*Saída*: a mensagem de saída do serviço de signaler do Azure.

*Largura de banda*: o tamanho total de todas as mensagens em 1 segundo.

*Modo padrão*: o modo de trabalho padrão quando uma instância do serviço de Signalr do Azure foi criada. O serviço de Signaler do Azure espera que o servidor de aplicativos estabeleça uma conexão com ele antes de aceitar qualquer conexão de cliente.

*Modo sem servidor*: um modo no qual o serviço de Signaler do Azure aceita somente conexões de cliente. Nenhuma conexão com o servidor é permitida.

## <a name="overview"></a>Visão geral

O serviço de Signaler do Azure define sete camadas padrão para diferentes capacidades de desempenho. Este guia responde às seguintes perguntas:

-   Qual é o desempenho típico do serviço de Signaler do Azure para cada camada?

-   O serviço de sinalizador do Azure atende aos meus requisitos de taxa de transferência de mensagens (por exemplo, envio de 100.000 mensagens por segundo)?

-   Para meu cenário específico, qual camada é adequada para mim? Ou como posso selecionar a camada apropriada?

-   Que tipo de servidor de aplicativos (tamanho da VM) é adequado para mim? Quantos deles devo implantar?

Para responder a essas perguntas, este guia primeiro fornece uma explicação de alto nível dos fatores que afetam o desempenho. Em seguida, ele ilustra o máximo de mensagens de entrada e saída para cada camada para casos de uso típicos: **Echo**, **Broadcast**, **Send to Group**e **Send to Connection** (chat ponto a ponto).

Este guia não pode abranger todos os cenários (e casos de uso diferentes, tamanhos de mensagem, padrões de envio de mensagens e assim por diante). Mas ele fornece alguns métodos para ajudá-lo a:

- Avalie seu requisito aproximado para as mensagens de entrada ou saída.
- Localize as camadas apropriadas verificando a tabela de desempenho.

## <a name="performance-insight"></a>Insight de desempenho

Esta seção descreve as metodologias de avaliação de desempenho e lista todos os fatores que afetam o desempenho. No final, ele fornece métodos para ajudá-lo a avaliar os requisitos de desempenho.

### <a name="methodology"></a>Metodologia

A *taxa de transferência* e a *latência* são dois aspectos típicos da verificação de desempenho. Para o serviço de Signaler do Azure, cada camada de SKU tem sua própria política de limitação de taxa de transferência. A política define *a taxa de transferência máxima permitida (largura de banda de entrada e saída)* como a taxa de transferência máxima obtida quando 99 por cento das mensagens têm latência inferior a 1 segundo.

Latência é o período de tempo da conexão que envia a mensagem para receber a mensagem de resposta do serviço de Signaler do Azure. Vamos fazer um **eco** como exemplo. Cada conexão de cliente adiciona um carimbo de data/hora na mensagem. O Hub do servidor de aplicativos envia a mensagem original de volta para o cliente. Portanto, o atraso de propagação é facilmente calculado por cada conexão de cliente. O carimbo de data/hora é anexado a cada mensagem em **difusão**, **Enviar para o grupo**e **Enviar para a conexão**.

Para simular milhares de conexões de cliente simultâneas, várias VMs são criadas em uma rede virtual privada no Azure. Todas essas VMs se conectam à mesma instância do serviço Signaler do Azure.

No modo padrão do serviço de Signaler do Azure, as VMs do servidor de aplicativo são implantadas na mesma rede virtual privada que as VMs cliente. Todas as VMs de cliente e VMs de servidor de aplicativo são implantadas na mesma rede da mesma região para evitar a latência entre regiões.

### <a name="performance-factors"></a>Fatores de desempenho

Teoricamente, a capacidade do serviço do Signalr do Azure é limitada por recursos de computação: CPU, memória e rede. Por exemplo, mais conexões com o serviço de Signaler do Azure fazem com que o serviço use mais memória. Para tráfego de mensagens maior (por exemplo, cada mensagem tem mais de 2.048 bytes), o serviço do Azure Signalr precisa gastar mais ciclos de CPU para processar o tráfego. Enquanto isso, a largura de banda da rede do Azure também impõe um limite para o tráfego máximo.

O tipo de transporte é outro fator que afeta o desempenho. Os três tipos são [WebSocket](https://en.wikipedia.org/wiki/WebSocket), [servidor-enviado-evento](https://en.wikipedia.org/wiki/Server-sent_events)e [sondagem longa](https://en.wikipedia.org/wiki/Push_technology). 

WebSocket é um protocolo de comunicação bidirecional e Full-duplex em uma única conexão TCP. O servidor enviado-evento é um protocolo unidirecional para enviar mensagens do servidor para o cliente. Long-Polling exige que os clientes monitorem periodicamente as informações do servidor por meio de uma solicitação HTTP. Para a mesma API sob as mesmas condições, o WebSocket tem o melhor desempenho, o servidor-enviado-evento é mais lento e Long-Polling é o mais lento. O serviço de sinalizador do Azure recomenda WebSocket por padrão.

O custo de roteamento de mensagens também limita o desempenho. O serviço de sinalizador do Azure desempenha uma função como um roteador de mensagem, que roteia a mensagem de um conjunto de clientes ou servidores para outros clientes ou servidores. Um cenário ou API diferente requer uma política de roteamento diferente. 

Para **Echo**, o cliente envia uma mensagem para si mesmo e o destino de roteamento também é o próprio. Esse padrão tem o menor custo de roteamento. Mas, para **difusão**, **envio para grupo**e **envio para conexão**, o serviço de signaler do Azure precisa procurar as conexões de destino por meio da estrutura de dados distribuída interna. Esse processamento extra usa mais CPU, memória e largura de banda de rede. Como resultado, o desempenho é mais lento.

No modo padrão, o servidor de aplicativos também pode se tornar um afunilamento para determinados cenários. O SDK do Signaler do Azure precisa invocar o Hub, enquanto mantém uma conexão dinâmica com cada cliente por meio de sinais de pulsação.

No modo sem servidor, o cliente envia uma mensagem por HTTP post, que não é tão eficiente quanto WebSocket.

Outro fator é o protocolo: JSON e [MessagePack](https://msgpack.org/index.html). MessagePack é menor em tamanho e é entregue mais rápido que o JSON. No entanto, o MessagePack pode não melhorar o desempenho. O desempenho do serviço de Signaler do Azure não é sensível aos protocolos porque ele não decodifica a carga da mensagem durante o encaminhamento de mensagens de clientes para servidores ou vice-versa.

Em resumo, os seguintes fatores afetam a capacidade de entrada e de saída:

-   Camada de SKU (CPU/memória)

-   Número de conexões

-   Tamanho da mensagem

-   Taxa de envio da mensagem

-   Tipo de transporte (WebSocket, servidor-enviado-evento ou sondagem longa)

-   Cenário de caso de uso (custo de roteamento)

-   Servidor de aplicativo e conexões de serviço (no modo de servidor)


### <a name="finding-a-proper-sku"></a>Encontrando um SKU adequado

Como você pode avaliar a capacidade de entrada/saída ou descobrir qual camada é adequada para um caso de uso específico?

Suponha que o servidor de aplicativos seja eficiente o suficiente e não seja o afunilamento de desempenho. Em seguida, verifique a largura de banda máxima de entrada e saída para cada camada.

#### <a name="quick-evaluation"></a>Avaliação rápida

Vamos simplificar a avaliação primeiro supondo algumas configurações padrão: 

- O tipo de transporte é WebSocket.
- O tamanho da mensagem é 2.048 bytes.
- Uma mensagem é enviada A cada 1 segundo.
- O serviço de Signaler do Azure está no modo padrão.

Cada camada tem sua própria largura de banda de entrada máxima e largura de banda de saída. Uma experiência de usuário tranqüila não é garantida após a conexão de entrada ou saída exceder o limite.

O **eco** fornece a largura de banda de entrada máxima porque tem o menor custo de roteamento. A **difusão** define a largura de banda de mensagem de saída máxima.

*Não* exceda os valores realçados nas duas tabelas a seguir.

|       Echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões                       | 1,000 | 2\.000 | 5\.000 | 10.000 | 20,000 | 50.000 | 100.000 |
| **Largura de banda de entrada** | **2 Mbps**    | **4 Mbps**    | **10 Mbps**   | **20 MBps**    | **40 MBps**    | **100 MBps**   | **200 MBps**    |
| Largura de banda de saída | 2 Mbps   | 4 Mbps   | 10 Mbps  | 20 MBps   | 40 MBps   | 100 MBps  | 200 MBps   |


|     Transmissão             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| conexões               | 1,000 | 2\.000 | 5\.000  | 10.000 | 20,000 | 50.000  | 100.000 |
| Largura de banda de entrada  | 4 KBps   | 4 KBps   | 4 KBps    | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps    |
| **Largura de banda de saída** | **4 Mbps**    | **8 Mbps**    | **20 MBps**    | **40 MBps**    | **80 MBps**    | **200 MBps**    | **400 MBps**   |

A largura de *banda de entrada* e a largura de *banda de saída* são o tamanho total da mensagem por segundo.  Aqui estão as fórmulas para eles:
```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

- *inboundConnections*: o número de conexões que enviam a mensagem.

- *outboundConnections*: o número de conexões que recebem a mensagem.

- *messages*: o tamanho de uma única mensagem (valor médio). Uma mensagem pequena com menos de 1.024 bytes tem um impacto de desempenho semelhante a uma mensagem de 1.024 bytes.

- *sendInterval*: a hora de enviar uma mensagem. Normalmente, é de 1 segundo por mensagem, o que significa enviar uma mensagem a cada segundo. Um intervalo menor significa enviar mais mensagens em um período de tempo. Por exemplo, 0,5 segundos por mensagem significa enviar duas mensagens a cada segundo.

- *Conexões*: o limite máximo confirmado do serviço de Signaler do Azure para cada camada. Se o número de conexão for mais elevado, ele será afetado da limitação de conexão.

#### <a name="evaluation-for-complex-use-cases"></a>Avaliação para casos de uso complexos

##### <a name="bigger-message-size-or-different-sending-rate"></a>Tamanho de mensagem maior ou taxa de envio diferente

O caso de uso real é mais complicado. Ele pode enviar uma mensagem com mais de 2.048 bytes ou a taxa de mensagens de envio não é uma mensagem por segundo. Vamos pegar Unit100's broadcast como um exemplo para descobrir como avaliar seu desempenho.

A tabela a seguir mostra um caso de uso real da **difusão**. Mas o tamanho da mensagem, a contagem de conexões e a taxa de envio de mensagens são diferentes do que presumimos na seção anterior. A pergunta é como podemos deduzir qualquer um desses itens (tamanho da mensagem, contagem de conexões ou taxa de envio de mensagens) se soubermos apenas dois deles.

| Transmissão  | Tamanho da mensagem | Mensagens de entrada por segundo | conexões | Intervalos de envio |
|---|---------------------|--------------------------|-------------|-------------------------|
| 1 | 20 KB                | 1                        | 100.000     | 5 segundos                      |
| 2 | 256 KB               | 1                        | 8,000       | 5 segundos                      |

A fórmula a seguir é fácil de inferir com base na fórmula anterior:

```
outboundConnections = outboundBandwidth * sendInterval / messageSize
```

Para Unit100, a largura de banda de saída máxima é de 400 MB da tabela anterior. Para um tamanho de mensagem de 20 KB, o máximo de conexões de saída deve ser 400 MB \* 5/20 KB = 100.000, que corresponde ao valor real.

##### <a name="mixed-use-cases"></a>Casos de uso misto

O caso de uso real normalmente combina os quatro casos de uso básicos juntos: **eco**, **difusão**, **envio para grupo**e **envio para conexão**. A metodologia que você usa para avaliar a capacidade é:

1. Divida os casos de uso misto em quatro casos de uso básicos.
1. Calcule a largura de banda máxima da mensagem de entrada e de saída usando as fórmulas anteriores separadamente.
1. Some os cálculos de largura de banda para obter a largura de banda máxima de entrada/saída. 

Em seguida, pegue a camada apropriada das tabelas máximas de largura de banda de entrada/saída.

> [!NOTE]
> Para enviar uma mensagem para centenas ou milhares de pequenos grupos, ou para milhares de clientes que enviam uma mensagem entre si, o custo de roteamento se tornará dominante. Considere esse impacto em conta.

Para o caso de uso do envio de uma mensagem aos clientes, verifique se o servidor de aplicativos *não* é o afunilamento. A seção "estudo de caso" a seguir fornece diretrizes sobre quantos servidores de aplicativos você precisa e quantas conexões de servidor devem ser configuradas.

## <a name="case-study"></a>Estudo de caso

As seções a seguir passam por quatro casos de uso típicos para o transporte WebSocket: **Echo**, **Transmit**, **Send to Group**e **Send to Connection**. Para cada cenário, a seção lista a capacidade atual de entrada e de saída para o serviço de Signaler do Azure. Ele também explica os principais fatores que afetam o desempenho.

No modo padrão, o servidor de aplicativos cria cinco conexões de servidor com o serviço de Signaler do Azure. O servidor de aplicativos usa o SDK do serviço de Signaler do Azure por padrão. Nos resultados do teste de desempenho a seguir, as conexões do servidor são aumentadas para 15 (ou mais para difusão e envio de uma mensagem para um grande grupo).

Diferentes casos de uso têm requisitos diferentes para servidores de aplicativos. A **transmissão** precisa de um número pequeno de servidores de aplicativos. **Echo** ou **Enviar para a conexão precisa de** muitos servidores de aplicativos.

Em todos os casos de uso, o tamanho da mensagem padrão é 2.048 bytes e o intervalo de envio da mensagem é de 1 segundo.

### <a name="default-mode"></a>Modo padrão

Clientes, servidores de aplicativos Web e o serviço de Signaler do Azure estão envolvidos no modo padrão. Cada cliente significa uma única conexão.

#### <a name="echo"></a>Echo

Primeiro, um aplicativo Web se conecta ao serviço de Signaler do Azure. Em segundo lugar, muitos clientes se conectam ao aplicativo Web, que redireciona os clientes para o serviço de Signaler do Azure com o token de acesso e o ponto de extremidade. Em seguida, os clientes estabelecem conexões WebSocket com o serviço de Signaler do Azure.

Depois que todos os clientes estabelecem conexões, eles começam a enviar uma mensagem que contém um carimbo de data/hora para o Hub específico a cada segundo. O Hub ecoa a mensagem de volta para seu cliente original. Cada cliente calcula a latência quando recebe a mensagem de eco de volta.

No diagrama a seguir, 5 a 8 (tráfego em vermelho realçado) estão em um loop. O loop é executado por uma duração padrão (5 minutos) e obtém a estatística de toda a latência da mensagem.

![Tráfego para o caso de uso de eco](./media/signalr-concept-performance/echo.png)

O comportamento do **eco** determina que a largura de banda de entrada máxima é igual à largura de banda de saída máxima. Para obter detalhes, consulte a tabela a seguir.

|       Echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões                       | 1,000 | 2\.000 | 5\.000 | 10.000 | 20,000 | 50.000 | 100.000 |
| Mensagens de entrada/saída por segundo | 1,000 | 2\.000 | 5\.000 | 10.000 | 20,000 | 50.000 | 100.000 |
| Largura de banda de entrada/saída | 2 Mbps   | 4 Mbps   | 10 Mbps  | 20 MBps   | 40 MBps   | 100 MBps  | 200 MBps   |

Nesse caso de uso, cada cliente invoca o Hub definido no servidor de aplicativos. O Hub apenas chama o método definido no lado do cliente original. Esse Hub é o Hub mais leve para **eco**.

```
        public void Echo(IDictionary<string, object> data)
        {
            Clients.Client(Context.ConnectionId).SendAsync("RecordLatency", data);
        }
```

Mesmo para esse Hub simples, a pressão de tráfego no servidor de aplicativos é proeminente à medida que a carga de mensagem de entrada de **eco** aumenta. Essa pressão de tráfego requer muitos servidores de aplicativos para grandes camadas de SKU. A tabela a seguir lista a contagem do servidor de aplicativos para cada camada.


|    Echo          | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões      | 1,000 | 2\.000 | 5\.000 | 10.000 | 20,000 | 50.000 | 100.000 |
| Contagem do servidor de aplicativos | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> O número de conexão do cliente, o tamanho da mensagem, a taxa de envio de mensagens, a camada de SKU e a CPU/memória do servidor de aplicativos afetam o desempenho geral do **eco**.

#### <a name="broadcast"></a>Transmissão

Para **difusão**, quando o aplicativo Web recebe a mensagem, ele é transmitido para todos os clientes. Quanto mais clientes houver para difundir, mais tráfego de mensagens haverá para todos os clientes. Confira o diagrama a seguir.

![Tráfego para o caso de uso de difusão](./media/signalr-concept-performance/broadcast.png)

Um número pequeno de clientes está transmitindo. A largura de banda da mensagem de entrada é pequena, mas a largura de banda de saída é enorme. A largura de banda da mensagem de saída aumenta à medida que a conexão do cliente ou a taxa de difusão aumenta.

A tabela a seguir resume as conexões de cliente máximas, a contagem de mensagens de entrada/saída e a largura de banda.

|     Transmissão             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| conexões               | 1,000 | 2\.000 | 5\.000  | 10.000 | 20,000 | 50.000  | 100.000 |
| Mensagens de entrada por segundo  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Mensagens de saída por segundo | 2\.000 | 4.000 | 10.000 | 20,000 | 40.000 | 100.000 | 200.000 |
| Largura de banda de entrada  | 4 KBps   | 4 KBps   | 4 KBps    | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps     |
| Largura de banda de saída | 4 Mbps   | 8 Mbps   | 20 MBps   | 40 MBps   | 80 MBps   | 200 MBps   | 400 MBps   |

Os clientes de difusão que postam mensagens não são mais do que quatro. Eles precisam de menos servidores de aplicativos em comparação com o **eco** porque o valor da mensagem de entrada é pequeno. Dois servidores de aplicativos são suficientes para considerações de desempenho e SLA. Mas você deve aumentar as conexões de servidor padrão para evitar desequilíbrio, especialmente para Unit50 e Unit100.

|   Transmissão      | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões      | 1,000 | 2\.000 | 5\.000 | 10.000 | 20,000 | 50.000 | 100.000 |
| Contagem do servidor de aplicativos | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Aumente as conexões de servidor padrão de 5 a 40 em cada servidor de aplicativo para evitar possíveis conexões de servidor desbalanceada com o serviço de Signaler do Azure.
>
> O número de conexão do cliente, o tamanho da mensagem, a taxa de envio de mensagens e a camada de SKU afetam o desempenho geral da **transmissão**.

#### <a name="send-to-group"></a>Enviar para o grupo

O caso de uso de **Enviar para grupo** tem um padrão de tráfego semelhante para **difundir**. A diferença é que, depois que os clientes estabelecem conexões WebSocket com o serviço de Signaler do Azure, eles devem ingressar em grupos antes que possam enviar uma mensagem a um grupo específico. O diagrama a seguir ilustra o fluxo de tráfego.

![Tráfego para o caso de uso de enviar para grupo](./media/signalr-concept-performance/sendtogroup.png)

O membro do grupo e a contagem de grupos são dois fatores que afetam o desempenho. Para simplificar a análise, definimos dois tipos de grupos:

- **Pequeno grupo**: cada grupo tem 10 conexões. O número do grupo é igual a (contagem máxima de conexões)/10. Por exemplo, para Unit1, se houver 1.000 contagens de conexão, temos 1000/10 = 100 grupos.

- **Big Group**: o número do grupo é sempre 10. A contagem de membros do grupo é igual a (contagem máxima de conexões)/10. Por exemplo, para Unit1, se houver 1.000 contagens de conexão, cada grupo terá 1000/10 = 100 membros.

**Enviar para o grupo** traz um custo de roteamento para o serviço de Signaler do Azure porque ele precisa localizar as conexões de destino por meio de uma estrutura de dados distribuída. À medida que as conexões de envio aumentam, o custo aumenta.

##### <a name="small-group"></a>Pequeno grupo

O custo de roteamento é significativo para enviar mensagens a muitos pequenos grupos. Atualmente, a implementação do serviço de Signaler do Azure atinge o limite de custo de roteamento em Unit50. Adicionar mais CPU e memória não ajuda, portanto, o Unit100 não pode melhorar ainda mais por design. Se precisar de mais largura de banda de entrada, entre em contato com o atendimento ao cliente.

|   Enviar para grupo pequeno     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50 | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| conexões               | 1,000 | 2\.000 | 5\.000  | 10.000 | 20,000 | 50.000 | 100.000
| Contagem de membros do grupo        | 10    | 10    | 10     | 10     | 10     | 10     | 10 
| Contagem de grupos               | 100   | 200   | 500    | 1,000  | 2\.000  | 5\.000  | 10.000 
| Mensagens de entrada por segundo  | 200   | 400   | 1,000  | 2\.500  | 4.000  | 7.000  | 7.000   |
| Largura de banda de entrada  | 400 KBps  | 800 KBps  | 2 Mbps     | 5 Mbps     | 8 Mbps     | 14 MBps    | 14 MBps     |
| Mensagens de saída por segundo | 2\.000 | 4.000 | 10.000 | 25,000 | 40.000 | 70.000 | 70.000  |
| Largura de banda de saída | 4 Mbps    | 8 Mbps    | 20 MBps    | 50 MBps     | 80 MBps    | 140 MBps   | 140 MBps    |

Muitas conexões de cliente estão chamando o Hub, portanto, o número do servidor de aplicativos também é crítico para o desempenho. A tabela a seguir lista as contagens sugeridas do servidor de aplicativos.

|  Enviar para grupo pequeno   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões      | 1,000 | 2\.000 | 5\.000 | 10.000 | 20,000 | 50.000 | 100.000 |
| Contagem do servidor de aplicativos | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> O número de conexão do cliente, o tamanho da mensagem, a taxa de envio de mensagens, o custo de roteamento, a camada de SKU e a CPU/memória do servidor de aplicativos afetam o desempenho geral do **grupo Enviar para pequeno**.

##### <a name="big-group"></a>Grupo grande

Para **Enviar para o grupo grande**, a largura de banda de saída torna-se o afunilamento antes de atingir o limite de custo de roteamento. A tabela a seguir lista a largura de banda de saída máxima, que é quase igual à de **Broadcast**.

|    Enviar para grupo grande      | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| conexões               | 1,000 | 2\.000 | 5\.000  | 10.000 | 20,000 | 50.000  | 100.000
| Contagem de membros do grupo        | 100   | 200   | 500    | 1,000  | 2\.000  | 5\.000   | 10.000 
| Contagem de grupos               | 10    | 10    | 10     | 10     | 10     | 10      | 10
| Mensagens de entrada por segundo  | 20    | 20    | 20     | 20     | 20     | 20      | 20      |
| Largura de banda de entrada  | 80 KBps   | 40 KBps   | 40 KBps    | 20 KBps    | 40 KBps    | 40 KBps     | 40 KBps     |
| Mensagens de saída por segundo | 2\.000 | 4.000 | 10.000 | 20,000 | 40.000 | 100.000 | 200.000 |
| Largura de banda de saída | 8 Mbps    | 8 Mbps    | 20 MBps    | 40 MBps    | 80 MBps    | 200 MBps    | 400 MBps    |

A contagem de conexões de envio não é mais do que 40. A carga no servidor de aplicativos é pequena, portanto, o número sugerido de aplicativos Web é pequeno.

|  Enviar para grupo grande  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões      | 1,000 | 2\.000 | 5\.000 | 10.000 | 20,000 | 50.000 | 100.000 |
| Contagem do servidor de aplicativos | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Aumente as conexões de servidor padrão de 5 a 40 em cada servidor de aplicativo para evitar possíveis conexões de servidor desbalanceada com o serviço de Signaler do Azure.
> 
> O número de conexão do cliente, o tamanho da mensagem, a taxa de envio de mensagens, o custo de roteamento e a camada de SKU afetam o desempenho geral do **envio para o grupo grande**.

#### <a name="send-to-connection"></a>Enviar para conexão

No caso de uso de **Enviar para conexão** , quando os clientes estabelecem as conexões com o serviço de Signaler do Azure, cada cliente chama um hub especial para obter sua própria ID de conexão. O parâmetro de comparação de desempenho coleta todas as IDs de conexão, embaralha-as e as reatribui a todos os clientes como um destino de envio. Os clientes continuam enviando a mensagem para a conexão de destino até que o teste de desempenho seja concluído.

![Tráfego para o caso de uso de enviar para cliente](./media/signalr-concept-performance/sendtoclient.png)

O custo de roteamento para **Enviar para conexão** é semelhante ao custo para **Enviar para um grupo pequeno**.

À medida que a contagem de conexão aumenta, o custo de roteamento limita o desempenho geral. Unit50 atingiu o limite. Como resultado, o Unit100 não pode melhorar ainda mais.

A tabela a seguir é um resumo estatístico após muitas rodadas da execução do parâmetro de comparação de **envio para conexão** .

|   Enviar para conexão   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50          | Unit100         |
|------------------------------------|-------|-------|-------|--------|--------|-----------------|-----------------|
| conexões                        | 1,000 | 2\.000 | 5\.000 | 10.000 | 20,000 | 50.000          | 100.000         |
| Mensagens de entrada/saída por segundo | 1,000 | 2\.000 | 5\.000 | 8,000  | 9\.000  | 20,000 | 20,000 |
| Largura de banda de entrada/saída | 2 Mbps    | 4 Mbps    | 10 Mbps   | 16 MBps    | 18 MBps    | 40 MBps       | 40 MBps       |

Esse caso de uso requer alta carga no lado do servidor de aplicativos. Consulte a contagem sugerida do servidor de aplicativos na tabela a seguir.

|  Enviar para conexão  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões      | 1,000 | 2\.000 | 5\.000 | 10.000 | 20,000 | 50.000 | 100.000 |
| Contagem do servidor de aplicativos | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> O número de conexão do cliente, o tamanho da mensagem, a taxa de envio de mensagens, o custo de roteamento, a camada de SKU e a CPU/memória do servidor de aplicativos afetam o desempenho geral do **envio para conexão**.

#### <a name="aspnet-signalr-echo-broadcast-and-send-to-small-group"></a>ASP.NET Signalr Echo, difundir e enviar para pequeno grupo

O serviço de Signaler do Azure fornece a mesma capacidade de desempenho para o Signalr ASP.NET. 

O teste de desempenho usa aplicativos Web do Azure do [plano de serviço padrão S3](https://azure.microsoft.com/pricing/details/app-service/windows/) para o signalr ASP.net.

A tabela a seguir fornece a contagem de aplicativos Web sugeridos para **eco**do signalr ASP.net.

|   Echo           | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões      | 1,000 | 2\.000 | 5\.000 | 10.000 | 20,000 | 50.000 | 100.000 |
| Contagem do servidor de aplicativos | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

A tabela a seguir fornece a contagem de aplicativos Web sugeridos para **difusão**do signalr ASP.net.

|  Transmissão       | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões      | 1,000 | 2\.000 | 5\.000 | 10.000 | 20,000 | 50.000 | 100.000 |
| Contagem do servidor de aplicativos | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

A tabela a seguir fornece a contagem do aplicativo Web sugerido para **Enviar para o grupo pequeno**do signalr ASP.net.

|  Enviar para grupo pequeno     | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| conexões      | 1,000 | 2\.000 | 5\.000 | 10.000 | 20,000 | 50.000 | 100.000 |
| Contagem do servidor de aplicativos | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

### <a name="serverless-mode"></a>Modo sem servidor

Os clientes e o serviço de Signaler do Azure estão envolvidos no modo sem servidor. Cada cliente significa uma única conexão. O cliente envia mensagens por meio da API REST para outro cliente ou transmite mensagens para todos.

O envio de mensagens de alta densidade por meio da API REST não é tão eficiente quanto usar WebSocket. Ele exige que você crie uma nova conexão HTTP a cada vez, e isso é um custo extra no modo sem servidor.

#### <a name="broadcast-through-rest-api"></a>Transmitir por meio da API REST
Todos os clientes estabelecem conexões WebSocket com o serviço de Signaler do Azure. Em seguida, alguns clientes iniciam a transmissão por meio da API REST. A mensagem de envio (entrada) é tudo por meio de HTTP post, que não é eficiente em comparação com WebSocket.

|   Transmitir por meio da API REST     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| conexões               | 1,000 | 2\.000 | 5\.000  | 10.000 | 20,000 | 50.000  | 100.000 |
| Mensagens de entrada por segundo  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Mensagens de saída por segundo | 2\.000 | 4.000 | 10.000 | 20,000 | 40.000 | 100.000 | 200.000 |
| Largura de banda de entrada  | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps     | 4 KBps     | 4 KBps      | 4 KBps      |
| Largura de banda de saída | 4 Mbps    | 8 Mbps    | 20 MBps    | 40 MBps    | 80 MBps    | 200 MBps    | 400 MBps    |

#### <a name="send-to-user-through-rest-api"></a>Enviar para o usuário por meio da API REST
O parâmetro de comparação atribui nomes de usuários a todos os clientes antes que eles comecem a se conectar ao serviço de Signaler do Azure. Depois que os clientes estabelecem conexões WebSocket com o serviço de Signaler do Azure, eles começam a enviar mensagens para outras pessoas por meio do HTTP post.

|   Enviar para o usuário por meio da API REST | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| conexões               | 1,000 | 2\.000 | 5\.000  | 10.000 | 20,000 | 50.000  | 100.000 |
| Mensagens de entrada por segundo  | 300   | 600   | 900    | 1.300  | 2\.000  | 10.000  | 18.000  |
| Mensagens de saída por segundo | 300   | 600   | 900    | 1.300  | 2\.000  | 10.000  | 18.000 |
| Largura de banda de entrada  | 600 KBps  | 1,2 MBps  | 1,8 MBps   | 2,6 MBps   | 4 Mbps     | 10 Mbps     | 36 MBps    |
| Largura de banda de saída | 600 KBps  | 1,2 MBps  | 1,8 MBps   | 2,6 MBps   | 4 Mbps     | 10 Mbps     | 36 MBps    |

## <a name="performance-test-environments"></a>Ambientes de teste de desempenho

Para todos os casos de uso listados anteriormente, conduzimos os testes de desempenho em um ambiente do Azure. No máximo, usamos 50 VMs de cliente e 20 VMs de servidor de aplicativos. Aqui estão alguns detalhes:

- Tamanho da VM do cliente: StandardDS2V2 (2 vCPU, 7G Memory)

- Tamanho da VM do servidor de aplicativos: StandardF4sV2 (4 vCPU, memória 8G)

- Conexões do servidor do SDK do Azure Signalr: 15

## <a name="performance-tools"></a>Ferramentas de desempenho

Você pode encontrar ferramentas de desempenho para o serviço de Signaler do Azure no [GitHub](https://github.com/Azure/azure-signalr-bench/).

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você obteve uma visão geral do desempenho do serviço de Signaler do Azure em cenários de caso de uso típicos.

Para obter detalhes sobre os elementos internos do serviço e o dimensionamento para ele, leia os seguintes guias:

* [Componentes internos do Serviço do Azure SignalR](signalr-concept-internals.md)
* [Dimensionamento de serviço do Azure Signalr](signalr-howto-scale-multi-instances.md)
