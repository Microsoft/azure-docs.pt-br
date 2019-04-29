---
title: Instalar e usar o Giraph no HDInsight do Azure
description: Saiba como instalar o Giraph em clusters HDInsight usando ações de script. Você pode usar o Giraph para fazer o gráfico de processamento no Apache Hadoop na nuvem do Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: aa13d8dfc65f020f3f27183423913933cd0b9404
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61395345"
---
# <a name="install-apache-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>Instalar o Apache Giraph nos clusters Hadoop do HDInsight e usar o Giraph para processar grafos em grande escala

Saiba como instalar o Apache Giraph em um cluster HDInsight. O recurso de ação de script do HDInsight permite que você personalize o cluster executando um script de bash. Scripts podem ser usados para personalizar os clusters durante e após a criação do cluster.

## <a name="whatis"></a>O que é Giraph

O [Apache Giraph](https://giraph.apache.org/) permite executar processamento de grafos usando o Hadoop e pode ser usado com o Azure HDInsight. Grafos moldam as relações entre objetos. Por exemplo, as conexões entre roteadores em uma rede grande como a Internet ou relações entre pessoas em redes sociais. O processamento de grafos permite que você faça a análise das relações entre objetos em um grafo, como:

* Identificar possíveis amigos com base em suas relações atuais.

* Identificar a menor rota entre dois computadores em uma rede.

* Calcular a ordem de classificação de página da Web.

> [!WARNING]  
> Há suporte total a componentes fornecidos com o cluster do HDInsight – o Suporte da Microsoft ajudará a isolar e resolver problemas relacionados a esses componentes.
>
> Componentes personalizados, como o Giraph, recebem suporte comercialmente razoável para ajudá-lo a solucionar o problema. O Suporte da Microsoft pode ser capaz de resolver o problema. Caso contrário, você deve consultar comunidades de software livre em que é possível encontrar conhecimento aprofundado sobre essa tecnologia. Por exemplo, existem muitos sites da comunidade que podem ser usados, como: [Fórum do MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Além disso, os projetos Apache têm sites de projeto em [https://apache.org](https://apache.org), por exemplo: [Hadoop](https://hadoop.apache.org/).


## <a name="what-the-script-does"></a>O que o script faz

O script executa as ações a seguir:

* Instala o Giraph no `/usr/hdp/current/giraph`

* Copia o arquivo `giraph-examples.jar` para o armazenamento padrão (WASB) para o seu cluster: `/example/jars/giraph-examples.jar`

## <a name="install"></a>Instalar o Giraph usando Ações de Script

Um script de exemplo para instalar o Giraph em um cluster HDInsight está disponível na seguinte localização:

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

Esta seção fornece instruções sobre como usar o exemplo de script durante a criação do cluster usando o Portal do Azure.

> [!NOTE]  
> As ações de script podem ser aplicadas por meio dos seguintes métodos:
> * Azure PowerShell
> * A CLI do Azure
> * O SDK .NET do HDInsight
> * Modelos do Azure Resource Manager
> 
> Também é possível aplicar ações de script a clusters que já estão em execução. Para saber mais, veja [Personalizar clusters HDInsight com as Ações de Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Inicie a criação de um cluster usando as etapas em [Criar clusters de HDInsight baseados em Linux](hdinsight-hadoop-create-linux-clusters-portal.md), mas não conclua a criação.

2. Na seção **Configuração Opcional**, escolha **Ações de Script** e forneça as seguintes informações:

   * **NOME**: Insira um nome amigável para a ação de script.

   * **URI DO SCRIPT**: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

   * **HEAD**: Marque esta entrada.

   * **TRABALHADOR**: Deixe esta entrada desmarcada.

   * **ZOOKEEPER**: Deixe esta entrada desmarcada.

   * **PARÂMETROS**: Deixe este campo em branco.

3. Na parte inferior das **Ações de Script**, use o botão **Selecionar** para salvar a configuração. Por fim, use o botão **Selecionar** na parte inferior da seção **Configuração Opcional** para salvar as informações de configuração opcional.

4. Continue a criação do cluster conforme descrito em [Criar clusters HDInsight baseados em Linux](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usegiraph"></a>Como usar o Giraph no HDInsight?

Quando o cluster tiver sido criado, use as etapas a seguir para executar o exemplo SimpleShortestPathsComputation incluído com o Giraph. Este exemplo usa a implementação do [Pregel](https://people.apache.org/~edwardyoon/documents/pregel.pdf) básico para encontrar o caminho mais curto entre objetos em um grafo.

1. Conecte-se ao cluster HDInsight usando SSH:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Para obter informações, consulte [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Use o comando a seguir para criar um novo arquivo chamado **tiny_graph.txt**:

    ```bash
    nano tiny_graph.txt
    ```

    Use o seguinte texto como o conteúdo deste arquivo:

    ```text
    [0,0,[[1,1],[3,3]]]
    [1,0,[[0,1],[2,2],[3,1]]]
    [2,0,[[1,2],[4,4]]]
    [3,0,[[0,3],[1,1],[4,4]]]
    [4,0,[[3,4],[2,4]]]
    ```

    Esses dados descrevem uma relação entre objetos em um grafo direcionado, usando o formato `[source_id, source_value,[[dest_id], [edge_value],...]]`. Cada linha representa uma relação entre um objeto `source_id` e um ou mais objetos `dest_id`. O `edge_value` pode ser considerado a força ou a distância da conexão entre `source_id` e `dest\_id`.

    Desenhados e utilizando o valor (ou peso) como distância entre os objetos, os dados acima podem se parecer com o diagrama a seguir:

    ![tiny_graph.txt Desenhado como círculos com linhas de distância variável entre](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph.png)

3. Para salvar o arquivo, use **Ctrl + X**, em seguida, **Y** e, finalmente, **Enter** para aceitar o nome de arquivo.

4. Use o seguinte para armazenar os dados no armazenamento primário para o cluster HDInsight:

    ```bash
    hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt
    ```

5. Execute o exemplo SimpleShortestPathsComputation usando o comando a seguir:

    ```bash
    yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2
    ```

    Os parâmetros usados com este comando são descritos na tabela a seguir:

   | Parâmetro | O que faz |
   | --- | --- |
   | `jar` |O arquivo jar que contém os exemplos. |
   | `org.apache.giraph.GiraphRunner` |A classe usada para iniciar os exemplos. |
   | `org.apache.giraph.examples.SimpleShortestPathsCoputation` |O exemplo usado. Nesse exemplo, ele calcula o caminho mais curto entre a ID 1 e todas as outras IDs no grafo. |
   | `-ca mapred.job.tracker` |O nó de cabeçalho do cluster. |
   | `-vif` |O formato de entrada a ser usado para os dados de entrada. |
   | `-vip` |O arquivo de dados de entrada. |
   | `-vof` |O formato de saída. Nesse exemplo, a ID e o valor como texto sem formatação. |
   | `-op` |O local de saída. |
   | `-w 2` |O número de trabalhos a usar. Neste exemplo, 2. |

    Para obter mais informações sobre esses e outros parâmetros usados com exemplos do Giraph, consulte o [Guia de início rápido do Giraph](https://giraph.apache.org/quick_start.html).

6. Depois que o trabalho foi concluído, os resultados são armazenados na **/example/out/shortestpaths** directory. Os nomes de arquivo saída começam com **part-m-** e terminam com um número indicando o primeiro, segundo etc. arquivo. Use o comando a seguir para exibir a saída:

    ```bash
    hdfs dfs -text /example/output/shortestpaths/*
    ```

    A saída é semelhante ao texto a seguir:

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    O exemplo SimpleShortestPathComputation está codificado para começar com a ID do objeto 1 e localizar o caminho mais curto para os outros objetos. A saída está no formato de `destination_id` e `distance`. `distance` é o valor (ou peso) das bordas percorridas entre a ID do objeto 1 e a ID de destino.

    Visualizando esses dados, você pode verificar os resultados percorrendo os caminhos mais curtos entre a ID 1 e todos os outros objetos. Observe que o caminho mais curto entre a ID 1 e a ID 4 é 5. Esse valor é a distância total entre <span style="color:orange">ID 1 e 3</span> e, em seguida, entre <span style="color:red">ID 3 e 4</span>.

    ![Desenho dos objetos como círculos com os percursos mais curtos entre](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph-out.png)

## <a name="next-steps"></a>Próximas etapas

* [Instalar e usar matiz em clusters HDInsight](hdinsight-hadoop-hue-linux.md).
