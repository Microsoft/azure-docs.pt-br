---
title: Dimensionar automaticamente conjuntos de dimensionamento de máquinas virtuais no portal do Azure | Microsoft Docs
description: Como criar regras de dimensionamento automático para conjuntos de dimensionamento de máquinas virtuais no portal do Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: cynthn
ms.openlocfilehash: 648bc0295cd5435e9c3e44f33b7ae80522fa8e0e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60618777"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-the-azure-portal"></a>Dimensionamento automático de um conjunto de dimensionamento de máquinas virtuais no portal do Azure
Ao criar um conjunto de dimensionamento, o número de instâncias de VM que você deseja executar é definido. À medida que seu aplicativo precisar de alterações, você poderá aumentar ou diminuir automaticamente o número de instâncias de VM. A capacidade de autoescala permite acompanhar a demanda do cliente ou reagir a alterações de desempenho do aplicativo durante todo o ciclo de vida do aplicativo.

Este artigo mostra como criar regras de dimensionamento automático no portal do Azure que monitoram o desempenho das instâncias de VM no seu conjunto de dimensionamento. Essas regras de autoescala aumentam ou diminuem o número de instâncias de VM em resposta a essas métricas de desempenho. Você também pode concluir essas etapas com [Azure PowerShell](tutorial-autoscale-powershell.md) ou o [CLI do Azure](tutorial-autoscale-cli.md).


## <a name="prerequisites"></a>Pré-requisitos
Para criar regras de autoescala, você precisará de um conjunto de dimensionamento de máquinas virtuais existente. Você pode criar um conjunto de dimensionamento com o [portal do Azure](quick-create-portal.md), [do Azure PowerShell](quick-create-powershell.md), ou [CLI do Azure](quick-create-cli.md).


## <a name="create-a-rule-to-automatically-scale-out"></a>Crie uma regra para expandir automaticamente
Se a demanda do aplicativo aumentar, a carga em instâncias de VM no seu conjunto de dimensionamento também aumentará. Se esse aumento de carga for consistente, em vez de apenas uma demanda breve, configure as regras de dimensionamento automático para aumentar o número de instâncias de VM no conjunto de dimensionamento. Quando essas instâncias de VM forem criadas e os aplicativos implantados, o conjunto de dimensionamento começará a distribuir o tráfego para eles por meio do balanceador de carga. Você controla quais métricas são monitoradas, como CPU ou disco, por quanto tempo a carga do aplicativo deve atender a determinado limite e quantas instâncias de VM devem ser adicionadas ao conjunto de dimensionamento.

1. Abra o portal do Azure e selecione **Grupos de recursos** no menu esquerdo do painel.
2. Selecione o grupo de recursos que contém o conjunto de dimensionamento e escolha a conjunto de dimensionamentos da lista de recursos.
3. Escolha **Colocação em escala** no menu à esquerda da janela do conjunto de dimensionamento. Selecione o botão para **Habilitar dimensionamento automático**:

    ![Habilitar dimensionamento automático no portal do Azure](media/virtual-machine-scale-sets-autoscale-portal/enable-autoscale.png)

4. Insira um nome para as configurações, como *dimensionamento automático* e selecione a opção para **Adicionar uma regra**.

5. Vamos criar uma regra que aumenta o número de instâncias de VM em um conjunto de dimensionamento definido quando a carga da CPU média for maior que 70% por um período de 10 minutos. Quando a regra dispara, o número de instâncias de VM aumentará em 20%. Em conjuntos de dimensionamento com um pequeno número de instâncias de VM, você poderia definir a **Operação** para *Aumentar a contagem em* e especificar *1* ou *2* na *Contagem de instâncias*. Em conjuntos de dimensionamento com um grande número de instâncias de VM, um aumento de 10% ou 20% de instâncias de VM pode ser mais apropriado.

    Especifique as seguintes configurações para a regra:
    
    | Parâmetro              | Explicação                                                                                                         | Value          |
    |------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
    | *Tempo de agregação*     | Define como as métricas coletadas devem ser agregadas para análise.                                                | Média        |
    | *Nome da métrica*          | A métrica de desempenho a ser monitorada, na qual as ações do conjunto de dimensionamento serão aplicadas.                                                   | Porcentagem de CPU |
    | *Estatística de intervalo de agregação* | Define como as métricas coletadas em cada intervalo de agregação devem ser agregadas para análise.                             | Média        |
    | *Operador*             | Operador usado para comparar os dados da métrica com o limite.                                                     | Maior que   |
    | *Limite*            | A porcentagem que faz com que a regra de dimensionamento automático dispare uma ação.                                                 | 70             |
    | *Duração*             | O tempo monitorado antes de comparar os valores de métrica e de limite.                                   | 10 minutos     |
    | *Operação*            | Define se o conjunto de dimensionamento deve ser dimensionado expandido ou reduzido quando a regra se aplica e qual o incremento                        | Aumentar porcentagem em |
    | *Contagem de instâncias*       | O percentual de instâncias de VM que deve ser alterado quando a regra disparar.                                            | 20             |
    | *Tempo de resfriamento (minutos)*  | O tempo de espera antes da regra ser aplicada novamente para que as ações de autoescala tenham tempo para entrar em vigor. | 5 minutos      |

    Os exemplos a seguir mostram uma regra criada no portal do Azure que corresponde a essas configurações:

    ![Criar uma regra de dimensionamento automático para aumentar o número de instâncias de VM](media/virtual-machine-scale-sets-autoscale-portal/rule-increase.png)

6. Para criar a regra, selecione **Adicionar**


## <a name="create-a-rule-to-automatically-scale-in"></a>Criar uma regra para reduzir automaticamente
À noite ou durante o fim de semana, a demanda do seu aplicativo pode diminuir. Se essa diminuição de carga for consistente durante determinado período, configure as regras de dimensionamento automático para reduzir o número de instâncias de VM no conjunto de dimensionamento. Esta ação de redução diminui o custo para executar seu conjunto de dimensionamento, visto que apenas o número de instâncias necessárias para atender à demanda atual é executado.

1. Escolha **Adicionar uma regra** novamente.
2. Crie uma regra que diminui o número de instâncias de VM em um conjunto de dimensionamento definido quando a carga da CPU média for menor que 30% por um período de 10 minutos. Quando a regra dispara, o número de instâncias de VM é reduzido em 20%.

    Use a mesma abordagem da regra anterior. Ajuste as seguintes configurações para a regra:
    
    | Parâmetro              | Explicação                                                                                                          | Value          |
    |------------------------|----------------------------------------------------------------------------------------------------------------------|----------------|
    | *Operador*             | Operador usado para comparar os dados da métrica com o limite.                                                      | Menor que   |
    | *Limite*            | A porcentagem que faz com que a regra de dimensionamento automático dispare uma ação.                                                 | 30             |
    | *Operação*            | Define se o conjunto de dimensionamento deve ser dimensionado expandido ou reduzido quando a regra se aplica e qual o incremento                         | Reduzir porcentagem em |
    | *Contagem de instâncias*       | O percentual de instâncias de VM que deve ser alterado quando a regra disparar.                                             | 20             |

3. Para criar a regra, selecione **Adicionar**


## <a name="define-autoscale-instance-limits"></a>Definir limites de instância de dimensionamento automático
Seu perfil de dimensionamento automático deve definir números mínimo, máximo e padrão de instâncias de VM. Quando são aplicadas as regras de dimensionamento automático, esses limites de instância fazem com que você não escale ou reduza horizontalmente além do número máximo de instâncias.

1. Defina os seguintes limites de instância:

    | Mínimo | Máximo | Padrão|
    |---------|---------|--------|
    | 2       | 10      | 2      |

2. Para aplicar suas regras de dimensionamento automático e limites de instância, selecione **Salvar**.


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Monitorar o número de instâncias em um conjunto de dimensionamento
Para ver o número e o status de instâncias de VM, selecione **Instâncias** no menu à esquerda da janela do conjunto de dimensionamento. O status indica se a instância VM aparece como *Criando* à medida que o conjunto de dimensionamento é expandido automaticamente ou como *Excluindo* à medida que a escala é reduzida automaticamente.

![Exibir uma lista de instâncias de VM do conjunto de dimensionamento](media/virtual-machine-scale-sets-autoscale-portal/view-instances.png)


## <a name="autoscale-based-on-a-schedule"></a>Dimensionamento automático baseado em agendamento
Os exemplos anteriores dimensionaram automaticamente um conjunto de dimensionamento com métricas de host básicas como uso da CPU. Também é possível criar regras de dimensionamento automático baseadas em agendamentos. Essas regras baseadas em agendamento permitem expandir automaticamente o número de instâncias de VM antes de um aumento previsto na demanda do aplicativo, como horas de trabalho principal e, depois, reduz automaticamente o número de instâncias no momento em que uma demanda menor for prevista, como durante o fim de semana.

1. Escolha **Colocação em escala** no menu à esquerda da janela do conjunto de dimensionamento. Para excluir as regras existentes de dimensionamento automático criadas nos exemplos anteriores, escolha o ícone de lixeira.

    ![Excluir as regras de dimensionamento automático existentes](media/virtual-machine-scale-sets-autoscale-portal/delete-rules.png)

2. Escolha **Adicionar uma condição de dimensionamento**. Selecione o ícone de lápis ao lado do nome da regra e forneça um nome como *Escalar horizontalmente durante cada dia de trabalho*.

    ![Renomear a regra de dimensionamento automático padrão](media/virtual-machine-scale-sets-autoscale-portal/rename-rule.png)

3. Selecione o botão de opção para **Dimensionar para uma contagem de instâncias específica**.
4. Para aumentar o número de instâncias, digite *10* como a contagem de instância.
5. Escolha **Repetir dias específicos** para o tipo **Agendamento**.
6. Selecione todos os dias de trabalho, de segunda-feira a sexta-feira.
7. Escolha o fuso horário apropriado e especifique uma **Hora de início** *09:00*.
8. Escolha **Adicionar uma condição de dimensionamento** novamente. Repita o processo para criar uma agenda denominada *Reduzir horizontalmente durante a noite* que dimensione para *3* instâncias e se repita a cada dia da semana, iniciando às *18:00*.
9. Para aplicar suas regras de dimensionamento automático com base em agendamento, selecione **Salvar**.

    ![Criar regras de dimensionamento automático que são dimensionadas com agendamento](media/virtual-machine-scale-sets-autoscale-portal/schedule-autoscale.PNG)

Para ver como as regras de dimensionamento automático são aplicadas, selecione **Histórico de execuções** na parte superior da janela **Colocação em Escala**. A lista de eventos e grafo mostra quando as regras de dimensionamento automático são acionadas e o número de instâncias da VM no seu conjunto de dimensionamento aumenta ou diminui.


## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a usar as regras de autoescala para escalar horizontalmente e expandir ou diminuir o *número* de instâncias de VM no seu conjunto de dimensionamento. Também é possível escalar verticalmente para aumentar ou diminuir o *tamanho* da instância VM. Para obter mais informações, consulte [Dimensionamento vertical automático com conjuntos de Dimensionamento de Máquinas Virtuais](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Para obter informações sobre como gerenciar suas instâncias de VM, consulte [Gerenciar conjuntos de dimensionamento de máquinas virtuais com o Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Para saber como gerar alertas quando suas regras de dimensionamento automático forem disparadas, consulte [Usar ações de dimensionamento automático para enviar notificações de alerta por email e webhook no Azure Monitor](../azure-monitor/platform/autoscale-webhook-email.md). Também é possível [Usar logs de auditoria para enviar notificações de alerta por email e webhook no Azure Monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
