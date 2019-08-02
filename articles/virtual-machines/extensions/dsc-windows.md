---
title: Manipulador de extensão da Configuração de Estado Desejado do Azure | Microsoft Docs
description: Atualizar e aplicar uma configuração de DSC do PowerShell em uma VM do Azure usando a extensão DSC
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: windows
ms.workload: ''
ms.date: 03/26/2018
ms.author: robreed
ms.openlocfilehash: 89d652f440e97650b7e7ac63cccc7fde75d7204a
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798275"
---
# <a name="powershell-dsc-extension"></a>Extensão de DSC do PowerShell

## <a name="overview"></a>Visão geral

A extensão DSC PowerShell para Windows é publicada e recebe suporte da Microsoft. A extensão atualiza e aplica uma configuração de DSC do PowerShell em uma VM do Azure. A extensão de DSC chama a DSC do PowerShell para aplicar a configuração DSC recebida na VM. Este documento detalha as plataformas com opções de plataformas, configurações e implantação com suporte para a extensão da máquina virtual do DSC para Windows.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operacional

A Extensão DSC suporta os seguintes OS

Windows Server 2019, Windows Server 2016, Windows Server 2012R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10

### <a name="internet-connectivity"></a>Conectividade com a Internet

A extensão de DSC para Windows requer que a máquina virtual de destino é capaz de se comunicar com o Azure e o local do pacote de configuração (arquivo. zip), se ele estiver armazenado em um local fora do Azure. 

## <a name="extension-schema"></a>Esquema de extensão

O JSON a seguir mostra o esquema que serve para a parte das configurações da extensão DSC em um modelo do Azure Resource Manager. 

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "Microsoft.Powershell.DSC",
  "apiVersion": "2018-10-01",
  "location": "<location>",
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.77",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "wmfVersion": "latest",
        "configuration": {
            "url": "http://validURLToConfigLocation",
            "script": "ConfigurationScript.ps1",
            "function": "ConfigurationFunction"
        },
        "configurationArguments": {
            "argument1": "Value1",
            "argument2": "Value2"
        },
        "configurationData": {
            "url": "https://foo.psd1"
        },
        "privacy": {
            "dataCollection": "enable"
        },
        "advancedOptions": {
            "forcePullAndApply": false,
            "downloadMappings": {
                "specificDependencyKey": "https://myCustomDependencyLocation"
            }
        } 
    },
    "protectedSettings": {
        "configurationArguments": {
            "parameterOfTypePSCredential1": {
                "userName": "UsernameValue1",
                "password": "PasswordValue1"
            },
            "parameterOfTypePSCredential2": {
                "userName": "UsernameValue2",
                "password": "PasswordValue2"
            }
        },
        "configurationUrlSasToken": "?g!bber1sht0k3n",
        "configurationDataUrlSasToken": "?dataAcC355T0k3N"
    }
  }
}
```

### <a name="property-values"></a>Valores de propriedade

| NOME | Valor/Exemplo | Tipo de dados |
| ---- | ---- | ---- |
| apiVersion | 01-10-2018 | date |
| publisher | Microsoft.Powershell.DSC | string |
| type | DSC | string |
| typeHandlerVersion | 2.77 | int |

### <a name="settings-property-values"></a>Valores da Propriedade de Configurações

| NOME | Tipo de dados | DESCRIÇÃO
| ---- | ---- | ---- |
| settings.wmfVersion | string | Especifica a versão do Windows Management Framework que deve ser instalada em sua VM. Configurar essa propriedade como 'latest' instalará a versão mais atualizada do WMF. Os únicos valores possíveis atualmente para essa propriedade são ‘4.0’, ‘5.0’, e a mais recente. Esses valores possíveis estão sujeitos a atualizações. O valor padrão é ‘latest’. |
| settings.configuration.url | string | Especifica o local da URL de onde baixar o arquivo zip configuração DSC. Se a URL fornecida exigir um token SAS para acesso, será necessário definir a propriedade protectedSettings.configurationUrlSasToken como o valor do token de SAS. Esta propriedade será necessária se settings.configuration.script e/ou settings.configuration.function estiverem definidas.
| settings.configuration.script | string | Especifica o nome do arquivo do script que contém a definição de sua configuração DSC. Esse script deve estar na pasta raiz do arquivo zip baixado da URL especificada pela propriedade configuration.url. Esta propriedade é necessária se settings.configuration.url e/ou settings.configuration.script estiverem definidas.
| settings.configuration.function | string | Especifica o nome da configuração DSC. A configuração denominada deve estar contida no script definido por configuration.script. Esta propriedade será necessária se settings.configuration.script.url e/ou settings.configuration.function estiverem definidas.
| settings.configurationArguments | Collection | Define os parâmetros que você deseja passar para a configuração de DSC. Esta propriedade não será criptografada.
| settings.configurationData.url | string | Especifica a URL de onde baixar o arquivo de dados de configuração (.pds1) para usar como entrada para a sua configuração de DSC. Se a URL fornecida exigir um token SAS para acesso, será necessário definir a propriedade protectedSettings.configurationDataUrlSasToken como o valor do token de SAS.
| settings.privacy.dataEnabled | string | Habilita ou desabilita a coleta de telemetria. Os únicos valores possíveis para essa propriedade são ‘Enable’, ‘Disable’, ”, ou $null. Deixar esta propriedade em branco ou nulo permitirá telemetria
| settings.advancedOptions.forcePullAndApply | Bool | Esta configuração é projetada para aprimorar a experiência de trabalhar com a extensão para registrar os nós com o DSC de automação do Azure.  Se o valor for `$true`, aguardará a extensão para a primeira execução da configuração extraída do serviço antes de retornar êxito/falha.  Se o valor é definido como $false, o status retornado pela extensão só fará referência a se o nó foi registrado com a configuração de estado de automação do Azure com êxito e a configuração de nó não será executada durante o registro.
| settings.advancedOptions.downloadMappings | Collection | Define locais alternativos para fazer o download de dependências como WMF e .NET

### <a name="protected-settings-property-values"></a>Valores da Propriedade de Configurações Protegidos

| NOME | Tipo de dados | DESCRIÇÃO
| ---- | ---- | ---- |
| protectedSettings.configurationArguments | string | Define os parâmetros que você deseja passar para a configuração de DSC. Esta propriedade será criptografada. |
| protectedSettings.configurationUrlSasToken | string | Especifica o token SAS para acessar a URL definida por configuration.url. Esta propriedade será criptografada. |
| protectedSettings.configurationDataUrlSasToken | string | Especifica o token SAS para acessar a URL definida por configurationData.url. Esta propriedade será criptografada. |


## <a name="template-deployment"></a>Implantação de modelo

Extensões de VM do Azure podem ser implantadas com modelos do Azure Resource Manager.
Modelos são ideais ao implantar uma ou mais máquinas virtuais que exigem configuração pós-implantação.
Um modelo do Resource Manager que inclui a extensão de DSC para Windows pode ser encontrado na [Galeria de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-automation-configuration/nested/provisionServer.json#L91).

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="troubleshoot"></a>Solução de problemas

Dados sobre o estado das implantações de extensão podem ser recuperados do Portal do Azure usando a CLI do Azure. Para ver o estado da implantação das extensões de uma determinada VM, execute o comando a seguir usando a CLI do Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Pacote de extensão é baixado e implantado para esse local na VM do Azure
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}
```

Arquivo de status de extensão contém o status de sub-rotina e códigos de status de êxito/erro juntamente com o erro detalhado e uma descrição para cada execução da extensão.
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}\Status\{0}.Status  -> {0} being the sequence number
```

Logs de saída de extensão são registrados no seguinte diretório:

```
C:\WindowsAzure\Logs\Plugins\{Extension_Name}\{Extension_Version}
```

### <a name="error-codes-and-their-meanings"></a>Códigos de erro e seus significados

| Código do Erro | Significado | Ação possível |
| :---: | --- | --- |
| 1000 | Erro genérico | A mensagem de erro é fornecida pela exceção específica em logs de extensão |
| 52 | Erro de Instalação da Extensão | A mensagem de erro é fornecida pela exceção específica |
| 1002 | Erro de instalação Wmf | Erro ao instalar WMF. |
| 1004 | Pacote Zip Inválido | Zip inválido ; Erro ao desempacotar o zip |
| 1100 | Erro de Argumento | Indica um problema na entrada fornecida pelo usuário. A mensagem de erro é fornecida pela exceção específica|


### <a name="support"></a>Suporte

Caso precise de mais ajuda em qualquer ponto deste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).
