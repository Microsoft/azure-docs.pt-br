---
title: "Modelo do Resource Manager para Configuração do Estado Desejado| Microsoft Docs"
description: "Definição do modelo do Resource Manager para Configuração de Estado Desejado no Azure com exemplos e solução de problemas"
services: virtual-machines-windows
documentationcenter: 
author: zjalexander
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
keywords: 
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 09/15/2016
ms.author: zachal
ms.openlocfilehash: 4292f9d8cd181073fdf0adff99fcb9624e0e9f55
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="windows-vmss-and-desired-state-configuration-with-azure-resource-manager-templates"></a>Windows VMSS e configuração de estado desejado com modelos do Azure Resource Manager
Este artigo descreve o modelo do Resource Manager para o [manipulador de extensão da Configuração de Estado Desejado](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

## <a name="template-example-for-a-windows-vm"></a>Exemplo de modelo para uma VM do Windows
O trecho a seguir vai na seção Recursos do modelo.

```json
            "name": "Microsoft.Powershell.DSC",
            "type": "extensions",
             "location": "[resourceGroup().location]",
             "apiVersion": "2015-06-15",
             "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": true,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "dscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }
              }

```

## <a name="template-example-for-windows-vmss"></a>Exemplo de modelo para VMSS do Windows
Um nó VMSS tem uma seção "properties" com o atributo "VirtualMachineProfile", "extensionProfile". O DSC é adicionado sob "extensões". 

```json
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": true,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
        }
```

## <a name="detailed-settings-information"></a>Informações de configuração detalhadas
O esquema a seguir serve para a parte das configurações da extensão DSC do Azure em um modelo do Azure Resource Manager.

```json

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
    "downloadMappings": {
      "customWmfLocation": "http://myWMFlocation"
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

```

## <a name="details"></a>Detalhes
| Nome da Propriedade | Tipo | Descrição |
| --- | --- | --- |
| settings.wmfVersion |string |Especifica a versão do Windows Management Framework que deve ser instalada em sua VM. Configurar essa propriedade como 'latest' instala a versão mais atualizada do WMF. Os únicos valores possíveis no momento para essa propriedade são **'4.0', '5.0', ' 5.0PP' e 'latest'**. Esses valores possíveis estão sujeitos a atualizações. O valor padrão é 'latest'. |
| settings.configuration.url |string |Especifica o local da URL de onde baixar o arquivo zip configuração DSC. Se a URL fornecida exigir um token SAS para acesso, será necessário definir a propriedade protectedSettings.configurationUrlSasToken como o valor do token de SAS. Esta propriedade será necessária se settings.configuration.script e/ou settings.configuration.function estiverem definidas. |
| settings.configuration.script |string |Especifica o nome do arquivo do script que contém a definição de sua configuração DSC. Esse script deve estar na pasta raiz do arquivo zip baixado da URL especificada pela propriedade configuration.url. Esta propriedade é necessária se settings.configuration.url e/ou settings.configuration.script estiverem definidas. |
| settings.configuration.function |string |Especifica o nome da configuração DSC. A configuração denominada deve estar contida no script definido por configuration.script. Esta propriedade será necessária se settings.configuration.script.url e/ou settings.configuration.function estiverem definidas. |
| settings.configurationArguments |Coleção |Define os parâmetros que você deseja passar para a configuração de DSC. Essa propriedade não é criptografada. |
| settings.configurationData.url |string |Especifica a URL de onde baixar o arquivo de dados de configuração (.psd1) para usar como entrada para a sua configuração de DSC. Se a URL fornecida exigir um token SAS para acesso, será necessário definir a propriedade protectedSettings.configurationDataUrlSasToken como o valor do token de SAS. |
| settings.privacy.dataEnabled |string |Habilita ou desabilita a coleta de telemetria. Os únicos valores possíveis para essa propriedade são **'Enable', 'Disable', ou '$null'**. Deixar esta propriedade em branco ou nulo permite telemetria. O valor padrão é ''. [Mais informações](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/) |
| settings.advancedOptions.downloadMappings |Coleção |Define os locais alternativos de onde baixar o WMF. [Mais informações](http://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx) |
| protectedSettings.configurationArguments |Coleção |Define os parâmetros que você deseja passar para a configuração de DSC. Essa propriedade é criptografada. |
| protectedSettings.configurationUrlSasToken |string |Especifica o token SAS para acessar a URL definida por configuration.url. Essa propriedade é criptografada. |
| protectedSettings.configurationDataUrlSasToken |string |Especifica o token SAS para acessar a URL definida por configurationData.url. Essa propriedade é criptografada. |

## <a name="settings-vs-protectedsettings"></a>Settings versus ProtectedSettings
Todas as configurações foram salvas em um arquivo de texto de configurações na VM.
Propriedades em 'settings' são propriedades públicas, pois não são criptografadas no arquivo de texto de configurações.
Propriedades em 'protectedSettings' são criptografadas com um certificado e não são mostradas em texto sem formatação neste arquivo na VM.

Se a configuração precisar de credenciais, elas poderão ser incluídas em protectedSettings:

```json
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
               "userName": "UsernameValue1",
               "password": "PasswordValue1"
        }
    }
}
```

## <a name="example"></a>Exemplo
O exemplo a seguir é derivado da seção "Introdução" da página [Visão geral do manipulador de extensão de DSC](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
Este exemplo usa modelos do Resource Manager, em vez de cmdlets para implantar a extensão. Salve a configuração de "IisInstall.ps1", coloque-o em um arquivo .ZIP e carregue-o em uma URL acessível. Este exemplo usa o Armazenamento de Blobs do Azure, mas é possível baixar os arquivos .ZIP de qualquer local aleatório.

No modelo do Azure Resource Manager, o código a seguir instrui a VM a baixar o arquivo correto e executar a função apropriada do PowerShell:

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
    } 
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="updating-from-the-previous-format"></a>Atualização do formato anterior
Todas as configurações no formato anterior (que contém as propriedades públicas ModulesUrl, ConfigurationFunction, SasToken ou Properties) serão automaticamente adaptadas ao formato atual e executadas exatamente como antes.

O esquema de configurações a seguir se parece com o esquema de configurações anterior:

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties":  {
        "ParameterToConfigurationFunction1":  "Value1",
        "ParameterToConfigurationFunction2":  "Value2",
        "ParameterOfTypePSCredential1": {
            "UserName": "UsernameValue1",
            "Password": "PrivateSettingsRef:Key1" 
        },
        "ParameterOfTypePSCredential2": {
            "UserName": "UsernameValue2",
            "Password": "PrivateSettingsRef:Key2"
        }
    }
},
"protectedSettings": { 
    "Items": {
        "Key1": "PasswordValue1",
        "Key2": "PasswordValue2"
    },
    "DataBlobUri": "https://UrlToConfigurationDataWithOptionalSasToken.psd1"
}
```

Veja como formato anterior se adapta ao formato atual:

| Nome da Propriedade | Equivalente ao esquema anterior |
| --- | --- |
| settings.wmfVersion |settings.wmfVersion |
| settings.configuration.url |settings.ModulesUrl |
| settings.configuration.script |Primeira parte de settings.ConfigurationFunction (antes de '\\\\') |
| settings.configuration.function |Segunda parte de settings.ConfigurationFunction (depois de '\\\\') |
| settings.configurationArguments |settings.Properties |
| settings.configurationData.url |protectedSettings.DataBlobUri (sem token SAS) |
| settings.privacy.dataEnabled |settings.privacy.dataEnabled |
| settings.advancedOptions.downloadMappings |settings.advancedOptions.downloadMappings |
| protectedSettings.configurationArguments |protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken |settings.SasToken |
| protectedSettings.configurationDataUrlSasToken |Token SAS de protectedSettings.DataBlobUri |

## <a name="troubleshooting---error-code-1100"></a>Solução de problemas - Código de erro 1100
Código de erro 1100 indica que há um problema com a entrada do usuário para a extensão de DSC.
O texto desses erros é variável e pode mudar.
Confira alguns erros que você pode encontrar e como corrigi-los.

### <a name="invalid-values"></a>Valores inválidos
"Privacy.dataCollection é '{0}'. Os únicos valores possíveis são '', 'Enable' e 'Disable'" "WmfVersion é '{0}'. Únicos valores possíveis são... e 'latest'"

Problema: um valor fornecido não é permitido.

Solução: altere o valor inválido para um valor válido. Consulte a tabela na seção Detalhes.

### <a name="invalid-url"></a>URL inválida
"ConfigurationData.url é '{0}'. Essa não é uma URL válida" "DataBlobUri é '{0}'. Essa não é uma URL válida" "Configuration.url é '{0}'. Essa não é uma URL válida"

Problema: uma URL fornecida não é válida.

Solução: verifique todas as URLs fornecidas. Certifique-se de que todas as URLs resolvem para locais válidos que a extensão pode acessar no computador remoto.

### <a name="invalid-configurationargument-type"></a>Tipo ConfigurationArgument inválido
"Tipo configurationArguments inválido {0}"

Problema: a propriedade ConfigurationArguments não pode resolver um objeto Hashtable. 

Solução: torne sua propriedade ConfigurationArguments uma tabela de hash. Siga o formato fornecido no exemplo anterior. Fique atento às chaves, vírgulas e aspas.

### <a name="duplicate-configurationarguments"></a>ConfigurationArguments duplicado
"Argumentos '{0}' duplicados encontrados em configurationArguments públicos e protegidos"

Problema: ConfigurationArguments nas configurações públicas, e ConfigurationArguments nas configurações protegidas contêm propriedades com o mesmo nome.

Solução: remova uma das propriedades duplicadas.

### <a name="missing-properties"></a>Propriedades ausentes
"Configuration.function exige que configuration.url ou configuration.module seja especificado"

"Configuration.url exige que configuration.script seja especificado"

"Configuration.script exige que configuration.url seja especificado"

"Configuration.url exige que configuration.function seja especificado"

"ConfigurationUrlSasToken exige que configuration.url seja especificado"

"ConfigurationDataUrlSasToken exige que configurationData.url seja especificado"

Problema: uma propriedade definida precisa de outra propriedade que não está presente.

Soluções: 

* Forneça a propriedade ausente.
* Remova a propriedade que precisa da propriedade ausente.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre DSC e conjuntos de escala de máquina virtual em [Uso de Conjuntos de Escala de Máquina Virtual com a Extensão de DSC do Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md)

Encontre mais detalhes sobre [Gerenciamento de credenciais seguras do DSC](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Para saber mais sobre o manipulador de extensões DSC do Azure, confira [Introduction to the Azure Desired State Configuration extension handler (Introdução ao manipulador de extensões Configuração de Estado Desejado do Azure)](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Para saber mais sobre a DSC do PowerShell, [visite o centro de documentação do PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

