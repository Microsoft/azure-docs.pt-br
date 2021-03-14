---
title: Conectar o servidor HTTP do Apache ao Azure Sentinel | Microsoft Docs
description: Saiba como usar o conector do servidor HTTP do Apache para efetuar pull de logs do Apache no Azure Sentinel. Exiba os dados do Apache em pastas de trabalho, crie alertas e aprimore a investigação.
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
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100096847"
---
# <a name="connect-your-apache-http-server-to-azure-sentinel"></a>Conectar o servidor HTTP do Apache ao Azure Sentinel

> [!IMPORTANT]
> O conector do servidor HTTP do Apache está atualmente em versão **prévia**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

Este artigo explica como conectar seu servidor HTTP do Apache ao Azure Sentinel. O conector do servidor HTTP do Apache permite que você ingerir facilmente os logs do servidor HTTP do Apache para o Azure Sentinel, para que você possa exibir os dados em pastas de trabalho, consultá-los para criar alertas personalizados e incorporá-los para melhorar a investigação. A integração entre o Apache HTTP Server e o Azure Sentinel usa o processamento de arquivo local pelo agente de Log Analytics.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter permissão de gravação no espaço de trabalho do Azure Sentinel.

## <a name="configure-and-integrate-apache-http-server-logs-via-log-analytics-agent"></a>Configurar e integrar logs do Apache HTTP Server via agente de Log Analytics

Configure o Apache HTTP Server para enviar arquivos de log para seu espaço de trabalho do Azure por meio do agente de Log Analytics.
Configurar o agente de Log Analytics para ler arquivos de log do Apache HTTP Server.

1. Siga as instruções em https://httpd.apache.org/docs/2.4/logs.html para configurar o local dos arquivos de log no Apache HTTP Server.

1. No menu de navegação do Azure Sentinel, selecione **conectores de dados** e, em seguida, selecione **Apache HTTP Server (visualização)**.

1. Selecione a **página abrir conector**.

1. Siga as instruções na página do servidor HTTP do Apache.

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados são exibidos em Log Analytics em ApacheHTTPServer_CL.

## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar o Apache HTTP Server ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade de seus dados e ameaças em potencial](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.
