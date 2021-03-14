---
title: Conectar dados do firewall do Sophos XG ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados do Sophos XG firewall ao Azure Sentinel.
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
ms.openlocfilehash: 0b8b6247a735bceaf98029740bf9d4f7e233069d
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100097561"
---
# <a name="connect-your-sophos-xg-firewall-to-azure-sentinel"></a>Conectar seu firewall do Sophos XG ao Azure Sentinel

> [!IMPORTANT]
> O The Sophos XG firewall data Connector no Azure Sentinel está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo explica como conectar seu dispositivo de [Firewall do SOPHOS XG](https://www.sophos.com/products/next-gen-firewall.aspx) ao Azure Sentinel. O The Sophos XG firewall data Connector permite que você conecte facilmente seus logs de firewall do Sophos XG com o Azure Sentinel, exiba painéis, crie alertas personalizados e melhore a investigação. A integração entre o Firewall do Sophos XG e o Azure Sentinel usa o syslog.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="forward-sophos-xg-firewall-logs-to-the-syslog-agent"></a>Encaminhar logs de firewall do Sophos XG para o agente de syslog  

Configure o Firewall do Sophos XG para encaminhar mensagens de syslog para seu espaço de trabalho do Azure por meio do agente de syslog.

1. No portal do Azure Sentinel, clique em **conectores de dados** e selecione **XG de firewall do Sophos** .

1. Selecione a **página abrir conector**.

1. Siga as instruções na página de **Firewall do SOPHOS XG** .

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem em Log Analytics em syslog.

## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar o Sophos XG firewall ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.