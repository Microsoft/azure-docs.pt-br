---
title: Conectar dados do Citrix WAF ao Azure Sentinel | Microsoft Docs
description: Saiba como usar o conector de dados Citrix WAF para efetuar pull de seus logs para o Azure Sentinel. Exibir dados do Citrix WAF em pastas de trabalho, criar alertas e melhorar a investigação.
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
ms.date: 10/25/2020
ms.author: yelevin
ms.openlocfilehash: 475e04ad3dd8c7cc51d6345b4b51dd68cff5b597
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93102791"
---
# <a name="connect-your-citrix-waf-to-azure-sentinel"></a>Conectar seu Citrix WAF ao Azure Sentinel

> [!IMPORTANT]
> O conector de dados do WAF (firewall do aplicativo Web) da Citrix no Azure Sentinel está atualmente em visualização pública. Esse recurso é fornecido sem um contrato de nível de serviço. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo explica como conectar seu dispositivo Citrix Web Application Firewall (WAF) ao Azure Sentinel. O conector de dados Citrix WAF permite que você conecte facilmente seus logs do Citrix WAF com o Azure Sentinel, exiba painéis, crie alertas personalizados e melhore a investigação. Conectando os logs do Citrix WAF CEF ao Azure Sentinel, você pode tirar proveito da pesquisa e correlação, alertas e enriquecimento de inteligência contra ameaças para cada log.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="forward-citrix-waf-logs-to-the-syslog-agent"></a>Encaminhe os logs do Citrix WAF para o agente de syslog  

O Citrix WAF envia mensagens de syslog no formato CEF para um servidor de encaminhamento de log baseado em Linux (executando rsyslog ou syslog-ng) com o agente de Log Analytics instalado, que encaminha os logs para seu espaço de trabalho do Azure Sentinel.

1. Se você não tiver esse servidor de encaminhamento de log, consulte [estas instruções](connect-cef-agent.md) para colocar um em funcionamento.

1. Siga as instruções fornecidas pela Citrix para [Configurar o WAF](https://support.citrix.com/article/CTX234174), [Configurar o log de CEF](https://support.citrix.com/article/CTX136146)e [Configurar o envio dos logs para o encaminhador de log](https://docs.citrix.com/en-us/citrix-adc/13/system/audit-logging/configuring-audit-logging.html). Certifique-se de enviar os logs para a porta TCP 514 no endereço IP do computador do encaminhador de log.

1. Valide sua conexão e verifique a ingestão de dados usando [estas instruções](connect-cef-verify.md). Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics.

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem nos **logs**, na seção **Sentinela do Azure** , na tabela *CommonSecurityLog* .

Para consultar os logs do Citrix WAF em Log Analytics, digite `CommonSecurityLog` na parte superior da janela de consulta.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar o Citrix WAF ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.