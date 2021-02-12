---
title: Extensão de script do Azure personalizado para o Windows
description: Automatizar tarefas de configuração de VM do Windows usando a Extensão de Script Personalizado
services: virtual-machines-windows
manager: carmonm
author: bobbytreed
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/31/2020
ms.author: robreed
ms.openlocfilehash: d4cfb8d6a48ac41b4deb8913d4277f07cbb43208
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258697"
---
# <a name="custom-script-extension-for-windows"></a>Extensão de script personalizado para o Windows

A extensão de script personalizado baixa e executa scripts em máquinas virtuais do Azure. Essa extensão é útil para a configuração de pós-implantação, instalação de software ou qualquer outra configuração ou tarefas de gerenciamento. Os scripts podem ser baixados do armazenamento do Azure ou do GitHub, ou fornecidos ao Portal do Azure no tempo de execução da extensão. A Extensão de Script Personalizado se integra com modelos do Azure Resource Manager e pode ser executada usando a CLI do Azure, o PowerShell, o portal do Azure ou a API REST da máquina virtual do Azure.

Este documento detalha como usar a Extensão de Script Personalizado usando o módulo do Azure PowerShell e modelos do Azure Resource Manager, além de detalhar as etapas da solução de problemas em sistemas Windows.

## <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]  
> Não use a Extensão de Script Personalizado para executar Update-AzVM com a mesma VM como seu parâmetro, pois ela aguardará por si própria.  

### <a name="operating-system"></a>Sistema operacional

A Extensão de Script Personalizado para Windows executará nos SOs de extensão compatíveis da extensão.
### <a name="windows"></a>Windows

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows 10
* Windows Server 2016
* Windows Server 2016 Core
* Windows Server 2019
* Windows Server 2019 Core

### <a name="script-location"></a>Local do script

Você pode configurar a extensão para usar suas credenciais do Armazenamento de Blobs do Azure para acessar esse armazenamento. A localização do script pode estar em qualquer lugar, desde que a VM possa rotear para esse ponto de extremidade, como o GitHub ou um servidor de arquivos interno.

### <a name="internet-connectivity"></a>Conectividade com a Internet

Se você precisar fazer o download um script externamente, como do GitHub ou do Armazenamento do Azure, será necessário abrir portas adicionais do firewall e do Grupo de Segurança de Rede. Por exemplo, se o script estiver localizado no Armazenamento do Azure, você poderá permitir acesso usando Marcas de Serviço do NSG do Azure para [Armazenamento](../../virtual-network/network-security-groups-overview.md#service-tags).

Observe que a extensão CustomScript não tem nenhuma maneira de ignorar a validação do certificado. Portanto, se você estiver baixando de um local seguro com por exemplo. um certificado autoassinado, você pode acabar com erros como *"o certificado remoto é inválido de acordo com o procedimento de validação"*. Certifique-se de que o certificado esteja instalado corretamente no repositório *"autoridades de certificação raiz confiáveis"* na máquina virtual.

Se o script estiver em um servidor local, ainda poderá ser necessário abrir portas adicionais do firewall e do Grupo de Segurança de Rede.

### <a name="tips-and-tricks"></a>Dicas e truques

* A taxa de falha mais alta para esta extensão acontece devido a erros de sintaxe no script. Teste as execuções de script sem erros e também insira um registro em log adicional no script para facilitar a localização da falha.
* Escreva scripts idempotentes. Isso garante que, se eles forem executados novamente por acidente, não causarão alterações no sistema.
* Assegure-se de que os scripts não exigirão a entrada do usuário quando forem executados.
* É permitido que o script seja executado em até 90 minutos e um período mais longo resultará em falha na provisão da extensão.
* Não coloque reinicializações dentro do script, pois essa ação causará problemas com outras extensões que estão sendo instaladas. Após a reinicialização, a extensão não continuará depois de reiniciar.
* Se você tiver um script que causará uma reinicialização, instalará aplicativos e executará scripts, você poderá agendar a reinicialização usando uma Tarefa Agendada do Windows ou usar ferramentas como as extensões DSC, Chef ou Puppet.
* Não é recomendável executar um script que causará uma parada ou atualização do agente de VM. Isso pode deixar a extensão em um estado de transição, levando a um tempo limite.
* A extensão executará um script somente uma vez. Se você quiser executar um script em cada inicialização, use a extensão pra criar uma Tarefa Agendada do Windows.
* Se você quiser agendar quando um script será executado, use a extensão para criar uma Tarefa Agendada do Windows.
* Quando o script for executado, você só verá um status da extensão 'em transição' no portal do Azure ou no CLI. Se quiser atualizações de status mais frequentes de um script em execução, será necessário criar sua própria solução.
* A extensão de script personalizado não dá suporte nativo para servidores proxy. No entanto, é possível usar uma ferramenta de transferência de arquivos que dá suporte a servidores proxy no script, como a *Curl*
* Esteja ciente dos locais de diretório não padrão nos quais os scripts ou comandos podem confiar e mantenha uma lógica para lidar com essa situação.
* A extensão de script personalizado será executada na conta LocalSystem
* Se você planeja usar as propriedades *storageAccountName* e *storageAccountKey* , essas propriedades deverão ser posicionadas no *protectedSettings*.

## <a name="extension-schema"></a>Esquema de extensão

A configuração de extensão de script personalizado especifica itens como localização de script e o comando a ser executado. Você pode armazenar essa configuração em arquivos de configuração, especificá-la na linha de comando ou especificá-la em um modelo do Azure Resource Manager.

Você pode armazenar dados confidenciais em uma configuração protegida, que é criptografada e descriptografada somente dentro da máquina virtual. A configuração protegida é útil quando o comando de execução inclui segredos, como uma senha.

Esses itens devem ser tratados como dados confidenciais e especificados na configuração de definição protegida por extensões. Os dados de configuração protegidos pela extensão da VM do Azure são criptografados, sendo descriptografados apenas na máquina virtual de destino.

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "virtualMachineName/config-app",
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
        "typeHandlerVersion": "1.10",
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
            "storageAccountKey": "myStorageAccountKey",
            "managedIdentity" : {}
        }
    }
}
```

> [!NOTE]
> A propriedade managedIdentity **não pode** ser usada em conjunto com as propriedades storageAccountName ou storageAccountKey

> [!NOTE]
> Somente uma versão de uma extensão pode ser instalada em uma VM em um determinado momento. A especificação de um script personalizado duas vezes no mesmo modelo do Resource Manager para a mesma VM falhará.

> [!NOTE]
> Podemos usar esse esquema dentro do recurso VirtualMachine ou como um recurso autônomo. O nome do recurso precisará estar neste formato: "nomeDaMáquinaVirtual/nomeDaExtensão", se essa extensão for usada como um recurso autônomo no modelo do ARM.

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor/Exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publicador | Microsoft.Compute | string |
| type | CustomScriptExtension | string |
| typeHandlerVersion | 1,10 | INT |
| fileUris (por exemplo) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | matriz |
| carimbo de data/hora (exemplo) | 123456789 | Inteiro de 32 bits |
| commandToExecute (por exemplo) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | string |
| storageAccountName (por exemplo) | examplestorageacct | string |
| storageAccountKey (por exemplo) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |
| managedIdentity (por exemplo) | { } ou { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" } ou { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" } | objeto JSON |

>[!NOTE]
>Esses nomes de propriedade diferenciam maiúsculas de minúsculas. Para evitar problemas de implantação, use os nomes conforme mostrado aqui.

#### <a name="property-value-details"></a>Detalhes de valor de propriedade

* `commandToExecute`: (**necessária**, cadeia de caracteres) o script de ponto de entrada a ser executado. Use esse campo se o comando contiver segredos, como senhas, ou se os fileUris diferenciarem maiúsculas de minúsculas.
* `fileUris`: (opcional, matriz de cadeia de caracteres) as URLs dos arquivos a serem baixados.
* `timestamp` (opcional, inteiro de 32 bits) use esse campo apenas para disparar uma nova execução do script, alterando o valor desse campo.  Qualquer valor inteiro é aceitável. Só deve ser diferente do valor anterior.
* `storageAccountName`: (opcional, cadeia de caracteres) o nome da conta de armazenamento. Se você especificar credenciais de armazenamento, todos os `fileUris` deverão ser URLs para Blobs do Azure.
* `storageAccountKey`: (opcional, cadeia de caracteres) a chave de acesso da conta de armazenamento
* `managedIdentity`: (opcional, objeto JSON) a [identidade gerenciada](../../active-directory/managed-identities-azure-resources/overview.md) para baixar arquivos
  * `clientId`: (opcional, cadeia de caracteres) a ID do cliente da identidade gerenciada
  * `objectId`: (opcional, cadeia de caracteres) a ID de objeto da identidade gerenciada

Os valores a seguir podem ser definidos nas configurações públicas ou protegidas. A extensão rejeitará qualquer configuração em que os valores abaixo estejam definidos tanto nas configurações protegidas quanto nas públicas.

* `commandToExecute`

Usar configurações públicas talvez seja útil para depuração, mas é recomendável usar configurações protegidas.

As configurações públicas são enviadas em texto não criptografado para a VM na qual o script será executado.  As configurações protegidas são criptografadas usando uma chave conhecida apenas pelo Azure e pela VM. As configurações são salvas na VM conforme são enviadas, ou seja, se as configurações forem criptografadas, elas serão salvas criptografadas na VM. O certificado usado para descriptografar os valores criptografados é armazenado na VM e usado para descriptografar as configurações (se necessário) no runtime.

####  <a name="property-managedidentity"></a>Propriedade: managedIdentity
> [!NOTE]
> Essa propriedade **precisa** ser especificada somente em configurações protegidas.

O CustomScript (versão 1.10 em diante) é compatível com [identidade gerenciada](../../active-directory/managed-identities-azure-resources/overview.md) para baixar arquivos de URLs fornecidas na configuração "fileUris". Ele permite que o CustomScript acesse contêineres ou blobs privados do Armazenamento do Azure sem que o usuário precise passar segredos como tokens SAS ou chaves de conta de armazenamento.

Para usar esse recurso, o usuário precisa adicionar uma identidade [atribuída pelo sistema](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity) ou [atribuída pelo usuário](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-user-assigned-identity) à VM ou VMSS em que se espera que o CustomScript seja executado e [permitir acesso de identidade gerenciada ao blob ou contêiner do Armazenamento do Azure](../../active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage.md#grant-access).

Para usar a identidade atribuída pelo sistema na VM/VMSS de destino, defina o campo "managedidentity" como um objeto JSON vazio. 

> Exemplo:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : {}
> }
> ```

Para usar a identidade atribuída pelo usuário na VM/VMSS de destino, configure o campo "managedidentity" com a ID do cliente ou a ID de objeto da identidade gerenciada.

> Exemplos:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" }
> }
> ```
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" }
> }
> ```

> [!NOTE]
> A propriedade managedIdentity **não pode** ser usada em conjunto com as propriedades storageAccountName ou storageAccountKey

## <a name="template-deployment"></a>Implantação de modelo

Extensões de VM do Azure podem ser implantadas com modelos do Azure Resource Manager. O esquema JSON, detalhado na seção anterior, pode ser usado em um modelo do Azure Resource Manager para executar a Extensão de Script Personalizado durante uma implantação. Os exemplos a seguir mostram como usar a extensão de Script personalizado:

* [Tutorial: Implantar extensões de máquina virtual com modelos do Azure Resource Manager](../../azure-resource-manager/templates/template-tutorial-deploy-vm-extensions.md)
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

## <a name="additional-examples"></a>Mais exemplos

### <a name="using-multiple-scripts"></a>Usando vários scripts

Neste exemplo, você tem três scripts que são usados para criar seu servidor. O **commandToExecute** chama o primeiro script e, em seguida, você tem opções sobre como os outros são chamados. Por exemplo, você pode ter um script mestre que controla a execução, com o tratamento de erro, o registro em log e o gerenciamento de estado corretos. Os scripts são baixados no computador local para execução. Por exemplo, em `1_Add_Tools.ps1`, você chamaria `2_Add_Features.ps1` adicionando `.\2_Add_Features.ps1` ao script e repetiria esse processo para os outros scripts definidos em `$settings`.

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
    -TypeHandlerVersion "1.10" `
    -Settings $settings `
    -ProtectedSettings $protectedSettings;
```

### <a name="running-scripts-from-a-local-share"></a>Executando scripts de um compartilhamento local

Neste exemplo, talvez você queira usar um servidor SMB local para a localização do script. Se fizer isso, você não precisará fornecer outras configurações, exceto **commandToExecute**.

```powershell
$protectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};

Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "serverUpdate"
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.10" `
    -ProtectedSettings $protectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Como executar o script personalizado mais de uma vez com a CLI

Se você quiser executar a extensão do script personalizado mais de uma vez, poderá executar essa ação somente sob estas condições:

* O parâmetro **Name** da extensão é o mesmo que o da implantação anterior da extensão.
* Atualize a configuração, caso contrário, o comando não executará novamente. É possível adicionar uma propriedade dinâmica ao comando, como um carimbo de data/hora.

Como alternativa, você pode definir a propriedade [ForceUpdateTag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) como **true**.

### <a name="using-invoke-webrequest"></a>Como usar Invoke-WebRequest

Se você estiver usando [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) em seu script, precisará especificar o parâmetro `-UseBasicParsing` ou, caso contrário, receberá o seguinte erro ao verificar o status detalhado:

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```
## <a name="virtual-machine-scale-sets"></a>Conjuntos de Dimensionamento de Máquinas Virtuais

Para implantar a extensão de script personalizado em um conjunto de dimensionamento, confira [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension)

## <a name="classic-vms"></a>VMs clássicas

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Para implantar a extensão de script personalizado em VMs clássicas, você pode usar o portal do Azure ou os cmdlets clássicos do Azure PowerShell.

### <a name="azure-portal"></a>Portal do Azure

Navegue até o recurso de VM clássica. Em **Configurações**, selecione **Extensões**.

Clique em **+ Adicionar** e, na lista de recursos, escolha **Extensão de Script Personalizado**.

Na página **Instalar a extensão**, selecione o arquivo local do PowerShell, preencha eventuais argumentos e clique em **Ok**.

### <a name="powershell"></a>PowerShell

Use o cmdlet [Set-AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure.service/set-azurevmcustomscriptextension) para adicionar a extensão de script personalizado a uma máquina virtual existente.

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

A saída da extensão é registrada nos arquivos localizados na pasta a seguir, na máquina virtual de destino.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Os arquivos especificados são baixados na pasta a seguir, na máquina virtual de destino.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

em que `<n>` é um inteiro decimal que pode ser alterado entre as execuções da extensão.  O valor `1.*` corresponde ao valor `typeHandlerVersion` atual e real da extensão.  Por exemplo, o diretório real pode ser `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

Ao executar o comando `commandToExecute`, a extensão definirá esse diretório (por exemplo, `...\Downloads\2`) como o diretório de trabalho atual. Esse processo permite o uso de caminhos relativos para localizar os arquivos baixados por meio da propriedade `fileURIs`. Veja a tabela abaixo para obter exemplos.

Como o caminho absoluto do download pode variar ao longo do tempo, é melhor optar por caminhos de arquivo/script relativos na cadeia de caracteres `commandToExecute`, sempre que possível. Por exemplo:

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

As informações de caminho após o primeiro segmento do URI são retidas para os arquivos baixados por meio da lista de propriedades `fileUris`.  Conforme mostrado na tabela a seguir, os arquivos baixados são mapeados em subdiretórios de download para refletir a estrutura dos valores `fileUris`.  

#### <a name="examples-of-downloaded-files"></a>Exemplos de Arquivos Baixados

| URI no fileUris | Localização baixada relativa | Localização baixada absoluta <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> Os caminhos de diretório absolutos são alterados durante o tempo de vida da VM, mas não em uma execução da extensão CustomScript.

### <a name="support"></a>Suporte

Caso precise de mais ajuda em qualquer ponto deste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/forums/). Você também pode registrar um incidente de Suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).
