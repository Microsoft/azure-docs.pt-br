---
title: Extensões e recursos da máquina virtual do Azure
description: Saiba mais sobre extensões de VM do Azure
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.date: 08/03/2020
ms.openlocfilehash: e1b96293db0389201fdab3340d8f0e74fefc4c52
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559708"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Extensões e recursos da máquina virtual do Azure
Extensões são pequenos aplicativos que fornecem configuração pós-implantação e automação em VMs do Azure. A plataforma Azure hospeda muitas extensões que abrangem aplicativos de configuração, monitoramento, segurança e utilitário da VM. Os editores usam um aplicativo, o encapsulam em uma extensão e simplificam a instalação. Tudo o que você precisa fazer é fornecer parâmetros obrigatórios. 

## <a name="how-can-i-find-what-extensions-are-available"></a>Como posso encontrar quais extensões estão disponíveis?
Você pode exibir as extensões disponíveis selecionando uma VM, selecionando **extensões** no menu à esquerda. Para efetuar pull de uma lista completa de extensões, consulte [descobrindo extensões de VM para Linux](features-linux.md) e [descobrindo extensões de VM para Windows](features-windows.md).

## <a name="how-can-i-install-an-extension"></a>Como instalar uma extensão?
As extensões de VM do Azure podem ser gerenciadas usando o CLI do Azure, o PowerShell, os modelos do Resource Manager e o portal do Azure. Para tentar uma extensão, vá para a portal do Azure, selecione a extensão de script personalizado e, em seguida, passe um comando ou script para executar a extensão.

Para obter mais informações, consulte [extensão de script personalizado do Windows](custom-script-windows.md) e extensão de [script personalizado do Linux](custom-script-linux.md).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Como gerenciar o ciclo de vida do aplicativo de extensão?
Você não precisa se conectar a uma VM diretamente para instalar ou excluir uma extensão. O ciclo de vida da extensão do Azure é gerenciado fora da VM e integrado à plataforma Azure.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Qualquer outra coisa que devo estar pensando sobre extensões?
Alguns aplicativos de extensão VM individuais podem ter seus próprios pré-requisitos de ambiente, como acesso a um ponto de extremidade. Cada extensão tem um artigo que explica os pré-requisitos, incluindo quais sistemas operacionais têm suporte.

## <a name="troubleshoot-extensions"></a>Solucionar problemas de extensões

As informações de solução de problemas para cada extensão podem ser encontradas na seção **solução de problemas e suporte** na visão geral da extensão. Aqui está uma lista das informações de solução de problemas disponíveis:

| Namespace | Solução de problemas |
|-----------|-----------------|
| Microsoft. Azure. Monitoring. dependencyagent. dependencyagentlinux | [Dependência de Azure Monitor para Linux](agent-dependency-linux.md#troubleshoot-and-support) |
| Microsoft. Azure. Monitoring. dependencyagent. dependencyagentwindows | [Dependência Azure Monitor para Windows](agent-dependency-windows.md#troubleshoot-and-support) |
| Microsoft. Azure. Security. azurediskencryptionforlinux | [Azure Disk Encryption para Linux](azure-disk-enc-linux.md#troubleshoot-and-support) |
| Microsoft. Azure. Security. azurediskencryption | [Azure Disk Encryption para o Windows](azure-disk-enc-windows.md#troubleshoot-and-support) |
| Microsoft. COMPUTE. customscriptextension | [Script personalizado para Windows](custom-script-windows.md#troubleshoot-and-support) |
| Microsoft. ostcextensions. às customscriptforlinux | [Configuração de estado desejado para Linux](dsc-linux.md#troubleshoot-and-support) |
| Microsoft. PowerShell. DSC | [Configuração de estado desejado para o Windows](dsc-windows.md#troubleshoot-and-support) |
| Microsoft. hpccompute. nvidiagpudriverlinux | [Extensão de Driver NVIDIA GPU para Linux](hpccompute-gpu-linux.md#troubleshoot-and-support) |
| Microsoft. hpccompute. nvidiagpudriverwindows | [Extensão de Driver NVIDIA GPU para Windows](hpccompute-gpu-windows.md#troubleshoot-and-support) |
| Microsoft. Azure. Security. iaasantimalware da | [Extensão antimalware para Windows](iaas-antimalware-windows.md#troubleshoot-and-support) |
| Microsoft. enterprisecloud. Monitoring. omsagentforlinux | [Azure Monitor para Linux](oms-linux.md#troubleshoot-and-support)
| Microsoft. enterprisecloud. Monitoring. extensão microsoftmonitoringagent | [Azure Monitor para Windows](oms-windows.md#troubleshoot-and-support) |
| stackify. linuxagent. Extension. stackifylinuxagentextension | [Stackify retrace para Linux](stackify-retrace-linux.md#troubleshoot-and-support) |
| vmaccessforlinux. Microsoft. ostcextensions | [Redefinir senha para Linux](vmaccess.md#troubleshoot-and-support) |
| Microsoft. recoveryservices. vmsnapshot | [Instantâneo para Linux](vmsnapshot-linux.md#troubleshoot-and-support) |
| Microsoft. recoveryservices. vmsnapshot | [Instantâneo para Windows](vmsnapshot-windows.md#troubleshoot-and-support) |


## <a name="next-steps"></a>Próximas etapas
* Para obter mais informações sobre como o agente e as extensões do Linux funcionam, consulte [recursos e extensões de VM do Azure para Linux](features-linux.md).
* Para obter mais informações sobre como o agente convidado do Windows e as extensões funcionam, consulte [extensões e recursos de VM do Azure para Windows](features-windows.md).  
* Para instalar o agente convidado do Windows, consulte [visão geral do agente de máquina virtual do Windows do Azure](agent-windows.md).  
* Para instalar o agente do Linux, consulte [visão geral do agente de máquina virtual Linux do Azure](agent-linux.md).  

