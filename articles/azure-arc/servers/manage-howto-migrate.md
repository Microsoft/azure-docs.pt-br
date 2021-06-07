---
title: Como migrar servidores habilitados para Arc do Azure entre regiões
description: Saiba como migrar um servidor habilitado para Arc do Azure de uma região para outra.
ms.date: 02/10/2021
ms.topic: conceptual
ms.openlocfilehash: 251a347205d93af715add52db293d8000438df44
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101650168"
---
# <a name="how-to-migrate-azure-arc-enabled-servers-across-regions"></a>Como migrar servidores habilitados para Arc do Azure entre regiões

Há cenários em que você deseja mover seu servidor habilitado para Arc do Azure existente de uma região para outra. Por exemplo, você percebeu que o computador foi registrado na região errada, para melhorar a capacidade de gerenciamento ou para se mover por motivos de governança.

Para migrar um servidor habilitado para Arc do Azure de uma região do Azure para outra, você precisa desinstalar as extensões de VM, excluir o recurso no Azure e recriá-lo na outra região. Antes de executar essas etapas, você deve auditar o computador para verificar quais extensões de VM estão instaladas.

> [!NOTE]
> Embora as extensões instaladas continuem a ser executadas e executem sua operação normal após a conclusão desse procedimento, você não poderá gerenciá-las. Se você tentar reimplantar as extensões no computador, poderá ocorrer um comportamento imprevisível.

## <a name="move-machine-to-other-region"></a>Mover computador para outra região

> [!NOTE]
> Durante essa operação, isso resultará em tempo de inatividade durante a migração.

1. Remova as extensões de VM instaladas do [portal do Azure](manage-vm-extensions-portal.md#uninstall-extension), usando o [CLI do Azure](manage-vm-extensions-cli.md#remove-an-installed-extension)ou usando [Azure PowerShell](manage-vm-extensions-powershell.md#remove-an-installed-extension).

2. Use a ferramenta **azcmagent** com o parâmetro [Disconnect](manage-agent.md#disconnect) para desconectar o computador do arco do Azure e excluir o recurso de máquina do Azure. Desconectar o computador de servidores habilitados para ARC não remove o agente de computador conectado e você não precisa remover o agente como parte desse processo. Você pode executar isso manualmente enquanto estiver conectado interativamente, ou automatizar usando a mesma entidade de serviço usada para carregar vários agentes ou com um [token de acesso](../../active-directory/develop/access-tokens.md)da plataforma de identidade da Microsoft. Se você não usou uma entidade de serviço para registrar a máquina com os servidores habilitados para Arc do Azure, consulte o [artigo](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) a seguir para criar uma entidade de serviço.

3. Registre novamente o agente do computador conectado com servidores habilitados para Arc na outra região. Execute a `azcmagent` ferramenta com o parâmetro [Connect](manage-agent.md#connect) para concluir esta etapa.

4. Reimplante as extensões de VM que foram originalmente implantadas no computador de servidores habilitados para Arc. Se você tiver implantado o agente do Azure Monitor para VMs (insights) ou o agente do Log Analytics usando uma política do Azure, os agentes serão reimplantados após o próximo [ciclo de avaliação](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

## <a name="next-steps"></a>Próximas etapas

* Informações de solução de problemas podem ser encontradas no [guia solucionar problemas do agente do computador conectado](troubleshoot-agent-onboard.md).

* Saiba como gerenciar seu computador usando [Azure Policy](../../governance/policy/overview.md), para tarefas como [configuração de convidado](../../governance/policy/concepts/guest-configuration.md)de VM, verificar se o computador está relatando para o espaço de trabalho esperado log Analytics, habilitar o monitoramento com a política de [Azure monitor com VMs](../../azure-monitor/vm/vminsights-enable-policy.md) e muito mais.