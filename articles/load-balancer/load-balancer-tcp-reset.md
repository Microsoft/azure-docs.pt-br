---
title: Load Balancer a redefinição de TCP e o tempo limite de ociosidade no Azure
titleSuffix: Azure Load Balancer
description: Com este artigo, saiba mais sobre Azure Load Balancer com pacotes TCP RST bidirecionais no tempo limite de ociosidade.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/07/2020
ms.author: allensu
ms.openlocfilehash: 0d02b46345af13770f77a7dac452127a665e01fd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94696737"
---
# <a name="load-balancer-tcp-reset-and-idle-timeout"></a>Load Balancer o tempo limite de ociosidade e de redefinição de TCP

Você pode usar o [Standard Load Balancer](./load-balancer-overview.md) para criar um comportamento de aplicativo mais previsível para seus cenários, permitindo a Redefinição de TCP no modo ocioso para uma determinada regra. O comportamento de padrão do Load Balancer é remover fluxos silenciosamente quando o tempo limite de ociosidade de um fluxo for atingido.  Habilitar esse recurso fará o Load Balancer enviar Redefinições de TCP bidirecionais (pacote TCP RST) no tempo limite de ociosidade.  Isso informará os pontos de extremidade do aplicativo de que a conexão atingiu o tempo limite e não é mais utilizável.  Pontos de extremidade podem estabelecer imediatamente uma nova conexão se necessário.

![Redefinição de TCP do Balanceador de Carga](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)
 
## <a name="tcp-reset"></a>Redefinição de TCP

Alterar esse comportamento padrão e habilitar envio de Redefinições de TCP no tempo limite de ociosidade em regras NAT de entrada, regras de balanceamento de carga e [regras de saída](./load-balancer-outbound-connections.md#outboundrules).  Quando habilitado por regra, o Load Balancer enviará Redefinições de TCP (pacotes TCP RST) para os pontos de extremidade do cliente e do servidor no momento do tempo limite de ociosidade para todos os fluxos correspondentes.

Os pontos de extremidade que recebem pacotes TCP RST fecham imediatamente o soquete correspondente. Isso fornece uma notificação imediata para os pontos de extremidade que a liberação da conexão ocorreu e qualquer comunicação futura na mesma conexão TCP falhará.  Aplicativos podem limpar conexões quando o soquete é fechado e restabelecer as conexões conforme necessário, sem esperar o tempo limite eventual da conexão TCP.

Para muitos cenários, isso pode reduzir a necessidade de enviar keepalives de TCP (ou de camada de aplicativo) para atualizar o tempo limite de um fluxo. 

Se as durações ociosas excederem aquelas permitidas pela configuração ou seu aplicativo apresentar um comportamento indesejado com Redefinições de TCP habilitadas, você ainda precisará usar keepalives de TCP (ou keepalives da camada de aplicativo) para monitorar a atividade das conexões TCP.  Além disso, keepalives também podem ser úteis para quando a conexão é transmitida com proxy em algum lugar no caminho, particularmente em keepalives de camada de aplicativo.  

Examine cuidadosamente todo o cenário de ponta a ponta para decidir se é vantagem habilitar Redefinições de TCP, ajustando o tempo limite de ociosidade, e se podem ser necessárias etapas adicionais para garantir o comportamento do aplicativo desejado.

## <a name="configurable-tcp-idle-timeout"></a>Tempo limite de ociosidade de TCP configurável

Azure Load Balancer tem o seguinte intervalo de tempo limite ocioso:
-  4 minutos a 100 minutos para regras de saída
-  4 minutos a 30 minutos para regras de Load Balancer e regras NAT de entrada

Por padrão, é definido como 4 minutos. Se um período de inatividade for maior que o valor de tempo limite, não haverá nenhuma garantia de que a sessão TCP ou HTTP seja mantida entre o cliente e o serviço de nuvem.

Quando a conexão estiver fechada, o aplicativo cliente poderá receber a seguinte mensagem de erro: "A conexão subjacente estava fechada: Uma conexão que deveria permanecer ativa foi fechada pelo servidor."

Uma prática comum é usar um TCP keep alive. Essa prática mantém a conexão ativa por um período maior. Para obter mais informações, consulte estes [exemplos do .NET](/dotnet/api/system.net.servicepoint.settcpkeepalive). Com o keep alive habilitado, os pacotes são enviados durante os períodos de inatividade na conexão. Os pacotes keep-alive garantem que o valor de tempo limite de ociosidade não será atingido, e a conexão será mantida por um longo período.

A configuração só funciona para conexões de entrada. Para evitar a perda da conexão, configure o TCP keep-alive com um intervalo menor do que a configuração de tempo limite de ociosidade ou aumente o valor do tempo limite de ociosidade. Para permitir esses cenários, o suporte a um tempo limite de ociosidade configurável foi adicionado.

O TCP keep-alive funciona para cenários em que a vida útil da bateria não é uma restrição. Não é recomendável para aplicativos móveis. Usar o TCP keep alive em um aplicativo móvel pode consumir a bateria do dispositivo mais rapidamente.


## <a name="limitations"></a>Limitações

- A redefinição de TCP é enviada somente durante a conexão TCP no estado estabelecido.
- A redefinição de TCP não é enviada para balanceadores de carga internos com portas de alta disponibilidade configuradas.
- O tempo limite de ociosidade de TCP não afeta as regras de balanceamento de carga no protocolo UDP.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [Standard Load Balancer](./load-balancer-overview.md).
- Saiba mais sobre [as regras de saída](./load-balancer-outbound-connections.md#outboundrules).
- [Configurar TCP RST no tempo limite ocioso](load-balancer-tcp-idle-timeout.md)