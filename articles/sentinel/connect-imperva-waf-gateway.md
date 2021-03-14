---
title: Conectar seu dispositivo de gateway Imperva WAF ao Azure sentinela | Microsoft Docs
description: Saiba como usar o conector de gateway Imperva WAF para efetuar pull de logs do Imperva WAF no Azure Sentinel. Exiba dados do Imperva WAF em pastas de trabalho, crie alertas e melhore a investigação.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: a37abf369d1f34dc8f4a27802dfad88dab79be44
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101698426"
---
# <a name="connect-your-imperva-waf-gateway-appliance-to-azure-sentinel"></a>Conectar seu dispositivo de gateway Imperva WAF ao Azure Sentinel

> [!IMPORTANT]
> O conector do gateway Imperva WAF está atualmente em **Visualização**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

Este artigo explica como conectar seu dispositivo de gateway Imperva WAF ao Azure Sentinel. O conector de dados do gateway Imperva WAF permite que você conecte facilmente seus logs de gateway do Imperva WAF com o Azure Sentinel, para que você possa exibir os dados em pastas de trabalho, usá-los para criar alertas personalizados e incorporá-los para melhorar a investigação. A integração entre o Imperva WAF gateway e o Azure Sentinel usa o syslog formatado por CEF, um encaminhador de log baseado em Linux e o agente de Log Analytics.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter permissões de leitura e gravação em seu espaço de trabalho do Azure Sentinel.

- Você deve ter permissões de leitura para chaves compartilhadas para o espaço de trabalho. [Saiba mais sobre as chaves do espaço de trabalho](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-imperva-waf-gateway-logs-to-azure-sentinel"></a>Enviar logs do gateway Imperva WAF para o Azure Sentinel

Para fazer seus logs no Azure Sentinel, configure seu dispositivo de gateway Imperva WAF para enviar mensagens de syslog no formato CEF para um servidor de encaminhamento de log baseado em Linux (executando rsyslog ou syslog-ng). Esse servidor terá o agente de Log Analytics instalado e o agente encaminha os logs para seu espaço de trabalho do Azure Sentinel.

1. No menu de navegação do Azure Sentinel, selecione **conectores de dados**.

1. Na Galeria de **conectores de dados** , selecione **Imperva WAF gateway (versão prévia)** e, em seguida, **abra a página conector**.

1. Siga as instruções na guia **instruções** , em **configuração**:

    1. Em **1. Configuração do agente de syslog do Linux** – faça esta etapa se você ainda não tiver um encaminhador de log em execução ou se precisar de outro. Consulte [a etapa 1: implantar o encaminhador de log](connect-cef-agent.md) na documentação do Azure Sentinel para obter instruções e explicações mais detalhadas.

    1. Em **2. Encaminhe os logs de CEF (formato de evento comum) para o agente de syslog** – este conector requer uma **interface de ação** e uma **ação definida** para ser criada no console de gerenciamento do **Imperva SecureSphere MX** . Siga as instruções do Imperva para [habilitar o log de alertas do gateway do Imperva WAF para o Azure Sentinel](https://community.imperva.com/blogs/craig-burlingame1/2020/11/13/steps-for-enabling-imperva-waf-gateway-alert). Essa configuração deve incluir os seguintes elementos:
        - Destino do log – o nome do host e/ou endereço IP do servidor de encaminhamento de log
        - Protocolo e porta – TCP 514
        - Formato de log – CEF
        - Tipos de log – todos disponíveis

    1. Menos de **3. Validar conexão** -Verifique a ingestão de dados copiando o comando na página do conector e executando-o no encaminhador de log. Consulte [etapa 3: validar a conectividade](connect-cef-verify.md) na documentação do Azure Sentinel para obter instruções e explicações mais detalhadas.

        Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics.

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem nos **logs**, na seção **Sentinela do Azure** , na tabela *CommonSecurityLog* .

Para consultar dados de gateway do Imperva WAF no Log Analytics, copie o seguinte na janela de consulta, aplicando outros filtros à medida que você escolher:

```kusto
CommonSecurityLog 
| where DeviceVendor == "Imperva Inc." 
| where DeviceProduct == "WAF Gateway" 
| where TimeGenerated == ago(5m)
```

Consulte a guia **próximas etapas** na página conector para obter exemplos de consulta mais úteis.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar o gateway do Imperva WAF ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade de seus dados e ameaças em potencial](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.