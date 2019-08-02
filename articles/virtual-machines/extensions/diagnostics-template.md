---
title: Adicionar monitoramento e diagnóstico a uma máquina virtual do Azure | Microsoft Docs
description: Use um modelo de Azure Resource Manager para criar uma nova máquina virtual do Windows com a extensão de diagnóstico do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: sbtron
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 8cde8fe7-977b-43d2-be74-ad46dc946058
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: saurabh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8d1c5598bd7ea5b3f35d5447935953d4cd55664a
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "67706764"
---
# <a name="use-monitoring-and-diagnostics-with-a-windows-vm-and-azure-resource-manager-templates"></a>Usar monitoramento e diagnóstico com uma VM Windows e modelos do Azure Resource Manager
A Extensão Diagnóstico do Azure fornece funcionalidades de monitoramento e diagnóstico em uma máquina virtual do Azure baseada no Windows. É possível habilitar esses recursos na máquina virtual incluindo a extensão como parte do modelo do Azure Resource Manager. Para saber mais sobre como incluir extensões como parte de um modelo de máquina virtual, confira [Criando modelos do Gerenciador de Recursos do Azure com extensões de VM](../windows/template-description.md#extensions) . Este artigo descreve como adicionar a extensão de diagnóstico do Microsoft Azure para a um modelo de máquina virtual do Windows.  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Adicionar a extensão do Diagnóstico do Microsoft Azure para a definição do recurso VM
Para habilitar a extensão de diagnóstico em uma máquina virtual do Windows, adicione a extensão como um recurso de VM no modelo do Resource Manager.

Para um Gerenciador de Recursos simples baseado em máquina virtual, adicione a configuração de extensão à matriz de *recursos* da máquina virtual: 

```json
"resources": [
    {
        "name": "Microsoft.Insights.VMDiagnosticsSettings",
        "type": "extensions",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-06-15",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "tags": {
            "displayName": "AzureDiagnostics"
        },
        "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.5",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
                "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
            },
            "protectedSettings": {
                "storageAccountName": "[parameters('existingdiagnosticsStorageAccountName')]",
                "storageAccountKey": "[listkeys(variables('accountid'), '2015-05-01-preview').key1]",
                "storageAccountEndPoint": "https://core.windows.net"
            }
        }
    }
]
```

Outra prática comum é adicionar a configuração de extensão ao nó de recursos raiz do modelo, em vez de defini-la no nó de recursos da máquina virtual. Com essa abordagem, você deve especificar claramente uma relação hierárquica entre a extensão e a máquina virtual com os valores *name* e *type*. Por exemplo: 

```json
"name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
"type": "Microsoft.Compute/virtualMachines/extensions",
```

A extensão é sempre associada à máquina virtual. Você pode defini-la diretamente no nó de recursos da máquina virtual ou no nível base e usar a convenção de nomenclatura hierárquica para associá-la à máquina virtual.

Para os Conjuntos de Dimensionamento de Máquinas Virtuais, a configuração de extensões é especificada na propriedade *extensionProfile* do *VirtualMachineProfile*.

A propriedade *publisher* com o valor **Microsoft.Azure.Diagnostics** e a propriedade *type* com o valor **IaaSDiagnostics** identificam exclusivamente a extensão do Diagnóstico do Azure.

Você pode usar o valor da propriedade *name* para fazer referência a extensão no grupo de recursos. Ao defini-lo especificamente como **Microsoft.Insights.VMDiagnosticsSettings**, ele será facilmente identificado pelo portal do Azure, garantindo a exibição correta dos gráficos de monitoramento no portal do Azure.

A propriedade *typeHandlerVersion* especifica a versão da extensão que você deseja usar. Definindo a versão secundária *autoUpgradeMinorVersion* como **true**, você obtém a versão secundária mais recente da extensão disponível. É altamente recomendável definir a propriedade *autoUpgradeMinorVersion* sempre como **true** para que você possa usar sempre a extensão de diagnóstico disponível mais recente, com todos os novos recursos e correções de erros. 

O elemento *settings* contém propriedades de configurações da extensão, que podem ser definidas e gravadas novamente a partir da extensão (também conhecido como configuração pública). A propriedade *xmlcfg* contém a configuração baseada em XML dos logs de diagnóstico, contadores de desempenho etc., que são coletados pelo agente de diagnóstico. Confira o artigo [Esquema de configuração de diagnóstico](https://msdn.microsoft.com/library/azure/dn782207.aspx) para saber mais sobre o esquema XML. Uma prática comum é armazenar a configuração XML real como uma variável no modelo do Gerenciador de Recursos do Azure e, em seguida, concatená-la e codificá-la Base64 para definir o valor de *xmlcfg*. Confira a seção [Variáveis de configuração de diagnóstico](#diagnostics-configuration-variables) para saber mais sobre como armazenar o XML em variáveis. A propriedade *storageAccount* especifica o nome da conta de armazenamento para a qual os dados de diagnóstico são transferidos. 

As propriedades em *protectedSettings* , conhecidas também como configuração particular, podem ser definidas, mas não podem ser gravadas novamente após a definição. A natureza somente gravação do elemento *protectedSettings* é útil para armazenar segredos, como a chave da conta de armazenamento e que os dados de diagnóstico são gravados.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Especificando uma conta de armazenamento de diagnóstico como parâmetro
O snippet JSON de extensão de diagnóstico acima considera os parâmetros *existingdiagnosticsStorageAccountName* e *existingdiagnosticsStorageResourceGroup* para especificar a conta de armazenamento de diagnóstico na qual os dados de diagnóstico serão armazenados. Quando você especifica a conta de armazenamento de diagnóstico como um parâmetro, fica mais fácil alterá-la em diferentes ambientes; por exemplo, convém usar uma conta de armazenamento de diagnóstico para testes e outra para a Implantação de Produção.  

```json
"existingdiagnosticsStorageAccountName": {
    "type": "string",
    "metadata": {
"description": "The name of an existing storage account to which diagnostics data is transfered."
    }
},
"existingdiagnosticsStorageResourceGroup": {
    "type": "string",
    "metadata": {
"description": "The resource group for the storage account specified in existingdiagnosticsStorageAccountName"
    }
}
```

Recomendamos especificar uma conta de armazenamento de diagnóstico em outro grupo de recursos, em vez de usar o grupo de recursos da máquina virtual. Um grupo de recursos pode ser considerado como uma unidade de implantação com seu próprio tempo de vida. Uma máquina virtual pode ser implantada e reimplantada à medida que surgem novas atualizações de configurações para ela, mas convém continuar armazenando os dados de diagnóstico na mesma conta de armazenamento entre as essas implantações de máquina virtual. Com a conta de armazenamento baseada em um recurso diferente, esta conta pode aceitar dados de diversas implantações de máquina virtual, o que facilita a solução de problemas entre as várias versões.

> [!NOTE]
> Quando você cria um modelo de máquina virtual do Windows no Visual Studio, a conta de armazenamento padrão pode ser definida para usar a mesma conta de armazenamento em que o VHD da máquina virtual é carregado. Isso serve para simplificar a configuração inicial da VM. Reconsidere o modelo para usar outra conta de armazenamento que pode ser transmitida como um parâmetro. 
> 
> 

## <a name="diagnostics-configuration-variables"></a>Variáveis de configuração de diagnóstico
O snippet JSON de extensão de diagnóstico anterior define uma variável *accountid* para simplificar a obtenção da chave da conta de armazenamento para o armazenamento de diagnóstico:   

```json
"accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"
```

A propriedade *xmlcfg* da extensão de diagnóstico é definida através do uso de diversas variáveis concatenadas. Os valores dessas variáveis estão em XML e devem ser escapados corretamente após a definição das variáveis JSON.

O exemplo a seguir descreve a configuração de diagnóstico XML que coleta contadores de desempenho no nível do sistema padrão juntamente com alguns logs de eventos do Windows e logs de infraestrutura de diagnóstico. Ela foi escapada e formatada corretamente, de modo que a configuração pode ser transferida diretamente na seção de variáveis do modelo. Confira o artigo [Esquema de configuração de diagnóstico](https://msdn.microsoft.com/library/azure/dn782207.aspx) para obter um exemplo mais legível da configuração XML.

```json
"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
"wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
"wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
"wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
"wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
"wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"
```

O nó xml de definição Métrica na configuração acima é um elemento de configuração importante, pois determina a forma de agregação e de armazenamento dos contadores de desempenho definidos anteriormente no xml, no nó *PerformanceCounter*. 

> [!IMPORTANT]
> Essas métricas conduzem os gráficos de monitoramento e alertas no portal do Azure.  O nó **Métricas** com *resourceID* e **MetricAggregation** deve ser incluído na configuração de diagnóstico de sua VM se você quiser ver os dados de monitoramento da VM no portal do Azure. 
> 
> 

O exemplo a seguir mostra o XML para definições de métricas: 

```xml
<Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
    <MetricAggregation scheduledTransferPeriod="PT1H"/>
    <MetricAggregation scheduledTransferPeriod="PT1M"/>
</Metrics>
```

O atributo *resourceID* identifica exclusivamente a máquina virtual na assinatura. Não deixe de usar as funções subscription() e resourceGroup() para que o modelo atualize automaticamente esses valores com base na assinatura e no grupo de recursos em que você está implantando.

Se estiver criando várias máquinas virtuais em um loop, você deverá preencher o valor *resourceID* com uma função copyIndex() para diferenciar corretamente as VMs individuais. O valor *xmlCfg* pode ser atualizado para dar suporte a essa operação da seguinte maneira:  

```json
"xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 
```

O valor MetricAggregation de *PT1M* e *PT1H* significa uma agregação ao longo de um minuto e uma agregação ao longo de uma hora, respectivamente.

## <a name="wadmetrics-tables-in-storage"></a>Tabelas WADMetrics no armazenamento
A configuração de métricas acima vai gerar tabelas na conta de armazenamento de diagnóstico com as seguintes convenções de nomenclatura:

* **WADMetrics**: Prefixo padrão para todas as tabelas WADMetrics
* **PT1H** ou **PT1M**: Significa que a tabela contém dados agregados para períodos de 1 hora ou 1 minuto
* **P10D**: Significa que a tabela conterá dados por 10 dias a partir do momento em que a tabela iniciou a coleção de dados
* **V2S**: Constante de cadeia de caracteres
* **yyyymmdd**: A data em que a tabela iniciou a coleção de dados

Exemplo: *WADMetricsPT1HP10DV2S20151108* inclui dados agregados das métricas para o período de uma hora durante 10 dias, com início em 11 de novembro de 2015    

Cada tabela WADMetrics inclui as seguintes colunas:

* **PartitionKey**: A partitionkey é criada com base no valor *resourceID* para identificar exclusivamente o recurso de VM. Por exemplo: `002Fsubscriptions:<subscriptionID>:002FresourceGroups:002F<ResourceGroupName>:002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>`  
* **RowKey**: Segue o formato `<Descending time tick>:<Performance Counter Name>`. O cálculo do tique de tempo decrescente é o tique do tempo máximo menos o tempo de início do período de agregação. Por exemplo, se o período de exemplo tivesse se iniciado em 10 de novembro de 2015 à 00:00 UTC, o cálculo seria: `DateTime.MaxValue.Ticks - (new DateTime(2015,11,10,0,0,0,DateTimeKind.Utc).Ticks)`. Para o contador de desempenho de bytes de memória disponível, a chave de linha se parecerá com:`2519551871999999999__:005CMemory:005CAvailable:0020Bytes`
* **CounterName**: É o nome do contador de desempenho. Ele corresponde ao *counterSpecifier* definido na configuração XML.
* **Maximum**: O valor máximo do contador de desempenho durante o período de agregação.
* **Mínimos**: O valor mínimo do contador de desempenho durante o período de agregação.
* **Total**: A soma de todos os valores do contador de desempenho relatados durante o período de agregação.
* **Contagem**: O número total de valores relatados do contador de desempenho.
* **Average**: O valor médio (total/contagem) do contador de desempenho durante o período de agregação.

## <a name="next-steps"></a>Próximas etapas
* Para obter um modelo de exemplo completo de uma máquina virtual do Windows com extensão de diagnóstico, confira [201-vm-monitoring-diagnostics-extension](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
* Implante o modelo do Azure Resource Manager usando o [Azure PowerShell](../windows/ps-template.md) ou a [Linha de Comando do Azure](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Saiba mais sobre a [Criação de modelos do Gerenciador de Recursos do Azure](../../resource-group-authoring-templates.md)
