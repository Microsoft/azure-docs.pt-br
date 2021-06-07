---
title: Conectar dados do Zscaler ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados do Zscaler ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: d788219e36ac94b1d13b2f4819c3e546622ddff1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94655248"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>Conectar o acesso à Internet do Zscaler ao Azure Sentinel

Este artigo explica como conectar seu dispositivo de acesso à Internet do Zscaler ao Azure Sentinel. O conector de dados do Zscaler permite que você conecte facilmente seus logs do ZIA (Zscaler Internet Access) com o Azure Sentinel, exiba painéis, crie alertas personalizados e melhore a investigação. Usar o Zscaler no Azure Sentinel fornecerá mais informações sobre o uso da Internet da sua organização e aprimorará seus recursos de operação de segurança. 


## <a name="configure-your-zscaler-to-send-cef-messages"></a>Configurar seu Zscaler para enviar mensagens CEF

1. No dispositivo Zscaler, você precisa definir esses valores para que o dispositivo envie os logs necessários no formato necessário para o agente de syslog do Azure Sentinel, com base no agente de Log Analytics. Você pode modificar esses parâmetros em seu dispositivo, contanto que também os modifique no daemon do syslog no agente do Azure Sentinel.
    - Protocolo = TCP
    - Porta = 514
    - Formato = CEF
    - Endereço IP-certifique-se de enviar as mensagens CEF para o endereço IP da máquina virtual que você dedicaram para essa finalidade.
 Para obter mais informações, consulte o [Guia de implantação do Zscaler e do Azure Sentinel](https://aka.ms/ZscalerCEFInstructions).
 
   > [!NOTE]
   > Esta solução oferece suporte a RFC 3164 ou a RFC 5424 do syslog.


1. Para usar o esquema relevante em Log Analytics para os eventos CEF, procure `CommonSecurityLog` .
1. Prossiga para a [ETAPA 3: Validar a conectividade](connect-cef-verify.md).


## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar o acesso à Internet do Zscaler ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](./tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.