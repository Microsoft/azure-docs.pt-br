---
title: Conectar o servidor secreto do Thycotic ao Azure sentinela | Microsoft Docs
description: Saiba como usar o conector de dados do servidor de segredo Thycotic para efetuar pull dos logs do servidor secreto Thycotic no Azure Sentinel. Exibir dados do servidor de segredo Thycotic em pastas de trabalho, criar alertas e melhorar a investigação.
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
ms.date: 12/13/2020
ms.author: yelevin
ms.openlocfilehash: a0056391dddec25511deb7033d37f59db3d835c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98567858"
---
# <a name="connect-your-thycotic-secret-server-to-azure-sentinel"></a>Conectar seu servidor de segredo do Thycotic ao Azure Sentinel

> [!IMPORTANT]
> O conector do servidor de segredo Thycotic está atualmente em versão **prévia**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

Este artigo explica como conectar seu dispositivo de servidor de segredo do Thycotic ao Azure Sentinel. O conector de dados do servidor Thycotic Secret permite que você conecte facilmente seus logs de servidor secreto do Thycotic com o Azure Sentinel, para que você possa exibir os dados em pastas de trabalho, usá-los para criar alertas personalizados e incorporá-los para melhorar a investigação. A integração entre o Thycotic e o Azure Sentinel faz uso do CEF data Connector para analisar e exibir corretamente as mensagens de syslog do servidor secreto.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter permissões de leitura e gravação em seu espaço de trabalho do Azure Sentinel.

- Você deve ter permissões de leitura para chaves compartilhadas para o espaço de trabalho.

- Seu servidor de segredo do Thycotic deve ser configurado para exportar logs via syslog.

## <a name="send-thycotic-secret-server-logs-to-azure-sentinel"></a>Enviar logs do servidor secreto do Thycotic para o Azure Sentinel

Para fazer seus logs no Azure Sentinel, configure seu servidor de segredo Thycotic para enviar mensagens de syslog no formato CEF para seu servidor de encaminhamento de log baseado em Linux (executando rsyslog ou syslog-ng). Esse servidor terá o agente de Log Analytics instalado e o agente encaminha os logs para seu espaço de trabalho do Azure Sentinel.

1. No menu de navegação do Azure Sentinel, selecione **conectores de dados**.

1. Na Galeria de **conectores de dados** , selecione **servidor Thycotic Secret (versão prévia)** e, em seguida, **abra a página conector**.

1. Siga as instruções na guia **instruções** , em **configuração**:

    1. Em **1. Configuração do agente de syslog do Linux** – faça esta etapa se você ainda não tiver um encaminhador de log em execução ou se precisar de outro. Consulte [a etapa 1: implantar o encaminhador de log](connect-cef-agent.md) na documentação do Azure Sentinel para obter instruções e explicações mais detalhadas.

    1. Em **2. Encaminhe os logs de CEF (formato de evento comum) para o agente de syslog** -siga as instruções do Thycotic para [Configurar o servidor secreto](https://thy.center/ss/link/syslog). Essa configuração deve incluir os seguintes elementos:
        - Destino do log – o nome do host e/ou endereço IP do servidor de encaminhamento de log
        - Protocolo e porta – **TCP 514** (se recomendado, caso contrário, certifique-se de fazer a alteração paralela no daemon do syslog em seu servidor de encaminhamento de log)
        - Formato de log – CEF
        - Tipos de log – todos disponíveis

    1. Menos de **3. Validar conexão** -Verifique a ingestão de dados copiando o comando na página do conector e executando-o no encaminhador de log. Consulte [etapa 3: validar a conectividade](connect-cef-verify.md) na documentação do Azure Sentinel para obter instruções e explicações mais detalhadas.

        Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics.

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem nos **logs**, na seção **Sentinela do Azure** , na tabela *CommonSecurityLog* .

Para consultar dados do servidor secreto do Thycotic em Log Analytics, copie o seguinte na janela de consulta, aplicando outros filtros à medida que você escolher:

```kusto
CommonSecurityLog 
| where DeviceVendor == "Thycotic Software"
```

Consulte a guia **próximas etapas** na página conector para ver algumas pastas de trabalho e exemplos de consulta úteis.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar o servidor secreto do Thycotic ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.