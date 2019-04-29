---
title: Extensão de configuração de estado desejado com os modelos do Azure Resource Manager
description: Saiba mais sobre a definição do modelo do Resource Manager para extensão da Configuração do Estado Desejado (DSC) no Azure.
services: virtual-machines-windows
author: bobbytreed
manager: carmonm
tags: azure-resource-manager
keywords: dsc
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 10/05/2018
ms.author: robreed
ms.openlocfilehash: 1bcec37e7642ae0cb5bd68de1426c8cc62085d38
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61475517"
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Extensão de configuração de estado desejado com os modelos do Azure Resource Manager

Este artigo descreve o modelo do Azure Resource Manager para o [manipulador de extensão da Configuração de Estado Desejado (DSC)](dsc-overview.md). Muitos dos exemplos usam **RegistrationURL** (fornecido como cadeia de caracteres) e **RegistrationKey** (fornecido como [PSCredential](/dotnet/api/system.management.automation.pscredential)) para fazer a integração com a Automação do Azure. Consulte os detalhes para obtenção desses valores em [Integração de computadores para gerenciamento pela Configuração de Estado de Automação do Azure – registro seguro](/azure/automation/automation-dsc-onboarding#secure-registration).

> [!NOTE]
> Você pode encontrar exemplos de esquema ligeiramente diferente. A alteração no esquema ocorreu na versão de outubro de 2016. Para obter detalhes, confira [Atualizar de um formato anterior](#update-from-a-previous-format).

## <a name="template-example-for-a-windows-vm"></a>Exemplo de modelo para uma VM do Windows

O snippet a seguir vai na seção **Recursos** do modelo.
A extensão de DSC herda propriedades de extensão padrão.
Para obter mais informações, consulte a [classe VirtualMachineExtension](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension?view=azure-dotnet).

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "Microsoft.Powershell.DSC",
  "apiVersion": "2018-06-30",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.77",
    "autoUpgradeMinorVersion": true,
    "protectedSettings": {
      "Items": {
        "registrationKeyPrivate": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-06-30').Keys[0].value]"
      }
    },
    "settings": {
      "Properties": [
        {
          "Name": "RegistrationKey",
          "Value": {
            "UserName": "PLACEHOLDER_DONOTUSE",
            "Password": "PrivateSettingsRef:registrationKeyPrivate"
          },
          "TypeName": "System.Management.Automation.PSCredential"
        },
        {
          "Name": "RegistrationUrl",
          "Value": "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
          "TypeName": "System.String"
        },
        {
          "Name": "NodeConfigurationName",
          "Value": "[parameters('nodeConfigurationName')]",
          "TypeName": "System.String"
        }
      ]
    }
  }
}
```

## <a name="template-example-for-windows-virtual-machine-scale-sets"></a>Exemplo de modelo para conjunto de dimensionamento de máquinas virtuais do Windows

Um nó de conjunto de dimensionamento de máquinas virtuais tem uma seção **propriedades** com um atributo **VirtualMachineProfile,** extensionProfile.
Em **extensões**, adicione os detalhes para a extensão de DSC.

A extensão de DSC herda propriedades de extensão padrão.
Para obter mais informações, consulte a [classe VirtualMachineScaleSetExtension](/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension?view=azure-dotnet).

```json
"extensionProfile": {
    "extensions": [
      {
        "name": "Microsoft.Powershell.DSC",
        "properties": {
          "publisher": "Microsoft.Powershell",
          "type": "DSC",
          "typeHandlerVersion": "2.77",
          "autoUpgradeMinorVersion": true,
          "protectedSettings": {
            "Items": {
              "registrationKeyPrivate": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-06-30').Keys[0].value]"
            }
          },
          "settings": {
            "Properties": [
              {
                "Name": "RegistrationKey",
                "Value": {
                  "UserName": "PLACEHOLDER_DONOTUSE",
                  "Password": "PrivateSettingsRef:registrationKeyPrivate"
                },
                "TypeName": "System.Management.Automation.PSCredential"
              },
              {
                "Name": "RegistrationUrl",
                "Value": "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
                "TypeName": "System.String"
              },
              {
                "Name": "NodeConfigurationName",
                "Value": "[parameters('nodeConfigurationName')]",
                "TypeName": "System.String"
              }
            ]
          }
        }
      }
    ]
  }
```

## <a name="detailed-settings-information"></a>Informações de configuração detalhadas

Use o esquema a seguir na seção **configurações** da extensão de DSC do Azure em um modelo do Resource Manager.

Para obter uma lista de argumentos disponíveis para o script de configuração padrão, consulte [Script de configuração padrão](#default-configuration-script).

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

| Nome da propriedade | Type | DESCRIÇÃO |
| --- | --- | --- |
| settings.wmfVersion |string |Especifica a versão do Windows Management Framework (WMF) que deve ser instalada em sua VM. Configurar essa propriedade como **latest** instala a versão mais recente de WMF. Atualmente, os únicos valores possíveis para essa propriedade são **4.0**, **5.0**, **5.1** e **o mais recente**. Esses valores possíveis estão sujeitos a atualizações. O valor padrão é **latest**. |
| settings.configuration.url |string |Especifica o local da URL de onde baixar seu arquivo .zip de configuração DSC. Se a URL fornecida exigir um token SAS para acesso, defina a propriedade **protectedSettings.configurationUrlSasToken** como o valor do seu token de SAS. Esta propriedade será necessária se **settings.configuration.script** ou **settings.configuration.function** estiverem definidas. Se nenhum valor for fornecido para essas propriedades, a extensão chama o script de configuração padrão para definir os metadados do LCM (Location Configuration Manager) e os argumentos devem ser fornecidos. |
| settings.configuration.script |string |Especifica o nome do arquivo do script que contém a definição de sua configuração DSC. Esse script deve estar na pasta raiz do arquivo .zip que é baixado da URL especificada pela propriedade **settings.configuration.url**. Esta propriedade é necessária se **settings.configuration.url** ou **settings.configuration.script** estiverem definidas. Se nenhum valor for fornecido para essas propriedades, a extensão chama o script de configuração padrão para definir os metadados do LCM e os argumentos devem ser fornecidos. |
| settings.configuration.function |string |Especifica o nome da configuração DSC. A configuração que é denominada deve estar contida no script definido por **settings.configuration.script**. Esta propriedade será necessária se **settings.configuration.script.url** ou **settings.configuration.function** estiverem definidas. Se nenhum valor for fornecido para essas propriedades, a extensão chama o script de configuração padrão para definir os metadados do LCM e os argumentos devem ser fornecidos. |
| settings.configurationArguments |Coleção |Define os parâmetros que você deseja passar para a configuração de DSC. Essa propriedade não é criptografada. |
| settings.configurationData.url |string |Especifica a URL de onde baixar o arquivo de dados de configuração (.psd1) para usar como entrada para a sua configuração de DSC. Se a URL fornecida exigir um token SAS para acesso, defina a propriedade **protectedSettings.configurationDataUrlSasToken** como o valor do seu token de SAS. |
| settings.privacy.dataCollection |string |Habilita ou desabilita a coleta de telemetria. Os únicos valores possíveis para essa propriedade são **Enable**, **Disable**, **''** ou **$null**. Deixar esta propriedade em branco ou nulo permite telemetria. O valor padrão é **''**. Para obter mais informações, consulte [coleta de dados de extensão de DSC do Azure](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/). |
| settings.advancedOptions.downloadMappings |Coleção |Define os locais alternativos de onde baixar o WMF. Para obter mais informações, consulte [extensão de DSC do Azure 2.8 e como mapear downloads das dependências de extensão para seu próprio local](https://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx). |
| protectedSettings.configurationArguments |Coleção |Define os parâmetros que você deseja passar para a configuração de DSC. Essa propriedade é criptografada. |
| protectedSettings.configurationUrlSasToken |string |Especifica o token SAS a usar para acessar a URL definida por **settings.configuration.url**. Essa propriedade é criptografada. |
| protectedSettings.configurationDataUrlSasToken |string |Especifica o token SAS a usar para acessar a URL definida por **settings.configurationData.url**. Essa propriedade é criptografada. |

## <a name="default-configuration-script"></a>Script de configuração padrão

Para obter mais informações sobre os seguintes valores, consulte [Configurações Básicas do Local Configuration Manager](/powershell/dsc/metaconfig#basic-settings).
Você pode usar o script de configuração padrão de extensão DSC para configurar apenas as propriedades do LCM que são listadas na tabela a seguir.

| Nome da propriedade | Type | DESCRIÇÃO |
| --- | --- | --- |
| protectedSettings.configurationArguments.RegistrationKey |PSCredential |Propriedade exigida. Especifica a chave que é usada para um nó para registrar com o serviço de Automação do Azure como a senha de um objeto de credencial do PowerShell. Esse valor pode ser descoberto automaticamente usando o método **listkeys** em relação à conta de Automação.  Veja o [exemplo](#example-using-referenced-azure-automation-registration-values). |
| settings.configurationArguments.RegistrationUrl |string |Propriedade exigida. Especifica a URL do ponto de extremidade de Automação em que o nó tenta registrar. Esse valor pode ser descoberto automaticamente usando o método de **referência** em relação à conta de Automação. |
| settings.configurationArguments.NodeConfigurationName |string |Propriedade exigida. Especifica a configuração de nó na conta de Automação para atribuir ao nó. |
| settings.configurationArguments.ConfigurationMode |string |Especifica o modo para o LCM. As opções válidas incluem **ApplyOny**, **ApplyandMonitior** e **ApplyandAutoCorrect**.  O valor padrão é **ApplyandMonitor**. |
| settings.configurationArguments.RefreshFrequencyMins | uint32 | Especifica com que frequência o LCM tenta verificar a conta de automação para atualizações.  O valor padrão é **30**.  O valor mínimo é **15**. |
| settings.configurationArguments.ConfigurationModeFrequencyMins | uint32 | Especifica com que frequência o LCM valida a configuração atual. O valor padrão é **15**. O valor mínimo é **15**. |
| settings.configurationArguments.RebootNodeIfNeeded | boolean | Especifica se um nó pode ser reinicializado automaticamente se uma operação de DSC solicitar. O valor padrão é **false**. |
| settings.configurationArguments.ActionAfterReboot | string | Especifica o que acontece após uma reinicialização ao aplicar uma configuração. As opções válidas são **ContinueConfiguration** e **StopConfiguration**. O valor padrão é **ContinueConfiguration**. |
| settings.configurationArguments.AllowModuleOverwrite | boolean | Especifica se o LCM substitui os módulos existentes no nó. O valor padrão é **false**. |

## <a name="settings-vs-protectedsettings"></a>settings vs.protectedSettings

Todas as configurações foram salvas em um arquivo de texto de configurações na VM.
Propriedades listadas em **Configurações** são propriedades públicas.
Propriedades públicas não são criptografadas no arquivo de texto de configurações.
Propriedades em **protectedSettings** são criptografadas com um certificado e não são mostradas em texto sem formatação no arquivo de configurações na VM.

Se a configuração precisar de credenciais, você pode incluir as credenciais em **protectedSettings**:

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

## <a name="example-configuration-script"></a>Exemplo de script de configuração

O exemplo a seguir mostra o comportamento padrão para a extensão de DSC, que é fornecer configurações de metadados para o LCM e registrar com o serviço de DSC de Automação.
Os argumentos de configuração são necessários.
Os argumentos de configuração são passados para o script de configuração padrão para definir metadados do LCM.

```json
"settings": {
    "RegistrationUrl" : "[parameters('registrationUrl1')]",
    "NodeConfigurationName" : "nodeConfigurationNameValue1"
},
"protectedSettings": {
    "configurationArguments": {
        "RegistrationKey": {
            "userName": "NOT_USED",
            "Password": "registrationKey"
        }
    }
}
```

## <a name="example-using-the-configuration-script-in-azure-storage"></a>Exemplo usando o script de configuração no Armazenamento do Azure

O exemplo a seguir é da [visão geral de manipulador de extensão de DSC](dsc-overview.md).
Este exemplo usa modelos do Resource Manager, em vez de cmdlets para implantar a extensão.
Salvar a configuração de IisInstall.ps1, coloque-o em um arquivo. zip (exemplo: `iisinstall.zip`) e, em seguida, carregue o arquivo em uma URL acessível.
Este exemplo usa o Armazenamento de Blobs do Azure, mas você pode baixar os arquivos .zip de qualquer local aleatório.

No modelo do Resource Manager, o código a seguir instrui a VM a baixar o arquivo correto e, em seguida, executar a função apropriada do PowerShell:

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/iisinstall.zip",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="example-using-referenced-azure-automation-registration-values"></a>Exemplo usando os valores de registro referenciados da Automação do Azure

O exemplo a seguir obtém a **RegistrationUrl** e a **RegistrationKey** referenciando as propriedades da conta de Automação do Azure e usando o método **listkeys** para recuperar a Chave Primária (0).  Neste exemplo, os parâmetros **automationAccountName** e **NodeConfigName** foram fornecidos ao modelo.

```json
"settings": {
    "RegistrationUrl" : "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
    "NodeConfigurationName" : "[parameters('NodeConfigName')]"
},
"protectedSettings": {
    "configurationArguments": {
        "RegistrationKey": {
            "userName": "NOT_USED",
            "Password": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-01-15').Keys[0].value]"
        }
    }
}
```

## <a name="update-from-a-previous-format"></a>Atualizar de um formato anterior

Todas as configurações em um formato anterior da extensão (e que têm as propriedades públicas **ModulesUrl**, **ModuleSource**, **ModuleVersion**, **ConfigurationFunction**, **SasToken** ou **Properties**) serão adaptadas automaticamente ao formato atual da extensão.
Elas são executadas como antes.

O esquema a seguir mostra como se parecia o esquema de configurações anterior:

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties": {
        "ParameterToConfigurationFunction1": "Value1",
        "ParameterToConfigurationFunction2": "Value2",
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

| Nome da Propriedade Atual | Equivalente ao esquema anterior |
| --- | --- |
| settings.wmfVersion |settings.wmfVersion |
| settings.configuration.url |settings.ModulesUrl |
| settings.configuration.script |Primeira parte de settings.ConfigurationFunction (antes de \\\\) |
| settings.configuration.function |Segunda parte de settings.ConfigurationFunction (depois de \\\\) |
| settings.configuration.module.name | settings.ModuleSource |
| settings.configuration.module.version | settings.ModuleVersion |
| settings.configurationArguments |settings.Properties |
| settings.configurationData.url |protectedSettings.DataBlobUri (sem token SAS) |
| settings.privacy.dataCollection |settings.Privacy.dataCollection |
| settings.advancedOptions.downloadMappings |settings.advancedOptions.downloadMappings |
| protectedSettings.configurationArguments |protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken |settings.SasToken |
| protectedSettings.configurationDataUrlSasToken |Token SAS de protectedSettings.DataBlobUri |

## <a name="troubleshooting"></a>solução de problemas

Confira alguns erros que você pode encontrar e como corrigi-los.

### <a name="invalid-values"></a>Valores inválidos

"Privacy.dataCollection é '{0}'.
Os únicos valores possíveis são '', 'Enable' e 'Disable'".
"WmfVersion é '{0}'.
Únicos valores possíveis são... e 'latest'".

**Problema**: Um valor fornecido não é permitido.

**Solução**: Altere o valor inválido para um valor válido.
Para saber mais, consulte a tabela em [Detalhes](#details).

### <a name="invalid-url"></a>URL inválida

"ConfigurationData.url é '{0}'. Essa não é uma URL válida" "DataBlobUri é '{0}'. Essa não é uma URL válida" "Configuration.url é '{0}'. Essa não é uma URL válida"

**Problema**: Uma URL fornecida não é válido.

**Solução**: Verifique todas as URLs fornecidas.
Certifique-se de que todas as URLs resolvem para locais válidos que a extensão pode acessar no computador remoto.

### <a name="invalid-registrationkey-type"></a>Tipo RegistrationKey inválido

“Tipo inválido para o parâmetro RegistrationKey do tipo PSCredential.”

**Problema**: O *RegistrationKey* valor em protectedSettings.configurationArguments não pode ser fornecido como qualquer tipo que não seja uma PSCredential.

**Solução**: Altere a entrada de protectedSettings.configurationArguments para RegistrationKey para um tipo PSCredential usando o seguinte formato:

```json
"configurationArguments": {
    "RegistrationKey": {
        "userName": "NOT_USED",
        "Password": "RegistrationKey"
    }
}
```

### <a name="invalid-configurationargument-type"></a>Tipo ConfigurationArgument inválido

"Tipo configurationArgument inválido {0}"

**Problema**: O *ConfigurationArguments* propriedade não pode ser resolvida para um **tabela de Hash** objeto.

**Solução**: Verifique sua *ConfigurationArguments* propriedade um **tabela de Hash**.
Siga o formato fornecido no exemplo anterior. Fique atento às chaves, vírgulas e aspas.

### <a name="duplicate-configurationarguments"></a>ConfigurationArguments duplicado

"Argumentos '{0}' duplicados encontrados em configurationArguments públicos e protegidos"

**Problema**: O *ConfigurationArguments* nas configurações públicas e o *ConfigurationArguments* nas configurações protegidas têm propriedades com o mesmo nome.

**Solução**: Remova uma das propriedades duplicadas.

### <a name="missing-properties"></a>Propriedades ausentes

“settings.Configuration.function requer a especificação de settings.configuration.url ou de settings.configuration.module”

“settings.Configuration.url requer a especificação de settings.configuration.script”

“settings.Configuration.script requer a especificação de settings.configuration.url”

“settings.Configuration.url requer a especificação de settings.configuration.function”

“protectedSettings.ConfigurationUrlSasToken requer a especificação de settings.configuration.url”

“protectedSettings.ConfigurationDataUrlSasToken requer a especificação de settings.configurationData.url”

**Problema**: Uma propriedade definida precisa de outra propriedade, que está ausente.

**Soluções**:

- Forneça a propriedade ausente.
- Remova a propriedade que precisa da propriedade ausente.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [Uso de conjuntos de dimensionamento de máquinas virtuais com a extensão de DSC do Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md).
- Encontre mais detalhes sobre [Gerenciamento de credenciais seguras do DSC](dsc-credentials.md).
- Obtenha uma [introdução ao manipulador de extensões DSC do Azure](dsc-overview.md).
- Para saber mais sobre a DSC do PowerShell, acesse o [centro de documentação do PowerShell](/powershell/dsc/overview).
