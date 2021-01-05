---
title: Recursos avançados do Azure Metrics Explorer
description: Saiba mais sobre os recursos avançados do Azure Monitor Metrics Explorer
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: a80eaecc02fa3c8c6618341c02e22241f0dc7faf
ms.sourcegitcommit: 5ef018fdadd854c8a3c360743245c44d306e470d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/01/2021
ms.locfileid: "97845037"
---
# <a name="advanced-features-of-azure-metrics-explorer"></a>Recursos avançados do Azure Metrics Explorer

> [!NOTE]
> Este artigo pressupõe que você esteja familiarizado com os recursos básicos do Metrics Explorer. Se você for um novo usuário e quiser saber como criar seu primeiro gráfico de métrica, consulte [introdução ao Azure Metrics Explorer](metrics-getting-started.md).

## <a name="metrics-in-azure"></a>Métricas no Azure

[Métricas no Azure Monitor](data-platform-metrics.md) são a série de valores medidos e as contagens coletadas e armazenadas ao longo do tempo. Há métricas padrão (ou da “plataforma”) e métricas personalizadas. As métricas padrão são fornecidas pela própria plataforma Azure. As métricas padrão refletem as estatísticas de uso e integridade dos recursos do Azure. Enquanto as métricas personalizadas são enviadas ao Azure por seus aplicativos usando a [api Application insights para métricas e eventos personalizados](../app/api-custom-events-metrics.md),  [extensão WAD (Windows diagnóstico do Azure)](./diagnostics-extension-overview.md)ou por [Azure monitor API REST](./metrics-store-custom-rest-api.md).

## <a name="resource-scope-picker"></a>Seletor de escopo de recurso
O seletor de escopo de recurso permite que você exiba métricas em recursos únicos e múltiplos. Veja abaixo instruções sobre como usar o seletor de escopo de recurso. 

### <a name="selecting-a-single-resource"></a>Selecionando um único recurso
Selecione **Métricas** no menu do **Azure Monitor** ou na seção **Monitoramento** do menu de um recurso. Clique no botão "selecionar um escopo" para abrir o seletor de escopo, que permitirá selecionar os recursos para os quais você deseja ver as métricas. Isso já deve estar preenchido se você abrir o Metrics Explorer no menu de um recurso. 

![Captura de tela do seletor de escopo do recurso](./media/metrics-charts/scope-picker.png)

Para determinados recursos, você só pode exibir as métricas de um único recurso por vez. Esses recursos estão na seção "todos os tipos de recurso" no menu suspenso tipos de recursos.

![Captura de tela de recurso único](./media/metrics-charts/single-resource-scope.png)

Depois de clicar no recurso desejado, você verá todas as assinaturas e grupos de recursos que contêm esse recurso.

![Captura de tela dos recursos disponíveis](./media/metrics-charts/available-single-resource.png)

> [!TIP]
> Se você quiser exibir as métricas de vários recursos ao mesmo tempo ou as métricas em uma assinatura ou grupo de recursos, clique no botão de voto.

Quando estiver satisfeito com sua seleção, clique em "aplicar".

### <a name="viewing-metrics-across-multiple-resources"></a>Exibindo métricas em vários recursos
Alguns tipos de recursos habilitaram a capacidade de consultar métricas em vários recursos, desde que estejam dentro da mesma assinatura e local. Esses tipos de recursos podem ser encontrados na parte superior da lista suspensa "tipos de recurso". Para obter mais detalhes sobre como exibir as métricas em vários recursos, exiba [este documento](metrics-dynamic-scope.md#selecting-multiple-resources).

![Captura de tela de tipos de recurso cruzado](./media/metrics-charts/multi-resource-scope.png)

Para tipos compatíveis com vários recursos, você também pode consultar métricas em uma assinatura ou em vários grupos de recursos. Para saber como fazer isso, veja [Este artigo](metrics-dynamic-scope.md#selecting-a-resource-group-or-subscription)


## <a name="create-views-with-multiple-metrics-and-charts"></a>Criar exibições com várias métricas e gráficos

Você pode criar gráficos que plotam várias linhas de métricas ou mostrar vários gráficos de métrica de uma só vez. Essa funcionalidade permite que você:

- Correlacione as métricas relacionadas no mesmo grafo para ver como um valor está relacionado a outro
- Exibir métricas com diferentes unidades de medida no próximo proximidade
- Agregue visualmente e compare métricas de vários recursos

Por exemplo, se você tiver 5 contas de armazenamento e quiser saber quanto espaço total é consumido entre elas, você pode criar um gráfico de área (empilhado) que mostra a pessoa e a soma de todos os valores em determinados pontos no tempo.

### <a name="multiple-metrics-on-the-same-chart"></a>Várias métricas no mesmo gráfico

Primeiro, [crie um novo gráfico](metrics-getting-started.md#create-your-first-metric-chart). Clique em **Adicionar métrica** e repita as etapas para adicionar outra métrica no mesmo gráfico.

   > [!NOTE]
   > Normalmente, você não deseja ter métricas com diferentes unidades de medida (ou seja, “milissegundos” e “quilobytes”) ou com uma escala consideravelmente diferente em um gráfico. Em vez disso, considere o uso de vários gráficos. Clique no botão Adicionar Gráfico para criar vários gráficos no Metrics Explorer.

### <a name="multiple-charts"></a>Vários gráficos

Clique em **Adicionar gráfico** e crie outro gráfico com uma métrica diferente.

### <a name="order-or-delete-multiple-charts"></a>Ordenar ou excluir vários gráficos

Para ordenar ou excluir vários gráficos, clique no símbolo de reticências ( **...** ) para abrir o menu do gráfico e escolha o item de menu apropriado de **mover para cima**, **mover para baixo** ou **excluir**.

## <a name="changing-aggregation"></a>Alterando a agregação

Quando você adiciona uma métrica a um gráfico, o Metrics Explorer seleciona automaticamente sua agregação padrão. O padrão faz sentido nos cenários básicos, mas você pode usar uma agregação diferente para obter informações adicionais sobre a métrica. A exibição de agregações diferentes em um gráfico requer que você entenda como as métricas Explorer as manipula. 

Métricas são a série de medições (ou "valores de métrica") capturados no período de tempo. Quando você plota um gráfico, os valores da métrica selecionada são agregados separadamente no intervalo de *tempo*. Você seleciona o tamanho do intervalo de tempo [usando o Metrics Explorer painel seletor de tempo](metrics-getting-started.md#select-a-time-range). Se você não fizer uma seleção explícita do intervalo de tempo, a granularidade de tempo será selecionada automaticamente com base no período de tempo atualmente selecionado. Depois que o intervalo de tempo é determinado, os valores de métrica que foram capturados durante cada intervalos de refinamento de tempo são agregados e colocados no gráfico-um DataPoint por intervalo de tempo.

Por exemplo, suponha que o gráfico esteja mostrando a métrica de **tempo de resposta do servidor** usando a agregação **média** no período das **últimas 24 horas** :

- Se a granularidade de tempo for definida como 30 minutos, o gráfico será desenhado de pontos de extremidade de 48 agregados (por exemplo, o gráfico de linhas conecta 48 pontos na área de plotagem do gráfico). Ou seja, 24 horas x 2 pontos de extremidade por hora. Cada DataPoint representa a *média* de todos os tempos de resposta capturados para solicitações de servidor ocorridas durante cada um dos períodos de tempo de 30 minutos relevantes.
- Se você alternar a granularidade de tempo para 15 minutos, obterá 96 pontos de extremidade agregados.  Ou seja, 24 horas x 4 pontos de extremidade por hora.

Há cinco tipos de agregação de estatísticas básicos disponíveis no Metrics Explorer: **soma**, **contagem**, **mín**., **máx**. e **média**. A agregação **sum** às vezes é referida como agregação **total** . Para muitas métricas, Metrics Explorer ocultará as agregações que são totalmente irrelevantes e não podem ser usadas.

**Sum** – a soma de todos os valores capturados sobre o intervalo de agregação

![Captura de tela da soma da solicitação](./media/metrics-charts/request-sum.png)

**Contagem** – o número de medições capturadas no intervalo de agregação. Observe que **Count** será igual a **sum** , no caso em que a métrica é sempre capturada com o valor de 1. Isso é comum quando a métrica acompanha a contagem de eventos distintos e cada medida representa um evento (ou seja, o código dispara um registro de métrica sempre que uma nova solicitação chega)

![Captura de tela da contagem de solicitações](./media/metrics-charts/request-count.png)

**Média** – a média dos valores de métrica capturados no intervalo de agregação

![Captura de tela da solicitação média](./media/metrics-charts/request-avg.png)

**Min** – o menor valor capturado sobre o intervalo de agregação

![Captura de tela da solicitação mínima](./media/metrics-charts/request-min.png)

**Max** – o maior valor capturado sobre o intervalo de agregação

![Captura de tela da solicitação máxima](./media/metrics-charts/request-max.png)

## <a name="apply-filters-to-charts"></a>Aplicar filtros a gráficos

Aplique filtros aos gráficos que mostram métricas com dimensões. Por exemplo, se a métrica “Contagem de transações” tiver uma dimensão, “Tipo de resposta”, que indica se a resposta das transações foi bem-sucedida ou falhou, a filtragem nessa dimensão plotará uma linha de gráfico apenas para as transações bem-sucedidas (ou apenas para aquelas com falha). 

### <a name="to-add-a-filter"></a>Para adicionar um filtro

1. Selecione **Adicionar filtro** acima do gráfico

2. Selecione qual dimensão (propriedade) você deseja filtrar

   ![Captura de tela que mostra as dimensões (Propriedades) que você pode filtrar.](./media/metrics-charts/028.png)

3. Selecione quais valores de dimensão você deseja incluir ao plotar o gráfico (este exemplo mostra a filtragem das transações de armazenamento bem-sucedidas):

   ![Captura de tela que mostra a filtragem das transações de armazenamento bem-sucedidas.](./media/metrics-charts/029.png)

4. Depois de selecionar os valores de filtro, clique fora do Seletor de Filtro para fechá-lo. Agora o gráfico mostra quantas transações de armazenamento falharam:

   ![Captura de tela que mostra quantas transações de armazenamento falharam](./media/metrics-charts/030.png)

5. Repita as etapas 1 a 4 para aplicar vários filtros aos mesmos gráficos.



## <a name="apply-splitting-to-a-chart"></a>Aplicar divisão a um gráfico

Divida uma métrica pela dimensão para visualizar uma comparação de diferentes segmentos da métrica em relação um ao outro e identificar os segmentos distantes de uma dimensão.

### <a name="apply-splitting"></a>Aplicar a separação

1. Clique em **Aplica-se a divisão** acima do gráfico.
 
   > [!NOTE]
   > A divisão não pode ser usada com gráficos com várias métricas. Além disso, você pode ter vários filtros, mas apenas uma dimensão de divisão aplicada a um único gráfico.

2. Escolha uma dimensão na qual você deseja segmentar o gráfico:

   ![Captura de tela que mostra a dimensão selecionada na qual você segmenta o gráfico.](./media/metrics-charts/031.png)

   Agora o gráfico mostra várias linhas, uma para cada segmento de dimensão:

   ![Captura de tela que mostra várias linhas, uma para cada segmento de dimensão.](./media/metrics-charts/032.png)

3. Clique fora do **Seletor de Agrupamento** para fechá-lo.

   > [!NOTE]
   > Use a Filtragem e a Divisão na mesma dimensão para ocultar os segmentos que são irrelevantes para seu cenário e facilitar a leitura dos gráficos.

## <a name="lock-boundaries-of-chart-y-axis"></a>Limites de bloqueio do eixo y do gráfico

Bloquear o intervalo do eixo y se torna importante quando o gráfico mostra flutuações menores de valores maiores. 

Por exemplo, quando o volume de solicitações bem-sucedidas cai de 99,99% para 99,5%, isso pode representar uma redução significativa na qualidade de serviço. No entanto, observar uma pequena flutuação de valor numérico seria difícil ou mesmo impossível com as configurações de gráfico padrão. Nesse caso, você pode bloquear o limite mais baixo do gráfico para 99%, o que tornaria essa pequena queda mais aparente. 

Outro exemplo é uma flutuação na memória disponível, em que o valor tecnicamente nunca alcançará 0. Fixar o intervalo para um valor maior pode facilitar a detecção de quedas na memória disponível. 

Para controlar o intervalo do eixo y, use o menu do gráfico "…" menu gráfico e selecione **configurações de gráfico** para acessar configurações avançadas de gráfico.

![Captura de tela que realça a opção de configurações de gráfico.](./media/metrics-charts/033.png)

 Modifique os valores na seção Intervalo do Eixo U ou use o botão **Automático** para reverter para os padrões.
 
 ![Captura de tela que realça a seção de intervalo do eixo Y.](./media/metrics-charts/034.png)

> [!WARNING]
> Bloquear os limites do eixo y para que os gráficos que acompanham várias contagens ou somas por um período (e, portanto, usam agregações máximas, mínimas, soma e contagem) geralmente requer especificar uma granularidade de tempo fixo, em vez de contar com os padrões automáticos. Isso é necessário porque os valores em gráficos mudam quando a granularidade de tempo é modificada automaticamente pelo usuário redimensionando a janela do navegador ou passando de uma resolução de tela para outra. A alteração de granularidade de tempo resultante afeta o visual do gráfico, invalidando a seleção atual do intervalo do eixo y.

## <a name="change-colors-of-chart-lines"></a>Alterar cores de linhas do gráfico

Depois de configurar os gráficos, as linhas do gráfico são automaticamente atribuídas a uma cor de uma paleta padrão. Você pode alterar essas cores.

Para alterar a cor de uma linha de gráfico, clique na barra colorida na legenda que corresponde ao gráfico. A caixa de diálogo Seletor de cores será aberta. Use o seletor de cores para configurar a cor da linha.

![Captura de tela que mostra como alterar a cor](./media/metrics-charts/035.png)

Depois que as cores do gráfico forem configuradas, elas permanecerão dessa maneira quando você fixar o gráfico em um painel. A seção a seguir mostra como fixar um gráfico.

## <a name="pin-charts-to-dashboards"></a>Fixar gráficos em painéis

Depois de configurar os gráficos, talvez você deseje adicioná-los aos painéis, de modo que você possa exibi-los novamente, possivelmente, no contexto de outra telemetria de monitoramento ou compartilhá-los com sua equipe.

Para fixar um gráfico configurado em um painel:

Depois de configurar o gráfico, clique em **fixar no painel** no canto superior direito do gráfico.

![Captura de tela que mostra como fixar no gráfico](./media/metrics-charts/036.png)

## <a name="create-alert-rules"></a>Criar regras de alerta

Você pode usar os critérios definidos para visualizar suas métricas como fundamento para uma regra de alerta com base na métrica. A nova regra de alerta incluirá seu recurso de destino, métrica, divisões e dimensões de filtro do seu gráfico. É possível alterar essas configurações posteriormente no painel de criação de regras de alerta.

### <a name="to-create-a-new-alert-rule-click-new-alert-rule"></a>Para criar uma nova regra de alerta, clique em **Nova regra de alerta**

![Botão de nova regra de alerta realçado em vermelho](./media/metrics-charts/042.png)

Você será levado para o painel de criação de regra de alerta com as dimensões de métrica subjacentes do gráfico preenchido previamente para facilitar ainda mais a gerar regras de alerta personalizadas.

![Criar regra de alerta](./media/metrics-charts/041.png)

Verifique este [artigo](alerts-metric.md) para saber mais sobre como configurar alertas de métrica.

## <a name="troubleshooting"></a>Solução de problemas

*Não vejo dados no gráfico.*

* Os filtros se aplicam a todos os gráficos do painel. Verifique se, ao se concentrar em um gráfico, não definiu um filtro que excluía todos os dados em outro.

* Se quiser definir filtros diferentes em gráficos diferentes, crie-os em folhas diferentes e os salve como favoritos separados. Se desejar, você poderá fixá-los ao painel para que eles sejam exibidos lado a lado.

* Se você segmentar um gráfico por uma propriedade que não esteja definida na métrica, o gráfico ficará vazio. Tente limpar a segmentação (divisão) ou escolha uma propriedade diferente.

## <a name="next-steps"></a>Próximas etapas

  Leia [Criar painéis personalizados de KPI](../learn/tutorial-app-dashboards.md) para saber mais sobre as melhores práticas para a criação de painéis acionáveis com métricas.
