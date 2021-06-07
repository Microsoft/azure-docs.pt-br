---
title: Conectar o Apache HTTP Server ao Azure Sentinel | Microsoft Docs
description: Saiba como usar o conector do Apache HTTP Server para efetuar pull de logs do Apache no Azure Sentinel. Veja os dados do Apache em pastas de trabalho, crie alertas e aprimore a investigação.
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
ms.openlocfilehash: 6a1a2a2a7dac961e49e6ced38803649ebf5ad523
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100096847"
---
# <a name="connect-your-apache-http-server-to-azure-sentinel"></a>Conectar o Apache HTTP Server ao Azure Sentinel

> [!IMPORTANT]
> Atualmente, o conector do Apache HTTP Server está em **VERSÃO PRÉVIA**. Veja os [Termos de Uso Complementares para Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, versão prévia ou que, de outra forma, ainda não foram lançados em disponibilidade geral.

Este artigo explica como conectar o Apache HTTP Server ao Azure Sentinel. O conector do Apache HTTP Server permite que você ingira com facilidade os logs do Apache HTTP Server no Azure Sentinel, de modo que possa ver os dados em pastas de trabalho, consultá-los para criar alertas personalizados e incorporá-los para aprimorar a investigação. A integração entre o Apache HTTP Server e o Azure Sentinel usa o processamento de arquivo local pelo agente do Log Analytics.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do workspace no qual você está executando o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Você precisa ter permissão de gravação no workspace do Azure Sentinel.

## <a name="configure-and-integrate-apache-http-server-logs-via-log-analytics-agent"></a>Configurar e integrar logs do Apache HTTP Server por meio do agente do Log Analytics

Configure o Apache HTTP Server para enviar arquivos de log para seu workspace do Azure por meio do agente do Log Analytics.
Configure o agente do Log Analytics para ler arquivos de log do Apache HTTP Server.

1. Siga as instruções descritas em https://httpd.apache.org/docs/2.4/logs.html para configurar a localização dos arquivos de log no Apache HTTP Server.

1. No menu de navegação do Azure Sentinel, selecione **Conectores de dados** e **Apache HTTP Server (Versão Prévia)** .

1. Clique em **Abrir página do conector**.

1. Siga as instruções da página do Apache HTTP Server.

## <a name="find-your-data"></a>Encontrar seus dados

Depois que uma conexão bem-sucedida for estabelecida, os dados serão exibidos no Log Analytics em ApacheHTTPServer_CL.

## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar até 20 minutos até que os logs comecem a ser exibidos no Log Analytics.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar o Apache HTTP Server ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.
