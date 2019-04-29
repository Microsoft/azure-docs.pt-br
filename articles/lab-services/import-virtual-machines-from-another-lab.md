---
title: Importar máquinas virtuais do laboratório outro no Azure DevTest Labs | Microsoft Docs
description: Saiba como importar as máquinas virtuais do laboratório de outro para o laboratório atual.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: ca6ed58cfabb5027830828812c4820c1b586875c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61322801"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>Importar máquinas virtuais do laboratório outro no Azure DevTest Labs
Este artigo fornece informações sobre como importar as máquinas virtuais do laboratório de outro para seu laboratório.

## <a name="scenarios"></a>Cenários
Aqui estão alguns cenários em que você precisa importar máquinas virtuais de um laboratório para outro laboratório:

- Uma pessoa da equipe está migrando para outro grupo dentro da empresa e deseja levar a área de trabalho do desenvolvedor para DevTest Labs a nova equipe.
- O grupo de atingir uma [cota de nível de assinatura](../azure-subscription-service-limits.md) e deseja dividir as equipes em algumas assinaturas
- A empresa esteja migrando para a rota expressa (ou alguns outra nova topologia de rede) e a equipe quer mover as máquinas virtuais para usar essa nova infra-estrutura

## <a name="solution-and-constraints"></a>Solução e restrições
Esse recurso permite que você importe as VMs em um laboratório (origem) para outro laboratório (destino). Opcionalmente, você pode dar um novo nome para a VM de destino no processo. O processo de importação inclui todas as dependências, como discos, agendas, as configurações de rede e assim por diante.

O processo levar algum tempo e é afetado pelos seguintes fatores:

- Número/tamanho dos discos anexados à máquina de origem (já que é uma operação de cópia e não uma operação de movimentação)
- Distância para o destino (por exemplo, região Leste dos EUA para o sudeste asiático).

Depois que o processo for concluído, a máquina Virtual de origem permanece desligamento e a nova um está em execução no laboratório de destino.

Há duas restrições de chave a serem consideradas ao planejar importar VMs de um laboratório para outro laboratório:

- Importações de máquina virtual entre assinaturas e entre regiões têm suporte, mas as assinaturas devem ser associadas ao mesmo locatário do Active Directory do Azure.
- As máquinas virtuais não deve estar em um estado declarável no laboratório de origem.
- Você é o proprietário da VM do laboratório de origem e o proprietário de laboratório no laboratório de destino.
- Atualmente, esse recurso tem suporte apenas por meio do Powershell e a API REST.

## <a name="use-powershell"></a>Usar o PowerShell
Baixar arquivo ImportVirtualMachines.ps1 do [GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines). Você pode usar o script para importar uma única VM ou todas as VMs no laboratório de origem para o laboratório de destino.

### <a name="use-powershell-to-import-a-single-vm"></a>Usar o PowerShell para importar uma única VM
Executar este script do powershell requer a identificação a VM de origem e o laboratório de destino e, opcionalmente, fornecendo um novo nome a ser usado para a máquina de destino:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>Usar o PowerShell para importar todas as VMs no laboratório de origem
Se a máquina Virtual de origem não for especificado, o script importa automaticamente todas as VMs em DevTest Labs.  Por exemplo: 

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>Usar HTTP REST para importar uma máquina virtual
A chamada REST é simple. Você dar informações suficientes para identificar os recursos de origem e de destino. Lembre-se de que a operação é executada no recurso de laboratório de destino.

```REST
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>Próximas etapas
Confira os seguintes artigos:

- [Definir políticas para um laboratório](devtest-lab-get-started-with-lab-policies.md)
- [Perguntas frequentes](devtest-lab-faq.md)
