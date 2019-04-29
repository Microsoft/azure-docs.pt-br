---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 3c008e77116a9b42a2ea137069529c5e241adddd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61456831"
---
Você pode [dimensionar automaticamente](../articles/azure-monitor/platform/autoscale-best-practices.md) suas [VMs (máquinas virtuais)](../articles/virtual-machines/windows/overview.md) quando usa [conjuntos de dimensionamento de máquinas virtuais](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) e o [recurso de dimensionamento automático do Azure Monitor](../articles/azure-monitor/platform/autoscale-overview.md). Suas VMs precisam ser membros de um conjunto de dimensionamento definido para serem dimensionadas automaticamente. Este artigo fornece informações que permitem a você entender melhor como dimensionar suas VMs vertical e horizontalmente usando métodos automáticos e manuais.

## <a name="horizontal-or-vertical-scaling"></a>Dimensionamento horizontal ou vertical

O recurso de dimensionamento automático do Azure Monitor só pode ser dimensionado horizontalmente, que é um aumento ("expansão") ou diminuição ("redução") do número de VMs. O dimensionamento horizontal é mais flexível em uma situação de nuvem, pois permite que você execute milhares de VMs para lidar com a carga. Você escalar horizontalmente alterando a capacidade (ou contagem de instância) do conjunto de dimensionamento automaticamente ou manualmente. 

O dimensionamento vertical mantém o mesmo número de VMs, mas torna as VMs mais ("para cima") ou menos ("para baixo") potentes. A capacidade é medida em atributos como espaço em disco, velocidade da CPU ou memória. O dimensionamento vertical depende da disponibilidade de um hardware maior, que atinge rapidamente um limite superior e pode variar por região. O dimensionamento vertical normalmente também exige que uma VM pare e reinicie. O dimensionamento vertical é feito definindo um novo tamanho na configuração das VMs no conjunto de dimensionamento.

Usando runbooks na [Automação do Azure](../articles/automation/automation-intro.md), você pode facilmente [ escalar VMs em um conjunto de dimensionamento](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-vertical-scale-reprovision.md) verticalmente.

## <a name="create-a-virtual-machine-scale-set"></a>Criar um conjunto de dimensionamento de máquinas virtuais

Os conjuntos de dimensionamento facilitam a implantação e o gerenciamento de VMs idênticas como um conjunto. Você pode criar conjuntos de dimensionamento Linux ou Windows usando o [portal do Azure](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md), o [Azure PowerShell](../articles/virtual-machines/windows/tutorial-create-vmss.md) ou a [CLI do Azure](../articles/virtual-machines/linux/tutorial-create-vmss.md). Você também pode criar e gerenciar conjuntos de dimensionamento com SDKs, como [Python](https://azure.microsoft.com/develop/python/) ou [Node.js](/nodejs/azure), ou diretamente com as [APIs REST](/rest/api/compute/virtualmachinescalesets). O dimensionamento automático de VMs é feito pela aplicação de métrica e regras ao conjunto de dimensionamento.

## <a name="configure-autoscale-for-a-scale-set"></a>Configurar o dimensionamento automático para um conjunto de dimensionamento

O dimensionamento automático fornece o número correto de VMs para lidar com a carga em seu aplicativo. Ele permite que você adicione VMs para lidar com o aumento de carga e economizar dinheiro removendo VMs ociosas. Você especifica um número mínimo e máximo de VMs a serem executadas com base em um conjunto de regras. Ter um mínimo assegura que seu aplicativo estará sempre em execução, mesmo sem carga. Ter um valor máximo limita seu custo por hora total possível.

Você pode habilitar o dimensionamento automático quando cria o conjunto de dimensionamento usando o [Azure PowerShell](../articles/azure-monitor/platform/powershell-quickstart-samples.md#create-and-manage-autoscale-settings) ou a [CLI do Azure](https://docs.microsoft.com/cli/azure/monitor/autoscale-settings). Você também pode habilitá-lo depois que o conjunto de dimensionamento é criado. Crie um conjunto de dimensionamento, instale a extensão e configure o dimensionamento automático usando um [modelo do Azure Resource Manager](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md). No portal do Azure, habilite o dimensionamento automático do Azure Monitor ou habilite o dimensionamento automático nas configurações do conjunto de dimensionamento.

![Habilitar dimensionamento automático](./media/virtual-machines-autoscale/virtual-machines-autoscale-enable.png)
 
### <a name="metrics"></a>Métricas

O recurso de dimensionamento automático do Azure Monitor permite que você aumente ou diminua o número de VMs em execução com base em [métricas](../articles/azure-monitor/platform/autoscale-common-metrics.md). Por padrão, as VMs fornecem métricas básicas no nível do host para uso da CPU, rede e disco. Quando você configura a coleta de dados de diagnóstico usando a extensão de diagnóstico, outros contadores de desempenho do sistema operacional convidado ficam disponíveis para disco, CPU e memória.

![Critérios de métrica](./media/virtual-machines-autoscale/virtual-machines-autoscale-criteria.png)

Se seu aplicativo precisa ser dimensionado com base nas métricas que não estão disponíveis por meio do host, as VMs no conjunto de dimensionamento precisam ter a [extensão de diagnóstico do Linux](../articles/virtual-machines/linux/diagnostic-extension.md) ou a [extensão de diagnóstico do Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) instalada. Se você criar um conjunto de dimensionamento usando o portal do Azure, precisará também usar o Azure PowerShell ou a CLI do Azure para instalar a extensão com a configuração de diagnóstico necessária.
 
### <a name="rules"></a>Regras

As [regras](../articles/monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md) combinam uma métrica com uma ação a ser executada. Quando as condições da regra forem atendidas, uma ou mais ações de dimensionamento automático são disparadas. Por exemplo, você pode ter uma regra definida que aumenta o número de VMs em 1 se o uso médio da CPU fica acima de 85%.

![Ações de dimensionamento automático](./media/virtual-machines-autoscale/virtual-machines-autoscale-actions.png)
 
### <a name="notifications"></a>Notificações

Você pode [configurar gatilhos](../articles/azure-monitor/platform/autoscale-webhook-email.md) para que URLs Web específicas sejam chamadas ou emails sejam enviados com base nas regras de dimensionamento automático criadas. Webhooks permitem rotear as notificações de alerta do Azure para outros sistemas para pós-processamento ou notificações personalizadas.

## <a name="manually-scale-vms-in-a-scale-set"></a>Dimensionar automaticamente as VMs em um conjunto de dimensionamento

### <a name="horizontal"></a>Horizontal

Você pode adicionar ou remover VMs alterando a capacidade do conjunto de dimensionamento. No portal do Azure, você pode reduzir ou aumentar o número de VMs (mostrado como **contagem de instâncias**) no conjunto de dimensionamento deslizando a barra Condição de substituição na tela Dimensionamento para a esquerda ou para a direita.

Usando o Azure PowerShell, você precisa obter o objeto do conjunto de dimensionamento usando [Get-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss). Defina a propriedade **sku.capacity** para o número de VMs que você deseja e atualize o conjunto de dimensionamento com [Update-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/update-azvmss). Usando a CLI do Azure, altere a capacidade com o parâmetro **– new-capacity** para o comando [az vmss scale](/cli/azure/vmss?view=azure-cli-latest#az-vmss-scale).

### <a name="vertical"></a>Vertical

Você pode alterar manualmente o tamanho das VMs no portal do Azure na tela Tamanho do conjunto de dimensionamento. Você pode usar o Azure PowerShell com Get-AzVmss, definindo a propriedade sku de referência da imagem, em seguida, usando [Update-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/update-azvmss) e [Update-AzVmssInstance](https://docs.microsoft.com/powershell/module/az.compute/update-azvmssinstance).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre conjuntos de dimensionamento em [Considerações de design para conjuntos de dimensionamento](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview.md).

