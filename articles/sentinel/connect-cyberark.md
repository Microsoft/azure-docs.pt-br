---
title: Conectar dados do CyberArk EPV ao Azure Sentinel | Microsoft Docs
description: Saiba como usar o conector de dados do CyberArk Enterprise password Vault (EPV) para efetuar pull de seus logs para o Azure Sentinel. Exiba dados do CyberArk EPV em pastas de trabalho, crie alertas e melhore a investigação.
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
ms.openlocfilehash: aa57963ce369e4c8f84f4aae5f99fe343181ff6b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100530494"
---
# <a name="connect-cyberark-enterprise-password-vault-epv-to-azure-sentinel"></a>Conectar o CyberArk Enterprise password Vault (EPV) ao Azure Sentinel

> [!IMPORTANT]
> O conector de dados do CyberArk Enterprise password Vault (EPV) no Azure Sentinel está atualmente em visualização pública. Esse recurso é fornecido sem um contrato de nível de serviço. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O conector de syslog CyberArk permite que você conecte facilmente todos os seus logs de solução de segurança do CyberArk com sua Sentinela do Azure, exiba painéis, crie alertas personalizados e melhore a investigação. A integração entre o CyberArk e o Azure sentinela usa o conector de dados CEF para analisar e exibir corretamente as mensagens do CyberArk syslog.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="configure-and-connect-cyberark-epv"></a>Configurar e conectar CyberArk EPV

Os logs do CyberArk EPV são enviados do cofre para um servidor de encaminhamento de log baseado em Linux (executando rsyslog ou syslog-ng) com o agente de Log Analytics instalado, que exporta os logs para o Azure Sentinel. Se você não tiver esse servidor de encaminhamento de log, consulte [estas instruções](connect-cef-agent.md) para colocar um em funcionamento.

1. No portal do Azure Sentinel, clique em **conectores de dados**, selecione **eventos do EPV (CyberArk Enterprise password Vault) (versão prévia)** e **abra a página conector**.

1. Siga as [instruções do CYBERARK EPV](https://docs.cyberark.com/Product-Doc/OnlineHelp/PAS/Latest/en/Content/PASIMP/DV-Integrating-with-SIEM-Applications.htm) para configurar o envio de dados syslog para o servidor de encaminhamento de log.

1. Valide sua conexão e verifique a ingestão de dados usando [estas instruções](connect-cef-verify.md). Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics.

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem nos **logs**, na seção **Sentinela do Azure** , na tabela *CommonSecurityLog* .

Para consultar os logs do CyberArk EPV no Log Analytics, digite `CommonSecurityLog` na parte superior da janela de consulta.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar os logs do cofre de senha do CyberArk Enterprise ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.
