---
title: Integrar o servidor habilitado para Arc do Azure ao Azure Sentinel
description: Saiba como adicionar seus servidores habilitados para Arc do Azure ao Azure Sentinel e monitorar de forma proativa seu status de segurança.
ms.date: 11/16/2020
ms.topic: conceptual
ms.openlocfilehash: 60018e710613a27bcb5c00f38ee90be6ff46c61d
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94810946"
---
# <a name="onboard-azure-arc-enabled-servers-to-azure-sentinel"></a>Integrar servidores habilitados para Arc do Azure ao Azure Sentinel

Este artigo destina-se a ajudá-lo a integrar seu servidor habilitado para o Azure Arc ao [Azure Sentinel](../../sentinel/overview.md) e a começar a coletar eventos relacionados à segurança. O Azure Sentinel fornece uma solução única para detecção de alertas, visibilidade de ameaças, busca proativa e resposta de ameaças em toda a empresa.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você atendeu aos seguintes requisitos:

- Um [workspace do Log Analytics](../../azure-monitor/platform/data-platform-logs.md). Para saber mais sobre workspaces do Log Analytics, confira [Criar sua implantação de logs do Azure Monitor](../../azure-monitor/platform/design-logs-deployment.md).

- Sentinela do Azure [habilitado em sua assinatura](../../sentinel/quickstart-onboard.md).

- Você é o computador ou o servidor está conectado aos servidores habilitados para Arc do Azure.

## <a name="onboard-azure-arc-enabled-servers-to-azure-sentinel"></a>Integrar servidores habilitados para Arc do Azure ao Azure Sentinel

O Azure Sentinel vem com vários conectores para soluções da Microsoft, disponíveis prontos para uso e fornecendo integração em tempo real. Para computadores e máquinas virtuais, você pode instalar o agente do Log Analytics que coleta os logs e os encaminha para o Azure Sentinel. Os servidores habilitados para Arc oferecem suporte à implantação do agente de Log Analytics usando os seguintes métodos:

- Usando a estrutura de extensões de VM.

    Esse recurso nos servidores habilitados para Arc do Azure permite que você implante a extensão de VM do agente de Log Analytics em um servidor Windows e/ou Linux que não seja do Azure. As extensões de VM podem ser gerenciadas usando os seguintes métodos em seus computadores híbridos ou servidores gerenciados por servidores habilitados para Arc:

    - O [Portal do Azure](manage-vm-extensions-portal.md)
    - A [CLI do Azure](manage-vm-extensions-cli.md)
    - [PowerShell do Azure](manage-vm-extensions-powershell.md)
    - Modelos do Azure [Resource Manager](manage-vm-extensions-template.md)

- Usando Azure Policy.

    Usando essa abordagem, você usa o Azure Policy [implantar o agente log Analytics em](../../governance/policy/samples/built-in-policies.md#monitoring) uma política interna do Windows Azure ARC para máquinas de arco para a auditoria se o servidor habilitado para Arc tiver o agente de log Analytics instalado. Se o agente não estiver instalado, ele o implantará automaticamente usando uma tarefa de correção. Como alternativa, se você planeja monitorar as máquinas com Azure Monitor para VMs, use a iniciativa [habilitar Azure monitor para VMs](../../governance/policy/samples/built-in-initiatives.md#monitoring) para instalar e configurar o agente de log Analytics.

É recomendável instalar o agente de Log Analytics para Windows ou Linux usando Azure Policy.

Depois que os servidores habilitados para Arc estiverem conectados, seus dados começarão a ser transmitidos para o Azure Sentinel e estarão prontos para começar a trabalhar com o. Você pode ver os logs nas [pastas de trabalho internas](../../sentinel/quickstart-get-visibility.md) e começar a criar consultas no Log Analytics para [investigar os dados](../../sentinel/tutorial-investigate-cases.md).

## <a name="next-steps"></a>Próximas etapas

Comece a [detectar ameaças com o Azure Sentinel](../../sentinel/tutorial-detect-threats-built-in.md).