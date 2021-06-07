---
title: Conectar dados VIP do Symantec ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados de VIP da Symantec ao Azure Sentinel.
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
ms.openlocfilehash: c7429108f70d735cb6e314a0d4daa27ba0d31637
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100090404"
---
# <a name="connect-your-symantec-vip-to-azure-sentinel"></a>Conectar seu Symantec VIP ao Azure Sentinel

> [!IMPORTANT]
> O conector de dados VIP da Symantec no Azure Sentinel está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo explica como conectar seu dispositivo de [VIP da Symantec](https://vip.symantec.com/) ao Azure Sentinel. O conector de dados de VIP da Symantec permite que você conecte facilmente seus logs de VIP da Symantec com o Azure Sentinel, exiba painéis, crie alertas personalizados e melhore a investigação. A integração entre o Symantec VIP e o Azure Sentinel usa o syslog.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="forward-symantec-vip-logs-to-the-syslog-agent"></a>Encaminhe os logs VIP do Symantec para o agente de syslog  

Configure o VIP da Symantec para encaminhar mensagens de syslog para seu espaço de trabalho do Azure por meio do agente de syslog.

1. No portal do Azure Sentinel, clique em **conectores de dados** e selecione conector de **VIP da Symantec** .

1. Clique em **Abrir página do conector**.

1. Siga as instruções na página de **VIP da Symantec** .

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem em Log Analytics em syslog.

## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics. 

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar os logs de VIP da Symantec ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.