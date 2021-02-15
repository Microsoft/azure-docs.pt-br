---
title: Executar exemplos de MapReduce do Apache Hadoop no HDInsight - Azure
description: Comece a usar os exemplos de MapReduce em arquivos jar incluídos no HDInsight. Utilize SSH para se conectar ao cluster e use o comando do Hadoop para executar trabalhos de exemplo.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/12/2019
ms.openlocfilehash: 5e75a39b1f9e8503097914b0c9e735915f9ae667
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943209"
---
# <a name="run-the-mapreduce-examples-included-in-hdinsight"></a>Executar os exemplos de MapReduce incluídos no HDInsight

[!INCLUDE [samples-selector](../../../includes/hdinsight-run-samples-selector.md)]

Saiba como executar os exemplos de MapReduce incluídos com Apache Hadoop no HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Hadoop no HDInsight. Consulte [Introdução ao HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Um cliente SSH. Para saber mais, confira [Conectar-se ao HDInsight (Apache Hadoop) usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="the-mapreduce-examples"></a>Os exemplos do MapReduce

Os exemplos estão localizados no cluster HDInsight em `/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar`. O código-fonte para esses exemplos está incluído no cluster HDInsight em `/usr/hdp/current/hadoop-client/src/hadoop-mapreduce-project/hadoop-mapreduce-examples`.

Os exemplos a seguir estão contidos neste arquivo:

|Amostra |Descrição |
|---|---|
|aggregatewordcount|Conta as palavras nos arquivos de entrada.|
|aggregatewordhist|Computa o histograma das palavras nos arquivos de entrada.|
|BBP|Usa Bailey-Borwein-Plouffe para computar os dígitos exatos de PI.|
|dbcount|Conta os logs de Pageview armazenados em um banco de dados.|
|distbbp|Usa uma fórmula de tipo BBP para computar bits exatos de PI.|
|grep|Conta as correspondências de um Regex na entrada.|
|join|Executa uma junção sobre conjuntos de valores classificados e particionados igualmente.|
|multifilewc|Conta palavras de vários arquivos.|
|pentomino|Programa de layout de bloco para encontrar soluções para problemas de pentomino.|
|pi|Estima PI usando um método de Carlo quase monte.|
|randomtextwriter|Grava 10 GB de dados textuais aleatórios por nó.|
|randomwriter|Grava 10 GB de dados aleatórios por nó.|
|SecondarySort|Define uma classificação secundária para a fase de redução.|
|sort|Classifica os dados gravados pelo gravador aleatório.|
|Sudoku|Um solucionador de sudoku.|
|teragen|Gera dados para o terasort.|
|terasort|Executa o terasort.|
|teravalidate|Verificação dos resultados do terasort.|
|WordCount|Conta as palavras nos arquivos de entrada.|
|wordmean|Conta o comprimento médio das palavras nos arquivos de entrada.|
|wordmedian|Conta o comprimento mediano das palavras nos arquivos de entrada.|
|wordstandarddeviation|Conta o desvio padrão do comprimento das palavras nos arquivos de entrada.|

## <a name="run-the-wordcount-example"></a>Executar o exemplo de wordcount

1. Conecte-se ao HDInsight usando o SSH. Substitua `CLUSTER` pelo nome do cluster e, em seguida, insira o seguinte comando:

    ```cmd
    ssh sshuser@CLUSTER-ssh.azurehdinsight.net
    ```

2. Na sessão SSH, use o seguinte comando para listar os exemplos:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
    ```

    Esse comando gera a lista de exemplos da seção anterior deste documento.

3. Use o comando a seguir para obter ajuda com um exemplo específico. Neste caso, o exemplo é **wordcount** :

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
    ```

    Você receberá a seguinte mensagem:

    ```output
    Usage: wordcount <in> [<in>...] <out>
    ```

    Essa mensagem indica que você pode fornecer vários caminhos de entrada para os documentos de origem. O caminho final é o local em que a saída (contagem de palavras em documentos de origem) está armazenada.

4. Use o seguinte para contar todas as palavras nos blocos de anotações do Leonardo da Vinci, que são fornecidas como dados de exemplo com o cluster:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount
    ```

    A entrada para esse trabalho é lida pela entrada `/example/data/gutenberg/davinci.txt`. A saída desse exemplo é armazenada em `/example/data/davinciwordcount`. Ambos os caminhos estão localizados no armazenamento padrão do cluster, não no sistema de arquivos local.

   > [!NOTE]  
   > Conforme observado na ajuda do exemplo wordcount, você também pode especificar vários arquivos de entrada. Por exemplo, `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` contaria as palavras em davinci.txt e ulysses.txt.

5. Quando o trabalho for concluído, use o seguinte comando para exibir a saída:

    ```bash
    hdfs dfs -cat /example/data/davinciwordcount/*
    ```

    Esse comando concatena todos os arquivos de saída produzidos pelo trabalho. Ele exibe a saída para o console. A saída é semelhante ao texto a seguir:

    ```output
    zum     1
    zur     1
    zwanzig 1
    zweite  1
    ```

    Cada linha representa uma palavra e quantas vezes ela ocorreu nos dados de entrada.

## <a name="the-sudoku-example"></a>O exemplo Sudoku

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) é um quebra-cabeça lógico composto por nove grades de 3x3. Algumas células da grade têm números, enquanto outros estão em branco, e a meta é encontrar os números correspondentes às células em branco. O link anterior tem mais informações sobre o quebra-cabeça, mas o propósito dessa amostra é encontrar as respostas para as células em branco. Sendo assim, nossa entrada deve ser um arquivo no seguinte formato:

* Nove linhas de nove colunas
* Cada coluna pode conter um número ou `?` (que indica uma célula em branco)
* As células são separadas por um espaço

Há uma certa forma de construir o quebra-cabeças Sudoku que não permite repetir um número na mesma coluna ou linha. Há um exemplo no cluster do HDInsight que está construído corretamente. Ele está localizado em `/usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta` e contém o seguinte texto:

```output
8 5 ? 3 9 ? ? ? ?
? ? 2 ? ? ? ? ? ?
? ? 6 ? 1 ? ? ? 2
? ? 4 ? ? 3 ? 5 9
? ? 8 9 ? 1 4 ? ?
3 2 ? 4 ? ? 8 ? ?
9 ? ? ? 8 ? 5 ? ?
? ? ? ? ? ? 2 ? ?
? ? ? ? 4 5 ? 7 8
```

Para executar este problema de exemplo por meio do exemplo do Sudoku, use o seguinte comando:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta
```

Os resultados são semelhantes ao texto a seguir:

```output
8 5 1 3 9 2 6 4 7
4 3 2 6 7 8 1 9 5
7 9 6 5 1 4 3 8 2
6 1 4 8 2 3 7 5 9
5 7 8 9 6 1 4 2 3
3 2 9 4 5 7 8 1 6
9 4 7 2 8 6 5 3 1
1 8 5 7 3 9 2 6 4
2 6 3 1 4 5 9 7 8
```

## <a name="pi--example"></a>Exemplo de pi (π)

O exemplo do pi usa um método estatístico (quasi-Monte Carlo) para estimar o valor de pi. Pontos são colocados aleatoriamente em um quadrado de unidade. O quadrado também contém um círculo. A probabilidade de os pontos ficarem dentro do círculo é igual à área do círculo, PI/4. O valor de pi pode ser estimado do valor de 4R. R é a proporção do número de pontos que estão dentro do círculo em relação ao número total de pontos que estão dentro do quadrado. Quanto maior a amostra de pontos usados, melhor será a estimativa.

Use o seguinte comando para executar o exemplo. O comando usa 16 mapas com 10.000.000 amostras cada um para estimar o valor de pi:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000
```

O valor retornado por este comando deve ser semelhante a **3,14159155000000000000**. Para referência, as 10 primeiras casas decimais de pi são 3,1415926535.

## <a name="10-gb-graysort-example"></a>exemplo de GraySort de 10 GB

GraySort é um tipo de parâmetro de comparação. A métrica é a taxa de classificação (TB/minuto) que é obtida durante a classificação de grandes quantidades de dados, geralmente um mínimo de 100 TB.

Este exemplo usa uma quantidade modesta de 10 GB de dados para que possa ser executado de modo relativamente rápido. Ele usa os aplicativos de MapReduce desenvolvidos por Owen O'Malley e Arun Murthy. Esses aplicativos ganharam o parâmetro de comparação anual de classificação de terabytes de uso geral ("Daytona") em 2009, com uma taxa de 0,578 TB/min (100 TB em 173 minutos). Para obter mais informações sobre esse e outros parâmetros de comparação de classificação, consulte o site de [comparação de classificação](https://sortbenchmark.org/) .

Este exemplo usa três conjuntos de programas MapReduce:

* **TeraGen**: um programa de MapReduce que gera linhas de dados para classificar

* **TeraSort**: amostra os dados de entrada e usa o MapReduce para classificar os dados em uma ordem total

    A TeraSort é uma classificação de MapReduce padrão, exceto por um particionador personalizado. O particionador usa uma lista classificada de N-1 chaves amostradas que definem o intervalo de chave para cada redução. Em particular, todas as chaves dessa amostra[i-1] <= chave < amostra[i] são enviadas para reduzir i. Esse particionador garante que as saídas da redução i sejam todas menores do que a saída da redução i+1.

* **TeraValidate**: um programa de MapReduce que valida que a saída é classificada globalmente

    Ele cria um mapa por arquivo no diretório de saída, e cada mapa garante que cada chave seja menor ou igual à anterior. A função map gera registros das primeiras e últimas chaves de cada arquivo. A função de redução garante que a primeira chave do arquivo i seja maior do que a última chave do arquivo i-1. Todos os problemas são relatados como uma saída da fase de redução, com as chaves que estão fora de ordem.

Use as seguintes etapas para gerar dados, classificar e validar a saída:

1. Gere 10 GB de dados, que são armazenados no armazenamento padrão do cluster HDInsight em `/example/data/10GB-sort-input`:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
    ```

    O `-Dmapred.map.tasks` informa o Hadoop quantas tarefas de mapeamento serão usadas para este trabalho. Os dois parâmetros finais instruem o trabalho a criar 10 GB de dados e armazená-los em `/example/data/10GB-sort-input`.

2. Use o comando a seguir para classificar os dados:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
    ```

    O `-Dmapred.reduce.tasks` informa o Hadoop quantas tarefas de redução serão usadas para o trabalho. Os dois parâmetros finais são apenas os locais de entrada e saída dos dados.

3. Use o seguinte para validar os dados gerados pela classificação:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate
    ```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a executar os exemplos incluídos com os clusters do HDInsight baseados em Linux. Para obter tutoriais sobre como usar o Pig, o Hive e o MapReduce com o HDInsight, consulte os seguintes tópicos:

* [Usar Apache Hive com Apache Hadoop no HDInsight](hdinsight-use-hive.md)
* [Usar o MapReduce com o Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)
