---
title: Obter análise de custo e definir orçamentos para o lote do Azure
description: Saiba como obter uma análise de custo, definir um orçamento e reduzir os custos para os recursos de computação subjacentes e as licenças de software usadas para executar suas cargas de trabalho do lote.
ms.topic: how-to
ms.date: 01/29/2021
ms.openlocfilehash: d1fc2d15a7037e56a8056efa67d2017badb77ffd
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99091320"
---
# <a name="get-cost-analysis-and-set-budgets-for-azure-batch"></a>Obter análise de custo e definir orçamentos para o lote do Azure

Este tópico ajudará você a entender os custos que podem ser associados ao lote do Azure, como definir um orçamento para um pool ou conta do lote e maneiras de reduzir os custos das cargas de trabalho do lote.

## <a name="understand-costs-associated-with-batch-resources"></a>Entender os custos associados aos recursos do lote

Não há nenhum custo para usar o lote do Azure em si, embora possa haver cobranças para os recursos de computação subjacentes e as licenças de software usadas para executar cargas de trabalho do lote. Os custos podem ser incorridos de máquinas virtuais (VMs) em um pool, transferência de dados da VM ou quaisquer dados de entrada ou saída armazenados na nuvem.

### <a name="virtual-machines"></a>Máquinas virtuais

As máquinas virtuais são o recurso mais significativo usado para processamento do Lote. O custo do uso de VMs para o Lote é calculado com base no tipo, na quantidade e na duração do uso. As opções de cobrança de VM incluem [Pagamento Conforme o Uso](https://azure.microsoft.com/offers/ms-azr-0003p/) ou [reserva](../cost-management-billing/reservations/save-compute-costs-reservations.md) (pagamento com antecedência). Ambas as opções de pagamento têm benefícios diferentes dependendo da carga de trabalho de computação e afetarão sua fatura de forma diferente.

Cada VM em um pool criado com a [configuração de máquina virtual](nodes-and-pools.md#virtual-machine-configuration) tem um disco de sistema operacional associado que usa discos gerenciados pelo Azure. Os discos gerenciados pelo Azure têm um custo adicional, e outros níveis de desempenho de disco também têm custos diferentes.

### <a name="storage"></a>Armazenamento

Quando os aplicativos são implantados em nós do Lote (VMs) usando [pacotes de aplicativos](batch-application-packages.md), você é cobrado pelos recursos de Armazenamento do Azure que seus pacotes de aplicativos consomem. Você também será cobrado pelo armazenamento de qualquer arquivo de entrada ou saída, como arquivos de recursos e outros dados de log.

Em geral, o custo dos dados de armazenamento associados ao Lote é muito menor do que o custo dos recursos de computação.

### <a name="networking-resources"></a>Recursos de rede

Os pools do lote usam recursos de rede, alguns dos quais têm custos associados. Em particular, para pools de configuração de máquina virtual, os balanceadores de carga padrão são usados, que exigem endereços IP estáticos. Os balanceadores de carga usados pelo lote são visíveis para [as contas](accounts.md#batch-accounts) configuradas no modo de assinatura do usuário, mas não aquelas no modo de serviço do lote.

Os balanceadores de carga padrão incorrem em encargos para todos os dados passados para e de VMs do pool do lote. Selecione as APIs do lote que recuperam dados de nós de pool (como obter arquivo de tarefa/nó), pacotes de aplicativos de tarefa, arquivos de recursos/saída e imagens de contêiner também incorrerão em encargos.

### <a name="additional-services"></a>Serviços adicionais

Dependendo de quais serviços você usa com sua solução do Lote, podem incorrer valores adicionais. Confira a [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/) para determinar o custo de cada serviço adicional. Os serviços normalmente usados com o lote que pode ter custos associados incluem:

- Application Insights
- Data Factory
- Azure Monitor
- Rede Virtual
- VMs com aplicativos gráficos

## <a name="view-cost-analysis-and-create-a-budget-for-a-pool"></a>Exibir análise de custo e criar um orçamento para um pool

No portal do Azure, você pode criar orçamentos e gastar alertas para seus pools do lote ou contas do lote. Orçamentos e alertas são úteis para notificar os participantes de qualquer risco de excesso de gastos, embora seja possível que haja um atraso em alertas de gastos e um pouco excedente a um orçamento.

> [!NOTE]
> O pool neste exemplo usa a **configuração de máquina virtual**, que é recomendada para a maioria dos pools e tem encargos com base na estrutura de preços de máquinas virtuais. Os pools que usam a **configuração de serviços de nuvem** são cobrados com base na estrutura de preços dos serviços de nuvem.

### <a name="view-cost-analysis-for-a-batch-pool"></a>Exibir análise de custo para um pool do lote

1. Na portal do Azure, digite ou selecione gerenciamento de **custos + cobrança** .
1. Selecione sua assinatura na seção **escopos de cobrança** .
1. Em **Gerenciamento de Custos**, selecione **Análise de custo**.
1. Selecione **Adicionar Filtro**. Na primeira lista suspensa, selecione **recurso**
1. Na segunda lista suspensa, selecione o pool do Lote. Quando o pool for selecionado, você verá a análise de custo para o pool, semelhante ao exemplo mostrado aqui.
    ![Captura de tela mostrando a análise de custo de um pool no portal do Azure.](./media/batch-budget/pool-cost-analysis.png)

A análise de custo resultante mostra o custo do pool, bem como os recursos que contribuem com esse custo. Neste exemplo, as VMs usadas no pool são o recurso mais dispendioso.

### <a name="create-a-budget-for-a-batch-pool"></a>Criar um orçamento para um pool do lote

1. Na página **análise de custo** , selecione **orçamento: nenhum**.
1. Selecione **criar novo orçamento >**.
1. Use a janela resultante para configurar um orçamento especificamente para o pool. Para obter mais informações, consulte [tutorial: criar e gerenciar orçamentos do Azure](../cost-management-billing/costs/tutorial-acm-create-budgets.md).

## <a name="minimize-costs-associated-with-azure-batch"></a>Minimizar os custos associados ao lote do Azure

Dependendo do seu cenário, talvez você queira reduzir os custos o máximo possível. Considere o uso de uma ou mais dessas estratégias para maximizar a eficiência de suas cargas de trabalho e reduzir os custos potenciais.

### <a name="use-low-priority-virtual-machines"></a>Usar máquinas virtuais de baixa prioridade

As [VMs de baixa prioridade](batch-low-pri-vms.md) reduzem o custo das cargas de trabalho do lote aproveitando a capacidade de computação excedente no Azure. Quando você especifica VMs de baixa prioridade em seus pools, o Lote usa esse excedente para executar sua carga de trabalho. Pode haver economias de custo substanciais quando você usa VMs de baixa prioridade em vez de VMs dedicadas.

### <a name="select-a-standard-virtual-machine-os-disk-type"></a>Selecionar um tipo de disco de sistema operacional de máquina virtual padrão

O Azure oferece vários [tipos de disco de sistema operacional de VM](../virtual-machines/disks-types.md). A maioria das séries de VM tem tamanhos que dão suporte ao armazenamento Premium e Standard. Quando um ' tamanho da VM ' é selecionado para um pool, o lote configura OS discos do sistema operacional SSD Premium. Quando o tamanho da VM ' não s' é selecionado, o tipo de disco HDD padrão mais barato é usado. Por exemplo, os discos do SO de SSD Premium são usados para `Standard_D2s_v3` e os discos do SO de HDD padrão são usados para `Standard_D2_v3`.

SSD Premium discos do sistema operacional são mais caros, mas têm melhor desempenho. As VMs com discos Premium podem começar um pouco mais rapidamente do que as VMs com discos do sistema operacional HDD padrão. Com o lote, o disco do sistema operacional geralmente não é muito usado, já que os aplicativos e os arquivos de tarefas estão localizados no disco SSD temporário da VM. Por isso, você geralmente pode selecionar o tamanho da VM "não-s" para evitar pagar o aumento do custo do SSD Premium que é provisionado quando um "tamanho da VM" é especificado.

### <a name="purchase-reservations-for-virtual-machine-instances"></a>Reservas de compra para instâncias de máquina virtual

Se você pretende usar o lote por um longo período de tempo, pode reduzir o custo das VMs usando as [reservas do Azure](../cost-management-billing/reservations/save-compute-costs-reservations.md) para suas cargas de trabalho. Uma taxa de reserva é consideravelmente menor do que uma taxa paga conforme o uso. As instâncias de máquina virtual usadas sem uma reserva são cobradas com a taxa paga conforme o uso. Quando você adquire uma reserva, o desconto de reserva é aplicado.

### <a name="use-automatic-scaling"></a>Usar o dimensionamento automático

O [dimensionamento automático](batch-automatic-scaling.md) dimensiona dinamicamente o número de VMs em seu pool do Lote com base nas demandas do trabalho atual. Ao dimensionar o pool com base no tempo de vida de um trabalho, o dimensionamento automático garante que as VMs sejam escaladas verticalmente e usadas somente quando houver um trabalho a ser executado. Quando o trabalho for concluído ou quando não houver nenhum trabalho, as VMs serão dimensionadas automaticamente para salvar os recursos de computação. O dimensionamento permite reduzir o custo geral da solução do Lote usando apenas os recursos de que você precisa.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Gerenciamento de custos do Azure + cobrança](../cost-management-billing/cost-management-billing-overview.md)
- Saiba mais sobre como [usar VMs de baixa prioridade com o lote](batch-low-pri-vms.md).
