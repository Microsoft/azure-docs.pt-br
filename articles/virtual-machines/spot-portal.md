---
title: Usar o portal para implantar máquinas virtuais do Azure Spot
description: Como usar Azure PowerShell para implantar máquinas virtuais spot para economizar nos custos.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/14/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 879a3e9b3d3f651a1dea17e76dba503cd2816b9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102098563"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-portal"></a>Implantar máquinas virtuais do Azure Spot usando o portal do Azure

O uso de [máquinas virtuais do Azure Spot](spot-vms.md) permite que você aproveite a capacidade não utilizada a uma economia de custo significativa. A qualquer momento, quando o Azure precisar da capacidade de volta, a infraestrutura do Azure removerá as máquinas virtuais do Azure Spot. Portanto, as máquinas virtuais de ponto do Azure são ótimas para cargas de trabalho que podem lidar com interrupções como trabalhos de processamento em lotes, ambientes de desenvolvimento/teste, cargas de trabalho de computação grande e muito mais.

Os preços para as máquinas virtuais do Azure Spot são variáveis, com base na região e no SKU. Para obter mais informações, consulte preços de VM para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Para obter mais informações sobre como definir o preço máximo, consulte [máquinas virtuais do Azure Spot – preços](spot-vms.md#pricing).

Você tem a opção de definir um preço máximo que está disposto a pagar, por hora, para a VM. O preço máximo para uma máquina virtual de ponto do Azure pode ser definido em dólares americanos (USD), usando até 5 casas decimais. Por exemplo, o valor `0.05701` seria um preço máximo de $0.5701 USD por hora. Se você definir o preço máximo como `-1` , a VM não será removida com base no preço. O preço da VM será o preço atual para o ponto ou o preço de uma VM padrão, o que nunca é menor, desde que haja capacidade e cota disponível.

Quando a VM é removida, você tem a opção de excluir a VM e o disco subjacente ou desalocar a VM para que ela possa ser reiniciada mais tarde.


## <a name="create-the-vm"></a>Criar a VM

Ao implantar uma VM, você pode optar por usar uma instância de spot do Azure.


Na guia **noções básicas** , na seção **detalhes da instância** , **não** é o padrão para usar uma instância de spot do Azure.

![Captura de tela para escolher não, não use uma instância de spot do Azure](./media/spot-portal/no.png)

Se você selecionar **Sim**, a seção será expandida e você poderá escolher o [tipo de remoção e a política de remoção](spot-vms.md#eviction-policy). 

![Captura de tela para escolher Sim, usar uma instância de spot do Azure](./media/spot-portal/yes.png)

Você também pode comparar as taxas de preço e remoção com outras regiões semelhantes selecionando **Exibir histórico de preços e comparar preços em regiões próximas**.

Neste exemplo, a região central do Canadá é mais barata e tem uma taxa de remoção mais baixa do que a região leste dos EUA.

:::image type="content" source="./media/spot-portal/pricing.png" alt-text="Captura de tela das opções de região com a diferença nas taxas de preço e remoção.":::

Você pode alterar a região selecionando a opção que funciona melhor para você e, em seguida, selecionando **OK**.

## <a name="simulate-an-eviction"></a>Simular uma remoção

Você pode [simular uma remoção](/rest/api/compute/virtualmachines/simulateeviction) de uma máquina virtual de ponto do Azure, para testar como seu aplicativo responderá a uma remoção repentina. 

Substitua o seguinte pelas suas informações: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

`Response Code: 204` significa que a remoção simulada foi bem-sucedida. 

## <a name="next-steps"></a>Próximas etapas

Você também pode criar máquinas virtuais do Azure Spot usando o [PowerShell](./windows/spot-powershell.md), a [CLI](./linux/spot-cli.md)ou um [modelo](./linux/spot-template.md).
