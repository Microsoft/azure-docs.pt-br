---
title: Conectar a plataforma de perícia de rede WireX (NFP) ao Azure Sentinel | Microsoft Docs
description: Saiba como usar o conector de dados WireX Systems NFP para efetuar pull WireX logs de NFP para o Azure Sentinel. Exiba dados de WireX NFP em pastas de trabalho, crie alertas e melhore a investigação.
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
ms.openlocfilehash: 9029b945eabd05b34306393b513e26ee9c1563f2
ms.sourcegitcommit: 949c0a2b832d55491e03531f4ced15405a7e92e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98541531"
---
# <a name="connect-your-wirex-network-forensics-platform-nfp-appliance-to-azure-sentinel"></a>Conectar seu dispositivo da plataforma de perícia de rede WireX (NFP) ao Azure Sentinel

> [!IMPORTANT]
> O conector WireX Systems NFP está atualmente em versão **prévia**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

Este artigo explica como conectar seu dispositivo da plataforma de perícia de rede do WireX Systems (NFP) ao Azure Sentinel. O conector de dados WireX NFP permite que você conecte facilmente seus logs NFP com o Azure Sentinel, para que você possa exibir os dados em pastas de trabalho, usá-los para criar alertas personalizados e incorporá-los para melhorar a investigação. 

> [!NOTE] 
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter permissões de leitura e gravação em seu espaço de trabalho do Azure Sentinel.

- Você deve ter permissões de leitura para chaves compartilhadas para o espaço de trabalho.

## <a name="send-wirex-nfp-logs-to-azure-sentinel"></a>Enviar logs WireX NFP para o Azure Sentinel

Para fazer seus logs no Azure Sentinel, configure seu dispositivo WireX Systems NFP para enviar mensagens de syslog no formato CEF para um servidor de encaminhamento de log baseado em Linux (executando rsyslog ou syslog-ng). Esse servidor terá o agente de Log Analytics instalado e o agente encaminha os logs para seu espaço de trabalho do Azure Sentinel.

1. No menu de navegação do Azure Sentinel, selecione **conectores de dados**.

1. Na Galeria de **conectores de dados** , selecione **WireX Network Forensics Platform (versão prévia)** e **abra a página conector**.

1. Siga as instruções na guia **instruções** , em **configuração**:

    1. **1. configuração do agente de syslog do Linux** – faça esta etapa se você ainda não tiver um encaminhador de log em execução ou se precisar de outro. Consulte [a etapa 1: implantar o encaminhador de log](connect-cef-agent.md) na documentação do Azure Sentinel para obter instruções e explicações mais detalhadas.

    1. **2. encaminhar logs de CEF (formato de evento comum) para o agente de syslog** -contate o [suporte do WireX](https://wirexsystems.com/contact-us/) para obter a configuração adequada da sua solução de WireX NFP. Essa configuração deve incluir os seguintes elementos:
        - Destino do log – o nome do host e/ou endereço IP do servidor de encaminhamento de log
        - Protocolo e porta – TCP 514 (se recomendado, caso contrário, certifique-se de fazer a alteração paralela no daemon do syslog em seu servidor de encaminhamento de log)
        - Formato de log – CEF
        - Tipos de log – tudo recomendado pelo WireX

    1. **3. validar conexão** -Verifique a ingestão de dados copiando o comando na página do conector e executando-o no encaminhador de log. Consulte [etapa 3: validar a conectividade](connect-cef-verify.md) na documentação do Azure Sentinel para obter instruções e explicações mais detalhadas.

        Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics.

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem nos **logs**, na seção **Sentinela do Azure** , na tabela *CommonSecurityLog* .

Para consultar dados de WireX NFP no Log Analytics, copie o seguinte na janela de consulta, aplicando outros filtros à medida que você escolher:

```kusto
CommonSecurityLog 
| where DeviceVendor == "WireX"
```

Consulte a guia **próximas etapas** na página conector para obter mais exemplos de consulta.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar o WireX Systems NFP ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.