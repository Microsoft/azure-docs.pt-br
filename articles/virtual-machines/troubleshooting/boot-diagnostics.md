---
title: Diagnósticos de inicialização para VMs no Azure | Microsoft Docs
description: Visão geral dos dois recursos de depuração para máquinas virtuais no Azure
services: virtual-machines
author: Deland-Han
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: delhan
ms.openlocfilehash: 7796d24b88ccc531b8042bfadf9d87f0072a2994
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709855"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-virtual-machines-in-azure"></a>Como usar o diagnóstico de inicialização para solucionar problemas das máquinas virtuais no Azure

Pode haver vários motivos pelos quais uma máquina virtual entra em um estado não inicializável. Para solucionar problemas com as máquinas virtuais criadas usando o modelo de implantação do Resource Manager, use os seguintes recursos de depuração: Suporte de Captura de Tela e Saída do Console para máquinas virtuais do Azure. 

Para Máquinas Virtuais do Linux, exiba a saída do log de console no portal. Para Máquinas Virtuais do Windows e do Linux, o Azure permite que você veja uma captura de tela da VM no hipervisor. Há suporte para os dois recursos nas máquinas virtuais do Azure em todas as regiões. Anotações, capturas de tela e saídas podem levar até 10 minutos para aparecer na sua conta de armazenamento.

Você pode selecionar a opção **Diagnósticos de inicialização** para exibir o log e a captura de tela.

![Gerenciador de Recursos](./media/virtual-machines-common-boot-diagnostics/screenshot1.png)

## <a name="common-boot-errors"></a>Erros comuns de inicialização

- [0xC000000E](https://support.microsoft.com/help/4010129)
- [0xC000000F](https://support.microsoft.com/help/4010130)
- [0xC0000011](https://support.microsoft.com/help/4010134)
- [0xC0000034](https://support.microsoft.com/help/4010140)
- [0xC0000098](https://support.microsoft.com/help/4010137)
- [0xC00000BA](https://support.microsoft.com/help/4010136)
- [0xC000014C](https://support.microsoft.com/help/4010141)
- [0xC0000221](https://support.microsoft.com/help/4010132)
- [0xC0000225](https://support.microsoft.com/help/4010138)
- [0xC0000359](https://support.microsoft.com/help/4010135)
- [0xC0000605](https://support.microsoft.com/help/4010131)
- [Não foi possível encontrar um sistema operacional](https://support.microsoft.com/help/4010142)
- [Falha de inicialização ou INACCESSIBLE_BOOT_DEVICE](https://support.microsoft.com/help/4010143)

## <a name="enable-diagnostics-on-a-virtual-machine-created-using-the-azure-portal"></a>Habilitar o diagnóstico em uma máquina virtual criada usando o portal do Azure

O procedimento a seguir destina-se a uma máquina virtual criada usando o modelo de implantação do Resource Manager.

Na guia **Gerenciamento**, na seção **Monitoramento**, verifique se a opção **Diagnóstico de inicialização** está ativada. Na lista suspensa **Conta de armazenamento de diagnóstico**, selecione uma conta de armazenamento na qual colocar os arquivos de diagnóstico.
 
![Criar VM](./media/virtual-machines-common-boot-diagnostics/enable-boot-diagnostics-vm.png)

> [!NOTE]
> O recurso de diagnóstico de inicialização não oferece suporte à conta de armazenamento premium. Se você usar a conta de armazenamento premium para o diagnóstico de inicialização, você poderá receber o erro StorageAccountTypeNotSupported quando você iniciar a VM.
>

### <a name="deploying-from-an-azure-resource-manager-template"></a>Implantação com base em um modelo do Azure Resource Manager

Se você estiver implantando um modelo do Azure Resource Manager, navegue até seu recurso de máquina virtual e acrescente a seção de diagnóstico do perfil. Defina o cabeçalho da versão de API como "2015-06-15" ou posterior. A última versão é "2018-10-01".

```json
{
  "apiVersion": "2018-10-01",
  "type": "Microsoft.Compute/virtualMachines",
  … 
```

O perfil de diagnóstico permite que você selecione a conta de armazenamento em que deseja colocar esses logs.

```json
    "diagnosticsProfile": {
    "bootDiagnostics": {
    "enabled": true,
    "storageUri": "[concat('https://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
    }
    }
    }
}
```

Para obter mais informações sobre como implantar recursos usando modelos, confira [Início Rápido: Crie e implante modelos do Azure Resource Manager usando o portal do Azure](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md).

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>Habilitar o diagnóstico de inicialização em uma máquina virtual existente 

Para habilitar o diagnóstico de inicialização em uma máquina virtual existente, siga estas etapas:

1. Faça logon no [Portal do Azure](https://portal.azure.com) e selecione a máquina virtual.
2. Na seção **Suporte + solução de problemas**, selecione **Diagnóstico de inicialização** e, em seguida, a guia **Configurações**.
3. Nas configurações de **Diagnóstico de inicialização**, altere o status para **Ativado** e, na lista suspensa **Conta de armazenamento**, selecione uma conta de armazenamento. 
4. Salve a alteração.

    ![Atualizar VM existente](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

É necessário reiniciar a máquina virtual para que a alteração entre em vigor.

### <a name="enable-boot-diagnostics-using-the-azure-cli"></a>Habilitar o diagnóstico de inicialização usando a CLI do Azure

Use a CLI do Azure para habilitar o diagnóstico de inicialização em uma máquina virtual existente do Azure. Para obter mais informações, confira [az vm boot-diagnostics](
https://docs.microsoft.com/cli/azure/vm/boot-diagnostics?view=azure-cli-latest).
