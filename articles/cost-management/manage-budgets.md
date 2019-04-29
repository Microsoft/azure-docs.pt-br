---
title: Gerenciar os orçamentos do Cloudyn no Azure | Microsoft Docs
description: Este artigo ajuda você a criar orçamentos e começar a gerenciá-los rapidamente no Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: seodec18
ms.openlocfilehash: 4a456ed0c239b58809fc0ffb83d97aa7db475b70
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60576745"
---
# <a name="manage-azure-budgets-with-cloudyn"></a>Gerenciar orçamentos do Azure com o Cloudyn

Configurar orçamentos e alertas com base no orçamento ajuda a melhorar sua governança em nuvem e contabilidade. Este artigo ajuda você a criar orçamentos e começar a gerenciá-los rapidamente no Cloudyn.

Quando você tiver uma conta Enterprise ou MSP, você pode usar a estrutura de entidade de custo hierárquico para atribuir as contas de orçamento para diferentes unidades de negócios, departamentos ou qualquer outra entidade de custo. Quando você tiver uma conta Premium, poderá usar a funcionalidade de gerenciamento de orçamento, que é então aplicada a todo seu gasto na nuvem. Todos os orçamentos são atribuídos manualmente.

Com base nos orçamentos atribuídos, você pode definir os alertas de limite com base na porcentagem do seu orçamento que consumiu e define a gravidade de cada limite.

Os relatórios de orçamento mostram o orçamento atribuído. Os usuários podem visualizar quando seu gasto acabar, abaixo, ou a par co seu consumo sobre o tempo. Ao selecionar **Exibir/Ocultar Campos** na parte superior de um relatório de orçamento, você pode visualizar o custo, orçamento, custo acumulado ou orçamento total.

O Gerenciamento de Custos do Azure oferece uma funcionalidade semelhante ao Cloudyn. O Gerenciamento de Custos do Azure é uma solução de gerenciamento de custos nativa do Azure. Ele ajuda a analisar custos, criar e gerenciar orçamentos, exportar dados e examinar e agir de acordo com as recomendações de otimização que visam economizar dinheiro. Para obter mais informações sobre os orçamentos no Gerenciamento de Custos, confira [Criar e gerenciar orçamentos](tutorial-acm-create-budgets.md).

## <a name="create-budgets"></a>Criar orçamentos

Ao criar um orçamento, você define-o para o seu exercício fiscal e aplica para uma entidade específica.

Para criar um orçamento e atribuir a uma entidade:

1. Navegue para **Custos** &gt; **Gerenciamento de Custos** &gt; **Orçamento**.
2. Na página de Gerenciamento do Orçamento, em **Entidades**, selecione a entidade onde você quer criar o orçamento.
3. No ano do orçamento, selecione o ano onde você quer criar o orçamento.
4. Para cada mês, definir um valor de orçamento. Ao terminar, clique em **Salvar**.
Neste exemplo, o orçamento mensal para junho de 2018 foi definido para US$135.000. O orçamento total para o ano é de US$1.615000,00.
![Criar uma página de orçamento em que você define um orçamento para cada mês](./media/manage-budgets/set-budget.png)


Para importar um arquivo para o orçamento anual:

1. Em **Ações**, selecione **Exportar** para realizar o download de um modelo de CSV vazio para usar como base para o seu orçamento.
2. Preencha o arquivo CSV com as suas entradas de orçamento e salve localmente.
3. Em **Ações**, selecione **Importar**.
4. Selecione seu arquivo salvo e clique em **OK**.

Para exportar o orçamento concluído como arquivo CSV em **Ações**, selecione **Exportar** para realizar o download do arquivo.

## <a name="view-budget-in-reports"></a>Visualizar orçamento nos relatórios

Ao concluir, seu orçamento será exibido na maioria dos relatórios de Custo em **Custos** &gt; **Análise de Custo** e no Custo vs. relatório de Custo ao Longo do Tempo Você também pode programar relatórios com base nos limites de orçamento usando **Ações**.

Aqui está um exemplo do relatório de Análise de Custos. Mostra o orçamento total e o custo por tipo de carga de trabalho e uso desde o início do ano.

![Exemplo de Análise de Custos com orçamento](./media/manage-budgets/cost-analysis-budget-example.png)

Neste exemplo, suponha que a data atual seja 22 de de junho. O custo para junho de 2018 é de US$71.611,28 comparado ao orçamento mensal de US$135,000. O custo é muito menor do que o orçamento mensal porque há ainda oito dias de gasto ants do fim do mês.

Outra forma de visualizar o relatório é analisar o custo acumulado em relação ao seu orçamento. Para ver os custos acumulados, em **Exibir/Ocultar Campos**, selecione **Custo Acumulado** e **Orçamento Total**. Aqui está exemplo mostrando o custo acumulado desde o começo do ano.

![Exemplo de custo acumulado e orçamento total mostrado no relatório de Custo vs. Orçamento ao Longo do Tempo](./media/manage-budgets/accumulated-budget.png)

Em algum momento no futuro, o custo acumulado poderá exceder o orçamento. É possível ver com mais facilidade que, se alterar a visualização do gráfico para o _tipo_  de linha.

![Orçamento mostrado em um gráfico de linhas no relatório de Custo por Meses](./media/manage-budgets/budget-line.png)

## <a name="create-budget-alerts-for-a-filter"></a>Criar alertas de orçamento para um filtro

No exemplo anterior, você pode ver que o custo acumulado aproximou-se do orçamento. É possível criar alertas automáticos de orçamento para ser notificado ao gastar abordagens ou exceder seu orçamento. Basicamente, o alerta é um relatório agendado com um limite. A métrica de limite de alerta de orçamento inclui:

- Custo restante vs. orçamento - especificar um limite de valor monetário
- Porcentagem de custo vs. orçamento - para especificar um limite de valor percentual

Vamos examinar um exemplo.

No custo vs. relatório de Orçamento ao Longo do Tempo, clique em **Ações** e, em seguida, selecione **Agendar relatório**. Na guia Limite, selecione uma métrica de limite. Por exemplo, **Porcentagem de custo vs orçamento**. Selecione um tipo de alerta e insira um valor percentual do orçamento. Se quiser ser notificado apenas uma vez, selecione **Número de alertas consecutivos** e, em seguida digite_1_. Clique em **Salvar**.

![Criando um alerta de orçamento na caixa Salvar ou Agendar este relatório](./media/manage-budgets/budget-alert.png)

## <a name="next-steps"></a>Próximas etapas

- Se você ainda não concluiu o primeiro tutorial do Cloudyn, leia-o em [Examinar o uso e os custos](tutorial-review-usage.md).
- Saiba mais sobre os [relatórios disponíveis no Cloudyn](use-reports.md).
