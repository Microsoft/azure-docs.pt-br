---
title: Usar políticas personalizadas para reduzir horizontalmente conjuntos de dimensionamento de máquinas virtuais do Azure
description: Saiba como usar políticas personalizadas para reduzir horizontalmente conjuntos de dimensionamento de máquinas virtuais do Azure que usam a configuração de dimensionamento automático para gerenciar a contagem de instâncias
services: virtual-machine-scale-sets
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: scale-in-policy
ms.date: 02/26/2020
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: 10ed223311995e746beca24f7a09d795f7b3f0e6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105935696"
---
# <a name="use-custom-scale-in-policies-with-azure-virtual-machine-scale-sets"></a>Usar políticas personalizadas para reduzir horizontalmente conjuntos de dimensionamento de máquinas virtuais do Azure

Uma implantação de conjunto de dimensionamento de máquinas virtuais pode ser expandida ou reduzida horizontalmente com base em uma matriz de métricas, incluindo plataforma e métricas personalizadas definidas pelo usuário. Embora uma expansão crie novas máquinas virtuais com base no modelo do conjunto de dimensionamento, uma redução horizontal afeta a execução de máquinas virtuais que podem ter diferentes configurações e/ou funções à medida que a carga de trabalho do conjunto de dimensionamento evolui. 

O recurso da política de redução horizontal fornece aos usuários uma maneira de configurar a ordem na qual as máquinas virtuais são reduzidas horizontalmente, por meio de três configurações de redução: 

1. Padrão
2. NewestVM
3. OldestVM

### <a name="default-scale-in-policy"></a>Política de redução horizontal padrão

Por padrão, o conjunto de dimensionamento de máquinas virtuais aplica essa política para determinar quais instâncias serão reduzidas. Com a política *Padrão*, as VMs são selecionadas para serem reduzidas horizontalmente na ordem a seguir:

1. Balancear máquinas virtuais entre zonas de disponibilidade (se o conjunto de dimensionamento for implantado na configuração por zona)
2. Balancear máquinas virtuais entre domínios de falha (melhor esforço)
3. Excluir máquina virtual com a ID de instância mais alta

Os usuários não precisam especificar uma política de redução horizontal se quiserem que a ordem padrão seja seguida.

Observe que o balanceamento entre zonas de disponibilidade ou domínios de falha não move instâncias entre zonas de disponibilidade ou domínios de falha. O balanceamento é obtido por meio da exclusão de máquinas virtuais de zonas de disponibilidade desbalanceadas ou domínios de falha, até que a distribuição de máquinas virtuais se torne equilibrada.

### <a name="newestvm-scale-in-policy"></a>Política de redução horizontal NewestVM

Essa política excluirá a máquina virtual mais nova criada no conjunto de dimensionamento, depois de balancear as VMs entre as zonas de disponibilidade (para implantações por zonas). A habilitação desta política requer uma alteração de configuração no modelo do conjunto de dimensionamento de máquinas virtuais.

### <a name="oldestvm-scale-in-policy"></a>Política de redução horizontal OldestVM

Essa política excluirá a máquina virtual mais antiga criada no conjunto de dimensionamento, depois de balancear as VMs entre as zonas de disponibilidade (para implantações por zonas). A habilitação desta política requer uma alteração de configuração no modelo do conjunto de dimensionamento de máquinas virtuais.

## <a name="enabling-scale-in-policy"></a>Habilitando políticas de redução horizontal

Uma política de redução horizontal é definida no modelo do conjunto de dimensionamento de máquinas virtuais. Conforme observado nas seções acima, uma definição de política de redução horizontal é necessária ao usar as políticas 'NewestVM' e 'OldestVM'. O conjunto de dimensionamento de máquinas virtuais usará automaticamente a política de redução horizontal 'Padrão' se não houver nenhuma definição de política de redução horizontal encontrada no modelo do conjunto de dimensionamento. 

Uma política de redução horizontal pode ser definida no modelo do conjunto de dimensionamento de máquinas virtuais das seguintes maneiras:

### <a name="azure-portal"></a>Portal do Azure
 
As etapas a seguir definem a política de redução horizontal ao criar um novo conjunto de dimensionamento. 
 
1. Vá para **Conjuntos de dimensionamento de máquinas virtuais**.
1. Selecione **Adicionar+** para criar um novo conjunto de dimensionamento.
1. Vá para a guia **Dimensionamento**. 
1. Encontre a seção **política de redução horizontal**.
1. Selecione uma política de redução horizontal no menu suspenso.
1. Quando terminar de criar o novo conjunto de dimensionamento, selecione o botão **Revisar + Criar**.

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

Crie um grupo de recursos e um novo conjunto de dimensionamento com a política de redução horizontal definida como *OldestVM*.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "<VMSS location>"
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "<VMSS location>" `
  -VMScaleSetName "myScaleSet" `
  -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>CLI do Azure 2.0

O exemplo a seguir adiciona uma política de redução ao criar um novo conjunto de dimensionamento. Primeiro crie um grupo de recursos e um novo conjunto de dimensionamento com a política de redução horizontal como *OldestVM*. 

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

### <a name="using-template"></a>Usando um Modelo

No modelo, em "propriedades", adicione o seguinte:

```json
"scaleInPolicy": {  
      "rules": ["OldestVM"]  
}
```

Os blocos acima especificam que o conjunto de dimensionamento de máquinas virtuais excluirá a VM mais antiga em um conjunto de dimensionamento com balanceamento de zona, quando uma redução horizontal for disparada (por meio de dimensionamento automático ou exclusão manual).

Quando um conjunto de dimensionamento de máquinas virtuais não for balanceado por zona, o conjunto de dimensionamento excluirá primeiro as VMs em todas as zonas desbalanceadas. Dentro das zonas desbalanceadas, o conjunto de dimensionamento usará a política de redução especificada acima para determinar qual VM será reduzida horizontalmente. Nesse caso, em uma zona desbalanceada, o conjunto de dimensionamento selecionará a VM mais antiga nessa zona a ser excluída.

Para o conjunto de dimensionamento de máquinas virtuais sem zonas, a política seleciona a VM mais antiga no conjunto de dimensionamento para exclusão.

O mesmo processo se aplica ao usar 'NewestVM' na política de redução horizontal acima.

## <a name="modifying-scale-in-policies"></a>Modificando políticas de redução horizontal

A modificação da política de redução horizontal segue o mesmo processo da aplicação da política de redução horizontal. Por exemplo, se você quiser alterar a política de 'OldestVM para 'NewestVM', no exemplo acima, você poderá fazer isso por meio do:

### <a name="azure-portal"></a>Portal do Azure

Você pode modificar a política de redução horizontal de um conjunto de dimensionamento existente por meio do portal do Azure. 
 
1. Em um conjunto de dimensionamento de máquinas virtuais existente, selecione **Dimensionamento** no menu à esquerda.
1. Selecione a guia **Política de redução horizontal**.
1. Selecione uma política de redução horizontal no menu suspenso.
1. Quando terminar, selecione **Salvar**. 

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

Atualizar a política de redução horizontal em um conjunto de dimensionamento existente:

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>CLI do Azure 2.0

A seguir temos um exemplo de como atualizar a política de redução horizontal de um conjunto de dimensionamento existente: 

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>Usando um Modelo

No modelo, em "Propriedades", modifique o modelo conforme abaixo e reimplante: 

```json
"scaleInPolicy": {  
      "rules": ["NewestVM"]  
} 
```

O mesmo processo será aplicado se você decidir alterar 'NewestVM' para 'Padrão' ou 'OldestVM'

## <a name="instance-protection-and-scale-in-policy"></a>Proteção de instância e política de redução horizontal

Os conjuntos de dimensionamento de máquinas virtuais fornecem dois tipos de [proteção de instância](./virtual-machine-scale-sets-instance-protection.md#types-of-instance-protection):

1. Proteger contra redução horizontal
2. Proteger contra as ações do conjunto de dimensionamento

Uma máquina virtual protegida não é excluída por meio de uma ação de redução horizontal, independentemente da política de redução aplicada. Por exemplo, se a VM_0 (VM mais antiga no conjunto de dimensionamento) estiver protegida da redução horizontal e o conjunto de dimensionamento tiver a política de dimensionamento 'OldestVM' habilitada, a VM_0 não será considerada para ser reduzida, mesmo que seja a VM mais antiga no conjunto de dimensionamento. 

Uma máquina virtual protegida pode ser excluída manualmente a qualquer momento pelo usuário, independentemente da política de redução horizontal habilitada no conjunto de dimensionamento. 

## <a name="usage-examples"></a>Exemplos de uso 

Os exemplos a seguir demonstram como um conjunto de dimensionamento de máquinas virtuais seleciona as VMs a serem excluídas quando um evento de redução é disparado. As máquinas virtuais com as IDs de instância mais alta são consideradas as VMs mais novas no conjunto de dimensionamento e as VMs com as IDs de instância menores são consideradas as VMs mais antigas no conjunto de dimensionamento. 

### <a name="oldestvm-scale-in-policy"></a>Política de redução horizontal OldestVM

| Evento                 | IDs de instância na Zona 1  | IDs de instância na Zona 2  | IDs de instância na Zona 3  | Seleção de redução horizontal                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Inicial               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Reduzir horizontalmente              | 3, 4, 5, 10            | ***2***, 6, 9, 11      | 1, 7, 8                | Escolha entre a Zona 1 e 2, mesmo que a Zona 3 tenha a VM mais antiga. Exclua a VM2 da Zona 2, como ela é a VM mais antiga nessa zona.   |
| Reduzir horizontalmente              | ***3***, 4, 5, 10      | 6, 9, 11               | 1, 7, 8                | Escolha a Zona 1 embora a Zona 3 tenha a VM mais antiga. Exclua a VM3 da Zona 1 como ela é a VM mais antiga nessa zona.                  |
| Reduzir horizontalmente              | 4, 5, 10               | 6, 9, 11               | ***1***, 7, 8          | As zonas são balanceadas. Exclua a VM1 na Zona 3, como ela é a VM mais antiga no conjunto de dimensionamento.                                               |
| Reduzir horizontalmente              | ***4***, 5, 10         | 6, 9, 11               | 7, 8                   | Escolha entre a Zona 1 e a Zona 2. Exclua a VM4 na Zona 1, como ela é a VM mais antiga entre as duas zonas.                              |
| Reduzir horizontalmente              | 5, 10                  | ***6***, 9, 11         | 7, 8                   | Escolha a Zona 2, mesmo que a Zona 1 tenha a VM mais antiga. Exclua a VM6 na Zona 1, como ela é a VM mais antiga nessa zona.                    |
| Reduzir horizontalmente              | ***5***, 10            | 9, 11                  | 7, 8                   | As zonas são balanceadas. Exclua a VM5 na Zona 1, como ela é a VM mais antiga no conjunto de dimensionamento.                                                |

Para conjuntos de dimensionamento de máquinas virtuais sem zonas, a política seleciona a VM mais antiga no conjunto de dimensionamento para exclusão. Qualquer VM "protegida" será ignorada na exclusão.

### <a name="newestvm-scale-in-policy"></a>Política de redução horizontal NewestVM

| Evento                 | IDs de instância na Zona 1  | IDs de instância na Zona 2  | IDs de instância na Zona 3  | Seleção de redução horizontal                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Inicial               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Reduzir horizontalmente              | 3, 4, 5, 10            | 2, 6, 9, ***11***      | 1, 7, 8                | Escolha entre a Zona 1 e 2. Exclua a VM11 da Zona 2, como ela é a VM mais nova entre as duas zonas.                                |
| Reduzir horizontalmente              | 3, 4, 5, ***10***      | 2, 6, 9                | 1, 7, 8                | Escolha a Zona 1, pois ela tem mais VMs do que as outras duas zonas. Exclua a VM10 da Zona 1, como ela é a VM mais nova nessa zona.          |
| Reduzir horizontalmente              | 3, 4, 5                | 2, 6, ***9***          | 1, 7, 8                | As zonas são balanceadas. Exclua a VM9 na Zona 2, como ela é a VM mais nova no conjunto de dimensionamento.                                                |
| Reduzir horizontalmente              | 3, 4, 5                | 2, 6                   | 1, 7, ***8***          | Escolha entre a Zona 1 e a Zona 3. Exclua a VM8 na Zona 3, como ela é a VM mais nova nessa zona.                                      |
| Reduzir horizontalmente              | 3, 4, ***5***          | 2, 6                   | 1, 7                   | Escolha a Zona 1, mesmo que a Zona 3 tenha a VM mais nova. Exclua a VM5 da Zona 1, como ela é a VM mais nova nessa zona.                    |
| Reduzir horizontalmente              | 3, 4                   | 2, 6                   | 1, ***7***             | As zonas são balanceadas. Exclua a VM7 na Zona 3, como ela é a VM mais nova no conjunto de dimensionamento.                                                |

Para conjuntos de dimensionamento de máquinas virtuais sem zonas, a política seleciona a VM mais nova no conjunto de dimensionamento para exclusão. Qualquer VM "protegida" será ignorada na exclusão. 

## <a name="troubleshoot"></a>Solucionar problemas

1. Falha ao habilitar a scaleInPolicy Se você receber um erro 'BadRequest' com uma mensagem de erro informando “não foi possível encontrar o membro 'scaleInPolicy' no objeto do tipo 'Properties'” verifique a versão da API usada para o conjunto de dimensionamento de máquinas virtuais. Para este recurso é necessária a versão de API 2019-03-01 ou superior.

2. A seleção errada de VMs para redução horizontal faz referência aos exemplos acima. Se o conjunto de dimensionamento de máquinas virtuais for uma implantação por zona, a política de redução horizontal será aplicada primeiro às zonas desbalanceadas e depois em todo o conjunto de dimensionamento, assim que a zona for balanceada. Se a ordem da redução não for consistente com os exemplos acima, gere uma consulta com a equipe do conjunto de dimensionamento de máquinas virtuais para solução de problemas.

## <a name="next-steps"></a>Próximas etapas

Aprenda como [Implantar o aplicativo](virtual-machine-scale-sets-deploy-app.md) em conjuntos de dimensionamento de máquinas virtuais
