---
title: Configure sua solução de segurança para conectar dados de CEF à visualização do Azure Sentinel | Microsoft Docs
description: Saiba como configurar sua solução de segurança para conectar dados de CEF ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 07685cccf5a8dce99ae13e6df5186a5da44b1338
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99559416"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>ETAPA 2: configurar sua solução de segurança para enviar mensagens CEF

Nesta etapa, você executará as alterações de configuração necessárias na sua própria solução de segurança para enviar logs para o agente CEF.

## <a name="configure-a-solution-with-a-connector"></a>Configurar uma solução com um conector

Se sua solução de segurança já tiver um conector existente, use as instruções específicas do conector da seguinte maneira:

- [Detecção de IA da Vectra](connect-ai-vectra-detect.md)
- [Eventos de segurança da Akamai](connect-akamai-security-events.md)
- [Check Point](connect-checkpoint.md)
- [Cisco ASA](connect-cisco.md)
- [Citrix WAF](connect-citrix-waf.md)
- [CyberArk Enterprise Password Vault](connect-cyberark.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [F5 ASM](connect-f5.md)
- [Produtos Forcepoint](connect-forcepoint-casb-ngfw.md)
- [Fortinet](connect-fortinet.md)
- [Illusive redes AMS](connect-illusive-attack-management-system.md)
- [Imperva WAF gateway](connect-imperva-waf-gateway.md)
- [One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [Servidor secreto Thycotic](connect-thycotic-secret-server.md)
- [Deep Security da Trend Micro](connect-trend-micro.md)
- [Trend Micro TippingPoint](connect-trend-micro-tippingpoint.md)
- [Plataforma de perícia de rede WireX](connect-wirex-systems.md)
- [Zscaler](connect-zscaler.md)
## <a name="configure-any-other-solution"></a>Configurar qualquer outra solução

Se não existir um conector para sua solução de segurança específica, use as instruções genéricas a seguir para encaminhar logs para o agente CEF.

1. Vá para o artigo de configuração específico para obter as etapas sobre como configurar sua solução para enviar mensagens CEF. Se sua solução não estiver listada, no dispositivo, você precisará definir esses valores para que o dispositivo envie os logs necessários no formato necessário para o agente de syslog do Sentinela do Azure, com base no agente de Log Analytics. Você pode modificar esses parâmetros em seu dispositivo, contanto que também os modifique no daemon do syslog no agente do Azure Sentinel.
    - Protocolo = TCP
    - Porta = 514
    - Formato = CEF
    - Endereço IP-certifique-se de enviar as mensagens CEF para o endereço IP da máquina virtual que você dedicaram para essa finalidade.

   Esta solução oferece suporte a RFC 3164 ou a RFC 5424 do syslog.

1. Para procurar eventos CEF em Log Analytics, insira `CommonSecurityLog` na janela de consulta.

1. Continue na etapa 3: [validar a conectividade](connect-cef-verify.md).

> [!NOTE]
> **Alterando a origem do campo TimeGenerated**
>
> - Por padrão, o agente de Log Analytics popula o campo *TimeGenerated* no esquema com a hora em que o agente recebeu o evento do daemon syslog. Como resultado, a hora em que o evento foi gerado no sistema de origem não é registrada no Azure Sentinel.
>
> - No entanto, você pode executar o comando a seguir, que baixará e executará o `TimeGenerated.py` script. Esse script configura o agente de Log Analytics para preencher o campo *TimeGenerated* com a hora original do evento em seu sistema de origem, em vez da hora em que ele foi recebido pelo agente.
>
>    ```bash
>    wget -O TimeGenerated.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/TimeGenerated.py && python TimeGenerated.py {ws_id}
>    ```

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar os dispositivos CEF ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](./tutorial-detect-threats-built-in.md).