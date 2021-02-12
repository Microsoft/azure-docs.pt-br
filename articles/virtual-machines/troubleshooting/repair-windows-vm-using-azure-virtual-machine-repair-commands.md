---
title: Reparar uma VM do Windows usando os comandos de reparo da Máquina Virtual do Azure | Microsoft Docs
description: Este artigo detalha como usar os comandos de reparo da VM do Azure para conectar o disco a outra VM do Windows para corrigir erros e, em seguida, reconstruir sua VM original.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 09/10/2019
ms.author: v-miegge
ms.openlocfilehash: 7763c1d856a09acf2523a7073c3d300be6b70b37
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98200695"
---
# <a name="repair-a-windows-vm-by-using-the-azure-virtual-machine-repair-commands"></a>Reparar uma VM do Windows usando os comandos de reparo da Máquina Virtual do Azure

Se sua máquina virtual (VM) do Windows no Azure encontrar um erro de inicialização ou disco, talvez seja necessário executar a atenuação no próprio disco. Um exemplo comum seria uma atualização de aplicativo com falha que impede a inicialização bem-sucedida da VM. Este artigo detalha como usar os comandos de reparo da VM do Azure para conectar o disco a outra VM do Windows para corrigir erros e, em seguida, reconstruir sua VM original.

> [!IMPORTANT]
> * Os scripts neste artigo se aplicam somente às VMs que usam [Disco Gerenciado](../../azure-resource-manager/management/overview.md).
> * A conectividade de saída da VM (porta 443) é necessária para que o script seja executado.
> * Somente um script pode ser executado de cada vez.
> * Não é possível cancelar um script em execução.
> * O tempo máximo que um script pode ser executado é 90 minutos. Após esse tempo, ele atingirá o tempo limite.
> * Para VMs que usam Azure Disk Encryption, somente os discos gerenciados criptografados com criptografia de passagem única (com ou sem KEK) têm suporte.


## <a name="repair-process-overview"></a>Visão geral do processo de reparo

Agora você pode usar os comandos de reparo da VM do Azure para alterar o disco do SO de uma VM e não é mais necessário excluir e recriar a VM.

Siga estas etapas para solucionar o problema da VM:

1. Iniciar o Azure Cloud Shell
2. Execute az extension add/update.
3. Execute az vm repair create.
4. Execute AZ VM Repair Run ou execute etapas de mitigação.
5. Execute az vm repair restore.

Para obter a documentação e as instruções adicionais, confira [az vm repair](/cli/azure/ext/vm-repair/vm/repair).

## <a name="repair-process-example"></a>Exemplo de processo de reparo

1. Iniciar o Azure Cloud Shell

   O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta.

   Para abrir o Cloud Shell, selecione **Experimentar** no canto superior direito de um bloco de código. Você também pode abrir o Cloud Shell em uma guia separada do navegador acessando [https://shell.azure.com](https://shell.azure.com).

   Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e selecione **Enter** para executá-lo.

   Se preferir instalar e usar a CLI localmente, este início rápido exigirá a CLI do Azure versão 2.0.30 ou posterior. Execute ``az --version`` para encontrar a versão. Se você precisar instalar ou atualizar sua CLI do Azure, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).
   
   Se você precisar fazer logon em Cloud Shell com uma conta diferente da que está conectada no momento ao portal do Azure com você pode usar ``az login`` [AZ login Reference](/cli/azure/reference-index#az-login&preserve-view=true).  Para alternar entre as assinaturas associadas à sua conta, você pode usar a ``az account set --subscription`` [referência do conjunto de contas AZ](/cli/azure/account#az-account-set&preserve-view=true).

2. Se esta é a primeira vez que você usa os comandos `az vm repair`, adicione a extensão da CLI vm-repair.

   ```azurecli-interactive
   az extension add -n vm-repair
   ```

   Se você já usou os comandos `az vm repair`, aplique as atualizações na extensão vm-repair.

   ```azurecli-interactive
   az extension update -n vm-repair
   ```

3. Execute `az vm repair create`. Esse comando criará uma cópia do disco do sistema operacional para a VM não funcional, criará uma VM de reparo em um novo grupo de recursos e anexará a cópia do disco do sistema operacional.  A VM de reparo terá o mesmo tamanho e região que a VM não funcional especificada. O grupo de recursos e o nome da VM usados em todas as etapas serão para a VM não funcional. Se sua VM estiver usando Azure Disk Encryption o comando tentará desbloquear o disco criptografado para que ele seja acessível quando anexado à VM de reparo.

   ```azurecli-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password 'password!234' --verbose
   ```

4. Execute `az vm repair run`. Este comando executará o script de reparo especificado no disco anexado por meio da VM de reparo. Se o guia de solução de problemas que você está usando especificou uma ID de execução, use-a aqui. Caso contrário, você pode usar `az vm repair list-scripts` para ver os scripts de reparo disponíveis. O grupo de recursos e o nome da VM usados aqui são para a VM não funcional usada na etapa 3. Informações adicionais sobre os scripts de reparo podem ser encontradas na [biblioteca de scripts de reparo](https://github.com/Azure/repair-script-library).

   ```azurecli-interactive
   az vm repair run -g MyResourceGroup -n MyVM --run-on-repair --run-id win-hello-world --verbose
   ```
   
   Opcionalmente, você pode executar as etapas de mitigação manual necessárias usando a VM de reparo e, em seguida, passar para a etapa 5.

5. Execute `az vm repair restore`. Este comando irá trocar o disco do SO reparado pelo disco do SO original da VM. O grupo de recursos e o nome da VM usados aqui são para a VM não funcional usada na etapa 3.

   ```azurecli-interactive
   az vm repair restore -g MyResourceGroup -n MyVM --verbose
   ```

## <a name="verify-and-enable-boot-diagnostics"></a>Verificar e habilitar o diagnóstico de boot

O seguinte exemplo habilita a extensão de diagnóstico na VM chamada ``myVMDeployed`` no grupo de recursos chamado ``myResourceGroup``:

CLI do Azure

```azurecli-interactive
az vm boot-diagnostics enable --name myVMDeployed --resource-group myResourceGroup --storage https://mystor.blob.core.windows.net/
```

## <a name="next-steps"></a>Próximas etapas

* Se estiver tendo problemas para se conectar à VM, consulte [Troubleshoot RDP connections to an Azure VM](./troubleshoot-rdp-connection.md) (Solucionar conexões RDP a uma VM do Azure).
* Para problemas com o acesso a aplicativos em execução na sua VM, consulte [Solucionar problemas de conectividade de aplicativos em máquinas virtuais no Azure](./troubleshoot-app-connection.md).
* Para obter mais informações sobre como usar o Resource Manager, consulte [Visão geral do Azure Resource Manager](../../azure-resource-manager/management/overview.md).
