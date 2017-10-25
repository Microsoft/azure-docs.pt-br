---
title: Criar recursos para os dados em um cluster Hadoop usando as consultas do Hive | Microsoft Docs
description: Exemplos de consultas de Hive que geram recursos em dados armazenados em um cluster Hadoop do Azure HDInsight.
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: e8a94c71-979b-4707-b8fd-85b47d309a30
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: hangzh;bradsev
ms.openlocfilehash: 0c8c2ab8c7daceb13fd39d2a109148a40430d59a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-features-for-data-in-an-hadoop-cluster-using-hive-queries"></a>Criar recursos para os dados em um cluster Hadoop usando as consultas do Hive
Este documento mostra como criar recursos para os dados armazenados em um cluster Hadoop do Azure HDInsight usando consultas do Hive. Essas consultas de Hive usam UDFs (funções definidas pelo usuário) de Hive incorporadas, Os scripts para eles são fornecidos.

As operações necessárias para criar recursos podem ter uso intensivo de memória. O desempenho das consultas do Hive se torna mais crítico nesses casos e pode ser melhorado com o ajuste de determinados parâmetros. O ajuste desses parâmetros é abordado na seção final.

Alguns exemplos de consultas apresentados são específicos para cenários de [Dados de Corridas de Táxi em Nova York](http://chriswhong.com/open-data/foil_nyc_taxi/) e também são fornecidos no [repositório GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Essas consultas já tem o esquema de dados especificado e estão prontas para ser enviadas para execução. Na seção final, são discutidos os parâmetros que os usuários podem ajustar para que o desempenho das consultas do Hive possa ser melhorado.

[!INCLUDE [cap-create-features-data-selector](../../../includes/cap-create-features-selector.md)]

Este **menu** leva você até os tópicos que descrevem como criar recursos para dados em vários ambientes. Essa tarefa é uma etapa no [TDSP (Processo de Ciência de Dados de Equipe)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="prerequisites"></a>Pré-requisitos
Este artigo supõe que você:

* Criou uma conta de armazenamento do Azure. Se precisar de instruções, confira [Criar uma conta de Armazenamento do Azure](../../storage/common/storage-create-storage-account.md#create-a-storage-account)
* Provisionou um cluster do Hadoop personalizado com o serviço HDInsight.  Se precisar de instruções, consulte [Personalizar os clusters do Hadoop do Azure HDInsight para análise avançada](customize-hadoop-cluster.md).
* Os dados foram carregados para tabelas Hive em clusters do Hadoop do Azure HDInsight. Se não, siga as instruções de [Criar e carregar dados nas tabelas Hive](move-hive-tables.md) para carregar dados nas tabelas Hive primeiro.
* Habilitou o acesso remoto ao cluster. Se precisar de instruções, consulte [Acessar o nó principal do Cluster do Hadoop](customize-hadoop-cluster.md#headnode).

## <a name="hive-featureengineering"></a>Geração de recursos
Nesta seção, veja vários exemplos dos modos pelos quais os recursos podem ser gerados usando consultas de Hive. Depois de gerar recursos adicionais, você pode adicioná-los como colunas à tabela existente ou criar uma nova tabela com os recursos adicionais e a chave primária, que pode ser associada à tabela original. Estes são os exemplos apresentados:

1. [Geração de recursos baseada em frequência](#hive-frequencyfeature)
2. [Riscos de variáveis categóricas na classificação binária](#hive-riskfeature)
3. [Extrair recursos do campo Datetime](#hive-datefeatures)
4. [Extrair recursos de campo de texto](#hive-textfeatures)
5. [Calcular a distância entre coordenadas de GPS](#hive-gpsdistance)

### <a name="hive-frequencyfeature"></a>Geração de recursos baseada em frequência
Geralmente é útil calcular as frequências dos níveis de uma variável categórica ou as frequências de determinadas combinações de níveis de diversas variáveis categóricas. Os usuários podem usar o script a seguir para calcular tais frequências:

        select
            a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
        from
        (
            select
                <column_name1>,<column_name2>, count(*) as sub_count
            from <databasename>.<tablename> group by <column_name1>, <column_name2>
        )a
        order by frequency desc;


### <a name="hive-riskfeature"></a>Riscos de variáveis categóricas na classificação binária
Na classificação binária, é necessário converter variáveis categóricas não numéricas em recursos numéricos quando os modelos usados aceitam apenas recursos numéricos. Isso é feito substituindo cada nível não numéricos por um risco numérico. Nesta seção, mostraremos algumas consultas de Hive genéricas para calcular os valores de risco (probabilidade de log) de uma variável categórica.

        set smooth_param1=1;
        set smooth_param2=20;
        select
            <column_name1>,<column_name2>,
            ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
        from
            (
            select
                <column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
            from
                (
                select
                    <column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
                from <databasename>.<tablename>
                )a
            group by <column_name1>, <column_name2>
            )b

Neste exemplo, as variáveis `smooth_param1` e `smooth_param2` são definidas para suavizar os valores de risco calculados por meio dos dados. Riscos tem um intervalo entre -Inf e Inf. Riscos > 0 indica que a probabilidade de o destino ser igual a 1 é maior que 0,5.

Depois da tabela de risco ser calculada, os usuários podem atribuir valores de risco a uma tabela associando-a à tabela de risco. A consulta de associação de Hive foi indicada na seção anterior.

### <a name="hive-datefeatures"></a>Extrair recursos de campos Datetime
O Hive vem com um conjunto de UDFs para processar campos datetime. No Hive, o formato de datetime padrão é “aaaa-MM-dd 00:00:00” (“1970-01-01 12:21:32”, por exemplo). Nesta seção, vamos mostrar exemplos de como extrair o dia do mês e o mês de um campo datetime, bem como exemplos de conversão de uma cadeia de caracteres de datetime em um formato diferente do formato padrão para uma cadeia de caracteres de datetime no formato padrão.

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

Essa consulta de Hive pressupõe que o *&#60;campo datetime>* esteja no formato datetime padrão.

Se um campo datetime não estiver no formato padrão, é necessário primeiro converter o campo datetime em carimbo de data/hora de Unix e, em seguida, converter o carimbo de data/hora do Unix em uma cadeia de caracteres datetime no formato padrão. Quando o datetime estiver no formato padrão, os usuários poderão aplicar UDFs datetime incorporadas para extrair recursos.

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

Nesta consulta, se o *&#60;campo datetime>* tiver o padrão *03/26/2015 12:04:39*, o *'&#60;padrão do campo datetime >'* deve ser `'MM/dd/yyyy HH:mm:ss'`. Para testá-lo, os usuários podem executar

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

O *hivesampletable* nesta consulta vem pré-instalado com todos os clusters do Hadoop do Azure HDInsight por padrão quando os clusters são provisionados.

### <a name="hive-textfeatures"></a>Extrair recursos de campos de Texto
Quando a tabela Hive tem um campo de texto que contém uma cadeia de caracteres de palavras delimitada por espaços, a consulta a seguir extrai o comprimento da cadeia de caracteres e o número de palavras na cadeia de caracteres.

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

### <a name="hive-gpsdistance"></a>Calcular a distância entre conjuntos de coordenadas de GPS
A consulta fornecida nesta seção pode ser aplicada diretamente aos Dados de Viagens de Táxi em NYC. A finalidade dessa consulta é mostrar como aplicar funções matemáticas incorporadas no Hive para gerar recursos.

Os campos que são usados nesta consulta são coordenadas de GPS de locais de saída e chegada, chamadas *pickup\_longitude*, *pickup\_latitude*, *dropoff\_longitude* e *dropoff\_latitude*. As consultas que calculam a distância direta entre as coordenadas de saída e chegada são:

        set R=3959;
        set pi=radians(180);
        select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude,
            ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
            *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
            *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
            /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
            +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
            pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
        from nyctaxi.trip
        where pickup_longitude between -90 and 0
        and pickup_latitude between 30 and 90
        and dropoff_longitude between -90 and 0
        and dropoff_latitude between 30 and 90
        limit 10;

As equações matemáticas que calculam a distância entre duas coordenadas de GPS podem ser encontradas no site <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Scripts de Tipo Móvel</a>, as quais foram criadas por Peter Lapisu. No Javascript dele, a função `toRad()` é apenas *lat_or_lon*pi/180*, que converte graus em radianos. Aqui, *lat_or_lon* é a latitude ou a longitude. Como o Hive não fornece a função `atan2`, mas fornece a função `atan`, a função `atan2` é implementada pela função `atan` na consulta de Hive acima usando a definição fornecida na <a href="http://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipédia</a>.

![Criar espaço de trabalho](./media/create-features-hive/atan2new.png)

Uma lista completa de UDFs internas do Hive pode ser encontrada na seção **Funções Internas** no <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">wiki do Apache Hive</a>.  

## <a name="tuning"></a> Tópicos avançados: ajustar parâmetros de Hive para melhorar a velocidade de consulta
As configurações de parâmetro padrão do cluster de Hive talvez não sejam adequadas para as consultas de Hive e os dados que as consultas estão processando. Nesta seção, discutiremos alguns parâmetros que os usuários podem ajustar para melhorar o desempenho das consultas de Hive. Os usuários precisam adicionar as consultas de ajuste de parâmetro antes das consultas de processamento de dados.

1. **Espaço de heap de Java**: para consultas que envolvem ingressar grandes conjuntos de dados ou processar longos registros, um erro comum é **ficar sem espaço de heap**. Isso pode ser ajustado definindo os parâmetros *mapreduce.map.java.opts* e *mapreduce.task.io.sort.mb* com os valores desejados. Veja um exemplo:
   
        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;

    Esse parâmetro aloca memória de 4 GB para o espaço de heap de Java e também torna a classificação mais eficiente ao alocar mais memória para ela. É uma boa ideia explorar essas alocações se houver erros de falha de trabalho relacionados ao espaço de heap.

1. **Tamanho do bloco de DFS** : esse parâmetro define a menor unidade de dados armazenada pelo sistema de arquivos. Por exemplo, se o tamanho do bloco de DFS for 128 MB, todos os dados de tamanho menor ou até 128 MB serão armazenados em um único bloco, enquanto os dados maiores que 128 MB serão alocados em blocos extras. Escolher um tamanho de bloco muito pequeno causa grandes sobrecargas no Hadoop, pois o nó de nome precisa processar muitas solicitações a mais para localizar o bloco relevante em relação ao arquivo. Uma configuração recomendada ao trabalhar com gigabytes de dados (ou mais ainda) é:
   
        set dfs.block.size=128m;
2. **Otimizar a operação de ingresso no Hive** : embora operações de ingresso na estrutura de mapear/reduzir geralmente ocorram na fase de redução, algumas vezes, é possível obter enormes ganhos agendando associações na fase de mapeamento (também chamada de "mapjoins"). Para direcionar o Hive para fazer isso sempre que possível, podemos definir:
   
        set hive.auto.convert.join=true;
3. **Especificar o número de mapeadores de Hive** : embora o Hadoop permita ao usuário definir o número de redutores, o número de mapeadores não é normalmente definido pelo usuário. Um truque que permite certo grau de controle sobre esse número é escolher as variáveis do Hadoop, *mapred.min.split.size* e *mapred.max.split.size*, pois cada tarefa de mapeamento é determinada por:
   
        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
   
    Normalmente, o valor padrão de *mapred.min.split.size* é 0, o de *mapred.max.split.size* é **Long.MAX** e o de *dfs.block.size* é 64 MB. Como vemos, dado o tamanho dos dados, ajustar esses parâmetros ao “configurá-los” permite ajustar o número de mapeadores usado.
4. Algumas outras opções mais **avançadas** para otimizar o desempenho de Hive são mencionadas a seguir. Eles permitem definir a memória alocada para mapear e reduzir as tarefas e podem ser úteis no ajuste de desempenho. Tenha em mente que o *mapreduce.reduce.memory.mb* não pode ser maior que o tamanho da memória física de cada nó de trabalho no cluster do Hadoop.
   
        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;

