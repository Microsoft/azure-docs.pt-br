---
title: "Análise de desempenho de consultas para o Banco de dados SQL do Azure | Microsoft Docs"
description: O monitoramento do desempenho de consulta identifica as consultas que consumem mais CPU de um Banco de Dados SQL do Azure.
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: c2f580b2-3835-453f-89f5-140e02dd2ea7
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 07/05/2017
ms.author: sstein
ms.openlocfilehash: 1925d4ff8f5b16a0df56de987f8653cfd8441c52
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-sql-database-query-performance-insight"></a>Visão do desempenho de consulta de Banco de Dados SQL do Azure
Gerenciamento e ajuste do desempenho de bancos de dados relacionais são uma tarefa desafiadora que requer conhecimento significativo e investimento de tempo. A Análise de Desempenho de Consultas permite que você gaste menos tempo solucionando problemas de desempenho de banco de dados, fornecendo o seguinte:

* Mais informações sobre o consumo de recursos de bancos de dados (DTU). 
* As principais consultas por contagem de CPU/Duração/Execução, que potencialmente podem ser ajustadas para melhorar o desempenho.
* A capacidade de analisar os detalhes de uma consulta, exibir o texto e o histórico de utilização de recursos. 
* Anotações de ajuste de desempenho que mostram as ações executadas [Advisor do Banco de Dados SQL Azure](sql-database-advisor.md)  



## <a name="prerequisites"></a>Pré-requisitos
* A Análise de Desempenho de Consultas exige a execução do [Repositório de Consultas](https://msdn.microsoft.com/library/dn817826.aspx) em seu banco de dados. Se o Repositório de Consultas não estiver em execução, o portal solicitará que você o ative.

## <a name="permissions"></a>Permissões
As seguintes permissões de [controle de acesso baseado em função](../active-directory/role-based-access-control-what-is.md) são necessárias para usar a Visão do Desempenho de Consulta: 

* As permissões **Leitor**, **Proprietário**, **Colaborador**, **Colaborador do Banco de Dados SQL** ou **Colaborador do SQL Server** são necessárias para exibir as consultas e gráficos que consomem mais recursos. 
* As permissões **Proprietário**, **Colaborador**, **Colaborador do Banco de Dados SQL** ou **Colaborador do SQL Server** são necessárias para exibir o texto da consulta.

## <a name="using-query-performance-insight"></a>Usando a Visão de Desempenho de Consulta
A Visão do Desempenho de Consulta é fácil de usar:

* Abra o [portal do Azure](https://portal.azure.com/) e localize o banco de dados de localização que você deseja examinar. 
  * No menu do lado esquerdo, em suporte e solução de problemas, selecione "Análise de Desempenho de Consultas".
* Na primeira guia, examine a lista das consultas que consomem mais recursos.
* Escolha uma consulta individual para exibir seus detalhes.
* Abra o [Advisor do Banco de Dados do SQL Azure](sql-database-advisor.md) e verifique se há alguma recomendação disponível.
* Use os controles deslizantes ou os ícones de zoom para alterar o intervalo observado.
  
    ![painel de desempenho](./media/sql-database-query-performance/performance.png)

> [!NOTE]
> São necessárias duas horas de dados a serem capturados pelo Repositório de Consulta para fornecer as análises de desempenho de consultas no banco de dados SQL. Se o banco de dados não tem atividades ou o Armazenamento de Consulta não estava ativo durante um determinado período de tempo, os gráficos estarão vazios ao exibir o período de tempo. Você pode habilitar o Armazenamento de Consulta a qualquer momento se ele não estiver em execução.   
> 
> 

## <a name="review-top-cpu-consuming-queries"></a>Examinar as consultas que mais consomem CPU
No [portal](http://portal.azure.com) , faça o descrito a seguir:

1. Navegue até um banco de dados SQL e clique em **Todas as configurações** > **Suporte + Solução de problemas** > **Análise de desempenho de consultas**. 
   
    ![Análise de desempenho de consultas][1]
   
    A exibição das principais consultas é aberta e as consultas que consomem mais CPU são listadas.
2. Clique em torno do gráfico para obter detalhes.<br>A linha superior mostra a % de DTU geral do banco de dados, enquanto as barras mostram a % de CPU consumida pelas consultas selecionadas durante o intervalo escolhido (por exemplo, se **Semana passada** for escolhida, cada barra representará um dia).
   
    ![principais consultas][2]
   
    A grade inferior representa informações agregadas das consultas visíveis.
   
   * ID da consulta: identificador exclusivo da consulta no banco de dados.
   * CPU por consulta durante o intervalo observável (depende da função de agregação).
   * Duração por consulta (depende da função de agregação).
   * Número total de execuções para uma consulta específica.
     
     Marque ou desmarque as consultas individuais para incluir ou exclui-las do gráfico usando as caixas de seleção.
3. Se os dados se tornarem obsoletos, clique no botão **Atualizar** .
4. Você pode usar os controles deslizantes e os botões de zoom para alterar o intervalo de observação e investigar os picos: ![configurações](./media/sql-database-query-performance/zoom.png)
5. Opcionalmente, se você quiser uma exibição diferente, você pode selecionar a guia **Personalizar** e definir:
   
   * Métrica (CPU, duração, contagem de execução)
   * Intervalo de tempo (Últimas 24 horas, Última semana, Mês passado). 
   * Número de consultas.
   * Função de agregação.
     
     ![Configurações](./media/sql-database-query-performance/custom-tab.png)

## <a name="viewing-individual-query-details"></a>Exibindo detalhes de uma consulta individual
Para exibir detalhes da consulta:

1. Clique em qualquer consulta na lista de consultas principais.
   
    ![detalhes](./media/sql-database-query-performance/details.png)
2. A exibição de detalhes é aberta e o consumo de CPU/Duração/Contagem de execução de consultas é dividido ao longo do tempo.
3. Clique em torno do gráfico para obter detalhes.
   
   * O gráfico superior mostra a linha com a % de DTU do banco de dados geral e as barras são a % de CPU consumida pela consulta selecionada.
   * O segundo gráfico mostra a duração total pela consulta selecionada.
   * O gráfico na parte inferior mostra o número total de execuções pela consulta selecionada.
     
     ![detalhes da consulta][3]
4. Opcionalmente, use os controles deslizantes, os botões de zoom ou clique em **Configurações** para personalizar a exibição dos dados de consumo de CPU ou escolher um período de tempo diferente.

## <a name="review-top-queries-per-duration"></a>Analise as principais consultas por duração
Na atualização recente da Análise de Desempenho de Consultas, apresentamos duas novas métricas que podem ajudar a identificar possíveis afunilamentos: duração e contagem de execução.<br>

Consultas de longa execução tem o maior potencial para bloquear recursos por mais tempo, bloqueando outros usuários e limitando a escalabilidade. Elas também são as melhores candidatas para otimização.<br>

Para identificar consultas de longa execução:

1. Abra a guia **Personalizar** na Análise de Desempenho de Consultas do banco de dados selecionado
2. Altere as métricas para **duração**
3. Selecione o número de consultas e o intervalo de observação
4. Selecione a função de agregação
   
   * **Soma** adiciona todo o tempo de execução de consulta durante todo o intervalo de observação.
   * **Máximo** localiza consultas para as quais tempo de execução foi máximo no intervalo inteiro de observação.
   * **Média** encontra a média de tempo de execução de todas as execuções de consulta e mostra a você as primeiras entre essas médias. 
     
     ![duração da consulta][4]

## <a name="review-top-queries-per-execution-count"></a>Analise as principais consultas por contagem de execução
Um alto número de execuções pode não estar afetando o banco de dados propriamente dito e o uso de recursos pode ser baixo, mas o aplicativo pode ficar lento no geral.

Em alguns casos, a contagem de execução muito alta pode levar a um aumento das viagens de ida e volta na rede. Viagens de ida e afetam o desempenho de forma significativa. Elas estão sujeitas à latência de rede e à latência do servidor downstream. 

Por exemplo, muitos sites orientados a dados acessam bastante o banco de dados para cada solicitação do usuário. Apesar do pool de conexões ajudar, o maior tráfego de rede e a maior carga de processamento no servidor de banco de dados podem prejudicar o desempenho.  A orientação geral é manter a menor quantidade possível de viagens de ida e volta.

Para identificar consultas executadas com frequência, consultas ("tagarelas"):

1. Abra a guia **Personalizar** na Análise de Desempenho de Consultas do banco de dados selecionado
2. Altere as métricas para **contagem de execução**
3. Selecione o número de consultas e o intervalo de observação
   
    ![contagem de execução da consulta][5]

## <a name="understanding-performance-tuning-annotations"></a>Noções básicas sobre anotações de ajuste de desempenho
Ao explorar a carga de trabalho na Análise de Desempenho de Consultas, você pode notar ícones com uma linha vertical na parte superior do gráfico.<br>

Esses ícones são anotações; eles representam as ações que afetam o desempenho executadas pelo [Advisor do Banco de Dados SQL Azure](sql-database-advisor.md). Passando com o mouse sobre a anotação, você obtém informações básicas sobre a ação:

![anotação da consulta][6]

Se você quiser saber mais ou aplicar a recomendação do Advisor, clique no ícone. O ícone abrirá os detalhes da ação. Se for uma recomendação ativa, você pode aplicar imediatamente usando o comando.

![detalhes de anotação de consulta][7]

### <a name="multiple-annotations"></a>Várias anotações.
É possível, que por causa do nível de zoom, anotações que estão próximas umas às outras serão recolhidas em somente uma anotação. Isso será representado por um ícone especial, clicar nele irá abrirá uma nova folha onde a lista de anotações agrupadas será mostrada.
Correlacionar consultas e ações de ajuste de desempenho pode ajudar a compreender melhor a sua carga de trabalho. 

## <a name="optimizing-the-query-store-configuration-for-query-performance-insight"></a>Otimizando a configuração do Repositório de Consultas para Análise de Desempenho de Consultas
Durante o uso da Análise de Desempenho de Consultas, você poderá encontrar as seguintes mensagens do Repositório de Consultas:

* "O Repositório de Consulta não está corretamente configurado neste banco de dados. Clique aqui para saber mais."
* "O Repositório de Consulta não está corretamente configurado neste banco de dados. Clique aqui para alterar as configurações." 

Normalmente, essas mensagens aparecem quando o Repositório de Consultas não está apto a coletar novos dados. 

O primeiro caso ocorre quando o Repositório de Consultas está em estado somente leitura e os parâmetros são definidos de forma ideal. Você pode corrigir isso, aumentando o tamanho do Repositório de Consultas ou desmarcando o Repositório de Consultas.

![botão qds][8]

O segundo caso acontece quando o Repositório de Consultas está Desligado ou parâmetros não estão definidos de forma ideal. <br>Você pode alterar a política de Retenção e Captura e habilitar o Repositório de Consultas, executando os comandos a seguir ou diretamente do portal:

![botão qds][9]

### <a name="recommended-retention-and-capture-policy"></a>Política recomendada de retenção e captura
Há dois tipos de política de retenção:

* Baseada em tamanho — se definida para AUTOMÁTICA, ela limpará os dados automaticamente quando o tamanho máximo estiver perto de ser atingido.
* Baseada no tempo — por padrão, nós a definiremos para 30 dias, o que significa que, se o Repositório de Consultas ficar sem espaço, ele excluirá informações de consulta com mais de 30 dias

A política de captura pode ser definida para:

* **Todas**: captura todas as consultas.
* **Automática**: consultas pouco frequentes e consultas com duração de execução e compilação insignificantes são ignoradas. Os limites da duração de contagem de execução, compilação e tempo de execução são determinados internamente. Essa é a opção padrão.
* **Nenhuma** – o Repositório de Consultas interrompe a captura de novas consultas, porém, as estatísticas de tempo de execução para consultas já capturadas ainda são coletadas.

É recomendável definir todas as políticas para AUTOMÁTICA e a limpeza de políticas para 30 dias:

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);

Aumente o tamanho do Repositório de Consultas. Essa ação pode ser realizada conectando-se a um banco de dados e emitindo a seguinte consulta:

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);

Aplicar essas configurações eventualmente fará o Repositório de Consultas coletar novas consultas, no entanto, se você não quiser esperar, você pode limpar o Repositório de Consultas. 

> [!NOTE]
> A execução da consulta a seguir excluirá todas as informações atuais no Repositório de Consultas. 
> 
> 

    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;


## <a name="summary"></a>Resumo
A Visão do Desempenho de Consulta ajuda a entender o impacto de sua carga de trabalho de consulta e como ela se relaciona com o consumo de recursos do banco de dados. Com esse recurso, você saberá mais sobre as consultas que consomem mais recursos e identificará facilmente as que devem ser corrigidas antes que as mesmas se tornem um problema.

## <a name="next-steps"></a>Próximas etapas
Para obter recomendações adicionais sobre como aprimorar o desempenho do seu banco de dados SQL, clique em [Recomendações](sql-database-advisor.md) na folha **Análise de Desempenho de Consultas** .

![Performance Advisor](./media/sql-database-query-performance/ia.png)

<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png
[4]: ./media/sql-database-query-performance/top-duration.png
[5]: ./media/sql-database-query-performance/top-execution.png
[6]: ./media/sql-database-query-performance/annotation.png
[7]: ./media/sql-database-query-performance/annotation-details.png
[8]: ./media/sql-database-query-performance/qds-off.png
[9]: ./media/sql-database-query-performance/qds-button.png

