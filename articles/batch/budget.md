---
title: Análise de custo e orçamento-lote do Azure
description: Saiba como obter uma análise de custo e definir um orçamento para a carga de trabalho do lote.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 07/19/2019
ms.author: lahugh
ms.openlocfilehash: e6a99021a5e05f04672a4db2b4c208b8f4bad8c8
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361785"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Análise de custo e orçamentos para o lote do Azure

Não há encargos para o lote do Azure em si, somente os recursos de computação subjacentes e as licenças de software usadas para executar cargas de trabalho do lote. Em um alto nível, os custos são incorridos de VMs (máquinas virtuais) em um pool, a transferência de dados da VM ou quaisquer dados de entrada ou saída armazenados na nuvem. Vamos dar uma olhada em alguns dos principais componentes do lote para entender de onde vêm os custos, como definir um orçamento para um pool ou uma conta e algumas técnicas para tornar suas cargas de trabalho de lote mais econômicas.

## <a name="batch-resources"></a>Recursos do lote

As máquinas virtuais são o recurso mais significativo usado para processamento em lotes. O custo do uso de VMs para o lote é calculado com base no tipo, na quantidade e na duração do uso. As opções de cobrança de VM incluem pré- [pago](https://azure.microsoft.com/offers/ms-azr-0003p/) ou [reserva](../billing/billing-save-compute-costs-reservations.md) (pagamento antecipado). Ambas as opções de pagamento têm benefícios diferentes, dependendo de sua carga de trabalho de computação, e ambos os modelos de pagamento afetarão sua fatura de forma diferente.

Quando os aplicativos são implantados em VMs (nós do lote) usando [pacotes de aplicativos](batch-application-packages.md), você é cobrado pelos recursos de armazenamento do Azure consumidos por seus pacotes de aplicativos. Você também será cobrado pelo armazenamento de qualquer arquivo de entrada ou saída, como arquivos de recursos e outros dados de log. Em geral, o custo dos dados de armazenamento associados ao lote é muito menor do que o custo dos recursos de computação. Cada VM em um pool criado com **VirtualMachineConfiguration** tem um disco de sistema operacional associado que usa discos gerenciados pelo Azure. Os discos gerenciados pelo Azure têm um custo adicional, e outros níveis de desempenho de disco também têm custos diferentes.

Os pools do lote usam recursos de rede. Em particular, para os balanceadores de carga padrão de pools de **VirtualMachineConfiguration** são usados, que exigem endereços IP estáticos. Os balanceadores de carga usados pelo lote são visíveis para contas de **assinatura de usuário** , mas não são visíveis para contas de **serviço do lote** . Os balanceadores de carga padrão incorrem em encargos para todos os dados passados para e de VMs do pool do lote; Selecione as APIs do lote que recuperam dados de nós de pool (como obter arquivo de tarefa/nó), pacotes de aplicativos de tarefa, arquivos de recursos/saída e imagens de contêiner incorrem em encargos.

### <a name="additional-services"></a>Serviços adicionais

Serviços que não incluem VMs e armazenamento podem considerar o custo da sua conta do lote.

Outros serviços comumente usados com o lote podem incluir:

- Application Insights
- Data Factory
- Azure Monitor
- Rede Virtual
- VMs com aplicativos gráficos

Dependendo de quais serviços você usa com sua solução do lote, você pode incorrer em taxas adicionais. Consulte a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para determinar o custo de cada serviço adicional.

## <a name="cost-analysis-and-budget-for-a-pool"></a>Análise de custo e orçamento para um pool

Por meio do portal do Azure, você pode criar orçamentos e gastar alertas para os pools do lote ou a conta do lote. Orçamentos e alertas são úteis para notificar os participantes de qualquer risco de excesso de gastos. É possível que haja um atraso em alertas de gastos e que ele exceda um orçamento ligeiramente. Neste exemplo, vamos exibir a análise de custo de um pool de lote individual.

1. Na portal do Azure, selecione **Gerenciamento de custos + cobrança** na barra de navegação à esquerda.
1. Selecione sua assinatura na seção **minhas assinaturas**
1. Vá para **análise de custo** na seção **Gerenciamento de custos** da barra de navegação à esquerda, que mostrará um modo de exibição como este:
1. Selecione **Adicionar filtro**. Na primeira lista suspensa, selecione **recurso** ![selecionar o filtro de recursos](./media/batch-budget/resource-filter.png)
1. Na segunda lista suspensa, selecione o pool do lote. Quando o pool for selecionado, a análise de custo será semelhante à análise a seguir.
    ![Análise de custo de um pool](./media/batch-budget/pool-cost-analysis.png)

A análise de custo resultante mostra o custo do pool, bem como os recursos que contribuem com esse custo. Neste exemplo, as VMs usadas no pool são o recurso mais dispendioso.

Para criar um orçamento para o pool, selecione **orçamento: nenhum**e, em seguida, selecione **criar novo orçamento >** . Agora, use a janela para configurar um orçamento especificamente para o pool.

Para obter mais informações sobre como configurar um orçamento, consulte [criar e gerenciar orçamentos do Azure](../cost-management/tutorial-acm-create-budgets.md).

> [!NOTE]
> O Lote do Azure baseia-se na tecnologia de Serviços de Nuvem do Azure e Máquinas Virtuais do Azure. Quando você escolhe **configuração de serviços de nuvem**, você é cobrado com base na estrutura de preços dos serviços de nuvem. Ao escolher a **configuração de máquina virtual**, você será cobrado com base na estrutura de preços de máquinas virtuais. O exemplo nesta página usa a **configuração de máquina virtual**.

## <a name="minimize-cost"></a>Minimizar o custo

Usar várias VMs e serviços do Azure por longos períodos de tempo pode ser dispendioso. Felizmente, há serviços disponíveis para ajudar a reduzir seus gastos, bem como estratégias para maximizar a eficiência de sua carga de trabalho.

### <a name="low-priority-virtual-machines"></a>Máquinas virtuais de baixa prioridade

As VMs de baixa prioridade reduzem o custo das cargas de trabalho do lote aproveitando a capacidade de computação excedente no Azure. Quando você especifica VMs de baixa prioridade em seus pools, o lote usa esse excedente para executar a carga de trabalho. Há um economia substancial com o uso de VMs de baixa prioridade em vez de VMs dedicadas.

Saiba mais sobre como configurar VMs de baixa prioridade para sua carga de trabalho em [usar VMs de baixa prioridade com o lote](batch-low-pri-vms.md).

### <a name="virtual-machine-os-disk-type"></a>Tipo de disco do so da máquina virtual

Há vários [tipos de disco de sistema operacional de VM](../virtual-machines/windows/disks-types.md). A maioria das séries de VM tem tamanhos que dão suporte ao armazenamento Premium e Standard. Quando um ' tamanho da VM ' é selecionado para um pool, o lote configura OS discos do sistema operacional SSD Premium. Quando o tamanho da VM ' não s' é selecionado, o tipo de disco HDD padrão mais barato é usado. Por exemplo, os discos do sistema operacional SSD Premium `Standard_D2s_v3` são usados para o e os discos do `Standard_D2_v3`sistema operacional HDD padrão são usados para o.

SSD Premium discos do sistema operacional são mais caros, mas têm maior desempenho e VMs com discos Premium podem começar um pouco mais rapidamente do que as VMs com discos do sistema operacional HDD padrão. Com o lote, o disco do sistema operacional geralmente não é usado, pois os aplicativos e os arquivos de tarefa estão localizados no disco SSD temporário de VMs. Portanto, em muitos casos, não há necessidade de pagar o aumento do custo para o SSD Premium que é provisionado quando um "tamanho de VM" é especificado.

### <a name="reserved-virtual-machine-instances"></a>Instâncias de máquina virtual reservadas

Se você pretende usar o lote por um longo período de tempo, poderá economizar no custo das VMs usando as reservas do [Azure](../billing/billing-save-compute-costs-reservations.md) para suas cargas de trabalho. Uma taxa de reserva é consideravelmente menor do que uma taxa paga conforme o uso. As instâncias de máquina virtual usadas sem uma reserva são cobradas à taxa paga conforme o uso. Se você comprar uma reserva, o desconto de reserva será aplicado e você não será mais cobrado com as tarifas pago pelo uso.

### <a name="automatic-scaling"></a>Dimensionamento automático

O [dimensionamento automático](batch-automatic-scaling.md) dimensiona dinamicamente o número de VMs em seu pool do lote com base nas demandas do trabalho atual. Ao dimensionar o pool com base no tempo de vida de um trabalho, o dimensionamento automático garante que as VMs sejam escaladas verticalmente e usadas somente quando houver um trabalho a ser executado. Quando o trabalho for concluído ou não houver nenhum trabalho, as VMs serão dimensionadas automaticamente para salvar os recursos de computação. O dimensionamento permite reduzir o custo geral da solução do lote usando apenas os recursos de que você precisa.

Para obter mais informações sobre o dimensionamento automático, consulte [dimensionar automaticamente nós de computação em um pool do lote do Azure](batch-automatic-scaling.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as [APIs e ferramentas do lote](batch-apis-tools.md) disponíveis para criar e monitorar soluções de lote.  

- Saiba mais sobre [VMs de baixa prioridade com o lote](batch-low-pri-vms.md).
