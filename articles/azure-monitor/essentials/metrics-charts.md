---
title: Recursos avançados do Gerenciador de métricas do Azure
description: Saiba mais sobre usos avançados do Azure Metrics Explorer.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.openlocfilehash: d728dfb364cb0f82326a472196cb28d79b85b1e9
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031425"
---
# <a name="advanced-features-of-the-azure-metrics-explorer"></a>Recursos avançados do Gerenciador de métricas do Azure

> [!NOTE]
> Este artigo pressupõe que você esteja familiarizado com os recursos básicos do recurso do Azure Metrics Explorer de Azure Monitor. Se você for um novo usuário e quiser saber como criar seu primeiro gráfico de métrica, consulte [introdução ao Metrics Explorer](./metrics-getting-started.md).

No Azure Monitor, as [métricas](data-platform-metrics.md) são uma série de valores medidos e contagens que são coletadas e armazenadas ao longo do tempo. As métricas podem ser padrão (também chamada de "plataforma") ou personalizadas. 

As métricas padrão são fornecidas pela plataforma do Azure. Eles refletem as estatísticas de integridade e uso dos recursos do Azure. 

## <a name="resource-scope-picker"></a>Seletor de escopo de recurso
O seletor de escopo de recurso permite exibir métricas entre recursos únicos e vários recursos. As seções a seguir explicam como usar o seletor de escopo de recurso. 

### <a name="select-a-single-resource"></a>Selecionar um único recurso
Selecione **Métricas** no menu do **Azure Monitor** ou na seção **Monitoramento** do menu de um recurso. Em seguida, escolha **selecionar um escopo** para abrir o seletor de escopo. 

Use o seletor de escopo para selecionar os recursos cujas métricas você deseja ver. O escopo deverá ser preenchido se você tiver aberto o Azure Metrics Explorer no menu de um recurso. 

![Captura de tela mostrando como abrir o seletor de escopo de recurso.](./media/metrics-charts/scope-picker.png)

Para alguns recursos, você pode exibir apenas as métricas de um recurso por vez. No menu **tipos de recursos** , esses recursos estão na seção **todos os tipos de recursos** .

![Captura de tela mostrando um único recurso.](./media/metrics-charts/single-resource-scope.png)

Depois de selecionar um recurso, você verá todas as assinaturas e grupos de recursos que contêm esse recurso.

![Captura de tela mostrando os recursos disponíveis.](./media/metrics-charts/available-single-resource.png)

> [!TIP]
> Se você quiser a capacidade de exibir as métricas de vários recursos ao mesmo tempo ou para exibir as métricas em uma assinatura ou grupo de recursos, selecione fazer um **voto**.

Quando estiver satisfeito com sua seleção, selecione **aplicar**.

### <a name="view-metrics-across-multiple-resources"></a>Exibir métricas em vários recursos
Alguns tipos de recursos podem consultar métricas em vários recursos. Os recursos devem estar dentro da mesma assinatura e local. Encontre esses tipos de recursos na parte superior do menu de **tipos de recursos** . 

Para obter mais informações, consulte [selecionar vários recursos](./metrics-dynamic-scope.md#select-multiple-resources).

![Captura de tela mostrando tipos de recursos cruzados.](./media/metrics-charts/multi-resource-scope.png)

Para tipos que são compatíveis com vários recursos, você pode consultar métricas em uma assinatura ou em vários grupos de recursos. Para obter mais informações, consulte [selecionar um grupo de recursos ou uma assinatura](./metrics-dynamic-scope.md#select-a-resource-group-or-subscription).

## <a name="multiple-metric-lines-and-charts"></a>Vários gráficos e linhas de métrica

No Gerenciador de métricas do Azure, você pode criar gráficos que plotam várias linhas métricas ou mostrar vários gráficos de métrica ao mesmo tempo. Essa funcionalidade permite que você:

- Correlacione as métricas relacionadas no mesmo grafo para ver como um valor está relacionado a outro.
- Exibe as métricas que usam diferentes unidades de medida em proximidade.
- Agregue visualmente e compare métricas de vários recursos.

Por exemplo, imagine que você tem cinco contas de armazenamento e deseja saber quanto espaço eles consomem juntos. Você pode criar um gráfico de área (empilhado) que mostra os valores individuais e a soma de todos os valores em determinados pontos no tempo.

### <a name="multiple-metrics-on-the-same-chart"></a>Várias métricas no mesmo gráfico

Para exibir várias métricas no mesmo gráfico, primeiro [crie um novo gráfico](./metrics-getting-started.md#create-your-first-metric-chart). Em seguida, selecione **Adicionar métrica**. Repita essa etapa para adicionar outra métrica no mesmo gráfico.

> [!NOTE]
> Normalmente, os gráficos não devem misturar métricas que usam diferentes unidades de medida. Por exemplo, evite misturar uma métrica que usa milissegundos com outra que usa quilobytes. Além disso, evite misturar métricas cujas escalas diferem significativamente. 
>
> Nesses casos, considere usar vários gráficos em vez disso. No Metrics Explorer, selecione **Adicionar gráfico** para criar um novo gráfico.

### <a name="multiple-charts"></a>Vários gráficos

Para criar outro gráfico que usa uma métrica diferente, selecione **Adicionar gráfico**.

Para reordenar ou excluir vários gráficos, selecione o botão de reticências (**...**) para abrir o menu do gráfico. Em seguida, escolha **mover para cima**, **mover para baixo** ou **excluir**.

## <a name="aggregation"></a>Agregação

Quando você adiciona uma métrica a um gráfico, o Metrics Explorer aplica automaticamente uma agregação padrão. O padrão faz sentido em cenários básicos. Mas você pode usar uma agregação diferente para obter mais informações sobre a métrica. 

Antes de usar diferentes agregações em um gráfico, você deve entender como as métricas Explorer as manipula. As métricas são uma série de medições (ou "valores de métricas") que são capturadas durante um período de tempo. Quando você plota um gráfico, os valores da métrica selecionada são agregados separadamente no intervalo de *tempo*. 

Você seleciona o tamanho do intervalo de tempo usando o [painel seletor de tempo](./metrics-getting-started.md#select-a-time-range)do Metrics Explorer. Se você não selecionar explicitamente o detalhamento de tempo, o intervalo de tempo atualmente selecionado será usado por padrão. Depois que o intervalo de tempo é determinado, os valores de métrica que foram capturados durante cada intervalo de tempo são agregados no gráfico, um ponto de dados por intervalo de tempo.

Por exemplo, suponha que um gráfico mostre a métrica de *tempo de resposta do servidor* . Ele usa a agregação *média* ao longo do período das *últimas 24 horas*. Neste exemplo:

- Se a granularidade de tempo for definida como 30 minutos, o gráfico será desenhado a partir de 48 pontos de dados agregados. Ou seja, o gráfico de linhas conecta 48 pontos na área de plotagem do gráfico (24 horas x 2 pontos de dados por hora). Cada ponto de dados representa a *média* de todos os tempos de resposta capturados para solicitações de servidor que ocorreram durante cada um dos períodos de tempo de 30 minutos relevantes.
- Se você alternar a granularidade de tempo para 15 minutos, obterá 96 pontos de dados agregados.  Ou seja, você obtém 24 horas x 4 pontos de dados por hora.

O Metrics Explorer tem cinco tipos de agregação estatística básica: Sum, Count, min, Max e Average. A agregação *sum* às vezes é chamada de agregação *total* . Para muitas métricas, o Metrics Explorer oculta as agregações que são irrelevantes e não podem ser usadas.

* **Sum**: a soma de todos os valores capturados durante o intervalo de agregação.

    ![Captura de tela de uma solicitação Sum.](./media/metrics-charts/request-sum.png)

* **Contagem**: o número de medições capturadas durante o intervalo de agregação. 
    
    Quando a métrica é sempre capturada com o valor de 1, a agregação Count é igual à agregação Sum. Esse cenário é comum quando a métrica rastreia a contagem de eventos distintos e cada medida representa um evento. O código emite um registro de métrica toda vez que uma nova solicitação chega.

    ![Captura de tela de uma solicitação de contagem.](./media/metrics-charts/request-count.png)

* **Média**: a média dos valores de métrica capturados durante o intervalo de agregação.

    ![Captura de tela de uma solicitação média.](./media/metrics-charts/request-avg.png)

* **Min**: o menor valor capturado durante o intervalo de agregação.

    ![Captura de tela de uma solicitação mínima.](./media/metrics-charts/request-min.png)

* **Max**: o maior valor capturado durante o intervalo de agregação.

    ![Captura de tela de uma solicitação máxima.](./media/metrics-charts/request-max.png)

## <a name="filters"></a>Filtros

Você pode aplicar filtros a gráficos cujas métricas têm dimensões. Por exemplo, imagine uma métrica de "contagem de transações" que tem uma dimensão "tipo de resposta". Esta dimensão indica se a resposta das transações foi bem-sucedida ou falhou. Se você filtrar nessa dimensão, verá uma linha de gráfico somente para transações bem-sucedidas (ou somente com falha). 

### <a name="add-a-filter"></a>Adicionar um filtro

1. Acima do gráfico, selecione **Adicionar filtro**.

2. Selecione uma dimensão (Propriedade) para filtrar.

   ![Captura de tela que mostra as dimensões (Propriedades) que você pode filtrar.](./media/metrics-charts/028.png)

3. Selecione o operador que você deseja aplicar na dimensão (Propriedade). O operador padrão é = (Equals)

   ![Captura de tela que mostra o operador que você pode usar com o filtro.](./media/metrics-charts/filter-operator.png)

4. Selecione os valores de dimensão que você deseja aplicar ao filtro ao plotar o gráfico (Este exemplo mostra a filtragem das transações de armazenamento bem-sucedidas):

   ![Captura de tela que mostra as transações de armazenamento filtradas com êxito.](./media/metrics-charts/029.png)

5. Depois de selecionar os valores de filtro, clique fora do Seletor de Filtro para fechá-lo. Agora o gráfico mostra quantas transações de armazenamento falharam:

   ![Captura de tela que mostra quantas transações de armazenamento falharam.](./media/metrics-charts/030.png)

6. Você pode repetir as etapas de 1-5 para aplicar vários filtros aos mesmos gráficos.


## <a name="metric-splitting"></a>Divisão de métrica

Você pode dividir uma métrica por dimensão para visualizar como os diferentes segmentos da métrica se comparam. A divisão também pode ajudá-lo a identificar os segmentos distantes de uma dimensão.

### <a name="apply-splitting"></a>Aplicar a separação

1. Acima do gráfico, selecione **aplicar divisão**.
 
   > [!NOTE]
   > Os gráficos que têm várias métricas não podem usar a funcionalidade de divisão. Além disso, embora um gráfico possa ter vários filtros, ele pode ter apenas uma dimensão de divisão.

2. Escolha uma dimensão na qual segmentar seu gráfico:

   ![Captura de tela que mostra a dimensão selecionada na qual segmentar o gráfico.](./media/metrics-charts/031.png)

   O gráfico agora mostra várias linhas, uma para cada segmento de dimensão:

   ![Captura de tela que mostra várias linhas, uma para cada segmento de dimensão.](./media/metrics-charts/segment-dimension.png)
   
3. Escolha um limite para o número de valores a serem exibidos após a divisão pela dimensão selecionada. O limite padrão é 10, conforme mostrado no gráfico acima. O intervalo de limite é de 1-50.
   
   ![Captura de tela que mostra o limite de divisão, que restringe o número de valores após a divisão.](./media/metrics-charts/segment-dimension-limit.png)
   
4. Escolha a ordem de classificação em segmentos: crescente ou decrescente. A seleção padrão é decrescente.
   
   ![Captura de tela que mostra a ordem de classificação em valores divididos.](./media/metrics-charts/segment-dimension-sort.png)

5. Clique fora do **Seletor de Agrupamento** para fechá-lo.
   

   > [!NOTE]
   > Para ocultar os segmentos que são irrelevantes para seu cenário e facilitar a leitura de seus gráficos, use a filtragem e a divisão na mesma dimensão.

## <a name="locking-the-range-of-the-y-axis"></a>Bloqueando o intervalo do eixo y

Bloquear o intervalo do eixo dos valores (y) torna-se importante em gráficos que mostram pequenas flutuações de valores grandes. 

Por exemplo, uma queda no volume de solicitações bem-sucedidas de 99,99% a 99,5% pode representar uma redução significativa na qualidade do serviço. Mas observar uma pequena flutuação de valor numérico seria difícil ou mesmo impossível se você estiver usando as configurações de gráfico padrão. Nesse caso, você pode bloquear o limite mais baixo do gráfico para 99% para tornar um pequeno descarte mais aparente. 

Outro exemplo é uma flutuação na memória disponível. Nesse cenário, o valor tecnicamente nunca atingirá 0. Corrigir o intervalo para um valor mais alto pode tornar mais fácil a localização da memória disponível. 

Para controlar o intervalo do eixo y, abra o menu do gráfico (**...**). Em seguida, selecione **configurações de gráfico** para acessar as configurações avançadas do gráfico.

![Captura de tela que realça a seleção de configurações de gráfico.](./media/metrics-charts/033.png)

Modifique os valores na seção **intervalo do eixo Y** ou selecione **automático** para reverter para os valores padrão.
 
 ![Captura de tela que realça a seção de intervalo do eixo Y.](./media/metrics-charts/034.png)

> [!WARNING]
> Se você precisar bloquear os limites do eixo y para gráficos que controlam contagens ou somas ao longo de um período de tempo (usando agregações Count, Sum, min ou Max), geralmente você deve especificar uma granularidade de tempo fixa. Nesse caso, você não deve contar com os padrões automáticos. 
>
> Você escolhe uma granularidade de tempo fixa porque os valores do gráfico são alterados quando a granularidade de tempo é modificada automaticamente depois que um usuário redimensiona uma janela do navegador ou altera a resolução da tela. A alteração resultante na granularidade do tempo afeta a aparência do gráfico, invalidando a seleção atual do intervalo do eixo y.

## <a name="line-colors"></a>Cores da linha

Depois de configurar os gráficos, as linhas do gráfico são automaticamente atribuídas a uma cor de uma paleta padrão. Você pode alterar essas cores.

Para alterar a cor de uma linha de gráfico, selecione a barra colorida na legenda que corresponde ao gráfico. A caixa de diálogo Seletor de cores é aberta. Use o seletor de cores para configurar a cor da linha.

![Captura de tela que mostra como alterar a cor.](./media/metrics-charts/035.png)

Suas cores personalizadas são preservadas quando você fixa o gráfico em um painel. A seção a seguir mostra como fixar um gráfico.

## <a name="pinning-to-dashboards"></a>Fixação em painéis 

Depois de configurar um gráfico, talvez você queira adicioná-lo a um painel. Ao fixar um gráfico a um painel, você pode torná-lo acessível à sua equipe. Você também pode obter informações exibindo-as no contexto de outra telemetria de monitoramento.

Para fixar um gráfico configurado em um painel, no canto superior direito do gráfico, selecione **fixar no painel**.

![Captura de tela mostrando como fixar um gráfico em um painel.](./media/metrics-charts/036.png)

## <a name="alert-rules"></a>Regras de alerta

Você pode usar seus critérios de visualização para criar uma regra de alerta baseada em métrica. A nova regra de alerta incluirá as dimensões de recurso de destino, métrica, divisão e filtro do gráfico. Você pode modificar essas configurações usando o painel criação de regra de alerta.

Para começar, selecione **nova regra de alerta**.

![Captura de tela que mostra o novo botão de regra de alerta realçado em vermelho.](./media/metrics-charts/042.png)

O painel criação da regra de alerta é aberto. No painel, você vê as dimensões de métricas do gráfico. Os campos no painel são preenchidos previamente para ajudá-lo a personalizar a regra.

![Captura de tela mostrando o painel de criação de regra.](./media/metrics-charts/041.png)

Para obter mais informações, consulte [criar, exibir e gerenciar alertas de métrica](../alerts/alerts-metric.md).

## <a name="correlate-metrics-to-logs"></a>Correlacionar métricas a logs
Para ajudar o cliente a diagnosticar a causa raiz de anomalias em seu gráfico de métricas, criamos Drill-through nos logs. Analisar os logs permite que os clientes correlacionem picos em seu gráfico de métricas a logs e consultas. 

Antes de nos aprofundarmos na experiência, queremos primeiro introduzir os diferentes tipos de logs e consultas fornecidos. 

| Termo             | Definição  | 
|------------------|-------------|
| Logs de atividades    | Fornece informações sobre as operações em cada recurso do Azure na assinatura de fora (o plano de gerenciamento), além de atualizações em eventos de integridade do serviço. Use o log de atividades para determinar o que, quem e quando para qualquer operação de gravação (PUT, POST, DELETE) realizada nos recursos em sua assinatura. Há um único log de atividades para cada assinatura do Azure.  |   
| Log de diagnóstico   | Forneça informações sobre as operações que foram executadas em um recurso do Azure (o plano de dados), por exemplo, obter um segredo de um Key Vault ou fazer uma solicitação para um banco de dado. O conteúdo dos logs de recursos varia de acordo com o serviço do Azure e o tipo de recurso. **Observação:** Deve ser fornecido pelo serviço e habilitado pelo cliente  | 
| Log recomendado | Consultas baseadas em cenário que o cliente pode aproveitar para investigar anomalias em seu Metrics Explorer.  |

Atualmente, a análise detalhada de logs está disponível para provedores de recursos selecionados. Os provedores de recursos que têm a experiência completa de detalhamento dos logs são: 

* Application Insights 
* Autoscale 
* Serviços de Aplicativos  
* Armazenamento  

Veja abaixo uma experiência de exemplo para o provedor de recursos Application Insights.

![Picos em falhas na folha de métricas do App insights](./media/metrics-charts/drill-into-log-ai.png)

Para diagnosticar o pico em solicitações com falha, clique em "analisar logs".

![Captura de tela da lista suspensa examinar logs](./media/metrics-charts/drill-into-logs-dropdown.png)

Ao clicar na opção falha, você será levado a uma folha falha personalizada que fornece as operações de operação com falha, os principais tipos de exceções e as dependências. 

![Captura de tela da folha de falhas do App insights](./media/metrics-charts/ai-failure-blade.png)

### <a name="common-problems-with-drill-into-logs"></a>Problemas comuns com Drill in de logs

* Log e consultas estão desabilitadas-para exibir consultas e logs recomendados, você deve rotear seus logs de diagnóstico para Log Analytics. Leia [este documento](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) para saber como fazer isso. 
* Os logs de atividade são fornecidos apenas – o recurso drill in logs só está disponível para os provedores de recursos selecionados. Por padrão, os logs de atividade são fornecidos. 

 
## <a name="troubleshooting"></a>Solução de problemas

Se você não vir nenhum dado em seu gráfico, examine as seguintes informações de solução de problemas:

* Os filtros se aplicam a todos os gráficos no painel. Enquanto você se concentra em um gráfico, certifique-se de não definir um filtro que exclua todos os dados em outro gráfico.

* Para definir filtros diferentes em gráficos diferentes, crie os gráficos em diferentes folhas. Em seguida, salve os gráficos como favoritos separados. Se desejar, você pode fixar os gráficos no painel para poder vê-los juntos.

* Se você segmentar um gráfico por uma propriedade que a métrica não define, o gráfico não exibirá nenhum conteúdo. Tente limpar a segmentação (divisão) ou escolha uma propriedade diferente.

## <a name="next-steps"></a>Próximas etapas

Para criar painéis acionáveis usando métricas, consulte [criando painéis de KPI personalizados](../app/tutorial-app-dashboards.md).

