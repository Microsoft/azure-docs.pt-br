---
title: Conecte os dados de DLP (prevenção de perda de dados) Broadcom da Symantec ao Azure Sentinel | Microsoft Docs
description: Saiba como usar o conector de dados do Symantec DLP Broadcom para efetuar pull dos logs do Symantec DLP para o Azure Sentinel. Exiba dados do Symantec DLP em pastas de trabalho, crie alertas e melhore a investigação.
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
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: 4be182b3eee59f7f9d2ef704a3d0f57c6718b45d
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044967"
---
# <a name="connect-your-broadcom-symantec-data-loss-prevention-dlp-to-azure-sentinel"></a>Conecte sua DLP (prevenção de perda de dados) do Broadcom Symantec ao Azure Sentinel

> [!IMPORTANT]
> O conector da Symantec DLP Broadcom está atualmente em versão **prévia**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

Este artigo explica como conectar seu dispositivo de DLP do Broadcom Symantec ao Azure Sentinel. O Broadcom Symantec DLP data Connector permite que você conecte facilmente seus logs de DLP da Symantec com o Azure Sentinel, exiba painéis, crie alertas personalizados e melhore a investigação. Essa funcionalidade oferece a você mais informações sobre a informação da sua organização, onde elas viajam e aprimoram seus recursos de operação de segurança. A integração entre o Broadcom Symantec DLP e o Azure Sentinel usa o syslog formatado por CEF, um encaminhador de log baseado em Linux e o agente de Log Analytics. Ele também usa um Log Parser personalizado baseado em uma função Kusto.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter permissões de leitura e gravação em seu espaço de trabalho do Azure Sentinel.

- Você deve ter permissões de leitura para chaves compartilhadas para o espaço de trabalho. [Saiba mais sobre as chaves do espaço de trabalho](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-broadcom-symantec-dlp-logs-to-azure-sentinel"></a>Enviar logs do Symantec DLP Broadcom para o Azure Sentinel

Para fazer seus logs no Azure Sentinel, configure seu dispositivo de DLP da Symantec do Broadcom para enviar mensagens de syslog no formato CEF para um servidor de encaminhamento de log baseado em Linux (executando rsyslog ou syslog-ng). Esse servidor terá o agente de Log Analytics instalado e o agente encaminha os logs para seu espaço de trabalho do Azure Sentinel.

1. No menu de navegação do Azure Sentinel, selecione **conectores de dados**.

1. Na Galeria de **conectores de dados** , selecione o conector **Broadcom Symantec DLP (versão prévia)** e, em seguida, **abra a página conector**.

1. Siga as instruções na guia **instruções** , em **configuração**:

    1. Em **1. Configuração do agente de syslog do Linux** – faça esta etapa se você ainda não tiver um encaminhador de log em execução ou se precisar de outro. Consulte [a etapa 1: implantar o encaminhador de log](connect-cef-agent.md) na documentação do Azure Sentinel para obter instruções e explicações mais detalhadas.

    1. Em **2. Encaminhe os logs de DLP da Symantec para um agente de syslog** -siga as instruções da Broadcom para [Configurar o Symantec DLP](https://help.symantec.com/cs/DLP15.7/DLP/v27591174_v133697641/Configuring-the-Log-to-a-Syslog-Server-action?locale=EN_US). Essa configuração deve incluir os seguintes elementos:
        - Destino do log – o nome do host e/ou endereço IP do servidor de encaminhamento de log
        - Protocolo e porta – TCP 514 (se recomendado, caso contrário, certifique-se de fazer a alteração paralela no daemon do syslog em seu servidor de encaminhamento de log)
        - Formato de log – CEF
        - Tipos de log – todos disponíveis ou todos os apropriados

    1. Menos de **3. Validar conexão** -Verifique a ingestão de dados copiando o comando na página do conector e executando-o no encaminhador de log. Consulte [etapa 3: validar a conectividade](connect-cef-verify.md) na documentação do Azure Sentinel para obter instruções e explicações mais detalhadas.

        Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics.

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem nos **logs**, na seção **Sentinela do Azure** , na tabela *CommonSecurityLog* .

Esse conector de dados depende de um analisador baseado em uma função Kusto para funcionar conforme o esperado. [Siga estas etapas](https://aka.ms/sentinel-symantecdlp-parser) para criar o alias da função **SymantecDLP** Kusto.

Em seguida, para consultar dados de DLP do Symantec na Log Analytics, digite `SymantecDLP` na parte superior da janela de consulta.

Consulte a guia **próximas etapas** na página conector para obter alguns exemplos de consulta úteis.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar o Symantec DLP ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade de seus dados e ameaças em potencial](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.