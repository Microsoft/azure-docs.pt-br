---
title: Usar políticas de dimensionamento personalizadas com conjuntos de dimensionamento de máquinas virtuais do Azure
description: Saiba como usar políticas de expansão personalizadas com conjuntos de dimensionamento de máquinas virtuais do Azure que usam a configuração de dimensionamento automático para gerenciar a contagem de instâncias
services: virtual-machine-scale-sets
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 02/26/2020
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: 9ca6310705d54d563aae746ab2dbfe6cb412e6a9
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92747804"
---
# <a name="use-custom-scale-in-policies-with-azure-virtual-machine-scale-sets"></a>Usar políticas de dimensionamento personalizadas com conjuntos de dimensionamento de máquinas virtuais do Azure

Uma implantação de conjunto de dimensionamento de máquinas virtuais pode ser expandida ou dimensionada horizontalmente com base em uma matriz de métricas, incluindo métricas personalizadas e de plataforma definidas pelo usuário. Embora uma expansão crie novas máquinas virtuais com base no modelo do conjunto de dimensionamento, uma redução no afeta a execução de máquinas virtuais que podem ter diferentes configurações e/ou funções à medida que a carga de trabalho do conjunto de dimensionamento evolui. 

O recurso de política de escala horizontal fornece aos usuários uma maneira de configurar a ordem na qual as máquinas virtuais são dimensionadas, por meio de três configurações de dimensionamento: 

1. Padrão
2. NewestVM
3. OldestVM

### <a name="default-scale-in-policy"></a>Política de redução padrão

Por padrão, o conjunto de dimensionamento de máquinas virtuais aplica essa política para determinar quais instâncias serão dimensionadas. Com a política *padrão* , as VMs são selecionadas para reduzir horizontalmente na seguinte ordem:

1. Balancear máquinas virtuais entre zonas de disponibilidade (se o conjunto de dimensionamento for implantado na configuração zonal)
2. Balancear máquinas virtuais entre domínios de falha (melhor esforço)
3. Excluir máquina virtual com a ID de instância mais alta

Os usuários não precisam especificar uma política de dimensionamento se quiserem que a ordem padrão seja seguida.

Observe que o balanceamento entre zonas de disponibilidade ou domínios de falha não move instâncias entre zonas de disponibilidade ou domínios de falha. O balanceamento é obtido por meio da exclusão de máquinas virtuais de zonas de disponibilidade desbalanceadas ou domínios de falha até que a distribuição de máquinas virtuais se torne equilibrada.

### <a name="newestvm-scale-in-policy"></a>Política de dimensionamento NewestVM

Essa política excluirá a máquina virtual mais nova criada no conjunto de dimensionamento depois de balancear as VMs entre as zonas de disponibilidade (para implantações zonais). A habilitação desta política requer uma alteração de configuração no modelo do conjunto de dimensionamento de máquinas virtuais.

### <a name="oldestvm-scale-in-policy"></a>Política de dimensionamento OldestVM

Essa política excluirá a máquina virtual mais antiga criada no conjunto de dimensionamento depois de balancear as VMs entre as zonas de disponibilidade (para implantações zonais). A habilitação desta política requer uma alteração de configuração no modelo do conjunto de dimensionamento de máquinas virtuais.

## <a name="enabling-scale-in-policy"></a>Habilitando a política de dimensionamento

Uma política de dimensionamento é definida no modelo do conjunto de dimensionamento de máquinas virtuais. Conforme observado nas seções acima, uma definição de política de escala horizontal é necessária ao usar as políticas ' NewestVM ' e ' OldestVM '. O conjunto de dimensionamento de máquinas virtuais usará automaticamente a política de dimensionamento ' default ' se não houver nenhuma definição de política de escala horizontal encontrada no modelo do conjunto de dimensionamento. 

Uma política de redução na escala pode ser definida no modelo do conjunto de dimensionamento de máquinas virtuais das seguintes maneiras:

### <a name="azure-portal"></a>Portal do Azure
 
As etapas a seguir definem a política de redução na criação de um novo conjunto de dimensionamento. 
 
1. Vá para **conjuntos de dimensionamento de máquinas virtuais** .
1. Selecione **+ Adicionar** para criar um novo conjunto de dimensionamento.
1. Vá para a guia **dimensionamento** . 
1. Localize a seção **política de escala horizontal** .
1. Selecione uma política de redução na lista suspensa.
1. Quando terminar de criar o novo conjunto de dimensionamento, selecione o botão **revisar + criar** .

### <a name="using-api"></a>Usando a API

Execute um PUT no conjunto de dimensionamento de máquinas virtuais usando a API 2019-03-01:

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["OldestVM"]  
        } 
    }    
} 
```
### <a name="azure-powershell"></a>Azure PowerShell

Crie um grupo de recursos e crie um novo conjunto de dimensionamento com a política de dimensionamento definida como *OldestVM* .

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "<VMSS location>"
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "<VMSS location>" `
  -VMScaleSetName "myScaleSet" `
  -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>CLI do Azure 2.0

O exemplo a seguir adiciona uma política de redução na criação de um novo conjunto de dimensionamento. Primeiro, crie um grupo de recursos e, em seguida, crie um novo conjunto de dimensionamento com a política de dimensionamento como *OldestVM* . 

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>Usando o modelo

Em seu modelo, em "Propriedades", adicione o seguinte:

```json
"scaleInPolicy": {  
      "rules": ["OldestVM"]  
}
```

Os blocos acima especificam que o conjunto de dimensionamento de máquinas virtuais excluirá a VM mais antiga em um conjunto de dimensionamento com balanceamento de zona, quando uma redução no é disparada (por meio de dimensionamento automático ou exclusão manual).

Quando um conjunto de dimensionamento de máquinas virtuais não tiver balanceamento de zona, o conjunto de dimensionamento excluirá primeiro as VMs em todas as zonas desbalanceadas. Dentro das zonas desbalanceadas, o conjunto de dimensionamento usará a política de dimensionamento especificada acima para determinar qual VM será dimensionada. Nesse caso, em uma zona desbalanceada, o conjunto de dimensionamento selecionará a VM mais antiga nessa zona a ser excluída.

Para o conjunto de dimensionamento de máquinas virtuais não zonais, a política seleciona a VM mais antiga no conjunto de dimensionamento para exclusão.

O mesmo processo se aplica ao usar ' NewestVM ' na política de dimensionamento acima.

## <a name="modifying-scale-in-policies"></a>Modificando políticas de dimensionamento

A modificação da política de redução horizontal segue o mesmo processo que aplicar a política de redução horizontal. Por exemplo, se, no exemplo acima, você quiser alterar a política de ' OldestVM ' para ' NewestVM ', poderá fazer isso por meio de:

### <a name="azure-portal"></a>Portal do Azure

Você pode modificar a política de redução em um conjunto de dimensionamento existente por meio do portal do Azure. 
 
1. Em um conjunto de dimensionamento de máquinas virtuais existente, selecione **dimensionamento** no menu à esquerda.
1. Selecione a guia **política de escala horizontal** .
1. Selecione uma política de redução na lista suspensa.
1. Quando terminar, selecione **Salvar** . 

### <a name="using-api"></a>Usando a API

Execute um PUT no conjunto de dimensionamento de máquinas virtuais usando a API 2019-03-01:

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01 

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["NewestVM"]  
        } 
    }    
}
```
### <a name="azure-powershell"></a>Azure PowerShell

Atualizar a política de escala em um conjunto de dimensionamento existente:

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>CLI do Azure 2.0

Veja a seguir um exemplo de como atualizar a política de escala horizontal de um conjunto de dimensionamento existente: 

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>Usando o modelo

Em seu modelo, em "Propriedades", modifique o modelo como abaixo e reimplante: 

```json
"scaleInPolicy": {  
      "rules": ["NewestVM"]  
} 
```

O mesmo processo será aplicado se você decidir alterar ' NewestVM ' para ' default ' ou ' OldestVM '

## <a name="instance-protection-and-scale-in-policy"></a>Proteção de instância e política de expansão

Os conjuntos de dimensionamento de máquinas virtuais fornecem dois tipos de [proteção de instância](./virtual-machine-scale-sets-instance-protection.md#types-of-instance-protection):

1. Proteger contra redução horizontal
2. Proteger de ações de conjunto de dimensionamento

Uma máquina virtual protegida não é excluída por meio de uma ação de redução, independentemente da política de redução na aplicação. Por exemplo, se VM_0 (VM mais antiga no conjunto de dimensionamento) for protegida de scale-in e o conjunto de dimensionamento tiver a política de dimensionamento ' OldestVM ' habilitada, VM_0 não será considerada para ser dimensionada no mesmo, mesmo que seja a VM mais antiga no conjunto de dimensionamento. 

Uma máquina virtual protegida pode ser excluída manualmente pelo usuário a qualquer momento, independentemente da política de expansão habilitada no conjunto de dimensionamento. 

## <a name="usage-examples"></a>Exemplos de uso 

Os exemplos a seguir demonstram como um conjunto de dimensionamento de máquinas virtuais selecionará as VMs a serem excluídas quando um evento de redução na escala for disparado. As máquinas virtuais com as IDs de instância mais alta são consideradas as VMs mais novas no conjunto de dimensionamento e as VMs com as menores IDs de instância são consideradas as VMs mais antigas no conjunto de dimensionamento. 

### <a name="oldestvm-scale-in-policy"></a>Política de dimensionamento OldestVM

| Evento                 | IDs de instância em na zona 1  | IDs de instância em zona 1  | IDs de instância em na zona 3  | Seleção de escala                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Inicial               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Reduzir horizontalmente              | 3, 4, 5, 10            | **_2_* _, 6, 9, 11      | 1, 7, 8                | Escolha entre Zona 1 e 2, mesmo que Zona 3 tenha a VM mais antiga. Exclua VM2 de Zona 2 como é a VM mais antiga nessa zona.   |
| Reduzir horizontalmente              | _*_3_*_ , 4, 5, 10      | 6, 9, 11               | 1, 7, 8                | Escolha Zona 1 embora Zona 3 tenha a VM mais antiga. Exclua VM3 de Zona 1 como é a VM mais antiga nessa zona.                  |
| Reduzir horizontalmente              | 4, 5, 10               | 6, 9, 11               | _*_1_*_ , 7, 8          | As zonas são balanceadas. Exclua VM1 em Zona 3 como é a VM mais antiga no conjunto de dimensionamento.                                               |
| Reduzir horizontalmente              | _*_4_*_ , 5, 10         | 6, 9, 11               | 7, 8                   | Escolha entre Zona 1 e Zona 2. Exclua VM4 em Zona 1 como é a VM mais antiga entre as duas zonas.                              |
| Reduzir horizontalmente              | 5, 10                  | _*_6_*_ , 9, 11         | 7, 8                   | Escolha Zona 2 embora Zona 1 tenha a VM mais antiga. Exclua VM6 em Zona 1 como é a VM mais antiga nessa zona.                    |
| Reduzir horizontalmente              | _*_5_*_ , 10            | 9, 11                  | 7, 8                   | As zonas são balanceadas. Exclua VM5 em Zona 1 como é a VM mais antiga no conjunto de dimensionamento.                                                |

Para conjuntos de dimensionamento de máquinas virtuais não zonais, a política seleciona a VM mais antiga no conjunto de dimensionamento para exclusão. Qualquer VM "protegida" será ignorada para exclusão.

### <a name="newestvm-scale-in-policy"></a>Política de dimensionamento NewestVM

| Evento                 | IDs de instância em na zona 1  | IDs de instância em zona 1  | IDs de instância em na zona 3  | Seleção de escala                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Inicial               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Reduzir horizontalmente              | 3, 4, 5, 10            | 2, 6, 9, _*_11_*_      | 1, 7, 8                | Escolha entre Zona 1 e 2. Exclua VM11 de Zona 2 como é a VM mais recente entre as duas zonas.                                |
| Reduzir horizontalmente              | 3, 4, 5, _*_10_*_      | 2, 6, 9                | 1, 7, 8                | Escolha Zona 1, pois ela tem mais VMs do que as outras duas zonas. Exclua VM10 de Zona 1 como é a VM mais recente nessa zona.          |
| Reduzir horizontalmente              | 3, 4, 5                | 2, 6, _*_9_*_          | 1, 7, 8                | As zonas são balanceadas. Exclua VM9 em Zona 2, pois é a VM mais recente no conjunto de dimensionamento.                                                |
| Reduzir horizontalmente              | 3, 4, 5                | 2, 6                   | 1, 7, _*_8_*_          | Escolha entre Zona 1 e Zona 3. Exclua VM8 em Zona 3, pois é a VM mais recente nessa zona.                                      |
| Reduzir horizontalmente              | 3, 4, _*_5_*_          | 2, 6                   | 1, 7                   | Escolha Zona 1 embora Zona 3 tenha a VM mais recente. Exclua VM5 em Zona 1, pois é a VM mais recente nessa zona.                    |
| Reduzir horizontalmente              | 3, 4                   | 2, 6                   | 1, _ *_7_**             | As zonas são balanceadas. Exclua VM7 em Zona 3, pois é a VM mais recente no conjunto de dimensionamento.                                                |

Para conjuntos de dimensionamento de máquinas virtuais não zonais, a política seleciona a VM mais nova no conjunto de dimensionamento para exclusão. Qualquer VM "protegida" será ignorada para exclusão. 

## <a name="troubleshoot"></a>Solucionar problemas

1. Falha ao habilitar scaleInPolicy se você receber um erro ' BadRequest ' com uma mensagem de erro informando "não foi possível encontrar o membro ' scaleInPolicy ' no objeto do tipo ' Properties '" e, em seguida, verifique a versão da API usada para o conjunto de dimensionamento de máquinas virtuais. A versão de API 2019-03-01 ou superior é necessária para este recurso.

2. Seleção errada de VMs para Scale-in consulte os exemplos acima. Se o conjunto de dimensionamento de máquinas virtuais for uma implantação zonal, a política de dimensionamento será aplicada primeiro às zonas desbalanceadas e, em seguida, ao conjunto de dimensionamento após a zona equilibrada. Se a ordem de expansão não for consistente com os exemplos acima, gere uma consulta com a equipe do conjunto de dimensionamento de máquinas virtuais para solução de problemas.

## <a name="next-steps"></a>Próximas etapas

Aprenda como [Implantar o aplicativo](virtual-machine-scale-sets-deploy-app.md) em conjuntos de dimensionamento de máquinas virtuais
