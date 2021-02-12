---
title: Conectar dados do F5 ASM ao Azure Sentinel | Microsoft Docs
description: Saiba como usar o conector de dados do ASM do F5 para efetuar o pull dos logs do ASM em F5 no Azure Sentinel. Exiba dados do ASM em F5 em pastas de trabalho, crie alertas e melhore a investigação.
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
ms.date: 05/20/2020
ms.author: yelevin
ms.openlocfilehash: 81a0f0e01827c48518f2eb1fba062e9e6536d9df
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655690"
---
# <a name="connect-f5-asm-to-azure-sentinel"></a>Conectar o F5 ASM ao Azure Sentinel

Este artigo explica como usar o conector de dados do F5 ASM para extrair facilmente seus logs do F5 ASM para o Azure Sentinel. Isso permite que você exiba dados do F5 ASM em pastas de trabalho, use-os para criar alertas personalizados e incorpore-os para melhorar a investigação. Ter os dados do F5 ASM no Azure Sentinel fornecerá mais informações sobre a segurança de aplicativos Web de sua organização e aprimorará seus recursos de operações de segurança. 

## <a name="configure-your-f5-asm-to-send-cef-messages"></a>Configurar o F5 ASM para enviar mensagens em Formato Comum de Evento

1. Siga as instruções em [F5 Configurar o log de eventos de segurança do aplicativo](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html) para configurar o log remoto, usando as seguintes diretrizes:
   - Defina o **tipo de armazenamento remoto** como **CEF**.
   - Defina o **Protocolo** como **TCP**.
   - Defina o **endereço IP** como o endereço IP do servidor Syslog.
   - Defina o **número da porta** como **514** ou a porta que você definiu para uso pelo agente.
   - Você pode definir o **tamanho máximo da cadeia de caracteres de consulta** com o tamanho definido no agente.

1. Para usar o esquema relevante no Log Analytics para eventos em Formato Comum de Evento, procure `CommonSecurityLog`.

1. Prossiga para a [ETAPA 3: Validar a conectividade](connect-cef-verify.md).


## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar o F5 ASM ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](./tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.