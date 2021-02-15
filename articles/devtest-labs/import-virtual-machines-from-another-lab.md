---
title: Importar máquinas virtuais de outro laboratório no Azure DevTest Labs
description: Este artigo descreve como importar máquinas virtuais de outro laboratório para o laboratório atual no Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0f664a0ae399575ee936565adaf7364fd1c5ce5c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85475927"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>Importar máquinas virtuais de outro laboratório no Azure DevTest Labs
Este artigo fornece informações sobre como importar máquinas virtuais de outro laboratório para seu laboratório.

## <a name="scenarios"></a>Cenários
Aqui estão alguns cenários em que você precisa importar VMs de um laboratório para outro laboratório:

- Um indivíduo na equipe está mudando para outro grupo dentro da empresa e quer levar a área de trabalho do desenvolvedor para os laboratórios de DevTest da nova equipe.
- O grupo atingiu uma [cota de nível de assinatura](../azure-resource-manager/management/azure-subscription-service-limits.md) e deseja dividir as equipes em algumas assinaturas
- A empresa está mudando para a rota expressa (ou alguma outra nova topologia de rede) e a equipe deseja mover as máquinas virtuais para usar essa nova infraestrutura

## <a name="solution-and-constraints"></a>Solução e restrições
Esse recurso permite que você importe VMs em um laboratório (origem) em outro laboratório (destino). Opcionalmente, você pode fornecer um novo nome para a VM de destino no processo. O processo de importação inclui todas as dependências, como discos, agendas, configurações de rede e assim por diante.

O processo leva algum tempo e é afetado pelos seguintes fatores:

- Número/tamanho dos discos que são anexados à máquina de origem (já que é uma operação de cópia e não uma operação de movimentação)
- Distância para o destino (por exemplo, região leste dos EUA para Sudeste Asiático).

Quando o processo for concluído, a máquina virtual de origem permanecerá desligada e a nova será executada no laboratório de destino.

Há duas restrições principais que devem ser observadas ao planejar a importação de VMs de um laboratório para outro laboratório:

- As importações de máquina virtual entre assinaturas e regiões têm suporte, mas as assinaturas devem ser associadas ao mesmo locatário Azure Active Directory.
- As máquinas virtuais não devem estar em um estado de declaração no laboratório de origem.
- Você é o proprietário da VM no laboratório de origem e o proprietário do laboratório no laboratório de destino.
- Atualmente, esse recurso tem suporte apenas por meio do PowerShell e da API REST.

## <a name="use-powershell"></a>Usar o PowerShell
Baixe ImportVirtualMachines.ps1 arquivo do [GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines). Você pode usar o script para importar uma única VM ou todas as VMs no laboratório de origem para o laboratório de destino.

### <a name="use-powershell-to-import-a-single-vm"></a>Usar o PowerShell para importar uma única VM
A execução deste script do PowerShell requer a identificação da VM de origem e do laboratório de destino e, opcionalmente, fornecendo um novo nome a ser usado para o computador de destino:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>Usar o PowerShell para importar todas as VMs no laboratório de origem
Se a máquina virtual de origem não for especificada, o script importará automaticamente todas as VMs no DevTest Labs.  Por exemplo:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>Usar HTTP REST para importar uma VM
A chamada REST é simples. Você fornece informações suficientes para identificar os recursos de origem e de destino. Lembre-se de que a operação ocorre no recurso de laboratório de destino.

```REST
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Definir políticas para um laboratório](devtest-lab-set-lab-policy.md)
- [Perguntas frequentes](devtest-lab-faq.md)
