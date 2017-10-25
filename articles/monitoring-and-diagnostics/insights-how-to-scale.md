---
title: "Dimensionar manualmente a contagem de instância ou com dimensionamento automático com o Portal do Azure | Microsoft Docs"
description: "Saiba como dimensionar seus serviços do Azure."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 2397596a-071f-4d49-8893-bec5f735bd7b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: ancav
ms.openlocfilehash: d171538ea57839eccddcc74ca099a39aee34ea10
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="scale-instance-count-manually-or-automatically"></a>Dimensionar a contagem de instância manualmente ou automaticamente
No [Portal do Azure](https://portal.azure.com/), você pode definir manualmente a contagem de instâncias de seu serviço ou definir parâmetros para que o dimensionamento seja feito automaticamente com base na demanda. Isso é normalmente conhecido como *escala horizontal* ou *redução horizontal*.

Antes de dimensionamento com base na contagem de instâncias, você deve considerar que o dimensionamento é afetado pela **camada de preços** além de contagem de instâncias. Diferentes faixas de preço podem ter diferentes números de núcleos e memória e, portanto, terão melhor desempenho para o mesmo número de instâncias (o que significa *escalar verticalmente* ou *reduzir verticalmente*). Este artigo aborda especificamente a *redução horizontal* e a *escala horizontal*.

Você pode reduzir horizontalmente no portal e também pode usar a [API REST](https://msdn.microsoft.com/library/azure/dn931953.aspx) ou o [SDK .NET](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) para ajustar a escala manual ou automaticamente.

> [!NOTE]
> Este artigo descreve como criar uma configuração de dimensionamento automático no portal em [http://portal.azure.com](http://portal.azure.com). As configurações de autoescala criadas nesse portal não podem ser editadas no portal clássico ([http://manage.windowsazure.com](http://manage.windowsazure.com)).
> 
> 

## <a name="scaling-manually"></a>Dimensionando manualmente
1. No [Portal do Azure](https://portal.azure.com/), clique em **Procurar**, navegue até o recurso que você deseja dimensionar, como um **plano do Serviço de Aplicativo**.
2. Clique em **Configurações > Escalar horizontalmente (Plano do Serviço de Aplicativo).**
3. Na parte superior da folha **Escala**, você pode ver um histórico das ações de dimensionamento automático do serviço.
   
    ![Lâmina Escala](./media/insights-how-to-scale/Insights_ScaleBladeDayZero.png)
   
   > [!NOTE]
   > Apenas as ações executadas pelo dimensionamento automático aparecerão neste gráfico. Se você ajustar manualmente a contagem de instâncias, a alteração não será refletida no gráfico.
   > 
   > 
4. Você pode ajustar manualmente o número de **instâncias** com o controle deslizante.
5. Clique no comando **Salvar** e o dimensionamento será realizado para esse número de instâncias quase imediatamente.

## <a name="scaling-based-on-a-pre-set-metric"></a>Dimensionamento com base em uma métrica predefinida
Se você quiser que o número de instâncias seja ajustado automaticamente com base em uma métrica, selecione a métrica desejada no menu suspenso **Dimensionar por** . Por exemplo, para um **plano do Serviço de Aplicativo**, você pode dimensionar de acordo com a **porcentagem de CPU**.

1. Ao selecionar uma métrica, você obterá um controle deslizante e/ou caixas de texto para inserir o número de instâncias entre as quais deseja dimensionar:
   
    ![Lâmina Escala com a porcentagem de CPU](./media/insights-how-to-scale/Insights_ScaleBladeCPU.png)
   
    O dimensionamento automático nunca dimensionará seu serviço abaixo ou acima dos limites definidos, independentemente de sua carga.
2. Em segundo lugar, você escolhe o intervalo de destino para a métrica. Por exemplo, se escolher **Percentual de CPU**, você pode definir um destino para a média de CPU entre todas as instâncias no seu serviço. Uma escala horizontal acontecerá quando a média de CPU exceder o máximo definido, da mesma forma, uma redução horizontal ocorrerá sempre que a média de CPU ficar abaixo do mínimo.
3. Clique no comando **Salvar** . O dimensionamento automático verificará em intervalos regulares para certificar-se de que você está dentro do intervalo de instância e destino para sua métrica. Quando o serviço receber tráfego adicional, você obterá mais instâncias sem fazer nada.

## <a name="scale-based-on-other-metrics"></a>Dimensionamento com base em outras métricas
Você pode dimensionar com base em métricas que não sejam as métricas predefinidas que aparecem no menu suspenso **Dimensionar por** e pode até mesmo ter um conjunto complexo de regras de escala e redução horizontal.

### <a name="adding-or-changing-a-rule"></a>Adicionar ou alterar uma regra
1. Escolha as **regras de agendamento e desempenho** no menu suspenso **Dimensionar por**: ![Regras de desempenho](./media/insights-how-to-scale/Insights_PerformanceRules.png)
2. Se você tiver utilizado o dimensionamento automático anteriormente, você verá uma exibição das regras exatas utilizadas.
3. Para dimensionar com base em outra métrica clique na linha **Adicionar regra** . Você também pode clicar em uma das linhas existentes para alterar a métrica utilizada anteriormente para a métrica de acordo com a qual deseja dimensionar.
   ![Add rule](./media/insights-how-to-scale/Insights_AddRule.png)
4. Agora, você precisa selecionar de acordo com qual métrica deseja dimensionar. Ao escolher uma métrica, existem algumas coisas a serem consideradas:
   
   * O *recurso* do qual a métrica é proveniente. Normalmente, ele será o mesmo que o recurso que você está dimensionando. No entanto, se quiser dimensionar pela profundidade de uma fila de armazenamento, o recurso é a fila de acordo com a qual você deseja dimensionar.
   * O *nome da métrica* em si.
   * A *agregação de tempo* da métrica. É assim que os dados são combinados na *duração*.
5. Depois de escolher sua métrica, escolha o limite para a métrica e o operador. Por exemplo, você poderia dizer **Maior que** **80%**.
6. Em seguida, escolha a ação que deseja executar. Há alguns tipos de ações diferentes:
   
   * Aumentar ou diminui por - isso adicionará ou removerá o número do **valor** das instâncias definidas
   * Aumentar ou diminuir a porcentagem - isso alterará a contagem de instâncias de acordo com uma porcentagem. Por exemplo, você poderia colocar 25 no campo **valor** e, caso tenha atualmente 8 instâncias, 2 seriam adicionadas.
   * Aumentar ou diminuir para - isso definirá a contagem de instâncias para o **valor** definido.
7. Por fim, você pode escolher Resfriamento - quanto tempo esta regra deve esperar após a ação anterior de dimensionamento para dimensionar novamente.
8. Depois de configurar a regra, pressione **OK**.
9. Depois de configurar todas as regras que você deseja, certifique-se de pressionar o comando **Salvar** .

### <a name="scaling-with-multiple-steps"></a>Dimensionamento com várias etapas
Os exemplos acima são bastante básicos. No entanto, se você quiser ser mais agressivo sobre como expandir (ou reduzir), você pode adicionar até mesmo várias regras de dimensionamento para a mesma métrica. Por exemplo, você pode definir duas regras de dimensionamento de acordo com um percentual de CPU:

1. Escalar horizontalmente em uma instância se o percentual de CPU estiver acima de 60%
2. Escalar horizontalmente em três instâncias se o percentual de CPU estiver acima de 85%

![Várias regras de escala](./media/insights-how-to-scale/Insights_MultipleScaleRules.png)

Com essa regra adicional, se sua carga exceder 85% antes de uma ação de escala, você terá duas instâncias adicionais em vez de uma.

## <a name="scale-based-on-a-schedule"></a>Dimensionamento com base em um planejamento
Por padrão, quando você criar uma regra de dimensionamento, ela será sempre aplicada. Você pode ver isso ao clicar no cabeçalho do perfil:

![Perfil](./media/insights-how-to-scale/Insights_Profile.png)

No entanto, você talvez queira ter um dimensionamento mais agressivo durante o dia, ou a semana, do que no final de semana. Você pode até mesmo desligar o serviço totalmente fora do horário de trabalho.

1. Para fazer isso, em seu perfil, selecione **Recorrência** em vez de **Sempre** e escolha os horários em que você deseja que o perfil se aplique.
2. Por exemplo, para que um perfil se aplique durante a semana, no menu suspenso **Dias** desmarque **Sábado** e **Domingo**.
3. Para que um perfil se aplique durante o dia, defina o **horário de início** para o horário do dia em que você deseja iniciar.
   
    ![Recorrência padrão](./media/insights-how-to-scale/Insights_ProfileRecurrence.png)
4. Clique em **OK**.
5. Em seguida, você precisará adicionar o perfil que deseja aplicar em outros momentos. Clique na linha **Adicionar perfil** .
    ![Dia livre](./media/insights-how-to-scale/Insights_ProfileOffWork.png)
6. Nomeie o segundo novo perfil. Por exemplo, você poderia chamá-lo de **Dias livres**.
7. Selecione **Recorrência** novamente e escolha o intervalo de contagem de instância que você deseja durante esse tempo.
8. Assim como com o perfil padrão, escolha os **Dias** aos quais quer que esse perfil seja aplicado e o **Horário de início** durante o dia.
   
   > [!NOTE]
   > O dimensionamento automático usará as regras do horário de verão para o **fuso horário** selecionado. No entanto, durante o horário de verão, o deslocamento UTC mostrará o deslocamento de fuso horário base, não o deslocamento de UTC de horário de verão.
   > 
   > 
9. Clique em **OK**.
10. Agora, você precisará adicionar as regras que você deseja aplicar durante o segundo perfil. Clique em **Adicionar regra**e, em seguida, você pode construir a mesma regra usada durante o perfil padrão.
    
    ![Adicionar regra para dia livre](./media/insights-how-to-scale/Insights_RuleOffWork.png)
11. Certifique-se de criar uma regra para escala e redução horizontal, caso contrário, durante o perfil, a contagem de instâncias vai apenas aumentar (ou diminuir).
12. Por fim, clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas
* [Monitore as métricas de serviço](insights-how-to-customize-monitoring.md) para se certificar de que o serviço está disponível e responsivo.
* [Habilite o monitoramento e diagnóstico](insights-how-to-use-diagnostics.md) para coletar métricas detalhadas de alta frequência em seu serviço.
* [Receba notificações de alerta](insights-receive-alert-notifications.md) sempre que ocorrerem eventos operacionais ou métricas ultrapassarem um limite.
* [Monitore o desempenho do aplicativo](../application-insights/app-insights-azure-web-apps.md) se você quiser compreender exatamente como seu código está sendo executado na nuvem.
* [Exiba eventos e logs de atividade](insights-debugging-with-events.md) para saber tudo o que aconteceu no seu serviço.
* [Monitore a disponibilidade e a capacidade de resposta de qualquer página da Web](../application-insights/app-insights-monitor-web-app-availability.md) com o Application Insights para que você possa descobrir se a página está inativa.

