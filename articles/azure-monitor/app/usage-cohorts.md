---
title: Coortes de uso do Azure Application Insights| Microsoft Docs
description: Analisar diferentes conjuntos ou usuários, sessões, eventos ou operações que tenham algo em comum
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/10/2018
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: a575161be0a339973f9c59cc13c2320b38939d04
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60785028"
---
# <a name="application-insights-cohorts"></a>Coortes do Application Insights

Uma coorte é um conjunto de usuários, sessões, eventos ou operações que têm algo em comum. No Azure Application Insights, as coortes são definidas por uma consulta de análise. Nos casos em que é necessário analisar um conjunto específico de usuários ou eventos repetidamente, as coortes podem fornecer mais flexibilidade para expressar exatamente o conjunto em que você está interessado.

![Painel de coortes](./media/usage-cohorts/001.png)

## <a name="cohorts-versus-basic-filters"></a>Coortes em comparação com filtros básicos

Coortes são usadas de maneiras semelhantes aos filtros. Mas as definições de coortes são criadas a partir de consultas de análises personalizadas, por isso, são muito mais adaptáveis e complexas. Ao contrário de filtros, você pode salvar as coortes para que outros membros da equipe possam reutilizá-las.

É possível definir uma coorte de usuários que já experimentaram um novo recurso no aplicativo. É possível salvar essa coorte no recurso do Application Insights. No futuro, será fácil analisar esse grupo de usuários específicos salvo.

> [!NOTE]
> Após criadas, as coortes estarão disponíveis nas ferramentas Usuários, Sessões, Eventos e Fluxos dos Usuários.

## <a name="example-engaged-users"></a>Exemplo: Usuários envolvidos

Sua equipe define usuário envolvido como qualquer pessoa que usar seu aplicativo cinco ou mais vezes em um determinado mês. Nesta seção, você define uma coorte desses usuários envolvidos.

1. Abra a ferramenta Coortes.

2. Selecione a guia **Galeria de Modelos**. Uma coleção de modelos para várias coortes é exibida.

3. Selecione **Usuários Envolvidos -- por Dias de Uso**.

    Há três parâmetros para esse coorte:
    * **Atividades**, onde você escolhe quais eventos e exibições de página contam como “uso.”
    * **Período**, a definição de um mês.
    * **UsedAtleastCustom**, o número de vezes que os usuários precisam usar algo em um período para contar como envolvidos.

4. Altere **UsedAtleastCustom** para **5+ dias**, e deixe **Período** no padrão de 28 dias.

    ![Usuários envolvidos](./media/usage-cohorts/003.png)

    Agora, essa coorte representa todas as IDs de usuários enviadas com qualquer evento personalizado ou exibição de página em 5 dias separados nos últimos 28.

5. Clique em **Salvar**.

   > [!TIP]
   > Forneça um nome à coorte, como “Usuários Envolvidos (5 ou mais dias).” Salve-a em "Meus relatórios" ou "Relatórios compartilhados", dependendo se você deseja que outras pessoas que tenham acesso a esse recurso do Application Insights visualizem essa coorte.

6. Selecione **Volta à Galeria**.

### <a name="what-can-you-do-by-using-this-cohort"></a>O que é possível fazer usando essa coorte?

Abra a ferramenta Usuários. Na caixa suspensa **Mostrar**, escolha a coorte criada em **Usuários que pertencem a**.

Agora, a ferramenta Usuários é filtrada para essa coorte de usuários:

![Painel de usuários filtrado para uma determinada coorte](./media/usage-cohorts/004.png)

Alguns aspectos importantes a observar:

* Não é possível criar esse conjunto por meio de filtros normais. A lógica de data é mais avançada.
* Você pode filtrar ainda mais essa coorte, usando os filtros normais na ferramenta Usuários. Portanto, embora a coorte seja definida em janelas de 28 dias, você ainda poderá ajustar o intervalo de tempo na ferramenta Usuários em 30, 60 ou 90 dias.

Esses filtros dão suporte a perguntas mais sofisticadas impossíveis de serem expressas por meio do construtor de consultas. Um exemplo é _pessoas que estiveram envolvidas nos últimos 28 dias. Como essas mesmas pessoas comportaram-se nos últimos 60 dias?_

## <a name="example-events-cohort"></a>Exemplo: Coorte de eventos

Você também pode fazer coortes de eventos. Nesta seção, você definirá um coorte de eventos e as exibições de páginas. Em seguida, verá como utilizá-las a partir das outras ferramentas. Essa coorte pode definir um conjunto de eventos que a equipe considera _uso ativo_ ou um conjunto relacionado a um determinado novo recurso.

1. Abra a ferramenta Coortes.

2. Selecione a guia **Galeria de Modelos**. Uma coleção de modelos para várias coortes será exibida.

3. Selecione **Seletor de Eventos**.

    ![Captura de tela do seletor de eventos](./media/usage-cohorts/006.png)

4. Na caixa suspensa **Atividades**, selecione os eventos que você quer que estejam na coorte.

5. Salve a coorte e dê a ela um nome.

## <a name="example-active-users-where-you-modify-a-query"></a>Exemplo: Usuários ativos em que você modifica uma consulta

As duas coortes anteriores foram definidas usando caixas suspensas. Mas você também pode definir coortes usando consultas de análise para flexibilidade total. Para ver como, crie uma coorte de usuários do Reino Unido.

![Imagem animada mostrando o uso da ferramenta Coortes](./media/usage-cohorts/cohorts0001.gif)

1. Abra a ferramenta Coortes, selecione a guia **Galeria de Modelos** e selecione **Coorte de Usuários em branco**.

    ![Coorte de usuários em branco](./media/usage-cohorts/001.png)

    Há três seções:
   * Uma seção de texto Markdown, onde você descreve a coorte mais detalhadamente para outras pessoas da sua equipe.

   * Uma seção de parâmetros, onde você cria seus próprios parâmetros, como **Atividades** e outras caixas suspensas dos dois exemplos anteriores.

   * Uma seção de consulta, onde você define a coorte usando uma consulta de análise.

     Na seção de consulta, você [grava uma consulta de análise](/azure/kusto/query). A consulta seleciona o conjunto certo de linhas que descrevem a coorte que você deseja definir. A ferramenta Coortes, em seguida, adiciona implicitamente uma cláusula “| summarize by user_Id” à consulta. Esses dados são visualizados abaixo da consulta em uma tabela para garantir que sua consulta esteja retornando resultados.

     > [!NOTE]
     > Se você não vir a consulta, tente redimensionar a seção para torná-la mais alta e revelar a consulta. O .gif animado no começo desta seção ilustra o comportamento de redimensionamento.

2. Copie e cole o texto a seguir no editor de consultas:

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. Selecione **Executar Consulta**. Se as IDs de usuário não forem exibidas na tabela, altere para um país no qual o aplicativo tenha usuários.

4. Salve e nomeie a coorte.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

_Eu defini uma coorte de usuários de um determinado país. Quando comparo essa coorte na ferramenta Usuários apenas para definir um filtro nesse país, vejo resultados diferentes. Por quê?_

Coortes e filtros são diferentes. Suponha que você tenha uma coorte de usuários do Reino Unido (definidos como o exemplo anterior) e compare os resultados com a configuração do filtro “País ou região = Reino Unido.”

* A versão de coorte mostra todos os eventos de usuários que enviaram um ou mais eventos do Reino Unido no período atual. Se você dividir por país ou região, provavelmente verá muitos países e regiões.
* A versão dos filtros mostra apenas eventos do Reino Unido. Mas se você dividir por país ou região, verá apenas o Reino Unido.

## <a name="learn-more"></a>Saiba mais

* [Linguagem de consulta do Analytics](https://go.microsoft.com/fwlink/?linkid=856587)
* [Usuários, sessões, eventos](usage-segmentation.md)
* [Fluxos de usuários](usage-flows.md)
* [Visão geral do uso](usage-overview.md)