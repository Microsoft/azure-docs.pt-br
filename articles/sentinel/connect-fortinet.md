---
title: Conectar a Fortinet data ao Azure Sentinel | Microsoft Docs
description: Conecte seu dispositivo Fortinet ao Azure Sentinel para exibir painéis, criar alertas personalizados e melhorar a investigação.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 60be22f439547d006f54e489833b63171e617e3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92913986"
---
# <a name="connect-fortinet-to-azure-sentinel"></a>Conectar o Fortinet ao Azure Sentinel



Este artigo explica como conectar seu dispositivo Fortinet ao Azure Sentinel. O Fortinet data Connector permite que você conecte facilmente seus logs da Fortinet com o Azure Sentinel, exiba painéis, crie alertas personalizados e melhore a investigação. O uso do Fortinet no Azure Sentinel fornecerá mais informações sobre o uso da Internet da sua organização e aprimorará seus recursos de operação de segurança. 


 
## <a name="forward-fortinet-logs-to-the-syslog-agent"></a>Encaminhe os logs da Fortinet para o agente de syslog

Configure a Fortinet para encaminhar mensagens de syslog no formato CEF para seu espaço de trabalho do Azure por meio do agente de syslog.

1. Abra a CLI no seu dispositivo Fortinet e execute os seguintes comandos:

    ```console
    config log syslogd setting
    set status enable
    set format cef
    set port 514
    set server <ip_address_of_Receiver>
    end
    ```

    - Substitua o **endereço IP** do servidor pelo endereço IP do agente.
    - Defina a **porta do syslog** como **514** ou a porta definida no agente.
    - Para habilitar o formato CEF nas versões iniciais do FortiOS, talvez seja necessário executar o comando Set **CSV Disable**.
 
   > [!NOTE] 
   > Para obter mais informações, vá para a [biblioteca de documentos do Fortinet](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary). Selecione sua versão e use o **manual** e a **referência de mensagem de log**.

1. Para usar o esquema relevante no Azure Monitor Log Analytics para os eventos da Fortinet, procure `CommonSecurityLog` .

1. Prossiga para a [ETAPA 3: Validar a conectividade](connect-cef-verify.md).


## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a conectar o Fortinet appliances ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.


