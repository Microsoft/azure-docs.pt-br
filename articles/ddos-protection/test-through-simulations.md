---
title: Teste de simulação da proteção contra DDoS do Azure
description: Saiba mais sobre como testar por meio de simulações
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: e95495e48725a68ab1fe3f37d235e5765b2c8015
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806232"
---
# <a name="test-through-simulations"></a>Fazer testes por meio de simulações

É uma prática recomendada testar suas suposições sobre como os serviços responderão a um ataque ao conduzir simulações periódicas. Durante o teste, valide se os serviços ou aplicativos continuam funcionando conforme esperado e se não há interrupções na experiência do usuário. Identifique lacunas do ponto de vista de tecnologia e de processo, e incorpore-as à estratégia de resposta de DDoS. Recomendamos que realize esses testes em ambientes de preparo ou fora do horário de pico para minimizar o impacto no ambiente de produção.

Fizemos uma parceria com o [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud), um gerador de tráfego de autoatendimento, para criar uma interface em que os clientes do Azure possam gerar tráfego em relação aos pontos de extremidade públicos habilitados para proteção contra DDoS para simulações. Você pode usar a simulação para:

- Validar como a Proteção contra DDoS do Azure ajuda a proteger seus recursos do Azure contra ataques de DDoS.
- Otimize o processo de resposta a incidentes durante ataques de DDoS.
- Documente a conformidade de DDoS.
- Treine suas equipes de segurança de rede.

## <a name="prerequisites"></a>Pré-requisitos

- Antes de concluir as etapas deste tutorial, você deve primeiro criar um plano de [proteção padrão do DDoS do Azure](manage-ddos-protection.md) com endereços IP públicos protegidos.
- Você deve primeiro criar uma conta com o [BreakingPoint Cloud](http://breakingpoint.cloud/). 

## <a name="configure-a-ddos-test-attack"></a>Configurar um ataque de teste de DDoS

1. Insira ou selecione os valores a seguir e selecione **Iniciar teste**:

    |Configuração        |Valor                                              |
    |---------      |---------                                          |
    |Endereço IP de destino           | Insira um endereço IP público que você deseja testar.                     |
    |Número da porta   | Inserir _443_.                       |
    |Perfil de DDoS | Os valores possíveis incluem,,,,,,,,,, `DNS Flood` `NTPv2 Flood` `SSDP Flood` `TCP SYN Flood` `UDP 64B Flood` `UDP 128B Flood` `UDP 256B Flood` `UDP 512B Flood` `UDP 1024B Flood` `UDP 1514B Flood` `UDP Fragmentation` , `UDP Memcached` .|
    |Tamanho do teste       | Os valores possíveis incluem `100K pps, 50 Mbps and 4 source IPs` ,, `200K pps, 100 Mbps and 8 source IPs` `400K pps, 200Mbps and 16 source IPs` , `800K pps, 400 Mbps and 32 source IPs` .                                  |
    |Duração do teste | Os valores possíveis incluem,,, `10 Minutes` `15 Minutes` `20 Minutes` `25 Minutes` , `30 Minutes` .|

Agora deve aparecer assim:

![Exemplo de simulação de ataque de DDoS: nuvem BreakingPoint](./media/ddos-attack-simulation/ddos-attack-simulation-example-1.png)

## <a name="monitor-and-validate"></a>Monitorar e validar

1. Faça logon no https://portal.azure.com e acesse sua assinatura.
1. Selecione o endereço IP público no qual você testou o ataque.
1. Em **Monitoramento**, selecione **Métricas**.
1. Para **métrica**, selecione _sob ataque de DDoS ou não_.

Depois que o recurso estiver sob ataque, você verá que o valor muda de **0** para **1**, como a imagem a seguir:

![Exemplo de simulação de ataque de DDoS: Portal](./media/ddos-attack-simulation/ddos-attack-simulation-example-2.png)

### <a name="breakingpoint-cloud-api-script"></a>Script de API de nuvem do BreakingPoint

Esse [script de API](https://aka.ms/ddosbreakingpoint) pode ser usado para automatizar o teste de DDoS executando uma vez ou usando cron para agendar testes regulares. Isso é útil para validar se o registro em log está configurado corretamente e se os procedimentos de detecção e resposta entram em vigor. Os scripts exigem um sistema operacional Linux (testado com o Ubuntu 18, 4 LTS) e o Python 3. Instale os pré-requisitos e o cliente de API usando o script incluído ou usando a documentação no site do [BreakingPoint Cloud](http://breakingpoint.cloud/) .

## <a name="next-steps"></a>Próximas etapas

- Saiba como [Exibir e configurar a telemetria de proteção contra DDoS](telemetry.md).
- Saiba como [Exibir e configurar o log de diagnóstico de DDoS](diagnostic-logging.md).
- Saiba como [envolver uma resposta rápida de DDoS](ddos-rapid-response.md).
