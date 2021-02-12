---
title: Conectar a Trend Micro TippingPoint ao Azure Sentinel | Microsoft Docs
description: Saiba como usar o conector de dados Trend Micro TippingPoint para receber os logs de SMS da TippingPoint na sentinela do Azure. Exiba os dados da TippingPoint em pastas de trabalho, crie alertas e melhore a investigação.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2021
ms.author: yelevin
ms.openlocfilehash: 5c7491a0e0ba2a3bf604988c613e1fd8937f277d
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98752163"
---
# <a name="connect-your-trend-micro-tippingpoint-solution-to-azure-sentinel"></a>Conecte sua solução Trend Micro TippingPoint ao Azure Sentinel

> [!IMPORTANT]
> O conector Trend Micro TippingPoint está atualmente em versão **prévia**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

Este artigo explica como conectar a solução de sistema de proteção contra ameaças do Trend Micro TippingPoint ao Azure Sentinel. O conector de dados Trend Micro TippingPoint permite que você conecte facilmente seus logs de SMS (sistema de gerenciamento de segurança) da TippingPoint com o Azure Sentinel, para que você possa exibir os dados em pastas de trabalho, usá-los para criar alertas personalizados e incorporá-los para melhorar a investigação.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter permissões de leitura e gravação em seu espaço de trabalho do Azure Sentinel.

- Você deve ter permissões de leitura para chaves compartilhadas para o espaço de trabalho.

## <a name="send-trend-micro-tippingpoint-logs-to-azure-sentinel"></a>Enviar logs da Trend Micro TippingPoint para o Azure Sentinel

Para fazer seus logs no Azure Sentinel, configure sua solução TippingPoint TPS para enviar mensagens syslog no formato CEF para um servidor de encaminhamento de log baseado em Linux (executando rsyslog ou syslog-ng). Esse servidor terá o agente de Log Analytics instalado e o agente encaminha os logs para seu espaço de trabalho do Azure Sentinel. O conector usa uma função de analisador para converter os dados recebidos em um esquema normalizado. 

1. No menu de navegação do Azure Sentinel, selecione **conectores de dados**.

1. Na Galeria de **conectores de dados** , selecione **Trend Micro TippingPoint (versão prévia)** e **abra a página conector**.

1. Siga as instruções na guia **instruções** , em **configuração**:

    1. Em **1. Configuração do agente de syslog do Linux** – faça esta etapa se você ainda não tiver um encaminhador de log em execução ou se precisar de outro. Consulte [a etapa 1: implantar o encaminhador de log](connect-cef-agent.md) na documentação do Azure Sentinel para obter instruções e explicações mais detalhadas.

    1. Em **2. Encaminhar os logs de SMS da Trend Micro TippingPoint para o agente de syslog** -essa configuração deve incluir os seguintes elementos:
        - Destino do log – o nome do host e/ou endereço IP do servidor de encaminhamento de log
        - Protocolo e porta – **TCP 514** (se recomendado, caso contrário, certifique-se de fazer a alteração paralela no daemon do syslog em seu servidor de encaminhamento de log)
        - Formato de log – **formato ARCSIGHT CEF v 4.2**
        - Tipos de log – todos disponíveis

    1. Menos de **3. Validar conexão** -Verifique a ingestão de dados copiando o comando na página do conector e executando-o no encaminhador de log. Consulte [etapa 3: validar a conectividade](connect-cef-verify.md) na documentação do Azure Sentinel para obter instruções e explicações mais detalhadas.

        Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics.

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem nos **logs**, na seção **Sentinela do Azure** , na tabela *CommonSecurityLog* .

Para obter dados da Trend Micro TippingPoint em Log Analytics, você consultará a função do analisador em vez da tabela. Copie o seguinte na janela de consulta, aplicando outros filtros à medida que você escolher:

```kusto
TrendMicroTippingPoint
| sort by TimeGenerated
```

Consulte a guia **próximas etapas** na página conector para obter mais exemplos de consulta.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar a Trend Micro TippingPoint ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.
