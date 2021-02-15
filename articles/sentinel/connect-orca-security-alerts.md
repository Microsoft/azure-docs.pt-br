---
title: Conectar os alertas de segurança do Orca ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados de alerta de segurança do Orca ao Azure Sentinel, para exibir painéis, criar alertas personalizados e melhorar a investigação.
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
ms.openlocfilehash: 18a50e3d06135f2c17b7e7c5b2969be33f408c62
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100093073"
---
# <a name="connect-your-orca-security-alerts-to-azure-sentinel"></a>Conectar seus alertas de segurança do Orca ao Azure Sentinel 

> [!IMPORTANT]
> O conector de alertas de segurança do Orca no Azure Sentinel está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O Orca Security Alerts Connector permite que você traga facilmente alertas da solução de segurança de [alertas da orca](https://orca.security/) para o Azure Sentinel, para que você possa exibi-los em pastas de trabalho, usá-los para criar alertas personalizados e incorporá-los para melhorar a investigação. A integração entre os alertas de segurança do Orca e o Azure Sentinel usa a API REST.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="configure-and-connect-orca-security-alerts"></a>Configurar e conectar alertas de segurança do Orca

Os alertas de segurança da orca podem integrar e exportar logs diretamente para o Azure Sentinel.

1. No portal do Azure Sentinel, clique em **conectores de dados** e selecione **Orca Security Alerts** e **abra a página do conector**.

2. Consulte https://orcasecurity.zendesk.com/hc/en-us/articles/360043941992-Azure-Sentinel-integration para concluir a integração da plataforma orca.

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem em Log Analytics em **CustomLogs** na tabela **OrcaAlerts_CL** .
Para usar o esquema relevante no Log Analytics para os alertas do Orca, procure `OrcaAlerts_CL` .

## <a name="validate-connectivity"></a>Validar a conectividade
Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics. 


## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar os alertas de segurança do Orca ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.

