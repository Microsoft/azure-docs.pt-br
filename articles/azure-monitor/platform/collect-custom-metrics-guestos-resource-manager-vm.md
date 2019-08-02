---
title: Enviar métricas do SO convidado para o repositório de métricas do Azure Monitor usando um modelo do Resource Manager para uma máquina virtual do Windows
description: Enviar métricas do SO convidado para o repositório de métricas do Azure Monitor usando um modelo do Resource Manager para uma máquina virtual do Windows
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 85f7395cbfa4ef2ba6ab448c9541b3f107eb0e96
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68249812"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine"></a>Enviar métricas do SO convidado para o repositório de métricas do Azure Monitor usando um modelo do Resource Manager para uma máquina virtual do Windows

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Usando a [extensão de Diagnóstico](diagnostics-extension-overview.md) do Azure Monitor, você pode coletar logs e métricas do SO (sistema operacional) convidado executado como parte de uma máquina virtual, do serviço de nuvem ou do cluster do Service Fabric. A extensão pode enviar telemetria para [vários locais diferentes](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json).

Este artigo descreve o processo para enviar métricas de desempenho do SO convidado de uma máquina virtual do Windows para o armazenamento de dados do Azure Monitor. Começando com o Diagnóstico versão 1.11, você pode gravar as métricas diretamente no armazenamento de métricas do Azure Monitor, no qual as métricas da plataforma padrão já são coletadas.

Armazená-las nessa localização permite que você acesse as mesmas ações para as métricas da plataforma. As ações incluem alertas quase em tempo real, criação de gráficos, roteamento, acesso por meio de uma API REST e muito mais. Anteriormente, a Extensão de diagnóstico gravava no Armazenamento do Azure, mas não no armazenamento de dados do Azure Monitor.

Se você é novo nos modelos do Resource Manager, aprenda sobre [implantações de modelos](../../azure-resource-manager/resource-group-overview.md) e sua estrutura e sintaxe.

## <a name="prerequisites"></a>Pré-requisitos

- Sua assinatura deve ser registrada com [Microsoft. Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services).

- Você precisará ter o [Azure PowerShell](/powershell/azure) ou o [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) instalado.


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Configurar o Azure Monitor como um coletor de dados
A extensão do Diagnóstico do Azure usa um recurso chamado "coletores de dados" para encaminhar as métricas e os logs para locais diferentes. As etapas a seguir mostram como usar um modelo do Resource Manager e o PowerShell para implantar uma VM usando o novo coletor de dados do "Azure Monitor".

## <a name="author-resource-manager-template"></a>Criar modelo do Resource Manager
Para este exemplo, você pode usar um modelo de exemplo disponível publicamente. Os modelos iniciais estão em https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows.

- **Azuredeploy.json** é um modelo do Resource Manager pré-configurado para a implantação de uma máquina virtual.

- **Azuredeploy.parameters.json** é um arquivo de parâmetros que armazena informações como o nome de usuário e a senha que você deseja definir para sua VM. Durante a implantação, o modelo do Resource Manager usa os parâmetros definidos no arquivo.

Baixe e salve os dois arquivos localmente.

### <a name="modify-azuredeployparametersjson"></a>Modificar azuredeploy.parameters.json
Abra o arquivo *azuredeploy.parameters.json*

1. Insira valores de **adminUsername** e **adminPassword** para a VM. Esses parâmetros são usados para acesso remoto à VM. Para evitar que sua VM seja sequestrada, NÃO use os valores fornecidos neste modelo. Os bots examinam a Internet em busca de nomes de usuário e senhas em repositórios GitHub públicos. É provável que eles testem as VMs com esses padrões.

1. Crie um dnsname exclusivo para a VM.

### <a name="modify-azuredeployjson"></a>Modificar azuredeploy.json

Abra o arquivo *azuredeploy.json*

Adicione uma ID da conta de armazenamento à seção **variables** do modelo após a entrada para **storageAccountName**.

```json
// Find these lines.
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",

// Add this line directly below.
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
```

Adicione essa extensão de MSI (Identidade de Serviço Gerenciada) ao modelo na parte superior da seção **resources**. A extensão garante que o Azure Monitor aceite as métricas que estão sendo emitidas.

```json
//Find this code.
"resources": [
// Add this code directly below.
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'), '/', 'WADExtensionSetup')]",
        "apiVersion": "2017-12-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]" ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    },
```

Adicione a configuração de **identity** ao recurso da VM para garantir que o Azure atribua uma identidade do sistema à extensão de MSI. Essa etapa garante que a VM possa emitir métricas de convidado sobre si mesma para o Azure Monitor.

```json
// Find this section
                "subnet": {
            "id": "[variables('subnetRef')]"
            }
        }
        }
    ]
    }
},
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    // add these 3 lines below
    "identity": {
    "type": "SystemAssigned"
    },
    //end of added lines
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
    "hardwareProfile": {
    ...
```

Adicione a configuração a seguir para habilitar a extensão de diagnóstico em uma Máquina Virtual do Windows. Para uma máquina virtual simples baseada no Resource Manager, podemos adicionar a configuração de extensão à matriz de recursos da máquina virtual. A linha "sinks" – "AzMonSink" e a "SinksConfig" correspondente mais adiante na seção – permite que a extensão emita métricas diretamente para o Azure Monitor. Fique à vontade adicionar ou remover os contadores de desempenho conforme necessário.


```json
        "networkProfile": {
            "networkInterfaces": [
            {
                "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
            ]
        },
"diagnosticsProfile": {
    "bootDiagnostics": {
    "enabled": true,
    "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob]"
    }
}
},
//Start of section to add
"resources": [
{
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'), '/', 'Microsoft.Insights.VMDiagnosticsSettings')]",
            "apiVersion": "2017-12-01",
            "location": "[resourceGroup().location]",
            "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.12",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "WadCfg": {
                "DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "DiagnosticInfrastructureLogs": {
                    "scheduledTransferLogLevelFilter": "Error"
        },
                    "Directories": {
                    "scheduledTransferPeriod": "PT1M",
    "IISLogs": {
                        "containerName": "wad-iis-logfiles"
                    },
                    "FailedRequestLogs": {
                        "containerName": "wad-failedrequestlogs"
                    }
                    },
                    "PerformanceCounters": {
                    "scheduledTransferPeriod": "PT1M",
                    "sinks": "AzMonSink",
                    "PerformanceCounterConfiguration": [
                        {
                        "counterSpecifier": "\\Memory\\Available Bytes",
                        "sampleRate": "PT15S"
                        },
                        {
                        "counterSpecifier": "\\Memory\\% Committed Bytes In Use",
                        "sampleRate": "PT15S"
                        },
                        {
                        "counterSpecifier": "\\Memory\\Committed Bytes",
                        "sampleRate": "PT15S"
                        }
                    ]
                    },
                    "WindowsEventLog": {
                    "scheduledTransferPeriod": "PT1M",
                    "DataSource": [
                        {
                        "name": "Application!*"
                        }
                    ]
                    },
                    "Logs": {
                    "scheduledTransferPeriod": "PT1M",
                    "scheduledTransferLogLevelFilter": "Error"
                    }
                },
                "SinksConfig": {
                    "Sink": [
                    {
                        "name" : "AzMonSink",
                        "AzureMonitor" : {}
                    }
                    ]
                }
                },
                "StorageAccount": "[variables('storageAccountName')]"
            },
            "protectedSettings": {
                "storageAccountName": "[variables('storageAccountName')]",
                "storageAccountKey": "[listKeys(variables('accountid'),'2015-06-15').key1]",
                "storageAccountEndPoint": "https://core.windows.net/"
            }
            }
        }
        ]
//End of section to add
```


Salve e feche ambos os arquivos.


## <a name="deploy-the-resource-manager-template"></a>Implantar o modelo do Resource Manager

> [!NOTE]
> Você precisa ter a versão 1.5 ou posterior da extensão do Diagnóstico do Azure E precisa ter a propriedade **autoUpgradeMinorVersion**: definida para 'true' no modelo do Resource Manager. Então, o Azure carregará a extensão apropriada ao iniciar a VM. Se você não tiver essas configurações no seu modelo, altere-as e reimplemente o modelo.


Para implantar o modelo do Resource Manager, usamos o Azure PowerShell.

1. Inicie o PowerShell.
1. Faça logon no Azure usando `Login-AzAccount`.
1. Obtenha sua lista de assinaturas usando `Get-AzSubscription`.
1. Defina a assinatura que você está usando para criar/atualizar a máquina virtual em:

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>"
   ```
1. Para criar um novo grupo de recursos para a VM sendo implantada, execute o comando a seguir:

   ```powershell
    New-AzResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>"
   ```
   > [!NOTE]
   > Lembre-se de [usar uma região do Azure habilitada para métricas personalizadas](metrics-custom-overview.md).

1. Execute os comandos a seguir para implantar a VM usando o modelo do Resource Manager.
   > [!NOTE]
   > Se quiser atualizar uma VM existente, adicione *-Mode Incremental* ao final do comando a seguir.

   ```powershell
   New-AzResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>"
   ```

1. Depois que a implantação for bem-sucedida, a VM deverá estar no portal do Azure, emitindo métricas para o Azure Monitor.

   > [!NOTE]
   > Você pode encontrar erros relacionados ao vmSkuSize selecionado. Se isso acontecer, volte para o arquivo azuredeploy.json e atualize o valor padrão do parâmetro vmSkuSize. Nesse caso, recomendamos que você tente "Standard_DS1_v2".

## <a name="chart-your-metrics"></a>Fazer um gráfico das métricas

1. Faça logon no Portal do Azure.

2. No menu esquerdo, selecione **Monitorar**.

3. Na página do Monitor, selecione **Métricas**.

   ![Página de métricas](media/collect-custom-metrics-guestos-resource-manager-vm/metrics.png)

4. Altere o período de agregação para **Últimos 30 minutos**.

5. No menu suspenso de recursos, selecione a VM que você criou. Se você não alterou o nome do modelo, ele deve ser *SimpleWinVM2*.

6. No menu suspenso de namespaces, selecione **azure.vm.windows.guest**

7. No menu suspenso de métricas, selecione **Memória\%Bytes Confirmados em Uso**.


## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [métricas personalizadas](metrics-custom-overview.md).

