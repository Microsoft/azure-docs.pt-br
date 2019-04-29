---
title: Habilitar a fusão na visualização do Azure Sentinel | Microsoft Docs
description: Saiba como habilitar a fusão no Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 82becf50-6628-47e4-b3d7-18d7d72d505f
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 55d569d4a993a725137d7bfab37c113147fe81ef
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60714904"
---
# <a name="enable-fusion"></a>Habilitar fusion

> [!IMPORTANT]
> No momento, o Azure Sentinel está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Aprendizado de máquina no Azure Sentinel é interno desde o início. Cuidadosamente, criamos o sistema com inovações de ML para tornar os analistas de segurança, os cientistas de dados de segurança e engenheiros produtivos. Um tal inovação é que Azure Sentinel Fusion criado especialmente para reduzir fadiga alerta.

Fusão usa o grafo com algoritmos de machine learning para correlacionar entre milhões de atividades anormais de menor fidelidade de diferentes produtos como o Azure AD Identity Protection e o Microsoft Cloud App Security, combiná-los em um número gerenciável de casos de segurança interessantes.

## <a name="enable-fusion"></a>Habilitar fusion

1. No portal do Azure, selecione o ícone para abrir o Cloud Shell.
  ![Cloud Shell](./media/connect-fusion/cloud-shell.png)

2.  No **bem-vindo ao Cloud Shell** windows que é aberta abaixo, selecione o PowerShell.

3.  Escolha a assinatura na qual você implantou Sentinel do Azure, e **criar um armazenamento**.

4. Depois que você está autenticado e sua unidade do Azure baseia-se, no prompt de comando, execute os seguintes comandos:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=true --subscription "{Subscription Guid}"

## <a name="disable-fusion"></a>Desabilitar fusion

Siga o mesmo procedimento acima e execute o seguinte comando:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=false --subscription "{Subscription Guid}"

## <a name="view-the-status-of-fusion"></a>Exibir o status de fusão

            az resource show --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --subscription "{Subscription Guid}"


## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a habilitar a fusão no Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [Obtenha visibilidade sobre seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Introdução ao [detecção de ameaças com o Azure Sentinel](tutorial-detect-threats.md).

