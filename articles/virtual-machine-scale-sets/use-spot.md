---
title: Criar um conjunto de dimensionamento que usa máquinas virtuais do Azure Spot
description: Saiba como criar conjuntos de dimensionamento de máquinas virtuais do Azure que usam máquinas virtuais do Azure spot para economizar em custos.
author: JagVeerappan
ms.author: jagaveer
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: spot
ms.date: 02/26/2021
ms.reviewer: cynthn
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: ec73d1363fb18d1d6c46589fe69879a8f6df1dab
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722558"
---
# <a name="azure-spot-virtual-machines-for-virtual-machine-scale-sets"></a>Máquinas virtuais do Azure spot para conjuntos de dimensionamento de máquinas virtuais 

O uso de máquinas virtuais do Azure Spot em conjuntos de dimensionamento permite que você aproveite a capacidade não utilizada a uma economia de custo significativa. A qualquer momento, quando o Azure precisar da capacidade de volta, a infraestrutura do Azure removerá as instâncias de máquina virtual do Azure Spot. Portanto, as instâncias de máquina virtual do Azure Spot são ótimas para cargas de trabalho que podem lidar com interrupções como trabalhos de processamento em lotes, ambientes de desenvolvimento/teste, cargas de trabalho de computação grande e muito mais.

A quantidade de capacidade disponível pode variar com base no tamanho, região, hora do dia e etc. Ao implantar instâncias de máquina virtual do Azure Spot em conjuntos de dimensionamento, o Azure alocará a instância somente se houver capacidade disponível, mas não há SLA para essas instâncias. Um conjunto de dimensionamento de máquinas virtuais de pontos do Azure é implantado em um único domínio de falha e não oferece nenhuma garantia de alta disponibilidade.


## <a name="pricing"></a>Preços

Os preços das instâncias de máquina virtual do Azure Spot são variáveis, com base na região e no SKU. Para obter mais informações, consulte os preços para [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/). 


Com o preço variável, você tem a opção de definir um preço máximo, em dólares americanos (USD), usando até cinco casas decimais. Por exemplo, o valor `0.98765`seria um preço máximo de $0,98765 USD por hora. Se você definir o preço máximo como `-1`, a instância não será removida com base no preço. O preço da instância será o preço atual para a máquina virtual do Azure spot ou o preço de uma instância padrão, que nunca é menor, desde que haja capacidade e cota disponível.


## <a name="limitations"></a>Limitações

Não há suporte para os seguintes tamanhos em máquinas virtuais do Azure spot:
 - Série B
 - Versões promocionais de qualquer tamanho (como Dv2, NV, NC, tamanhos promocionais de H)

A máquina virtual do Azure Spot pode ser implantada em qualquer região, exceto Microsoft Azure a 21Vianet da China.

<a name="channel"></a>

Atualmente, há suporte para os seguintes [tipos de oferta](https://azure.microsoft.com/support/legal/offer-details/) :

-   Contrato Enterprise
-   Código de oferta pago pelo uso (003P)
-   Patrocinado (0036P e 0136P)
- Para o CSP (provedor de serviços de nuvem), consulte o [Partner Center](/partner-center/azure-plan-get-started) ou contate seu parceiro diretamente.

## <a name="eviction-policy"></a>Política de remoção

Ao criar um conjunto de dimensionamento usando máquinas virtuais do Azure Spot, você pode definir a política de remoção como *desalocar* (padrão) ou *excluir*. 

A política *Desalocar* move suas instâncias removidas para o estado de parado desalocado permitindo que você reimplante instâncias removidas. No entanto, não há nenhuma garantia de que a alocação terá êxito. As VMs deslocadas afetarão sua cota de instância do conjunto de dimensionamento e você será cobrado pelos discos subjacentes. 

Se você quiser que suas instâncias sejam excluídas quando forem removidas, poderá definir a *política de remoção* a ser excluída. Com a política de remoção definida para excluir, você pode criar novas VMs, aumentando a propriedade de contagem de instância do conjunto de dimensionamento. As VMs removidas são excluídas junto com seus discos subjacentes e, portanto, você não será cobrado pelo armazenamento. Você também pode usar o recurso de dimensionamento automático dos conjuntos de dimensionamento para tentar e compensar automaticamente as VMs removidas, mas não há nenhuma garantia de que a alocação terá êxito. É recomendável que você use apenas o recurso de dimensionamento automático em conjuntos de escala de máquina virtual do Azure Spot quando você define a política de remoção a ser excluída para evitar o custo dos seus discos e atingir os limites de cota. 

Os usuários podem optar por receber notificações na VM por meio [do Azure eventos agendados](../virtual-machines/linux/scheduled-events.md). Isso notificará você se suas VMs estiverem sendo removidas e você terá 30 segundos para concluir todos os trabalhos e realizar tarefas de desligamento antes da remoção. 

<a name="bkmk_try"></a>
## <a name="try--restore-preview"></a>Tentar restaurar & (versão prévia)

Esse novo recurso de nível de plataforma usará o ia para tentar restaurar automaticamente as instâncias de máquina virtual do Azure Spot removidas dentro de um conjunto de dimensionamento para manter a contagem de instâncias de destino. 

> [!IMPORTANT]
> Tente & Restore esteja atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Experimente & os benefícios da restauração:
- Tenta restaurar as máquinas virtuais do Azure Spot removidas devido à capacidade.
- As máquinas virtuais do Azure Spot restauradas devem ser executadas por uma duração maior, com uma probabilidade menor de uma remoção disparada pela capacidade.
- Melhora o tempo de vida de uma máquina virtual de ponto do Azure, para que as cargas de trabalho sejam executadas por uma duração maior.
- Ajuda os conjuntos de dimensionamento de máquinas virtuais a manter a contagem de destino para máquinas virtuais de ponto do Azure, semelhante a manter o recurso de contagem de destino que já existe para VMs pagas conforme o uso.

Tente & restauração seja desabilitada em conjuntos de dimensionamento que usam o [dimensionamento automático](virtual-machine-scale-sets-autoscale-overview.md). O número de VMs no conjunto de dimensionamento é orientado pelas regras de dimensionamento automático.

### <a name="register-for-try--restore"></a>Registrar-se para a restauração do try &

Antes de usar o recurso tentar & Restore, você deve registrar sua assinatura para a versão prévia. O registro pode levar vários minutos para ser concluído. Você pode usar o CLI do Azure ou o PowerShell para concluir o registro do recurso.


**Usar a CLI**

Use o [registro de recurso AZ](/cli/azure/feature#az-feature-register) para habilitar a visualização para sua assinatura. 

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SpotTryRestore 
```

O registro de recursos pode levar até 15 minutos. Para verificar o status do registro: 

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SpotTryRestore 
```

Depois que o recurso tiver sido registrado para sua assinatura, conclua o processo de aceitação propagando a alteração para o provedor de recursos de computação. 

```azurecli-interactive
az provider register --namespace Microsoft.Compute 
```
**Usar o PowerShell** 

Use o cmdlet [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) para habilitar a visualização para sua assinatura. 

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SpotTryRestore -ProviderNamespace Microsoft.Compute 
```

O registro de recursos pode levar até 15 minutos. Para verificar o status do registro: 

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SpotTryRestore -ProviderNamespace Microsoft.Compute 
```

Depois que o recurso tiver sido registrado para sua assinatura, conclua o processo de aceitação propagando a alteração para o provedor de recursos de computação. 

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute 
```

## <a name="placement-groups"></a>Grupos de posicionamento

O grupo de posicionamento é um constructo semelhante a um conjunto de disponibilidade do Azure, com seus próprios domínios de falha e domínios de atualização. Por padrão, um conjunto de dimensionamento consiste em um único grupo de posicionamento com tamanho máximo de 100 VMs. Se a propriedade do conjunto de dimensionamento chamada `singlePlacementGroup` for definida como *false*, o conjunto de dimensionamento poderá ser composto de vários grupos de posicionamento e terá um intervalo de 0-1.000 VMS. 

> [!IMPORTANT]
> A menos que você esteja usando o InfiniBand com o HPC, é altamente recomendável definir a propriedade do conjunto de dimensionamento `singlePlacementGroup` como *false* para habilitar vários grupos de posicionamento para um melhor dimensionamento na região ou zona. 

## <a name="deploying-azure-spot-virtual-machines-in-scale-sets"></a>Implantando máquinas virtuais do Azure Spot em conjuntos de dimensionamento

Para implantar máquinas virtuais de ponto do Azure em conjuntos de dimensionamento, você pode definir o novo sinalizador de *prioridade* para *identificar*. Todas as VMs no conjunto de dimensionamento serão definidas como Spot. Para criar um conjunto de dimensionamento com máquinas virtuais de ponto do Azure, use um dos seguintes métodos:
- [Azure portal](#portal)
- [CLI do Azure](#azure-cli)
- [PowerShell do Azure](#powershell)
- [Modelos do Gerenciador de Recursos do Azure](#resource-manager-templates)

## <a name="portal"></a>Portal

O processo para criar um conjunto de dimensionamento que usa máquinas virtuais do Azure Spot é o mesmo detalhado no [artigo de introdução](quick-create-portal.md). Ao implantar um conjunto de dimensionamento, você pode optar por definir o sinalizador de spot e a política de remoção: ![ criar um conjunto de dimensionamento com máquinas virtuais de ponto do Azure](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>CLI do Azure

O processo para criar um conjunto de dimensionamento com máquinas virtuais do Azure Spot é o mesmo detalhado no [artigo de introdução](quick-create-cli.md). Basta adicionar '--Priority Spot', e adicionar `--max-price`. Neste exemplo, usamos `-1` para `--max-price` para que a instância não seja removida com base no preço.

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --single-placement-group false \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 
```

## <a name="powershell"></a>PowerShell

O processo para criar um conjunto de dimensionamento com máquinas virtuais do Azure Spot é o mesmo detalhado no [artigo de introdução](quick-create-powershell.md).
Basta adicionar '-Priority Spot', e fornecer um `-max-price` para [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig).

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    -max-price -1
```

## <a name="resource-manager-templates"></a>Modelos do Gerenciador de Recursos

O processo para criar um conjunto de dimensionamento que usa máquinas virtuais do Azure Spot é o mesmo detalhado no artigo de introdução para [Linux](quick-create-template-linux.md) ou [Windows](quick-create-template-windows.md). 

Para implantações de modelo de máquina virtual do Azure Spot, use `"apiVersion": "2019-03-01"` ou posterior. 

Adicione as `priority` `evictionPolicy` Propriedades, e `billingProfile` à `"virtualMachineProfile":` seção e a `"singlePlacementGroup": false,` Propriedade à `"Microsoft.Compute/virtualMachineScaleSets"` seção em seu modelo:

```json

{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  },
  "properties": {
    "singlePlacementGroup": false,
    }

        "virtualMachineProfile": {
              "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
            },
```

Para excluir a instância depois que ela tiver sido removida, altere o parâmetro `evictionPolicy` para `Delete`.


## <a name="simulate-an-eviction"></a>Simular uma remoção

Você pode [simular uma remoção](/rest/api/compute/virtualmachines/simulateeviction) de uma máquina virtual de ponto do Azure para testar o quão bem seu aplicativo responderá a uma remoção repentina. 

Substitua o seguinte pelas suas informações: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

`Response Code: 204` significa que a remoção simulada foi bem-sucedida. 

## <a name="faq"></a>Perguntas frequentes

**P:** Uma vez criada, é uma instância de máquina virtual de ponto do Azure igual à instância padrão?

**R:** Sim, exceto que não há SLA para máquinas virtuais do Azure Spot e elas podem ser removidas a qualquer momento.


**P:** O que fazer quando você é removido, mas ainda precisa de capacidade?

**R:** Recomendamos que você use VMs padrão em vez de máquinas virtuais do Azure Spot se precisar de capacidade imediatamente.


**P:** Como a cota é gerenciada para a máquina virtual do Azure Spot?

**R:** As instâncias de máquina virtual do Azure Spot e as instâncias padrão terão pools de cotas separados. A cota de máquina virtual do Azure Spot será compartilhada entre as VMs e as instâncias do conjunto de dimensionamento. Para saber mais, confira [Assinatura e limites de serviço, cotas e restrições do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).


**P:** Posso solicitar cota adicional para a máquina virtual do Azure Spot?

**R:** Sim, você poderá enviar a solicitação para aumentar sua cota para máquinas virtuais do Azure Spot por meio do [processo de solicitação de cota padrão](../azure-portal/supportability/per-vm-quota-requests.md).


**P:** Posso converter os conjuntos de dimensionamento existentes em conjuntos de dimensionamento de máquinas virtuais especiais do Azure?

**R:** Não, a definição do sinalizador `Spot` só tem suporte no momento da criação.


**P:** Se eu estivesse usando `low` para conjuntos de dimensionamento de baixa prioridade, preciso começar a usar `Spot` em vez disso?

**R:** Por enquanto, `low` e `Spot` funcionarão, mas você deve começar a fazer a transição para usar `Spot`.


**P:** Posso criar um conjunto de dimensionamento com VMs regulares e máquinas virtuais de ponto do Azure?

**R:** Não, um conjunto de escala não oferece suporte a mais de um tipo de prioridade.


**P:**  Posso usar o dimensionamento automático com conjuntos de escala de máquina virtual do Azure Spot?

**R:** Sim, você pode definir regras de dimensionamento automático no conjunto de dimensionamento de máquinas virtuais do Azure Spot. Se suas VMs forem removidas, o dimensionamento automático poderá tentar criar novas máquinas virtuais do Azure Spot. Lembre-se de que essa capacidade não é garantida. 


**P:**  O dimensionamento automático funciona com as políticas de remoção (deslocar e excluir)?

**R:** Sim, no entanto, é recomendável que você defina sua política de remoção para excluir ao usar o dimensionamento automático. Isso ocorre porque instâncias desalocadas são contadas em relação a sua capacidade de contagem no conjunto de dimensionamento. Ao usar o dimensionamento automático, você provavelmente atingirá sua contagem de instâncias de destino rapidamente devido a instâncias desalocadas, removidas. Além disso, suas operações de dimensionamento podem ser afetadas por remoções de spot. Por exemplo, as instâncias do conjunto de dimensionamento de máquinas virtuais podem ficar abaixo da contagem mínima definida devido a várias remoções de spot durante operações de dimensionamento. 


**P:** Onde posso postar perguntas?

**R:** Você pode postar e marcar sua pergunta com `azure-spot` em [Perguntas e respostas](/answers/topics/azure-spot.html). 

## <a name="next-steps"></a>Próximas etapas

Confira a [página de preços de conjunto de dimensionamento de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) para obter detalhes sobre preços.
