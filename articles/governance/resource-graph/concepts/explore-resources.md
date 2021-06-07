---
title: Explore os recursos do Azure
description: Aprenda a usar a linguagem de consulta do Resource Graph para explorar seus recursos e descobrir como eles estão conectados.
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: a76ce2bb34fd6e99fedf5030c4723b6dd5acbb91
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98917717"
---
# <a name="explore-your-azure-resources-with-resource-graph"></a>Explore seus recursos do Azure com o Gráfico de Recursos

O Gráfico de Recursos do Azure fornece a capacidade de explorar e descobrir seus recursos do Azure rapidamente e em escala. Projetado para respostas rápidas, é uma ótima maneira de aprender sobre seu ambiente e também sobre as propriedades que existem em seus recursos do Azure.

## <a name="explore-virtual-machines"></a>Explore máquinas virtuais

Um recurso comum no Azure é uma máquina virtual. Assim como um tipo de recurso, as máquinas virtuais têm muitas propriedades que podem ser consultadas. Cada propriedade fornece uma opção para filtrar ou encontrar exatamente o recurso que você está procurando.

### <a name="virtual-machine-discovery"></a>Descoberta de máquina virtual

Vamos começar com uma consulta simples para obter uma única VM do nosso ambiente e observar as propriedades retornadas.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1" | ConvertTo-Json -Depth 100
```

> [!NOTE]
> O cmdlet `Search-AzGraph` do Azure PowerShell retorna um **PSCustomObject** por padrão. Para que a saída tenha a mesma aparência do que é retornado pela CLI do Azure, o cmdlet `ConvertTo-Json` é usado. O valor padrão da **Profundidade** é _2_. Ao definir como _100_, ele deve converter todos os níveis retornados.

Os resultados do JSON são estruturados de forma semelhante ao exemplo a seguir:

```json
[
  {
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1",
    "plan": {},
    "properties": {
      "hardwareProfile": {
        "vmSize": "Standard_B2s"
      },
      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "/subscriptions/<subscriptionId>/MyResourceGroup/providers/Microsoft.Network/networkInterfaces/contosovm1535",
            "resourceGroup": "MyResourceGroup"
          }
        ]
      },
      "osProfile": {
        "adminUsername": "localAdmin",
        "computerName": "ContosoVM1",
        "secrets": [],
        "windowsConfiguration": {
          "enableAutomaticUpdates": true,
          "provisionVMAgent": true
        }
      },
      "provisioningState": "Succeeded",
      "storageProfile": {
        "dataDisks": [],
        "imageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2016-Datacenter",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": 127,
          "managedDisk": {
            "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
            "resourceGroup": "MyResourceGroup",
            "storageAccountType": "Premium_LRS"
          },
          "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
          "osType": "Windows"
        }
      },
      "vmId": "bbb9b451-6dc7-4117-bec5-c971eb1118c6"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {},
    "subscriptionId": "<subscriptionId>",
    "tags": {},
    "type": "microsoft.compute/virtualmachines"
  }
]
```

As propriedades nos dizem informações adicionais sobre o recurso de máquina virtual em si. Essas propriedades incluem: sistema operacional, discos, marcas e o grupo de recursos e a assinatura da qual é membro.

### <a name="virtual-machines-by-location"></a>Máquinas virtuais por localização

Tomando o que aprendemos sobre o recurso de máquinas virtuais, vamos usar a propriedade **location** para contar todas as máquinas virtuais por local. Para atualizar a consulta, removeremos o limite e resumiremos a contagem dos valores de localização.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by location
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

Os resultados do JSON são estruturados de forma semelhante ao exemplo a seguir:

```json
[
  {
    "count_": 386,
    "location": "eastus"
  },
  {
    "count_": 215,
    "location": "southcentralus"
  },
  {
    "count_": 59,
    "location": "westus"
  }
]
```

Agora podemos ver quantas máquinas virtuais temos em cada região do Azure.

### <a name="virtual-machines-by-sku"></a>Máquinas virtuais por SKU

Voltando às propriedades originais da máquina virtual, vamos tentar encontrar todas as máquinas virtuais que possuem um tamanho de SKU **Standard_B2s**. Olhando para o JSON retornado, vemos que ele está armazenado em **properties.hardwareprofile.vmsize**. Atualizaremos a consulta para encontrar todas as VMs que correspondam a esse tamanho e retornemos apenas o nome da VM e região.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| project name, resourceGroup
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

### <a name="virtual-machines-connected-to-premium-managed-disks"></a>Máquinas virtuais conectadas a discos gerenciados premium

Para obter os detalhes dos discos gerenciados Premium que estão anexados a essas máquinas virtuais **Standard_B2s** , expandimos a consulta para retornar a ID de recurso desses discos gerenciados.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| extend disk = properties.storageProfile.osDisk.managedDisk
| where disk.storageAccountType == 'Premium_LRS'
| project disk.id
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

O resultado é uma lista de IDs de disco.

### <a name="managed-disk-discovery"></a>Descoberta de disco gerenciado

Com o primeiro registro da consulta anterior, exploraremos as propriedades que existem no disco gerenciado que foi anexado à primeira máquina virtual. A consulta atualizada usa a ID do disco e altera o tipo.

Exemplo de saída da consulta anterior, por exemplo:

```json
[
  {
    "disk_id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166"
  }
]
```

```kusto
Resources
| where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'
```

Antes de executar a consulta, como sabemos que o **tipo** deve ser agora **Microsoft.Compute / disks**? Se você olhar para o ID completo, você verá **/providers/Microsoft.Compute/disks/** como parte da cadeia de caracteres.
Esse fragmento de cadeia de caracteres fornece uma dica sobre o tipo de pesquisa. Um método alternativo seria remover o limite por tipo e, em vez disso, pesquisar apenas pelo campo ID. Como o ID é exclusivo, apenas um registro seria retornado e a propriedade **tipo** nele fornece esse detalhe.

> [!NOTE]
> Para este exemplo funcionar, você deve substituir o campo ID por um resultado de seu próprio ambiente.

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

Os resultados do JSON são estruturados de forma semelhante ao exemplo a seguir:

```json
[
  {
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "plan": {},
    "properties": {
      "creationData": {
        "createOption": "Empty"
      },
      "diskSizeGB": 127,
      "diskState": "ActiveSAS",
      "provisioningState": "Succeeded",
      "timeCreated": "2018-09-14T12:17:32.2570000Z"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {
      "name": "Premium_LRS",
      "tier": "Premium"
    },
    "subscriptionId": "<subscriptionId>",
    "tags": {
      "environment": "prod"
    },
    "type": "microsoft.compute/disks"
  }
]
```

## <a name="explore-virtual-machines-to-find-public-ip-addresses"></a>Explore máquinas virtuais para encontrar endereços IP públicos

Esse conjunto de consultas primeiro encontra e armazena todos os recursos de NIC (adaptadores de rede) conectados às máquinas virtuais. Em seguida, as consultas usam a lista de NICs para encontrar cada recurso de endereço IP que seja um endereço IP público e armazenar esses valores. Por fim, as consultas apresentam uma lista de endereços IP públicos.

```azurecli-interactive
# Use Resource Graph to get all NICs and store in the 'nics.txt' file
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20" --output table | tail -n +3 > nics.txt

# Review the output of the query stored in 'nics.txt'
cat nics.txt
```

```azurepowershell-interactive
# Use Resource Graph to get all NICs and store in the $nics variable
$nics = Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20"

# Review the output of the query stored in the variable
$nics.nic
```

Use o arquivo (CLI do Azure) ou a variável (Azure PowerShell) na próxima consulta para obter os detalhes dos recursos da interface de rede relacionados, em que há um endereço IP público conectado ao NIC.

```azurecli-interactive
# Use Resource Graph with the 'nics.txt' file to get all related public IP addresses and store in 'publicIp.txt' file
az graph query -q="Resources | where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$(awk -vORS="','" '{print $0}' nics.txt | sed 's/,$//')') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp" --output table | tail -n +3 > ips.txt

# Review the output of the query stored in 'ips.txt'
cat ips.txt
```

```azurepowershell-interactive
# Use Resource Graph  with the $nics variable to get all related public IP addresses and store in $ips variable
$ips = Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$($nics.nic -join "','")') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp"

# Review the output of the query stored in the variable
$ips.publicIp
```

Por último, use a lista de recursos de endereços IP públicos armazenados no arquivo (CLI do Azure) ou na variável (Azure PowerShell) para obter o endereço IP público real deles e exibi-los.

```azurecli-interactive
# Use Resource Graph with the 'ips.txt' file to get the IP address of the public IP address resources
az graph query -q="Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$(awk -vORS="','" '{print $0}' ips.txt | sed 's/,$//')') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip" --output table
```

```azurepowershell-interactive
# Use Resource Graph with the $ips variable to get the IP address of the public IP address resources
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$($ips.publicIp -join "','")') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip"
```

Para ver como realizar essas etapas em uma única consulta com o operador `join`, veja o exemplo em [Listar máquinas virtuais com a interface de rede e o IP público](../samples/advanced.md#join-vmpip).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [linguagem de consulta](query-language.md).
- Confira o idioma em uso em [Consultas iniciais](../samples/starter.md).
- Confira os usos avançados em [Consultas avançadas](../samples/advanced.md).
