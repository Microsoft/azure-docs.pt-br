---
title: Integridade do convidado de solução de problemas de insights da VM (versão prévia)
description: Descreve as etapas de solução de problemas que você pode tomar quando tiver problemas com a integridade do virtual insights.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/08/2020
ms.openlocfilehash: d8b37569ebaa8e75be601a1efd65a23a61aeaa75
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102051932"
---
# <a name="troubleshoot-vm-insights-guest-health-preview"></a>Integridade do convidado de solução de problemas de insights da VM (versão prévia)
Este artigo descreve as etapas de solução de problemas que você pode tomar quando tiver problemas com a integridade do virtual insights.

## <a name="error-message-that-no-data-is-available"></a>Mensagem de erro informando que não há dados disponíveis 

![Sem dados](media/vminsights-health-troubleshoot/no-data.png)


### <a name="verify-that-the-virtual-machine-meets-configuration-requirements"></a>Verificar se a máquina virtual atende aos requisitos de configuração

1. Verifique se esta é uma máquina virtual do Azure. No momento, não há suporte para o Azure Arc para servidores.
2. Verifique se a máquina virtual está executando um [sistema operacional com suporte](vminsights-health-enable.md?current-limitations.md).
3. Verifique se a máquina virtual foi instalada em uma [região com suporte](vminsights-health-enable.md?current-limitations.md).
4. Verifique se o workspace do Log Analytics foi instalado em uma [região com suporte](vminsights-health-enable.md?current-limitations.md).

### <a name="verify-that-the-vm-is-properly-onboarded"></a>Verifique se a VM está integrada corretamente
Verifique se a extensão do agente de Azure Monitor e o agente de integridade da VM convidada foram provisionados com êxito na máquina virtual. Selecione **extensões** no menu da máquina virtual na portal do Azure e verifique se os dois agentes estão listados.

![Extensões de VM](media/vminsights-health-troubleshoot/extensions.png)

### <a name="verify-the-system-assigned-identity-is-enabled-on-the-virtual-machine"></a>Verifique se a identidade atribuída ao sistema está habilitada na máquina virtual
Verifique se a identidade atribuída ao sistema está habilitada na máquina virtual. Selecione **identidade** no menu da máquina virtual na portal do Azure. Se a identidade gerenciada pelo usuário estiver habilitada, independentemente do status da identidade gerenciada do sistema, Azure Monitor agente não poderá se comunicar com o serviço de configuração e a extensão de integridade do convidado não funcionará.

![Identidade atribuída pelo sistema](media/vminsights-health-troubleshoot/system-identity.png)

### <a name="verify-data-collection-rule"></a>Verificar regra de coleta de dados
Verifique se a regra de coleta de dados especificando a extensão de integridade como uma fonte de dados está associada à máquina virtual.

## <a name="error-message-for-bad-request-due-to-insufficient-permissions"></a>Mensagem de erro para solicitação inadequada devido a permissões insuficientes
Esse erro indica que o provedor de recursos **Microsoft. WorkloadMonitor** não foi registrado na assinatura. Consulte [provedores de recursos do Azure e tipos](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) para obter detalhes sobre como registrar esse provedor de recursos. 

![Solicitação incorreta](media/vminsights-health-troubleshoot/bad-request.png)

## <a name="next-steps"></a>Próximas etapas

- [Obtenha uma visão geral do recurso de integridade de convidado do insights da VM](vminsights-health-overview.md)