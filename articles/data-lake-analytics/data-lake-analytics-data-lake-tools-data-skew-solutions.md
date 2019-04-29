---
title: Resolver problemas de distorção de dados usando as Ferramentas do Azure Data Lake para Visual Studio
description: Solucione problemas em possíveis soluções para problemas de distorção de dados usando as Ferramentas do Azure Data Lake para Visual Studio.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 12/16/2016
ms.openlocfilehash: af55c161944447f2e6e2245fbb920803779984ca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61399858"
---
# <a name="resolve-data-skew-problems-by-using-azure-data-lake-tools-for-visual-studio"></a>Resolver problemas de distorção de dados usando as Ferramentas do Azure Data Lake para Visual Studio

## <a name="what-is-data-skew"></a>O que é distorção de dados?

Resumidamente, distorção de dados é a super-representação de um valor. Imagine que você atribuiu 50 fiscais de impostos para auditar o imposto de renda, um examinador para cada estado dos EUA. Já que a população de Wyoming é pequena, o fiscal de lá tem pouco o que fazer. Na Califórnia, no entanto, o fiscal permanece muito ocupado devido à grande população do estado.
    ![Exemplo de problema de distorção de dados](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/data-skew-problem.png)

Em nosso cenário, os dados são distribuídos de modo desigual entre todos os fiscais de impostos, o que significa que alguns fiscais devem trabalhar mais do que outros. Em seu próprio trabalho, com frequência você enfrentar situações como o exemplo do fiscal de impostos fornecido aqui. Em termos mais técnicos, um vértice obtém muito mais dados do que seus colegas, uma situação que faz com que o vértice funcione mais do que os outros e isso eventualmente torna todo um trabalho mais lento. O que é pior, o trabalho pode falhar, pois vértices podem ter, por exemplo, uma limitação de 5 horas de tempo de execução e uma limitação de 6 GB de memória.

## <a name="resolving-data-skew-problems"></a>Resolvendo problemas de distorções de dados

As Ferramentas do Azure Data Lake para Visual Studio podem ajudar a detectar se o trabalho tem um problema de distorção de dados. Se algum problema existir, você poderá resolvê-lo experimentando as soluções nesta seção.

## <a name="solution-1-improve-table-partitioning"></a>Solução 1: Melhorar o particionamento de tabela

### <a name="option-1-filter-the-skewed-key-value-in-advance"></a>Opção 1: Filtrar o valor chave distorcido com antecedência

Se isso não afetar sua lógica de negócios, você poderá filtrar os valores mais frequentes com antecedência. Por exemplo, se houver muito 000-000-000 na coluna GUID, não convém agregar esse valor. Antes de você agregar, você pode escrever “WHERE GUID != “000-000-000”” para filtrar o valor de alta frequência.

### <a name="option-2-pick-a-different-partition-or-distribution-key"></a>Opção 2: Escolher uma chave de partição ou distribuição diferente

No exemplo anterior, se você desejar apenas verificar a carga de trabalho de auditoria de imposto em todo o país, você poderá melhorar a distribuição de dados selecionando o número de ID como sua chave. Às vezes, separar uma chave de partição/distribuição diferente pode distribuir os dados mais uniformemente, mas você precisa certificar-se de que essa escolha não afeta sua lógica de negócios. Por exemplo, para calcular a soma de imposto para cada estado, talvez você queira designar _Estado_ como a chave de partição. Se você continuar a ter esse problema, tente usar a Opção 3.

### <a name="option-3-add-more-partition-or-distribution-keys"></a>Opção 3: Adicionar mais chaves de partição ou distribuição

Em vez de usar apenas _Estado_ como uma chave de partição, você pode usar mais de uma chave para o particionamento. Por exemplo, considere adicionar _CEP_ como uma chave de partição adicional para reduzir os tamanhos das partições de dados e distribuir os dados mais uniformemente.

### <a name="option-4-use-round-robin-distribution"></a>Opção 4: Use a distribuição round robin

Se você não encontrar uma chave adequada para a partição e distribuição, poderá tentar usar a distribuição round robin. A distribuição round robin trata cada linha igualmente e as coloca aleatoriamente nos buckets correspondentes. Os dados são distribuídos uniformemente mas perdem as informações de localidade, um inconveniente que também pode reduzir o desempenho do trabalho para algumas operações. Além disso, se você estiver agregando para a chave distorcida mesmo assim, o problema de distorção de dados ainda permanecerá. Para saber mais sobre a distribuição round robin, consulte a seção distribuições de tabela U-SQL [CREATE TABLE (U-SQL): Criando uma tabela com esquema](/u-sql/ddl/tables/create/managed/create-table-u-sql-creating-a-table-with-schema#dis_sch).

## <a name="solution-2-improve-the-query-plan"></a>Solução 2: Melhorar o plano de consulta

### <a name="option-1-use-the-create-statistics-statement"></a>Opção 1: Use a instrução CREATE STATISTICS

O U-SQL fornece a instrução CREATE STATISTICS em tabelas. Essa instrução fornece mais informações sobre as características dos dados, assim como distribuição de valor, ao otimizador de consulta; essas informações são então armazenadas em uma tabela. Para a maioria das consultas, o otimizador de consulta já gera as estatísticas necessárias para um plano de consulta de alta qualidade. Ocasionalmente, talvez seja necessário melhorar o desempenho de consulta, criando estatísticas adicionais com CREATE STATISTICS ou modificando o design da consulta. Para obter mais informações, consulte a página [CREATE STATISTICS (U-SQL)](/u-sql/ddl/statistics/create-statistics).

Exemplo de código:

    CREATE STATISTICS IF NOT EXISTS stats_SampleTable_date ON SampleDB.dbo.SampleTable(date) WITH FULLSCAN;

>[!NOTE]
>Informações de estatísticas não são atualizadas automaticamente. Se você atualizar os dados em uma tabela sem recriar as estatísticas, o desempenho da consulta poderá cair.

### <a name="option-2-use-skewfactor"></a>Opção 2: Usar SKEWFACTOR

Se você deseja somar o imposto de cada estado, você deve usar GROUP BY estado, uma abordagem que não evita o problema de distorção de dados. No entanto, você pode fornecer uma dica de dados em sua consulta para identificar a distorção de dados nas chaves, de forma que o otimizador consiga preparar um plano de execução para você.

Normalmente, você pode definir o parâmetro como 0,5 e 1, sendo que 0,5 significa pouca distorção e 1 significa distorção pesada. Já que a dica afeta a otimização do plano de execução da instrução atual e todas as instruções subsequentes, certifique-se de adicionar a dica antes de uma agregação com possível distorção relacionada à chave.

    SKEWFACTOR (columns) = x

    Provides a hint that the given columns have a skew factor x from 0 (no skew) through 1 (very heavy skew).

Exemplo de código:

    //Add a SKEWFACTOR hint.
    @Impressions =
        SELECT * FROM
        searchDM.SML.PageView(@start, @end) AS PageView
        OPTION(SKEWFACTOR(Query)=0.5)
        ;

    //Query 1 for key: Query, ClientId
    @Sessions =
        SELECT
            ClientId,
            Query,
            SUM(PageClicks) AS Clicks
        FROM
            @Impressions
        GROUP BY
            Query, ClientId
        ;

    //Query 2 for Key: Query
    @Display =
        SELECT * FROM @Sessions
            INNER JOIN @Campaigns
                ON @Sessions.Query == @Campaigns.Query
        ;   

### <a name="option-3-use-rowcount"></a>Opção 3: Use o número de linhas  
Além de SKEWFACTOR, para casos específicos de junção de chaves com distorção, se você souber que o outro conjunto de linhas unido é pequeno, você poderá informar o otimizador adicionando uma dica ROWCOUNT na instrução U-SQL antes de JOIN. Dessa forma, o otimizador pode escolher uma estratégia de junção de difusão para ajudar a melhorar o desempenho. Lembre-se de que ROWCOUNT não resolve o problema de distorção de dados, mas ele pode oferecer alguma ajuda adicional.

    OPTION(ROWCOUNT = n)

    Identify a small row set before JOIN by providing an estimated integer row count.

Exemplo de código:

    //Unstructured (24-hour daily log impressions)
    @Huge   = EXTRACT ClientId int, ...
                FROM @"wasb://ads@wcentralus/2015/10/30/{*}.nif"
                ;

    //Small subset (that is, ForgetMe opt out)
    @Small  = SELECT * FROM @Huge
                WHERE Bing.ForgetMe(x,y,z)
                OPTION(ROWCOUNT=500)
                ;

    //Result (not enough information to determine simple broadcast JOIN)
    @Remove = SELECT * FROM Bing.Sessions
                INNER JOIN @Small ON Sessions.Client == @Small.Client
                ;

## <a name="solution-3-improve-the-user-defined-reducer-and-combiner"></a>Solução 3: Melhorar o combinador e o Redutor definido pelo usuário

Às vezes você pode gravar um operador definido pelo usuário para lidar com uma lógica de processo complicada e um redutor e um combinador bem escritos podem mitigar um problema de distorção de dados em alguns casos.

### <a name="option-1-use-a-recursive-reducer-if-possible"></a>Opção 1: Usar um redutor recursivo, se possível

Por padrão, um redutor definido pelo usuário será executado no modo não recursivo, o que significa que o trabalho de redução para uma chave será distribuído em um único vértice. Mas se os dados estiverem distorcidos, os grandes conjuntos de dados poderão ser processados em um único vértice e serem executados por um longo tempo.

Para melhorar o desempenho, você pode adicionar um atributo em seu código para definir a execução do redutor no modo recursivo. Desse modo, grandes conjuntos de dados podem ser distribuídos por vários vértices e executados em paralelo, o que acelera o seu trabalho.

Para alterar um redutor de não recursivo para recursivo, é preciso certificar-se de que o algoritmo seja associativo. Por exemplo, a soma é associativa e a mediana não. Além disso, você precisa garantir que a entrada e a saída do redutor mantenham o mesmo esquema.

Atributo do redutor recursivo:

    [SqlUserDefinedReducer(IsRecursive = true)]

Exemplo de código:

    [SqlUserDefinedReducer(IsRecursive = true)]
    public class TopNReducer : IReducer
    {
        public override IEnumerable<IRow>
            Reduce(IRowset input, IUpdatableRow output)
        {
            //Your reducer code goes here.
        }
    }

### <a name="option-2-use-row-level-combiner-mode-if-possible"></a>Opção 2: Usar o modo de combinador no nível de linha, se possível

Semelhante à dica ROWCOUNT para casos específicos de junção de chave distorcida, o modo combinador tenta distribuir grandes conjuntos de valores de chave distorcida em vários vértices, de forma que o trabalho possa ser executado simultaneamente. O modo combinador não pode resolver problemas de distorção de dados, mas pode auxiliar em casos de grandes conjuntos de valores de chaves distorcidas.

Por padrão, o modo combinador é Full, o que significa que os conjuntos de linhas direito e esquerdo não podem ser separados. Configurar o modo como Left/Right/Inner permite junção em nível de linha. O sistema separa os conjuntos de linhas correspondentes e os distribui em vários vértices que executam em paralelo. No entanto, antes de configurar o modo combinador, tome cuidado para garantir que os conjuntos de linhas correspondentes possam ser separados.

O exemplo a seguir mostra um conjunto de linhas à esquerda separado. Cada linha de saída depende de uma única linha de entrada da esquerda e, potencialmente, depende de todas as linhas da direita com o mesmo valor de chave. Se você definir o modo combinador como esquerda, o sistema separará o enorme conjunto de linhas à esquerda em conjuntos menores e os atribuirá a vários vértices.

![Ilustração do modo combinador](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/combiner-mode-illustration.png)

>[!NOTE]
>Se você definir o modo combinador errado, a combinação será menos eficiente e os resultados poderão ser incorretos.

Atributos do modo de combinador:

- [SqlUserDefinedCombiner(Mode=CombinerMode.Full)]: Every output row potentially depends on all the input rows from left and right with the same key value.

- SqlUserDefinedCombiner(Mode=CombinerMode.Left): Cada linha de saída depende de uma única linha de entrada da esquerda (e potencialmente de todas as linhas da direita com o mesmo valor de chave).

- qlUserDefinedCombiner(Mode=CombinerMode.Right): Cada linha de saída depende de uma única linha de entrada da direita (e potencialmente de todas as linhas da esquerda com o mesmo valor de chave).

- SqlUserDefinedCombiner(Mode=CombinerMode.Inner): Cada linha de saída depende de uma única linha de entrada da esquerda e direita com o mesmo valor.

Exemplo de código:

    [SqlUserDefinedCombiner(Mode = CombinerMode.Right)]
    public class WatsonDedupCombiner : ICombiner
    {
        public override IEnumerable<IRow>
            Combine(IRowset left, IRowset right, IUpdatableRow output)
        {
        //Your combiner code goes here.
        }
    }
