---
title: Executar scripts do PowerShell em uma VM do Windows no Azure
description: Este tópico descreve como executar scripts do PowerShell em uma máquina virtual Windows do Azure usando o recurso Executar Comando
services: automation
ms.service: virtual-machines
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: how-to
ms.custom: devx-track-azurecli
manager: carmonm
ms.openlocfilehash: eac6201f45b11cae223e2293644bd9d0144e6e31
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98203347"
---
# <a name="run-powershell-scripts-in-your-windows-vm-by-using-run-command"></a>Executar scripts do PowerShell na VM Windows usando o recurso Executar Comando

O recurso Executar Comando usa o agente da máquina virtual (VM) para executar scripts do PowerShell dentro de uma VM Windows do Azure. Use esses scripts para o gerenciamento geral de máquinas ou aplicativos. Eles podem ajudá-lo a diagnosticar e corrigir rapidamente problemas de rede e acesso à VM e colocar a VM em um bom estado.



## <a name="benefits"></a>Benefícios

Acesse as máquinas virtuais de várias maneiras. O recurso Executar Comando pode executar scripts nas máquinas virtuais remotamente usando o agente de VM. Use Executar Comando por meio do portal do Azure, da [API REST](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), ou do [PowerShell](/powershell/module/az.compute/invoke-azvmruncommand) para VMs do Windows.

Esse recurso é útil em todos os cenários em que você deseja executar um script em uma máquina virtual. É uma das únicas maneiras de solucionar problemas e corrigir uma máquina virtual que não tem a porta RDP ou SSH aberta devido à configuração imprópria da rede ou do usuário administrativo.

## <a name="restrictions"></a>Restrições

As seguintes restrições se aplicam ao usar o recurso Executar Comando:

* A saída está limitada aos últimos 4.096 bytes.
* O tempo mínimo para executar um script é de cerca de 20 segundos.
* Os scripts são executados como sistema no Windows.
* É possível executar um script por vez.
* Scripts que solicitam informações (modo interativo) não têm suporte.
* Não é possível cancelar um script em execução.
* O tempo máximo que um script pode ser executado é 90 minutos. Depois disso, ele atingirá o tempo limite.
* A conectividade de saída da VM é necessária para retornar os resultados do script.
* Não é recomendável executar um script que causará uma parada ou atualização do agente de VM. Isso pode permitir a extensão em um estado de transição, levando a um tempo limite.

> [!NOTE]
> Para funcionar corretamente, Executar Comando requer conectividade (porta 443) a endereços IP públicos do Azure. Se a extensão não tiver acesso a esses pontos de extremidade, os scripts poderão ser executados com êxito, mas não retornarão os resultados. Se você estiver bloqueando o tráfego na máquina virtual, poderá usar [marcas de serviço](../../virtual-network/network-security-groups-overview.md#service-tags) para permitir o tráfego para os endereços IP públicos do Azure usando a marca `AzureCloud`.

## <a name="available-commands"></a>Comandos disponíveis

Esta tabela mostra a lista de comandos disponíveis para VMs Windows. Use o comando **RunPowerShellScript** para executar qualquer script personalizado que desejar. Quando você estiver usando a CLI do Azure ou o PowerShell para executar um comando, o valor fornecido para o parâmetro `--command-id` ou `-CommandId` deve ser um dos seguintes valores listados. Quando especifica um valor que não é um comando disponível, recebe este erro:

```error
The entity was not found in this Azure location
```

|**Nome**|**Descrição**|
|---|---|
|**RunPowerShellScript**|Executa um script do PowerShell.|
|**EnableRemotePS**|Configura o computador para habilitar o PowerShell remoto.|
|**EnableAdminAccount**|Verifica se a conta de administrador local está desabilitada e, em caso positivo, a habilita.|
|**IPConfig**| Mostra informações detalhadas do endereço IP, da máscara de sub-rede e do gateway padrão de cada adaptador associado ao TCP/IP.|
|**RDPSettings**|Verifica as configurações do registro e de política de domínio. Sugere as ações de política se o computador fizer parte de um domínio ou modifica as configurações para os valores padrão.|
|**ResetRDPCert**|Remove o certificado TSL/SSL associado ao ouvinte RDP e restaura a segurança dele para o padrão. Use este script se houver algum problema com o certificado.|
|**SetRDPPort**|Define o padrão ou o número da porta do usuário especificado para conexões de Área de Trabalho Remota. Habilita as regras de firewall para acesso de entrada à porta.|

## <a name="azure-cli"></a>CLI do Azure

O exemplo a seguir usa o comando [az vm run-command](/cli/azure/vm/run-command#az-vm-run-command-invoke) para executar um script de shell em uma VM Windows do Azure.

```azurecli-interactive
# script.ps1
#   param(
#       [string]$arg1,
#       [string]$arg2
#   )
#   Write-Host This is a sample script with parameters $arg1 and $arg2

az vm run-command invoke  --command-id RunPowerShellScript --name win-vm -g my-resource-group \
    --scripts @script.ps1 --parameters "arg1=somefoo" "arg2=somebar"
```

## <a name="azure-portal"></a>Portal do Azure

Acesse uma VM no [portal do Azure](https://portal.azure.com) e selecione **Executar comando** em **OPERAÇÕES**. Você vê uma lista dos comandos disponíveis a serem executados na VM.

![Lista de comandos](./media/run-command/run-command-list.png)

Escolha um comando a ser executado. Alguns dos comandos podem ter parâmetros de entrada obrigatórios ou opcionais. Para esses comandos, os parâmetros são apresentados como campos de texto para você fornecer os valores de entrada. Para cada comando, é possível exibir o script que está em execução expandindo **Exibir script**. **RunPowerShellScript** é diferente dos outros comandos, uma vez que permite a você fornecer seu próprio script personalizado.

> [!NOTE]
> Os comandos internos não podem ser editados.

Depois de escolher o comando, selecione **Executar** para executar o script. Quando o script for concluído, ele retornará a saída e os erros na janela de saída. A captura de tela a seguir mostra um exemplo de saída da execução do comando **RDPSettings**.

![Executar saída de script de comando](./media/run-command/run-command-script-output.png)

## <a name="powershell"></a>PowerShell

O exemplo a seguir usa o cmdlet [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand) para executar um script do PowerShell em uma VM do Azure. O cmdlet espera que o script referenciado no parâmetro `-ScriptPath` seja o local onde o cmdlet é executado.

```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Limitando o acesso ao recurso Executar Comando

Listar os comandos de execução ou mostrar os detalhes de um comando requer a `Microsoft.Compute/locations/runCommands/read` permissão no nível de assinatura. A função [Leitor](../../role-based-access-control/built-in-roles.md#reader) interna e os níveis superiores têm essa permissão.

A execução de um comando requer a permissão `Microsoft.Compute/virtualMachines/runCommand/action`. A função [Colaborador de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e os níveis superiores têm essa permissão.

Use uma das [funções internas](../../role-based-access-control/built-in-roles.md) ou crie uma [função personalizada](../../role-based-access-control/custom-roles.md) para usar o recurso Executar Comando.

## <a name="next-steps"></a>Próximas etapas

Para conhecer outras maneiras de executar scripts e comandos remotamente em sua VM, consulte [Executar scripts na VM do Windows](run-scripts-in-vm.md).
