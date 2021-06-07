---
title: Conectar dados de logs do DNS do Windows NXLog ao Azure Sentinel | Microsoft Docs
description: Saiba como usar o conector de dados de logs DNS do NXLog para efetuar pull de eventos DNS do Windows no Azure Sentinel. Exiba dados DNS do Windows em pastas de trabalho, crie alertas e melhore a investigação.
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
ms.openlocfilehash: 880aad438d98605d11e5a2a7c314d89bd8beb5c5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101744289"
---
# <a name="connect-your-nxlog-windows-dns-logs-to-azure-sentinel"></a>Conectar seus logs DNS do Windows NXLog ao Azure Sentinel

> [!IMPORTANT]
> O conector de logs DNS do NXLog está atualmente em **Visualização**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

O conector de [logs de DNS do NXLog](https://nxlog.co/documentation/nxlog-user-guide/windows-dns-server.html) permite que você exporte facilmente todos os eventos do servidor DNS do Windows para o Azure Sentinel em tempo real, fornecendo informações sobre a atividade do servidor de nome de domínio em toda a sua organização. Ele emprega o [ETW (rastreamento de eventos de alto desempenho para Windows)](https://nxlog.co/documentation/nxlog-user-guide/windows-dns-server.html#dns_windows_etw) para coletar eventos de servidor DNS analíticos e de auditoria em tempo real e dá suporte ao enriquecimento de eventos com campos personalizados. A integração entre os logs DNS do NXLog e o Azure Sentinel é facilitada por meio da API REST.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="configure-and-connect-nxlog-dns-logs"></a>Configurar e conectar logs DNS do NXLog

O NXLog pode ser configurado para enviar eventos no formato JSON diretamente para o Azure Sentinel.

1. No portal do Azure Sentinel, clique em **conectores de dados** e selecione conector de **logs de DNS do NXLog** .

1. Clique em **Abrir página do conector**.

1. Siga as instruções detalhadas no tópico integração do guia do *usuário do NXLog* [Microsoft Azure sentinela](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) para configurar o encaminhamento por meio da API REST.

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem nos **logs** na seção  **logs personalizados** , na tabela *DNS_Logs_CL* .

## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a usar o NXLog para ingerir os logs de DNS do Windows no Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade de seus dados e ameaças em potencial](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.
