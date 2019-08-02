---
title: Enviar métricas do sistema operacional convidado para o repositório de métricas do Monitor do Azure usando um modelo do Azure Resource Manager para um conjunto de dimensionamento de máquina virtual do Windows
description: Enviar métricas do sistema operacional convidado para o repositório de métricas do Monitor do Azure usando um modelo do Gerenciador de Recursos para um conjunto de dimensionamento de máquina virtual do Windows
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 573c205cd2e208a1cb2b526d96fb08ca21331c80
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "66129637"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-by-using-an-azure-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>Enviar métricas do sistema operacional convidado para o repositório de métricas do Monitor do Azure usando um modelo do Azure Resource Manager para um conjunto de dimensionamento de máquina virtual do Windows

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Usando a extensão do [Windows Azure Diagnostics](diagnostics-extension-overview.md) do Monitor do Azure, é possível coletar métricas e logs do sistema operacional convidado (SO guest) executado como parte de uma máquina virtual, serviço de nuvem ou do Azure Service Fabric. grupo. A extensão pode enviar telemetria para vários locais diferentes listados no artigo vinculado anteriormente.  

O artigo descreve o processo para enviar métricas de desempenho do SO convidado de um conjunto de dimensionamento de máquinas virtuais do Windows para o armazenamento de dados do Azure Monitor. A partir do Windows Azure Diagnostics versão 1.11, você pode escrever métricas diretamente no repositório de métricas do Monitor do Azure, onde métricas de plataforma padrão já foram coletadas. Ao armazená-los nesse local, você pode acessar as mesmas ações que estão disponíveis para as métricas da plataforma. As ações incluem alertas, gráficos, roteamento, acesso da API REST e muito mais em tempo real. No passado, a extensão do Diagnóstico do Windows Azure era gravada no Armazenamento do Azure, mas não no armazenamento de dados do Monitor do Azure.  

Se você é novo nos modelos do Resource Manager, aprenda sobre [implantações de modelos](../../azure-resource-manager/resource-group-overview.md) e sua estrutura e sintaxe.  

## <a name="prerequisites"></a>Pré-requisitos

- Sua assinatura deve ser registrada com [Microsoft. Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

- Você precisa ter [Azure PowerShell](/powershell/azure) instalado, ou você pode usar [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). 


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Configurar o Azure Monitor como um coletor de dados 
A extensão de Diagnóstico do Azure usa um recurso chamado **coletores de dados** para encaminhar métricas e logs para locais diferentes. As etapas a seguir mostram como usar um modelo do Resource Manager e o PowerShell para implantar uma VM usando o novo coletor de dados do Monitor do Azure. 

## <a name="author-a-resource-manager-template"></a>Autor de um modelo do Resource Manager 
Para este exemplo, você pode usar um modelo de amostra [publicamente disponível](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale):  

- **Azuredeploy.json** é um modelo pré-configurado do Resource Manager para a implantação de um conjunto de dimensionamento de máquinas virtuais.

- **Azuredeploy.parameters.json** é um arquivo de parâmetros que armazena informações como o nome de usuário e a senha que você deseja definir para sua VM. Durante a implantação, o modelo do Resource Manager usa os parâmetros definidos no arquivo. 

Baixe e salve os dois arquivos localmente. 

###  <a name="modify-azuredeployparametersjson"></a>Modificar azuredeploy.parameters.json
Abra o **azuredeploy.parameters.json** arquivo:  
 
- Fornecer um **vmSKU** você deseja implantar. É recomendável Standard_D2_v3. 
- Especifique um **windowsOSVersion** você deseja para seu conjunto de dimensionamento de máquina virtual. É recomendável 2016-Datacenter. 
- Nomeie o recurso do conjunto de escala da máquina virtual a ser implantado usando uma propriedade **vmssName**. Um exemplo é **VMSS-WAD-TEST**.    
- Especifique o número de VMs que você deseja executar na escala da máquina virtual definida usando a propriedade **instanceCount**.
- Insira valores para **adminUsername** e **adminPassword** para o conjunto de dimensionamento de máquinas virtuais. Esses parâmetros são usados para o acesso remoto às VMs no conjunto de dimensionamento. Para evitar que sua VM seja invadida, **não** use os que estão neste modelo. Os bots verificam a internet em busca de nomes de usuários e senhas em repositórios públicos do GitHub. Eles provavelmente testarão VMs com esses padrões. 


###  <a name="modify-azuredeployjson"></a>Modificar azuredeploy.json
Abra o **azuredeploy. JSON** arquivo. 

Adicione uma variável para conter as informações da conta de armazenamento no modelo do Resource Manager. Todos os logs ou contadores de desempenho especificados no arquivo de configuração de diagnósticos são gravados no repositório de métricas do Azure Monitor e na conta de armazenamento que você especifica aqui: 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
```
 
Encontre a definição do conjunto de escala da máquina virtual na seção de recursos e inclua a seção **identidade** na configuração. Essa adição garante que o Azure atribua a ela uma identidade do sistema. Esta etapa também garante que as VMs no conjunto de dimensionamento possam emitir métricas de convidados sobre si mesmas para o Monitor do Azure:  

```json
    { 
      "type": "Microsoft.Compute/virtualMachineScaleSets", 
      "name": "[variables('namingInfix')]", 
      "location": "[resourceGroup().location]", 
      "apiVersion": "2017-03-30", 
      //add these lines below
      "identity": { 
           "type": "systemAssigned" 
       }, 
       //end of lines to add
```

No recurso do conjunto de dimensionamento de máquinas virtuais, localize a seção **virtualMachineProfile**. Adicione um novo perfil chamado **extensionsProfile** para gerenciar as extensões.  


No **extensionProfile**, adicione uma nova extensão ao modelo, conforme mostrado na seção **VMSS-WAD-extension**.  Esta seção é de identidades gerenciadas para a extensão de recursos do Azure que garante que as métricas que estão sendo emitidas sejam aceitas pelo Azure Monitor. O campo **nome** pode conter qualquer nome. 

O código a seguir da extensão MSI também adiciona a extensão e a configuração de diagnóstico como um recurso de extensão ao recurso de conjunto de escala da máquina virtual. Sinta-se à vontade para adicionar ou remover contadores de desempenho conforme necessário: 

```json
          "extensionProfile": { 
            "extensions": [ 
            // BEGINNING of added code  
            // Managed identities for Azure resources   
                { 
                 "name": "VMSS-WAD-extension", 
                 "properties": { 
                       "publisher": "Microsoft.ManagedIdentity", 
                       "type": "ManagedIdentityExtensionForWindows", 
                       "typeHandlerVersion": "1.0", 
                       "autoUpgradeMinorVersion": true, 
                       "settings": { 
                             "port": 50342 
                           }, 
                       "protectedSettings": {} 
                     } 
                                
            }, 
            // add diagnostic extension. (Remove this comment after pasting.)
            { 
              "name": "[concat('VMDiagnosticsVmExt','_vmNodeType0Name')]", 
              "properties": { 
                   "type": "IaaSDiagnostics", 
                   "autoUpgradeMinorVersion": true, 
                   "protectedSettings": { 
                        "storageAccountName": "[variables('storageAccountName')]", 
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')),'2015-05-01-preview').key1]", 
                        "storageAccountEndPoint": "https://core.windows.net/" 
                   }, 
                   "publisher": "Microsoft.Azure.Diagnostics", 
                   "settings": { 
                        "WadCfg": { 
                              "DiagnosticMonitorConfiguration": { 
                                   "overallQuotaInMB": "50000", 
                                   "PerformanceCounters": { 
                                       "scheduledTransferPeriod": "PT1M", 
                                       "sinks": "AzMonSink", 
                                       "PerformanceCounterConfiguration": [ 
                                          { 
              "counterSpecifier": "\\Memory\\% Committed Bytes In Use", 
                                              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Available Bytes", 
              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Committed Bytes", 
              "sampleRate": "PT15S" 
           } 
                                       ] 
                                 }, 
                                 "EtwProviders": { 
                                       "EtwEventSourceProviderConfiguration": [ 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Actors", 
                                              "scheduledTransferKeywordFilter": "1", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                              "eventDestination": "ServiceFabricReliableActorEventTable" 
                                           } 
                                           }, 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Services", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricReliableServiceEventTable" 
                                              } 
                                           } 
                                     ], 
                                     "EtwManifestProviderConfiguration": [ 
                                           { 
                                              "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8", 
                                              "scheduledTransferLogLevelFilter": "Information", 
                                              "scheduledTransferKeywordFilter": "4611686018427387904", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricSystemEventTable" 
                                              } 
                                          } 
                                     ] 
                               } 
                               }, 
                               "SinksConfig": { 
                                     "Sink": [ 
                                          { 
                                              "name": "AzMonSink", 
                                              "AzureMonitor": {} 
                                          } 
                                      ] 
                               } 
                         }, 
                         "StorageAccount": "[variables('storageAccountName')]" 
                         }, 
                        "typeHandlerVersion": "1.11" 
                  } 
           } 
            ] 
          }
          }
      }
    },
    //end of added code plus a few brackets. Be sure that the number and type of brackets match properly when done. 
    {
      "type": "Microsoft.Insights/autoscaleSettings",
...
```


Adicione **dependsOn** para a conta de armazenamento para garantir que ela seja criada na ordem correta: 

```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
```

Se já não é criado no modelo, crie uma conta de armazenamento: 

```json
"resources": [
// add this code    
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[resourceGroup().location]",
    "properties": {
    "accountType": "Standard_LRS"
    }
},
// end added code
{ 
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
```

Salve e feche ambos os arquivos. 

## <a name="deploy-the-resource-manager-template"></a>Implantar o modelo do Resource Manager 

> [!NOTE]  
> Você deve estar executando a extensão 1.5 ou superior do Diagnóstico do Azure **e** ter a propriedade **autoUpgradeMinorVersion:** definida como **verdadeira** no modelo do Resource Manager. Então, o Azure carregará a extensão apropriada ao iniciar a VM. Se você não tiver essas configurações no seu modelo, altere-as e reimplemente o modelo. 


Para implantar o modelo do Resource Manager, use o Azure PowerShell:  

1. Inicie o PowerShell. 
1. Entre no Azure usando `Login-AzAccount`.
1. Obtenha a lista de assinaturas usando `Get-AzSubscription`.
1. Defina a assinatura que você criará ou atualize a máquina virtual: 

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Crie um novo grupo de recursos para a VM sendo implantada. Execute o seguinte comando: 

   ```powershell
    New-AzResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > Lembre-se de usar uma região do Azure ativada para métricas personalizadas. Lembre-se de usar uma região do [Azure ativada para métricas personalizadas](https://github.com/MicrosoftDocs/azure-docs-pr/pull/metrics-custom-overview.md#supported-regions).
 
1. Execute os seguintes comandos para implantar a VM:  

   > [!NOTE]  
   > Se você quiser atualizar um conjunto de escala existente, adicione **-Mode Incremental** ao final do comando. 
 
   ```powershell
   New-AzResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. Depois que sua implantação for bem-sucedida, você deverá localizar a escala da máquina virtual definida no portal do Azure. Ele deve emitir métricas para o Monitor do Azure. 

   > [!NOTE]  
   > Você pode encontrar erros ao redor selecionado **vmSkuSize**. Nesse caso, volte ao seu arquivo **azuredeploy.json** e atualize o valor padrão do parâmetro **vmSkuSize**. É recomendável que você tente **Standard_DS1_v2**. 


## <a name="chart-your-metrics"></a>Fazer um gráfico das métricas 

1. Entre no Portal do Azure. 

1. No menu à esquerda, selecione **Monitor**. 

1. Sobre o **Monitor** página, selecione **métricas**. 

   ![Monitor - página de métricas](media/collect-custom-metrics-guestos-resource-manager-vmss/metrics.png) 

1. Altere o período de agregação para **Últimos 30 minutos**.  

1. No menu suspenso de recursos, selecione o conjunto de escalas da máquina virtual que você criou.  

1. No menu suspenso namespaces, selecione **azure.vm.windows.guest**. 

1. No menu suspenso de métricas, selecione **Memória\%Bytes Confirmados em Uso**.  

Você também pode optar por usar as dimensões nessa métrica para traçar uma VM específica ou plotar cada VM no conjunto de escala. 



## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [métricas personalizadas](metrics-custom-overview.md).


