---
title: Executar scripts de shell em uma VM Linux no Azure
description: Este tópico descreve como executar scripts dentro de uma máquina virtual Linux do Azure usando o recurso Executar Comando
services: automation
ms.service: virtual-machines
ms.collection: linux
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: how-to
manager: carmonm
ms.openlocfilehash: 73dd15a5eed3e27d9b72bc0357e35901c04ba7a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552925"
---
# <a name="run-shell-scripts-in-your-linux-vm-by-using-run-command"></a>Executar scripts de shell em sua VM Linux usando o recurso Executar Comando

O recurso Executar Comando usa o agente da máquina virtual (VM) para executar scripts de shell dentro de uma VM Linux do Azure. Use esses scripts para o gerenciamento geral de máquinas ou aplicativos. Eles podem ajudá-lo a diagnosticar e corrigir rapidamente problemas de rede e acesso à VM e colocar a VM em um bom estado.

## <a name="benefits"></a>Benefícios

Acesse as máquinas virtuais de várias maneiras. O recurso Executar Comando pode executar scripts em suas máquinas virtuais remotamente usando o agente de VM. Use o comando Executar por meio do portal do Azure, [API REST](/rest/api/compute/virtual%20machines%20run%20commands/runcommand) ou [CLI do Azure](/cli/azure/vm/run-command#az_vm_run_command_invoke) para VMs do Linux.

Esse recurso é útil em todos os cenários em que você deseja executar um script em uma máquina virtual. É uma das únicas maneiras de solucionar problemas e corrigir uma máquina virtual que não tem a porta RDP ou SSH aberta devido à configuração imprópria da rede ou do usuário administrativo.

## <a name="restrictions"></a>Restrições

As seguintes restrições se aplicam ao usar o recurso Executar Comando:

* A saída está limitada aos últimos 4.096 bytes.
* O tempo mínimo para executar um script é de cerca de 20 segundos.
* Scripts executados por padrão como um usuário com privilégios elevados no Linux.
* É possível executar um script por vez.
* Scripts que solicitam informações (modo interativo) não têm suporte.
* Não é possível cancelar um script em execução.
* O tempo máximo que um script pode ser executado é 90 minutos. Depois disso, o script atingirá o tempo limite.
* A conectividade de saída da VM é necessária para retornar os resultados do script.

> [!NOTE]
> Para funcionar corretamente, Executar Comando requer conectividade (porta 443) a endereços IP públicos do Azure. Se a extensão não tiver acesso a esses pontos de extremidade, os scripts poderão ser executados com êxito, mas não retornarão os resultados. Se você estiver bloqueando o tráfego na máquina virtual, poderá usar [marcas de serviço](../../virtual-network/network-security-groups-overview.md#service-tags) para permitir o tráfego para os endereços IP públicos do Azure usando a marca `AzureCloud`.

## <a name="available-commands"></a>Comandos disponíveis

Esta tabela mostra a lista de comandos disponíveis para VMs Linux. Use o comando **RunShellScript** para executar qualquer script personalizado que desejar. Quando você estiver usando a CLI do Azure ou o PowerShell para executar um comando, o valor fornecido para o parâmetro `--command-id` ou `-CommandId` deverá ser um dos seguintes valores listados. Quando especifica um valor que não é um comando disponível, recebe este erro:

```error
The entity was not found in this Azure location
```

|**Nome**|**Descrição**|
|---|---|
|**RunShellScript**|Executa um script de shell do Linux.|
|**ifconfig**| Obtém a configuração de todas as interfaces de rede.|

## <a name="azure-cli"></a>CLI do Azure

O exemplo a seguir usa o comando [az vm run-command](/cli/azure/vm/run-command#az_vm_run_command_invoke) para executar um script de shell em uma VM Linux do Azure.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "apt-get update && apt-get install -y nginx"
```

> [!NOTE]
> Para executar comandos como um usuário diferente, insira `sudo -u` para especificar uma conta de usuário.

## <a name="azure-portal"></a>Portal do Azure

Acesse uma VM no [portal do Azure](https://portal.azure.com) e selecione **Executar comando** em **OPERAÇÕES**. Você vê uma lista dos comandos disponíveis a serem executados na VM.

![Lista de comandos](./media/run-command/run-command-list.png)

Escolha um comando a ser executado. Alguns dos comandos podem ter parâmetros de entrada obrigatórios ou opcionais. Para esses comandos, os parâmetros são apresentados como campos de texto para você fornecer os valores de entrada. Para cada comando, é possível exibir o script que está em execução expandindo **Exibir script**. **RunShellScript** é diferente dos outros comandos, uma vez que permite a você fornecer seu próprio script personalizado.

> [!NOTE]
> Os comandos internos não podem ser editados.

Depois de escolher o comando, selecione **Executar** para executar o script. Quando o script for concluído, ele retornará a saída e os erros na janela de saída. A captura de tela a seguir mostra um exemplo de saída da execução do comando **ifconfig**.

![Executar saída de script de comando](./media/run-command/run-command-script-output.png)

### <a name="powershell"></a>PowerShell

O exemplo a seguir usa o cmdlet [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand) para executar um script do PowerShell em uma VM do Azure. O cmdlet espera que o script referenciado no parâmetro `-ScriptPath` seja o local onde o cmdlet é executado.

```powershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Limitando o acesso ao recurso Executar Comando

Listar os comandos de execução ou mostrar os detalhes de um comando requer a permissão `Microsoft.Compute/locations/runCommands/read`. A função [Leitor](../../role-based-access-control/built-in-roles.md#reader) interna e os níveis superiores têm essa permissão.

A execução de um comando requer a permissão `Microsoft.Compute/virtualMachines/runCommand/action`. A função [Colaborador de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e os níveis superiores têm essa permissão.

Use uma das [funções internas](../../role-based-access-control/built-in-roles.md) ou crie uma [função personalizada](../../role-based-access-control/custom-roles.md) para usar o recurso Executar Comando.

## <a name="next-steps"></a>Próximas etapas

Para conhecer outras maneiras de executar scripts e comandos remotamente em sua VM, consulte [Executar scripts em sua VM Linux](run-scripts-in-vm.md).
