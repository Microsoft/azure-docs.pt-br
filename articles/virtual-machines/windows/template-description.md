---
title: Máquinas virtuais em um modelo do Azure Resource Manager | Microsoft Azure
description: Saiba mais sobre como o recurso de máquina virtual é definido em um modelo do Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: f63ab5cc-45b8-43aa-a4e7-69dc42adbb99
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/03/2019
ms.author: cynthn
ms.openlocfilehash: fd4fad40ef4809c756321493854f38fd813569ca
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710282"
---
# <a name="virtual-machines-in-an-azure-resource-manager-template"></a>Máquinas virtuais em um modelo do Azure Resource Manager

Este artigo descreve os aspectos de um modelo do Azure Resource Manager aplicáveis a máquinas virtuais. Este artigo não descreve um modelo completo para a criação de uma máquina virtual. Para isso, são necessárias definições de recursos para contas de armazenamento, interfaces de rede, endereços IP públicos e redes virtuais. Para obter mais informações sobre como esses recursos podem ser definidos juntos, consulte o [Passo a passo do modelo do Resource Manager](../../azure-resource-manager/resource-manager-template-walkthrough.md).

Há muitos [modelos na galeria](https://azure.microsoft.com/documentation/templates/?term=VM) que incluem o recurso de VM. Nem todos os elementos que podem ser incluídos em um modelo são descritos aqui.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

Este exemplo mostra uma seção de recursos típicos de um modelo para a criação de um número especificado de VMs:

```json
"resources": [
  { 
    "apiVersion": "2016-04-30-preview", 
    "type": "Microsoft.Compute/virtualMachines", 
    "name": "[concat('myVM', copyindex())]", 
    "location": "[resourceGroup().location]",
    "copy": {
      "name": "virtualMachineLoop", 
      "count": "[parameters('numberOfInstances')]"
    },
    "dependsOn": [
      "[concat('Microsoft.Network/networkInterfaces/myNIC', copyindex())]" 
    ], 
    "properties": { 
      "hardwareProfile": { 
        "vmSize": "Standard_DS1" 
      }, 
      "osProfile": { 
        "computername": "[concat('myVM', copyindex())]", 
        "adminUsername": "[parameters('adminUsername')]", 
        "adminPassword": "[parameters('adminPassword')]" 
      }, 
      "storageProfile": { 
        "imageReference": { 
          "publisher": "MicrosoftWindowsServer", 
          "offer": "WindowsServer", 
          "sku": "2012-R2-Datacenter", 
          "version": "latest" 
        }, 
        "osDisk": { 
          "name": "[concat('myOSDisk', copyindex())]",
          "caching": "ReadWrite", 
          "createOption": "FromImage" 
        },
        "dataDisks": [
          {
            "name": "[concat('myDataDisk', copyindex())]",
            "diskSizeGB": "100",
            "lun": 0,
            "createOption": "Empty"
          }
        ] 
      }, 
      "networkProfile": { 
        "networkInterfaces": [ 
          { 
            "id": "[resourceId('Microsoft.Network/networkInterfaces',
              concat('myNIC', copyindex()))]" 
          } 
        ] 
      },
      "diagnosticsProfile": {
        "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('https://', variables('storageName'), '.blob.core.windows.net')]"
        }
      } 
    },
    "resources": [ 
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
      {
        "name": "MyCustomScriptExtension",
        "type": "extensions",
        "apiVersion": "2016-03-30",
        "location": "[resourceGroup().location]",
        "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
        ],
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.7",
          "autoUpgradeMinorVersion": true,
          "settings": {
            "fileUris": [
              "[concat('https://', variables('storageName'),
                '.blob.core.windows.net/customscripts/start.ps1')]" 
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
          }
        }
      } 
    ]
  } 
]
``` 

> [!NOTE] 
>Este exemplo se baseia em uma conta de armazenamento criada anteriormente. É possível criar a conta de armazenamento implantando-a do modelo. O exemplo também se baseia em um adaptador de rede e seus recursos dependentes que seriam definidos no modelo. Esses recursos não são mostrados no exemplo.
>
>

## <a name="api-version"></a>Versão da API

Quando você implanta recursos usando um modelo, é necessário especificar uma versão da API a ser usada. O exemplo mostra o recurso de máquina virtual que usa esse elemento apiVersion:

```
"apiVersion": "2016-04-30-preview",
```

A versão da API especificada em seu modelo afeta quais propriedades você pode definir nele. Em geral, você deve selecionar a versão mais recente da API ao criar novos modelos. Para os modelos existentes, você pode decidir se deseja continuar usando uma versão de API anterior ou atualizar o modelo para a versão mais recente para aproveitar os novos recursos.

Use estas oportunidades para obter as versões mais recentes de API:

- API REST – [listar todos os provedores de recursos](https://docs.microsoft.com/rest/api/resources/providers)
- PowerShell - [Get-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/get-azresourceprovider)
- CLI do Azure - [az provider show](https://docs.microsoft.com/cli/azure/provider)


## <a name="parameters-and-variables"></a>Parâmetros e variáveis

Os [parâmetros](../../resource-group-authoring-templates.md) facilitam para você especificar valores para o modelo ao executá-lo. Esta seção de parâmetros é usada no exemplo:

```        
"parameters": {
  "adminUsername": { "type": "string" },
  "adminPassword": { "type": "securestring" },
  "numberOfInstances": { "type": "int" }
},
```

Quando você implantar o modelo de exemplo, insira valores para o nome e a senha da conta de administrador em cada VM e o número de VMs a serem criadas. Você tem a opção de especificar valores de parâmetro em um arquivo separado gerenciado com o modelo ou fornecer valores quando solicitado.

As [variáveis](../../resource-group-authoring-templates.md) facilitam para você configurar valores no modelo usados repetidamente ao longo dele ou que podem mudar com o tempo. Esta seção de variáveis é usada no exemplo:

```
"variables": { 
  "storageName": "mystore1",
  "accountid": "[concat('/subscriptions/', subscription().subscriptionId, 
    '/resourceGroups/', resourceGroup().name,
  '/providers/','Microsoft.Storage/storageAccounts/', variables('storageName'))]", 
  "wadlogs": "<WadCfg> 
    <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> 
      <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> 
      <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > 
        <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> 
        <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> 
        <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" />
      </WindowsEventLog>", 
  "wadperfcounters": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\">
      <PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\">
        <annotation displayName=\"Threads\" locale=\"en-us\"/>
      </PerformanceCounterConfiguration>
    </PerformanceCounters>", 
  "wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters'), 
    '<Metrics resourceId=\"')]", 
  "wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, 
    '/resourceGroups/', resourceGroup().name , 
    '/providers/', 'Microsoft.Compute/virtualMachines/')]", 
  "wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/>
    <MetricAggregation scheduledTransferPeriod=\"PT1M\"/>
    </Metrics></DiagnosticMonitorConfiguration>
    </WadCfg>"
}, 
```

Quando você implantar o modelo de exemplo, os valores de variáveis serão usados para o nome e o identificador da conta de armazenamento criada anteriormente. As variáveis também são usadas para fornecer as configurações para a extensão de diagnóstico. Use [práticas recomendadas para criar modelos do Azure Resource Manager](../../resource-manager-template-best-practices.md) para ajudá-lo a decidir como você deseja estruturar os parâmetros e as variáveis no seu modelo.

## <a name="resource-loops"></a>loops de recursos

Quando você precisar de mais de uma máquina virtual para seu aplicativo, será possível usar um elemento de cópia em um modelo. Esse elemento opcional executa loops por meio da criação do número de VMs especificado como um parâmetro:

```
"copy": {
  "name": "virtualMachineLoop", 
  "count": "[parameters('numberOfInstances')]"
},
```

Além disso, observe, no exemplo, que o índice do loop é usado ao especificar alguns valores para o recurso. Por exemplo, se você inseriu uma contagem de instâncias de três, os nomes dos discos de sistema operacional são myOSDisk1, myOSDisk2 e myOSDisk3:

```
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
}
```

> [!NOTE] 
>Este exemplo usa discos gerenciados para as máquinas virtuais.
>
>

Tenha em mente que a criação de um loop para um recurso no modelo pode exigir que você use o loop ao criar ou acessar outros recursos. Por exemplo, várias VMs não podem usar o mesmo adaptador de rede. Portanto, se seu modelo executa loops por meio da criação de três VMs, ele também deve executar loops por meio da criação de três interfaces de rede. Ao atribuir um adaptador de rede a uma VM, o índice de loop é usado para identificá-lo:

```
"networkInterfaces": [ { 
  "id": "[resourceId('Microsoft.Network/networkInterfaces',
    concat('myNIC', copyindex()))]" 
} ]
```

## <a name="dependencies"></a>Dependências

A maioria dos recursos dependem de outros recursos para funcionar corretamente. As máquinas virtuais devem ser associadas a uma rede virtual e, para fazer isso, é necessária um adaptador de rede. O elemento [dependsOn](../../resource-group-define-dependencies.md) é usado para certificar-se de que o adaptador de rede está pronto para ser usada antes que as VMs sejam criadas:

```
"dependsOn": [
  "[concat('Microsoft.Network/networkInterfaces/', 'myNIC', copyindex())]" 
],
```

O Resource Manager implanta em paralelo quaisquer recursos que não dependem de outro recurso que está sendo implantado. Tenha cuidado ao configurar dependências, porque você pode inadvertidamente tornar sua implantação lenta ao especificar dependências desnecessárias. As dependências podem ser encadeadas por meio de vários recursos. Por exemplo, o adaptador de rede depende do endereço IP público e de recursos de rede virtual.

Como saber se uma dependência é necessária? Examine os valores definidos no modelo. Se um elemento na definição de recurso de máquina virtual apontar para outro recurso implantado no mesmo modelo, será necessário ter uma dependência. Por exemplo, sua máquina virtual de exemplo define um perfil de rede:

```
"networkProfile": { 
  "networkInterfaces": [ { 
    "id": "[resourceId('Microsoft.Network/networkInterfaces',
      concat('myNIC', copyindex())]" 
  } ] 
},
```

Para definir essa propriedade, o adaptador de rede deve existir. Portanto, é necessário ter uma dependência. Também será necessário definir uma dependência quando um recurso (um filho) for definido dentro de outro recurso (um pai). Por exemplo, as configurações de diagnóstico e as extensões de script personalizado são definidas como recursos filho da máquina virtual. Elas não poderão ser criadas enquanto a máquina virtual não existir. Portanto, os dois recursos são marcados como dependentes da máquina virtual.

## <a name="profiles"></a>Perfis

Vários elementos de perfil são usados ao definir um recurso de máquina virtual. Alguns são obrigatórios e alguns são opcionais. Por exemplo, os elementos hardwareProfile, osProfile, storageProfile e networkProfile são obrigatórios, mas diagnosticsProfile é opcional. Esses perfis definem configurações como:
   
- [tamanho](sizes.md)
- [nome](/azure/architecture/best-practices/naming-conventions) e credenciais
- disco e [configurações do sistema operacional](cli-ps-findimage.md)
- [adaptador de rede](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md) 
- diagnóstico de inicialização

## <a name="disks-and-images"></a>Discos e imagens
   
No Azure, arquivos VHD podem representar [discos ou imagens](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Quando o sistema operacional em um arquivo VHD é especializado para ser uma VM específica, ele é conhecido como um disco. Quando o sistema operacional em um arquivo VHD é generalizado para ser usado para criar muitas VMs, ele é conhecido como uma imagem.   
    
### <a name="create-new-virtual-machines-and-new-disks-from-a-platform-image"></a>Criar novas máquinas virtuais e novos discos de uma imagem de plataforma

Quando você cria uma VM, é necessário decidir qual o sistema operacional a ser usado. O elemento imageReference é usado para definir o sistema operacional de uma nova VM. O exemplo mostra uma definição de um sistema operacional Windows Server:

```
"imageReference": { 
  "publisher": "MicrosoftWindowsServer", 
  "offer": "WindowsServer", 
  "sku": "2012-R2-Datacenter", 
  "version": "latest" 
},
```

Se você desejar criar um sistema operacional Linux, use esta definição:

```
"imageReference": {
  "publisher": "Canonical",
  "offer": "UbuntuServer",
  "sku": "14.04.2-LTS",
  "version": "latest"
},
```

Definições de configuração do disco do sistema operacional são atribuídas com o elemento osDisk. O exemplo define um novo disco gerenciado com o modo de cache definido como **ReadWrite** e que o disco está sendo criado de uma [imagem de plataforma](cli-ps-findimage.md):

```
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
},
```

### <a name="create-new-virtual-machines-from-existing-managed-disks"></a>Criar novas máquinas virtuais de discos gerenciados existentes

Se você desejar criar máquinas virtuais de discos existentes, remova os elementos imageReference e osProfile e defina estas configurações de disco:

```
"osDisk": { 
  "osType": "Windows",
  "managedDisk": { 
    "id": "[resourceId('Microsoft.Compute/disks', [concat('myOSDisk', copyindex())])]" 
  }, 
  "caching": "ReadWrite",
  "createOption": "Attach" 
},
```

### <a name="create-new-virtual-machines-from-a-managed-image"></a>Criar novas máquinas virtuais de uma imagem gerenciada

Se você desejar criar uma máquina virtual de uma imagem gerenciada, altere o elemento imageReference e defina estas configurações de disco:

```
"storageProfile": { 
  "imageReference": {
    "id": "[resourceId('Microsoft.Compute/images', 'myImage')]"
  },
  "osDisk": { 
    "name": "[concat('myOSDisk', copyindex())]",
    "osType": "Windows",
    "caching": "ReadWrite", 
    "createOption": "FromImage" 
  }
},
```

### <a name="attach-data-disks"></a>Anexar discos de dados

Opcionalmente, é possível adicionar discos de dados às VMs. O [número de discos](sizes.md) depende do tamanho do disco do sistema operacional que você usa. Com o tamanho das VMs definido como Standard_DS1_v2, o número máximo de discos de dados que poderão ser adicionadas a eles é dois. No exemplo, um disco de dados gerenciado está sendo adicionado a cada VM:

```
"dataDisks": [
  {
    "name": "[concat('myDataDisk', copyindex())]",
    "diskSizeGB": "100",
    "lun": 0, 
    "caching": "ReadWrite",
    "createOption": "Empty"
  }
],
```

## <a name="extensions"></a>Extensões

Embora [extensões](extensions-features.md) sejam um recurso separado, elas estão estreitamente relacionadas a VMs. As extensões podem ser adicionadas como um recurso filho da VM ou como um recurso separado. O exemplo mostra a [Extensão de diagnóstico](extensions-diagnostics-template.md) que está sendo adicionada às VMs:

```
{ 
  "name": "Microsoft.Insights.VMDiagnosticsSettings", 
  "type": "extensions", 
  "location": "[resourceGroup().location]", 
  "apiVersion": "2016-03-30", 
  "dependsOn": [ 
    "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
  ], 
  "properties": { 
    "publisher": "Microsoft.Azure.Diagnostics", 
    "type": "IaaSDiagnostics", 
    "typeHandlerVersion": "1.5", 
    "autoUpgradeMinorVersion": true, 
    "settings": { 
      "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
      variables('wadmetricsresourceid'), 
      concat('myVM', copyindex()),
      variables('wadcfgxend')))]", 
      "storageAccount": "[variables('storageName')]" 
    }, 
    "protectedSettings": { 
      "storageAccountName": "[variables('storageName')]", 
      "storageAccountKey": "[listkeys(variables('accountid'), 
        '2015-06-15').key1]", 
      "storageAccountEndPoint": "https://core.windows.net" 
    } 
  } 
},
```

Esse recurso de extensão usa a variável storageName e as variáveis de diagnóstico para fornecer valores. Se você desejar alterar os dados coletados por essa extensão, será possível adicionar mais contadores de desempenho à variável wadperfcounters. Também seria possível optar por colocar os dados de diagnóstico em uma conta de armazenamento diferente de onde os discos de VM estão armazenados.

Há muitas extensões que podem ser instalados em uma VM, mas a mais útil é provavelmente a [Extensão de Script Personalizado](extensions-customscript.md). No exemplo, um script do PowerShell chamado start.ps1 é executado em cada VM quando ela é iniciada pela primeira vez:

```
{
  "name": "MyCustomScriptExtension",
  "type": "extensions",
  "apiVersion": "2016-03-30",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
  ],
  "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.7",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "[concat('https://', variables('storageName'),
          '.blob.core.windows.net/customscripts/start.ps1')]" 
      ],
      "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
    }
  }
}
```

O script start.ps1 pode executar várias tarefas de configuração. Por exemplo, os discos de dados adicionados às VMs no exemplo não são inicializados; é possível usar um script personalizado para inicializá-los. Se você tiver várias tarefas de inicialização para fazer, será possível usar o arquivo start.ps1 para chamar outros scripts do PowerShell no armazenamento do Azure. O exemplo usa o PowerShell, mas você pode usar qualquer método de script disponível no sistema operacional que você está usando.

É possível ver o status das extensões instaladas nas Configurações de extensões no portal:

![Obter status de extensão](./media/template-description/virtual-machines-show-extensions.png)

Também é possível obter informações sobre extensões usando o comando do PowerShell **Get-AzVMExtension**, o comando da CLI do Azure **vm extension get** ou a API REST **Obter informações de extensão**.

## <a name="deployments"></a>Implantações

Quando você implanta um modelo, o Azure rastreia os recursos implantados como um grupo e atribui automaticamente um nome a esse grupo implantado. O nome da implantação é o mesmo que o nome do modelo.

Se você estiver curioso sobre o status dos recursos na implantação, veja o grupo de recursos no portal do Azure:

![Obter informações sobre a implantação](./media/template-description/virtual-machines-deployment-info.png)
    
Não é um problema usar o mesmo modelo para criar recursos ou para atualizar recursos existentes. Quando você usa comandos para implantar modelos, você tem a oportunidade de dizer qual [modo](../../resource-group-template-deploy.md) você deseja usar. O modo pode ser definido como **Completo** ou **Incremental**. O padrão é realizar atualizações incrementais. Tenha cuidado ao usar o modo **Completo**, porque você pode excluir recursos acidentalmente. Quando você define o modo como **Completo**, o Resource Manager exclui os recursos no grupo de recursos que não estão no modelo.

## <a name="next-steps"></a>Próximas etapas

- Crie seu próprio modelo usando [Criação de modelos do Azure Resource Manager](../../resource-group-authoring-templates.md).
- Implante o modelo criado usando [Create a Windows virtual machine with a Resource Manager template (Criar uma máquina virtual do Windows com um modelo do Resource Manager)](ps-template.md).
- Saiba como gerenciar as VMs que você criou, revisando [Criar e gerenciar VMs do Windows com o módulo do Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Para informações sobre a sintaxe JSON e as propriedades de tipos de recursos em modelos, consulte [Referência de modelo do Azure Resource Manager](/azure/templates/).
