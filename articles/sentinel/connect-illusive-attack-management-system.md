---
title: Conectar dados do sistema de gerenciamento de ataques do illusive ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados do sistema de gerenciamento de ataques do illusive ao Azure Sentinel.
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
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: ea7953be25473357f7ed572fa8b76076edc6f75a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87038106"
---
# <a name="connect-your-illusive-attack-management-system-to-azure-sentinel"></a>Conectar seu sistema de gerenciamento de ataques do illusive ao Azure Sentinel

> [!IMPORTANT]
> O conector de dados do sistema de gerenciamento de ataques illusive no Azure Sentinel está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo explica como conectar seu [sistema de gerenciamento de ataques do illusive](https://www.illusivenetworks.com/technology/platform/attack-detection-system) ao Azure Sentinel. O conector de dados do sistema de gerenciamento de ataques illusive permite que você compartilhe os dados de análise da superfície de ataque do illusive e os logs de incidentes com o Azure Sentinel e exiba essas informações em painéis dedicados que oferecem insights sobre o risco de superfície de ataque da sua organização (painel do ADS) e acompanhe a movimentação lateral não autorizada na rede da sua organização (painel de anúncios)

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="forward-illusive-attack-management-system-logs-to-the-syslog-agent"></a>Encaminhar logs do sistema de gerenciamento de ataques de illusive para o agente de syslog  

Configure o sistema de gerenciamento de ataques para encaminhar mensagens de syslog no formato CEF para seu espaço de trabalho do Azure por meio do agente de syslog.

1. Faça logon no console do illusive e navegue até configurações->relatórios.

1. Localizar servidores syslog.

1. Forneça as seguintes informações:
   - Nome do host: endereço IP do agente de syslog do Linux ou nome do host FQDN
   - Porta: 514
   - Protocolo: TCP
   - Mensagens de auditoria: enviar mensagens de auditoria para o servidor

1. Para adicionar o servidor syslog, clique em Adicionar.

1. Para usar o esquema relevante nos **logs** do sistema de gerenciamento de ataques illusive, pesquise **CommonSecurityLog**.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar o sistema de gerenciamento de ataques illusive ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.
