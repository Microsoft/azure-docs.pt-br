---
title: "Extensão de script personalizado do Azure para Windows | Microsoft Docs"
description: "Automatizar tarefas de configuração de VM do Windows usando a Extensão de Script Personalizado"
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f4181fee-7a9d-4a1c-b517-52956f5b7fa1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/16/2017
ms.author: nepeters
ms.openlocfilehash: 4117b6020d2d75a953fd5f032b378e49d2c752ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="custom-script-extension-for-windows"></a>Extensão de script personalizado para o Windows

A extensão de script personalizado baixa e executa scripts em máquinas virtuais do Azure. Essa extensão é útil para a configuração de implantação de postagem, instalação de software ou qualquer outra configuração/tarefa de gerenciamento. Os scripts podem ser baixados do armazenamento do Azure ou do GitHub, ou fornecidos ao Portal do Azure no tempo de execução da extensão. A extensão de script personalizado se integra com modelos do Azure Resource Manager e também pode ser executada usando a CLI do Azure, o PowerShell, o portal do Azure ou a API REST da máquina virtual do Azure.

Este documento detalha como usar a Extensão de Script Personalizado usando o módulo do Azure PowerShell e modelos do Azure Resource Manager, além de detalhar as etapas da solução de problemas em sistemas Windows.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operacional

A Extensão de Script Personalizado para Windows pode ser executada no Windows 10 Client e nas versões Windows Server 2008 R2, 2012, 2012 R2 e 2016.

### <a name="script-location"></a>Local do script

O script precisa ser armazenado no Armazenamento de Blobs do Azure ou em qualquer outro local acessível por meio de uma URL válida.

### <a name="internet-connectivity"></a>Conectividade com a Internet

A Extensão de Script Personalizado para Windows requer que a máquina virtual de destino esteja conectada à Internet. 

## <a name="extension-schema"></a>Esquema de extensão

O JSON a seguir mostra o esquema para a Extensão de Script Personalizado. A extensão requer um local de script (Armazenamento do Azure ou outro local com URL válida) e um comando a ser executado. Se estiver usando o Armazenamento do Azure como a origem do script, o nome e a chave da conta de armazenamento do Azure são obrigatórios. Esses itens devem ser tratados como dados confidenciais e especificados na configuração de definição protegida por extensões. Os dados de configuração protegidos pela extensão da VM do Azure são criptografados, sendo descriptografados apenas na máquina virtual de destino.

```json
{
    "apiVersion": "2015-06-15",
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
            ]
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey"
        }
    }
}
```

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor/Exemplo |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publicador | Microsoft.Compute |
| type | extensions |
| typeHandlerVersion | 1.9 |
| fileUris (por exemplo) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 |
| commandToExecute (por exemplo) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 |
| storageAccountName (por exemplo) | examplestorageacct |
| storageAccountKey (por exemplo) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== |

**Observação** – esses nomes de propriedade diferenciam maiúsculas de minúsculas. Use os nomes conforme mostrado acima para evitar problemas de implantação.

## <a name="template-deployment"></a>Implantação de modelo

Extensões de VM do Azure podem ser implantadas com modelos do Azure Resource Manager. O esquema JSON detalhado na seção anterior pode ser usado em um modelo do Azure Resource Manager para executar a Extensão de Script Personalizado durante uma implantação de modelo do Azure Resource Manager. Um modelo de exemplo que inclui a Extensão de Script Personalizado pode ser encontrado aqui, [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="powershell-deployment"></a>Implantação do PowerShell

O comando `Set-AzureRmVMCustomScriptExtension` pode ser usado para adicionar a Extensão de Script Personalizado a uma máquina virtual existente. Para saber mais, confira [Set-AzureRmVMCustomScriptExtension ](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.compute/v2.1.0/set-azurermvmcustomscriptextension).
```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location myLocation `
    -FileUri myURL `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="troubleshoot"></a>Solucionar problemas

Os dados sobre o estado das implantações de extensão podem ser recuperados no Portal do Azure usando o módulo do Azure PowerShell. Para ver o estado da implantação das extensões de uma determinada VM, execute o comando a seguir.

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

A saída de execução da extensão é registrada nos arquivos localizados no diretório a seguir da máquina virtual de destino.
```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Os arquivos especificados são baixados no diretório a seguir da máquina virtual de destino.
```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
em que `<n>` é um inteiro decimal que pode ser alterado entre as execuções da extensão.  O valor `1.*` corresponde ao valor `typeHandlerVersion` atual e real da extensão.  Por exemplo, o diretório real pode ser `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

Ao executar o comando `commandToExecute`, a extensão terá definido esse diretório (por exemplo, `...\Downloads\2`) como o diretório de trabalho atual. Isso permite o uso de caminhos relativos para localizar os arquivos baixados por meio da propriedade `fileURIs`. Veja a tabela abaixo para obter exemplos.

Como o caminho absoluto do download pode variar ao longo do tempo, é melhor optar por caminhos de arquivo/script relativos na cadeia de caracteres `commandToExecute` sempre que possível. Por exemplo:
```json
    "commandToExecute": "powershell.exe . . . -File './scripts/myscript.ps1'"
```

As informações de caminho após o primeiro segmento do URI são retidas para os arquivos baixados por meio da lista de propriedades `fileUris`.  Conforme mostrado na tabela a seguir, os arquivos baixados são mapeados em subdiretórios de download para refletir a estrutura dos valores `fileUris`.  

#### <a name="examples-of-downloaded-files"></a>Exemplos de Arquivos Baixados

| URI no fileUris | Localização baixada relativa | Localização baixada absoluta * |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

\* Conforme ilustrado acima, os caminhos de diretório absolutos serão alterados durante o tempo de vida da VM, mas não em uma única execução da extensão do CustomScript.

### <a name="support"></a>Suporte

Caso precise de mais ajuda a qualquer momento neste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN] (https://azure.microsoft.com/en-us/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/en-us/support/options/) e selecione Obter suporte. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/en-us/support/faq/).
