---
title: Extensão de script personalizado do Azure para Windows | Microsoft Docs
description: Automatizar tarefas de configuração de VM do Windows usando a Extensão de Script Personalizado
services: virtual-machines-windows
manager: carmonm
author: georgewallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/15/2019
ms.author: gwallace
ms.openlocfilehash: e2b36633996f961d100f0a98abb09135fd4393e4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60869853"
---
# <a name="custom-script-extension-for-windows"></a>Extensão de script personalizado para o Windows

A extensão de script personalizado baixa e executa scripts em máquinas virtuais do Azure. Essa extensão é útil para a configuração pós-implantação, instalação de software, ou qualquer outra configuração ou tarefas de gerenciamento. Os scripts podem ser baixados do armazenamento do Azure ou do GitHub, ou fornecidos ao Portal do Azure no tempo de execução da extensão. Extensão de Script personalizado se integra com modelos do Azure Resource Manager e podem ser executado usando a CLI do Azure, PowerShell, portal do Azure ou API REST da máquina Virtual do Azure.

Este documento detalha como usar a Extensão de Script Personalizado usando o módulo do Azure PowerShell e modelos do Azure Resource Manager, além de detalhar as etapas da solução de problemas em sistemas Windows.

## <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]  
> Não use a Extensão de Script Personalizado para executar Update-AzVM com a mesma VM como seu parâmetro, pois ela aguardará por si própria.  

### <a name="operating-system"></a>Sistema operacional

A extensão de Script personalizado para o Windows será executado na extensão de extensão de suporte para sistemas operacionais, para obter mais informações, consulte este [sistemas operacionais com suporte do Azure extensão](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Local do script

Você pode configurar a extensão para usar suas credenciais de armazenamento de BLOBs do Azure para acessar o armazenamento de BLOBs do Azure. O local do script pode ser em qualquer lugar, desde que a VM possa rotear para o ponto de extremidade, como GitHub ou em um servidor de arquivos internos.

### <a name="internet-connectivity"></a>Conectividade com a Internet

Se você precisar baixar um script externamente, como do GitHub ou do armazenamento do Azure, em seguida, adicional de firewall e portas de grupo de segurança de rede precisam ser abertas. Por exemplo, se seu script está localizado no armazenamento do Azure, você pode permitir o acesso usando marcas de serviço do Azure NSG para [armazenamento](../../virtual-network/security-overview.md#service-tags).

Se seu script estiver em um servidor local, em seguida, você ainda pode precisar adicional de firewall e as portas de grupo de segurança de rede precisam ser abertas.

### <a name="tips-and-tricks"></a>Dicas e truques

* A taxa de falhas mais alta para essa extensão é devido a erros de sintaxe no script, teste o script é executado sem erro e também colocar registro em log para o script para torná-lo mais fácil de encontrar onde ele falha adicional.
* Escreva scripts que são idempotentes. Isso garante que se eles são executados novamente acidentalmente, ele não fará alterações no sistema.
* Assegure-se de que os scripts não exigirão a entrada do usuário quando forem executados.
* É permitido que o script seja executado em até 90 minutos e um período mais longo resultará em falha na provisão da extensão.
* Não coloque reinicializações dentro do script, pois essa ação causará problemas com outras extensões que estão sendo instaladas. Após a reinicialização, a extensão não continuará depois de reiniciar.
* Se você tiver um script que fazem com que uma reinicialização, em seguida, instalar aplicativos e executar scripts, você pode agendar o reinício usando uma tarefa agendada do Windows ou usar ferramentas como extensões de DSC, Chef ou Puppet.
* A extensão executará um script somente uma vez. Se você quiser executar um script em cada inicialização, use a extensão pra criar uma Tarefa Agendada do Windows.
* Se você quiser agendar quando um script será executado, use a extensão para criar uma Tarefa Agendada do Windows.
* Quando o script for executado, você só verá um status da extensão 'em transição' no portal do Azure ou no CLI. Se quiser atualizações de status mais frequentes de um script em execução, será necessário criar sua própria solução.
* A extensão de script personalizado não dá suporte nativo para servidores proxy. No entanto, é possível usar uma ferramenta de transferência de arquivos que dá suporte a servidores proxy no script, como a *Curl*
* Esteja ciente dos locais de diretório não padrão nos quais os scripts ou comandos podem confiar e mantenha uma lógica para lidar com essa situação.

## <a name="extension-schema"></a>Esquema de extensão

A configuração de extensão de script personalizado especifica itens como localização de script e o comando a ser executado. Você pode armazenar essa configuração em arquivos de configuração, especificá-la na linha de comando ou especificá-la em um modelo do Azure Resource Manager.

Você pode armazenar dados confidenciais em uma configuração protegida, que é criptografada e descriptografada somente dentro da máquina virtual. A configuração protegida é útil quando o comando de execução inclui segredos, como uma senha.

Esses itens devem ser tratados como dados confidenciais e especificados na configuração de definição protegida por extensões. Os dados de configuração protegidos pela extensão da VM do Azure são criptografados, sendo descriptografados apenas na máquina virtual de destino.

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
        "[variables('musicstoresqlName')]"
    ],
    "tags": {
        "displayName": "config-app"
    },
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.9",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ],
            "timestamp":123456789
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey"
        }
    }
}
```

> [!NOTE]
> Apenas uma versão de uma extensão pode ser instalada em uma VM em um ponto no tempo, especificando o script personalizado, duas vezes no mesmo modelo do Resource Manager para a mesma VM falhará.

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor/Exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publicador | Microsoft.Compute | string |
| Tipo | CustomScriptExtension | string |
| typeHandlerVersion | 1.9 | int |
| fileUris (por exemplo) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | matriz |
| carimbo de data/hora (exemplo) | 123456789 | Inteiro de 32 bits |
| commandToExecute (por exemplo) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | string |
| storageAccountName (por exemplo) | examplestorageacct | string |
| storageAccountKey (por exemplo) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |

>[!NOTE]
>Esses nomes de propriedade diferenciam maiúsculas de minúsculas. Para evitar problemas de implantação, use os nomes conforme mostrado aqui.

#### <a name="property-value-details"></a>Detalhes de valor de propriedade

* `commandToExecute`: (**necessária**, cadeia de caracteres) o script de ponto de entrada a ser executado. Use esse campo se o comando contiver segredos, como senhas, ou se os fileUris diferenciarem maiúsculas de minúsculas.
* `fileUris`: (opcional, matriz de cadeia de caracteres) as URLs dos arquivos a serem baixados.
* `timestamp` (opcional, inteiro de 32 bits) use esse campo apenas para disparar uma nova execução do script, alterando o valor desse campo.  Qualquer valor inteiro é aceitável. Só deve ser diferente do valor anterior.
* `storageAccountName`: (opcional, cadeia de caracteres) o nome da conta de armazenamento. Se você especificar credenciais de armazenamento, todos os `fileUris` deverão ser URLs para Blobs do Azure.
* `storageAccountKey`: (opcional, cadeia de caracteres) a chave de acesso da conta de armazenamento

Os valores a seguir podem ser definidos nas configurações públicas ou protegidas. A extensão rejeitará qualquer configuração em que os valores abaixo estejam definidos tanto nas configurações protegidas quanto nas públicas.

* `commandToExecute`

Usar as configurações de público pode ser útil para depuração, mas é recomendável que você use as configurações protegidas.

As configurações públicas são enviadas em texto não criptografado para a VM na qual o script será executado.  As configurações protegidas são criptografadas usando uma chave conhecida apenas pelo Azure e pela VM. As configurações são salvas na VM que foram enviadas, ou seja, se as configurações foram criptografadas elas são salvas criptografado na VM. O certificado usado para descriptografar os valores criptografados é armazenado na VM e usado para descriptografar as configurações (se necessário) no tempo de execução.

## <a name="template-deployment"></a>Implantação de modelo

Extensões de VM do Azure podem ser implantadas com modelos do Azure Resource Manager. O esquema JSON, que é detalhado na seção anterior pode ser usado em um modelo do Azure Resource Manager para executar a extensão de Script personalizado durante a implantação. Os exemplos a seguir mostram como usar a extensão de Script personalizado:

* [Tutorial: Implantar extensões de máquina virtual com modelos do Azure Resource Manager](../../azure-resource-manager/resource-manager-tutorial-deploy-vm-extensions.md)
* [Implante o aplicativo de duas camadas no Windows e no banco de dados SQL do Azure](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>Implantação do PowerShell

O comando `Set-AzVMCustomScriptExtension` pode ser usado para adicionar a Extensão de Script Personalizado a uma máquina virtual existente. Para obter mais informações, confira [Set-AzVMCustomScriptExtension](/powershell/module/az.compute/set-azvmcustomscriptextension).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName <resourceGroupName> `
    -VMName <vmName> `
    -Location myLocation `
    -FileUri <fileUrl> `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="additional-examples"></a>Exemplos adicionais

### <a name="using-multiple-scripts"></a>Usar vários scripts

Neste exemplo, você tem três scripts que são usados para criar seu servidor. O **commandToExecute** chama o primeiro script, em seguida, você tem opções sobre como os outros são chamados. Por exemplo, você pode ter um script mestre que controla a execução, com o tratamento de erro à direita, o registro em log e o gerenciamento de estado. Os scripts são baixados para o computador local para a execução. Por exemplo, em `1_Add_Tools.ps1` , você chamaria `2_Add_Features.ps1` adicionando `.\2_Add_Features.ps1` para o script e repita esse processo para outros scripts que você define no `$settings`.

```powershell
$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$settings = @{"fileUris" = $fileUri};

$storageAcctName = "xxxxxxx"
$storageKey = "1234ABCD"
$protectedSettings = @{"storageAccountName" = $storageAcctName; "storageAccountKey" = $storageKey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "buildserver1" `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.9" `
    -Settings $settings    `
    -ProtectedSettings $protectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>Executando scripts de um compartilhamento local

Neste exemplo, você talvez queira usar um servidor SMB local para seu local de script. Ao fazer isso, você não precisa fornecer outras configurações, exceto **commandToExecute**.

```powershell
$protectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};

Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "serverUpdate"
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.9" `
    -ProtectedSettings $protectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Como executar o script personalizado mais de uma vez com a CLI

Se você quiser executar a extensão do script personalizado mais de uma vez, poderá executar essa ação somente sob estas condições:

* A extensão **nome** parâmetro é o mesmo que a implantação anterior da extensão.
* Atualize a configuração caso contrário, que o comando não será executado novamente. É possível adicionar uma propriedade dinâmica ao comando, como um carimbo de data/hora.

## <a name="classic-vms"></a>VMs clássicas

Para implantar a extensão de Script personalizado em VMs clássicas, você pode usar o portal do Azure ou os cmdlets do PowerShell clássico do Azure.

### <a name="azure-portal"></a>Portal do Azure

Navegue até seu recurso de VM clássica. Selecione **extensões** sob **configurações**.

Clique em **+ adicionar** e na lista de recursos, escolha **extensão de Script personalizado**.

Sobre o **instalar a extensão** , selecione o arquivo local do PowerShell e preencha os argumentos e clique em **Okey**.

### <a name="powershell"></a>PowerShell

Use o [Set-AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure/set-azurevmcustomscriptextension) cmdlet pode ser usado para adicionar a extensão de Script personalizado para uma máquina virtual existente.

```powershell
# define your file URI
$fileUri = 'https://xxxxxxx.blob.core.windows.net/scripts/Create-File.ps1'

# create vm object
$vm = Get-AzureVM -Name <vmName> -ServiceName <cloudServiceName>

# set extension
Set-AzureVMCustomScriptExtension -VM $vm -FileUri $fileUri -Run 'Create-File.ps1'

# update vm
$vm | Update-AzureVM
```

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="troubleshoot"></a>Solucionar problemas

Os dados sobre o estado das implantações de extensão podem ser recuperados no Portal do Azure usando o módulo do Azure PowerShell. Para ver o estado da implantação das extensões de uma determinada VM, execute o comando a seguir:

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

A saída da extensão é registrada nos arquivos localizados na seguinte pasta na máquina virtual de destino.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Os arquivos especificados são baixados na pasta a seguir na máquina virtual de destino.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

em que `<n>` é um inteiro decimal que pode ser alterado entre as execuções da extensão.  O valor `1.*` corresponde ao valor `typeHandlerVersion` atual e real da extensão.  Por exemplo, o diretório real pode ser `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

Ao executar o comando `commandToExecute`, a extensão definirá esse diretório (por exemplo, `...\Downloads\2`) como o diretório de trabalho atual. Esse processo permite o uso de caminhos relativos para localizar os arquivos baixados por meio da propriedade `fileURIs`. Veja a tabela abaixo para obter exemplos.

Como o caminho absoluto do download pode variar ao longo do tempo, é melhor optar por caminhos de arquivo/script relativos na cadeia de caracteres `commandToExecute`, sempre que possível. Por exemplo: 

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Informações de caminho após o primeiro segmento do URI é mantido para os arquivos baixados por meio de `fileUris` lista de propriedades.  Conforme mostrado na tabela a seguir, os arquivos baixados são mapeados em subdiretórios de download para refletir a estrutura dos valores `fileUris`.  

#### <a name="examples-of-downloaded-files"></a>Exemplos de Arquivos Baixados

| URI no fileUris | Localização baixada relativa | Localização baixada absoluta <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> os caminhos de diretório absoluto mudam ao longo do tempo de vida da VM, mas não em uma única execução da extensão do CustomScript.

### <a name="support"></a>Suporte

Caso precise de mais ajuda em qualquer ponto deste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/forums/). Você também pode registrar um incidente de Suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).