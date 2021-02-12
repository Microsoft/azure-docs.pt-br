---
title: Solucionando problemas de falhas de extensão de VM do Windows
description: Saiba mais sobre como solucionar falhas da extensão de VM do Windows no Azure
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: gwallace
editor: ''
tags: top-support-issue,azure-resource-manager
ms.assetid: 878ab9b6-c3e6-40be-82d4-d77fecd5030f
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
ms.openlocfilehash: 343ddb109de41a0959533b16b11762841b5b1105
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98676752"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Solucionando problemas de falhas da extensão da VM do Windows no Azure
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Exibindo o status da extensão
Os modelos do Azure Resource Manager podem ser executados no Azure PowerShell. Depois que o modelo for executado, o status da extensão poderá ser exibido no Gerenciador de Recursos do Azure ou nas ferramentas de linha de comando.

Veja um exemplo:

Azure PowerShell:

```azurepowershell
Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status
```

Veja o exemplo de saída:

```output
Extensions:  {
  "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
  "Name": "myCustomScriptExtension",
  "SubStatuses": [
    {
      "Code": "ComponentStatus/StdOut/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
          \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
          test.txt                          \\n\\n",
                  "Time": null
      },
    {
      "Code": "ComponentStatus/StdErr/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "",
      "Time": null
    }
  ]
}
```

## <a name="troubleshooting-extension-failures"></a>Solução de problemas de falhas da extensão

### <a name="rerun-the-extension-on-the-vm"></a>Executar novamente a extensão na VM
Se estiver executando scripts na VM usando a Extensão de Script Personalizado, às vezes, você poderá se deparar com um erro em que a VM foi criada com êxito, mas o script falhou. Nessas condições, a maneira recomendada de se recuperar desse erro é remover a extensão e executar o modelo novamente.
Observação: no futuro, essa funcionalidade será aprimorada para eliminar a necessidade de desinstalar a extensão.

#### <a name="remove-the-extension-from-azure-powershell"></a>Remover a extensão do Azure PowerShell
```azurepowershell
Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"
```

Depois que a extensão tiver sido removida, o modelo poderá ser executado novamente para executar os scripts na VM.

### <a name="trigger-a-new-goalstate-to-the-vm"></a>Disparar uma nova metastate para a VM
Você pode observar que uma extensão não foi executada ou está falhando na execução por causa de um "gerador de certificado do Microsoft Azure CRP" (esse certificado é usado para proteger o transporte das configurações protegidas da extensão).
Esse certificado será regenerado automaticamente reiniciando o agente convidado do Windows de dentro da máquina virtual:
- Abrir o Gerenciador de tarefas
- Ir para a guia detalhes
- Localizar o processo de WindowsAzureGuestAgent.exe
- Clique com o botão direito do mouse e selecione "Finalizar tarefa". O processo será reiniciado automaticamente


Você também pode disparar uma nova metastate para a VM, executando uma "VM reapply". [Reaplicar](/rest/api/compute/virtualmachines/reapply) VM é uma API introduzida em 2020 para reaplicar o estado de uma VM. É recomendável fazer isso por vez, quando você puder tolerar um curto tempo de inatividade da VM. Embora a reaplicação em si não cause uma reinicialização da VM e a grande maioria dos tempos que chamam a reaplicação não reinicia a VM, há um risco muito pequeno de que alguma outra atualização pendente para o modelo de VM seja aplicada quando reaplicar dispara um novo estado de meta e que outra alteração pode exigir uma reinicialização. 

Portal do Azure:

No portal, selecione a VM e, no painel esquerdo, em **suporte + solução de problemas**, selecione **reimplantar + aplicar** novamente e, em seguida, selecione **reaplicar**.


Azure PowerShell *(substitua o nome RG e o nome da VM pelos valores)*:

```azurepowershell
Set-AzVM -ResourceGroupName <RG Name> -Name <VM Name> -Reapply
```

CLI do Azure *(substitua o nome RG e o nome da VM pelos valores)*:

```azurecli
az vm reapply -g <RG Name> -n <VM Name>
```

Se uma "VM reaplicar" não funcionou, você pode adicionar um novo disco de dados vazio à VM da Portal de Gerenciamento do Azure e, em seguida, removê-lo mais tarde, depois que o certificado tiver sido adicionado de volta.