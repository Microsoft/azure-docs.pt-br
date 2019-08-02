---
title: Redefinição de TCP do Load Balancer quando ocioso no Azure
titlesuffix: Azure Load Balancer
description: Load Balancer com pacotes TCP RST bidirecionais em tempo limite de ociosidade
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2019
ms.author: allensu
ms.openlocfilehash: 8485f4b6e8d4ff55de4930b3cfb7a07802cf1d41
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274150"
---
# <a name="load-balancer-with-tcp-reset-on-idle-public-preview"></a>Balanceador de Carga com Redefinição de TCP quando ocioso (Versão Prévia Pública)

Você pode usar o [Standard Load Balancer](load-balancer-standard-overview.md) para criar um comportamento de aplicativo mais previsível para seus cenários, permitindo a Redefinição de TCP no modo ocioso para uma determinada regra. O comportamento de padrão do Load Balancer é remover fluxos silenciosamente quando o tempo limite de ociosidade de um fluxo for atingido.  Habilitar esse recurso fará o Load Balancer enviar Redefinições de TCP bidirecionais (pacote TCP RST) no tempo limite de ociosidade.  Isso informará os pontos de extremidade do aplicativo de que a conexão atingiu o tempo limite e não é mais utilizável.  Pontos de extremidade podem estabelecer imediatamente uma nova conexão se necessário.

![Redefinição de TCP do Balanceador de Carga](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)

>[!NOTE] 
>Load Balancer com a funcionalidade de redefinição de TCP no tempo limite de ociosidade está disponível como visualização pública no momento. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.
 
Alterar esse comportamento padrão e habilitar envio de Redefinições de TCP no tempo limite de ociosidade em regras NAT de entrada, regras de balanceamento de carga e [regras de saída](https://aka.ms/lboutboundrules).  Quando habilitado por regra, o Load Balancer enviará Redefinições de TCP (pacotes TCP RST) para os pontos de extremidade do cliente e do servidor no momento do tempo limite de ociosidade para todos os fluxos correspondentes.

Os pontos de extremidade que recebem pacotes TCP RST fecham imediatamente o soquete correspondente. Isso fornece uma notificação imediata para os pontos de extremidade que a liberação da conexão ocorreu e qualquer comunicação futura na mesma conexão TCP falhará.  Aplicativos podem limpar conexões quando o soquete é fechado e restabelecer as conexões conforme necessário, sem esperar o tempo limite eventual da conexão TCP.

Para muitos cenários, isso pode reduzir a necessidade de enviar keepalives de TCP (ou de camada de aplicativo) para atualizar o tempo limite de um fluxo. 

Se as durações ociosas excederem aquelas permitidas pela configuração ou seu aplicativo apresentar um comportamento indesejado com Redefinições de TCP habilitadas, você ainda precisará usar keepalives de TCP (ou keepalives da camada de aplicativo) para monitorar a atividade das conexões TCP.  Além disso, keepalives também podem ser úteis para quando a conexão é transmitida com proxy em algum lugar no caminho, particularmente em keepalives de camada de aplicativo.  

Examine cuidadosamente todo o cenário de ponta a ponta para decidir se é vantagem habilitar Redefinições de TCP, ajustando o tempo limite de ociosidade, e se podem ser necessárias etapas adicionais para garantir o comportamento do aplicativo desejado.

## <a name="enabling-tcp-reset-on-idle-timeout"></a>Habilitar Redefinição de TCP após tempo limite de ociosidade

Usando a API versão 2018-07-01, você pode habilitar o envio de Redefinições de TCP bidirecionais após tempo limite de ociosidade em uma base por regra:

```json
      "loadBalancingRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "inboundNatRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "outboundRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

## <a name="regions"></a>Disponibilidade de região

Disponível em todas as regiões.

## <a name="limitations"></a>Limitações

- O portal não pode ser usado para configurar ou exibir a Redefinição de TCP.  Em vez disso, use modelos, API REST, Az CLI 2.0 ou PowerShell.
- TCP RST é enviado somente durante a conexão TCP no estado estabelecido.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Standard Load Balancer](load-balancer-standard-overview.md).
- Saiba mais sobre [regras de saída](load-balancer-outbound-rules-overview.md).
