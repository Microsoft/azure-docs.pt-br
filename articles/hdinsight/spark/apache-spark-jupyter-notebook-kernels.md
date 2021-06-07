---
title: Kernels para Jupyter Notebook em clusters Spark no Azure HDInsight
description: Saiba mais sobre os kernels PySpark, PySpark3 e Spark para Jupyter Notebook disponíveis com clusters Spark no Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: ef2bc5e00779200e5447c8829a437824657a2227
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104865971"
---
# <a name="kernels-for-jupyter-notebook-on-apache-spark-clusters-in-azure-hdinsight"></a>Kernels para Jupyter Notebook em clusters Apache Spark no Azure HDInsight

Os clusters do HDInsight Spark fornecem kernels que você pode usar com o Jupyter Notebook no [Apache Spark](./apache-spark-overview.md) para testar seus aplicativos. Um kernel é um programa que é executado e que interpreta seu código. Os três kernels são:

- **PySpark** - para aplicativos escritos em Python2.
- **PySpark3** - para aplicativos escritos em Python3.
- **Spark** - para aplicativos escritos em Scala.

Neste artigo, você aprenderá como usar esses kernels e os benefícios de usá-los.

## <a name="prerequisites"></a>Pré-requisitos

Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>Criar um Jupyter Notebook no Spark HDInsight

1. No [portal do Azure](https://portal.azure.com/), selecione o cluster Spark.  Consulte [lista e mostrar clusters](../hdinsight-administer-use-portal-linux.md#showClusters) para obter instruções. A exibição **visão geral** é aberta.

2. No modo de exibição **visão geral** , na caixa **painéis do Cluster** , selecione **Jupyter Notebook**. Se você receber uma solicitação, insira as credenciais de administrador para o cluster.

    :::image type="content" source="./media/apache-spark-jupyter-notebook-kernels/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png " alt-text="Jupyter Notebook em Apache Spark" border="true":::
  
   > [!NOTE]  
   > Você também pode acessar o Jupyter Notebook no cluster Spark abrindo a seguinte URL em seu navegador. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Selecione **novo** e, em seguida, selecione **Pyspark**, **PySpark3** ou **Spark** para criar um bloco de anotações. Use o kernel Spark para aplicativos em Scala, o kernel PySpark para aplicativos em Python2 e o kernel PySpark3 para aplicativos em Python3.

    :::image type="content" source="./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png " alt-text="Kernels para Jupyter Notebook no Spark" border="true":::

4. Um notebook é aberto com o kernel selecionado.

## <a name="benefits-of-using-the-kernels"></a>Benefícios de usar os kernels

Aqui estão alguns benefícios de usar os novos kernels com Jupyter Notebook em clusters HDInsight Spark.

- **Contextos de predefinição**. Com os kernels  **PySpark**, **PySpark3** ou **Spark** , você não precisa definir os contextos Spark ou Hive explicitamente antes de começar a trabalhar com seus aplicativos. Esses contextos estão disponíveis por padrão. Esses contextos são:

  - **sc** - para o contexto do Spark
  - **sqlContext** : para o contexto Hive

    Portanto, você **não** precisa executar instruções como as seguintes para definir os contextos:

    ```sql
    sc = SparkContext('yarn-client')
    sqlContext = HiveContext(sc)
    ```

    Em vez disso, pode usar os contextos predefinidos diretamente em seu aplicativo.

- **A mágica da célula**. O kernel PySpark fornece algumas "mágicas" predefinidas, que são comandos especiais com os quais você pode chamar `%%` (por exemplo, `%%MAGIC` `<args>` ). O comando mágico deve ser a primeira palavra em uma célula do código e de permitir várias linhas de conteúdo. A palavra mágica deve ser a primeira palavra na célula. Adicionar algo antes da palavra mágica, até mesmo comentários, causa um erro.     Para saber mais sobre palavras mágicas, clique [aqui](https://ipython.readthedocs.org/en/stable/interactive/magics.html).

    A tabela a seguir lista as diferentes palavras mágicas disponíveis por meio dos kernels.

   | Mágica | Exemplo | Descrição |
   | --- | --- | --- |
   | ajuda |`%%help` |Gera uma tabela de todos os comandos mágicos disponíveis com exemplo e descrição |
   | informações |`%%info` |Envia informações de sessão para o ponto de extremidade Livy atual |
   | CONFIGURAR |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |Configura os parâmetros para a criação de uma sessão. O sinalizador Force ( `-f` ) será obrigatório se uma sessão já tiver sido criada, o que garante que a sessão seja descartada e recriada. Veja o [Corpo da Solicitação POST /sessions da Livy](https://github.com/cloudera/livy#request-body) para obter uma lista de parâmetros válidos. Os parâmetros devem ser passados como uma cadeia de caracteres JSON e devem estar na linha seguinte, logo após a mágica, conforme mostrado na coluna de exemplo. |
   | sql |`%%sql -o <variable name>`<br> `SHOW TABLES` |Executa uma consulta do Hive no dqlContext. Se o parâmetro `-o` for passado, o resultado da consulta será persistido no contexto %%local do Python como um dataframe do [Pandas](https://pandas.pydata.org/) . |
   | local |`%%local`<br>`a=1` |Todo o código em linhas posteriores é executado localmente. O código deve ser um código de Python2 válido, não importa qual kernel você está usando. Portanto, mesmo que você tenha selecionado os kernels **PySpark3** ou **Spark** ao criar o notebook, se você usar a `%%local` mágica em uma célula, essa célula deverá ter apenas um código Python2 válido. |
   | logs |`%%logs` |Gera os logs da sessão atual do Livy. |
   | excluir |`%%delete -f -s <session number>` |Exclui uma sessão específica do ponto de extremidade atual do Livy. Não é possível excluir a sessão que é iniciada para o próprio kernel. |
   | limpeza |`%%cleanup -f` |Exclui todas as sessões do ponto de extremidade atual do Livy, incluindo a sessão deste notebook. O sinalizador de força -f é obrigatório. |

   > [!NOTE]  
   > Além das mágicas adicionados pelo kernel PySpark, você também pode usar as [mágicas internas do IPython](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics), incluindo `%%sh`. Você pode usar a mágica `%%sh` para executar scripts e bloco de código no nó principal do cluster.

- **Visualização automática**. O kernel Pyspark visualiza automaticamente a saída das consultas Hive e SQL. Escolha entre vários tipos diferentes de visualização, incluindo Tabela, Pizza, Linha, Área, Barra.

## <a name="parameters-supported-with-the-sql-magic"></a>Parâmetros compatíveis com a mágica de %%sql

A palavra mágica `%%sql` é compatível com diversos parâmetros que podem ser usados para controlar o tipo de saída que você recebe ao executar consultas. A tabela a seguir lista as saídas.

| Parâmetro | Exemplo | Descrição |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |Use esse parâmetro para manter o resultado da consulta, no contexto Python %%local, como um dataframe [Pandas](https://pandas.pydata.org/) . O nome da variável dataframe é o nome da variável que você especificar. |
| -Q |`-q` |Use esse parâmetro para desativar as visualizações da célula. Se você não quiser autovisualizar o conteúdo de uma célula e apenas desejar capturá-la como um dataframe, use `-q -o <VARIABLE>` . Se desejar desativar as visualizações sem capturar os resultados (por exemplo, para executar uma consulta SQL, como uma instrução `CREATE TABLE`), use `-q` sem especificar um argumento `-o`. |
| -M |`-m <METHOD>` |Onde **METHOD** é **take** ou **sample** (o padrão é **take**). Se o método for **`take`** , o kernel selecionará os elementos da parte superior do conjunto de dados de resultado especificado por MAXROWS (descrito mais adiante nesta tabela). Se o método for **sample**, o kernel experimentará aleatoriamente os elementos do conjunto de dados segundo o parâmetro `-r`, descrito a seguir nesta tabela. |
| -r |`-r <FRACTION>` |Aqui **FRACTION** é um número de ponto flutuante entre 0.0 e 1.0. Se o método de amostragem para a consulta SQL for `sample`, o kernel experimentará aleatoriamente a fração especificada dos elementos do conjunto de resultados. Por exemplo, se você executar uma consulta SQL com os argumentos `-m sample -r 0.01`, 1% das linhas resultantes serão amostradas aleatoriamente. |
| -n |`-n <MAXROWS>` |**MAXROWS** é um valor inteiro. O kernel limita o número de linhas de saída para **MAXROWS**. Se **MAXROWS** for um número negativo, como **-1**, o número de linhas no conjunto de resultados não será limitado. |

**Exemplo:**

```sql
%%sql -q -m sample -r 0.1 -n 500 -o query2
SELECT * FROM hivesampletable
```

A instrução acima faz as seguintes ações:

- Seleciona todos os registros de **hivesampletable**.
- Como usamos-q, ela desativa a visualização autovisual.
- Como usamos `-m sample -r 0.1 -n 500` , ele obtém aleatoriamente 10% das linhas no hivesampletable e limita o tamanho do conjunto de resultados a 500 linhas.
- Por fim, como usamos `-o query2` , ele também salva a saída em um dataframe chamado **query2**.

## <a name="considerations-while-using-the-new-kernels"></a>Considerações ao usar os novos kernels

Seja qual for o kernel usado, deixar os notebooks em execução consumirá os recursos de cluster.  Com esses kernels, como os contextos são predefinidos, simplesmente sair dos Notebooks não interrompe o contexto. E os recursos de cluster continuam em uso. Uma prática recomendada é usar a opção **fechar e parar** no menu **arquivo** do bloco de anotações quando terminar de usar o bloco de anotações. O fechamento interrompe o contexto e, em seguida, sai do bloco de anotações.

## <a name="where-are-the-notebooks-stored"></a>Onde os blocos de anotações são armazenados?

Se o cluster usar o armazenamento do Azure como a conta de armazenamento padrão, os blocos de anotações do Jupyter serão salvos na conta de armazenamento na pasta **/HdiNotebooks** .  Os notebooks, arquivos de texto e pastas que você cria no Jupyter podem ser acessados na conta de armazenamento.  Por exemplo, se você usar Jupyter para criar uma pasta **`myfolder`** e um bloco de anotações **MyFolder/mynotebook. ipynb**, poderá acessar esse notebook em `/HdiNotebooks/myfolder/mynotebook.ipynb` dentro da conta de armazenamento.  O inverso também é possível, ou seja, se você carregar um notebook diretamente em sua conta de armazenamento em `/HdiNotebooks/mynotebook1.ipynb`, ele também ficará visível no Jupyter.  Os logs são mantidos na conta de armazenamento mesmo após a exclusão do cluster.

> [!NOTE]  
> Os clusters HDInsight com o Azure Data Lake Storage como armazenamento padrão não armazenam notebooks no armazenamento associado.

A forma como os blocos de anotações são salvos na conta de armazenamento é compatível com [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html). Se você estiver usando o SSH no cluster, poderá usar os comandos de gerenciamento de arquivos:

| Comando | Descrição |
|---------|-------------|
| `hdfs dfs -ls /HdiNotebooks` | # Liste tudo no diretório raiz – tudo neste diretório está visível para Jupyter da home page |
| `hdfs dfs –copyToLocal /HdiNotebooks` | # Baixar o conteúdo da pasta HdiNotebooks|
| `hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks` | # Carregar um bloco de anotações example. ipynb na pasta raiz para que fique visível em Jupyter |

Se o cluster usa o armazenamento do Azure ou Azure Data Lake Storage como a conta de armazenamento padrão, os notebooks também são salvos no cluster cabeçalho em `/var/lib/jupyter` .

## <a name="supported-browser"></a>Navegador com suporte

Os notebooks Jupyter em clusters HDInsight Spark têm suporte apenas no Google Chrome.

## <a name="suggestions"></a>Sugestões

Os kernels novos estão evoluindo e amadurecerão com o tempo. Portanto, as APIs podem mudar à medida que esses kernels amadureceram. Agradecemos o envio quaisquer comentários que você tenha ao usar esses novos kernels. Os comentários são úteis para formatar a versão final desses kernels. Você pode deixar comentários/comentários na seção de **comentários** na parte inferior deste artigo.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)
- [Use os blocos de anotações do Apache Zeppelin com um cluster do Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
- [Usar pacotes externos com notebooks Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
- [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)
