---
title: Conectar dados do NXLog LinuxAudit ao Azure Sentinel | Microsoft Docs
description: Saiba como usar o conector de dados do NXLog LinuxAudit para efetuar pull de logs do LinuxAudit no Azure Sentinel. Exiba dados do LinuxAudit em pastas de trabalho, crie alertas e melhore a investigação.
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
ms.openlocfilehash: 2010b21a3cdb81f2b2aa4180f87857862cd02bf5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101700781"
---
# <a name="connect-your-nxlog-linuxaudit-to-azure-sentinel"></a>Conectar seu NXLog LinuxAudit ao Azure Sentinel

> [!IMPORTANT]
> O conector NXLog LinuxAudit está atualmente em **Visualização**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

O conector [NXLog LinuxAudit](https://nxlog.co/documentation/nxlog-user-guide/im_linuxaudit.html) permite que você exporte facilmente os eventos de segurança do Linux para o Azure Sentinel em tempo real, fornecendo informações sobre a atividade do servidor de nome de domínio em toda a sua organização. O módulo NXLog LinuxAudit dá suporte a regras de auditoria personalizadas e coleta logs sem auditoria ou qualquer outro software de espaço de usuário. Os endereços IP e as IDs de grupo/usuário são resolvidos para seus respectivos nomes, tornando os logs de [auditoria do Linux](https://nxlog.co/documentation/nxlog-user-guide/linux-audit.html) mais compreensíveis para os analistas de segurança. A integração entre o NXLog LinuxAudit e o Azure Sentinel é facilitada por meio da API REST.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="configure-and-connect-nxlog-linuxaudit"></a>Configurar e conectar NXLog LinuxAudit

O NXLog pode ser configurado para enviar eventos no formato JSON diretamente para o Azure Sentinel.

1. No portal do Azure Sentinel, clique em **conectores de dados** e selecione conector do **NXLog LinuxAudit** .

1. Clique em **Abrir página do conector**.

1. Siga as instruções detalhadas no tópico integração do guia do *usuário do NXLog* [Microsoft Azure sentinela](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) para configurar o encaminhamento por meio da API REST.

## <a name="find-your-data"></a>Encontre seus dados

Depois que uma conexão bem-sucedida é estabelecida, os dados aparecem nos **logs** na seção  **logs personalizados** , na tabela *LinuxAudit_CL* .

## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a usar o NXLog LinuxAudit para ingerir eventos de segurança do Linux no Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade de seus dados e ameaças em potencial](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.
