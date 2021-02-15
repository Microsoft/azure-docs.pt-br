---
title: Solucionar problemas com o cluster Apache Spark no Azure HDInsight
description: Saiba mais sobre problemas relacionados aos clusters Apache Spark no Azure HDInsight e como solucioná-los.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: c92b55d3ac7f4476b7b74d25b40150a74c6ea1cf
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930152"
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>Problemas conhecidos do cluster do Apache Spark no HDInsight

Este documento mantém o registro de todos os problemas conhecidos para a visualização pública do Spark no HDInsight.  

## <a name="apache-livy-leaks-interactive-session"></a>Apache Livy vaza sessão interativa
Quando o [Apache Livy](https://livy.incubator.apache.org/) é reiniciado (do [Apache Ambari](https://ambari.apache.org/) ou da reinicialização da máquina virtual headnode 0) com uma sessão interativa ainda ativa, uma sessão de trabalho interativa é perdida. Como resultado, novos trabalhos podem estar paralisados no estado Aceito.

**Atenuação**

Use o procedimento a seguir para contornar o problema:

1. SSH no nó principal. Para obter informações, consulte [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Execute o seguinte comando para encontrar as IDs de aplicativo dos trabalhos interativos iniciados por Livy.

   ```bash
   yarn application –list
   ```

    Os nomes de trabalho padrão serão Livy se os trabalhos foram iniciados com uma sessão interativa Livy sem nenhum nome explícito especificado. Para a sessão Livy iniciada pelo [Jupyter Notebook](https://jupyter.org/), o nome do trabalho começa com `remotesparkmagics_*` .

3. Execute o seguinte comando para eliminar esses trabalhos.

   ```bash
   yarn application –kill <Application ID>
   ```

A execução de novos trabalhos tem início.

## <a name="spark-history-server-not-started"></a>Servidor de histórico Spark não iniciado
O servidor de histórico Spark não é iniciado automaticamente depois de um cluster ser criado.  

**Atenuação**

Inicie manualmente o servidor de histórico do Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>Problema de permissão no diretório de log do Spark
O hdiuser obtém o seguinte erro ao enviar um trabalho usando o envio spark:

```
java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (Permission denied)
```

E nenhum driver log é gravado.

**Atenuação**

1. Adicione hdiuser ao grupo Hadoop.
2. Forneça 777 permissões em /var/log/spark após a criação do cluster.
3. Atualize o local do log spark usando o Ambari para ser um diretório com 777 permissões.  
4. Execute spark-submit como sudo.  

## <a name="spark-phoenix-connector-is-not-supported"></a>Não há suporte para o conector Spark Phoenix

Os clusters de HDInsight Spark não dão suporte para o conector Spark Phoenix.

**Atenuação**

Use o conector Spark-HBase em vez disso. Para obter instruções, confira [Como usar o conector Spark-HBase](https://web.archive.org/web/20190112153146/https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/).

## <a name="issues-related-to-jupyter-notebooks"></a>Problemas relacionados a blocos de anotações do Jupyter

A seguir estão alguns problemas conhecidos relacionados aos notebooks Jupyter.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Notebooks com caracteres não ASCII nos nomes de arquivos

Não use caracteres não ASCII em nomes de Jupyter Notebook. Se você tentar carregar um arquivo por meio da UI Jupyter, que tem um nome de arquivo não ASCII, ele falha sem qualquer mensagem de erro. Jupyter não permite que você carregue o arquivo, mas ele não gerará um erro visível.

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Erro ao carregar notebooks de tamanhos maiores

Você poderá ver um erro **`Error loading notebook`** ao carregar blocos de anotações que tenham tamanho maior.  

**Atenuação**

Se você visualizar esse erro, não significa que seus dados estão corrompidos ou foram perdidos.  Os notebooks ainda estão no disco, em `/var/lib/jupyter`, e você pode usar o SSH no cluster para acessá-los. Para obter informações, consulte [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Depois de se conectar ao cluster usando o SSH, é possível copiar os blocos de anotações do cluster para o computador local (usando SCP ou WinSCP) como um backup para impedir a perda de quaisquer dados importantes no notebook. Você pode aplicar SSH no túnel no nó de cabeçalho na porta 8001 para acessar o Jupyter sem passar pelo gateway.  A partir daí, você pode limpar a saída do bloco de anotações e salvá-lo novamente para minimizar o tamanho dele.

Para evitar que esse erro aconteça no futuro, você deve seguir algumas práticas recomendadas:

* É importante manter um tamanho pequeno de bloco de anotações. Qualquer saída de trabalho no Spark que é enviada de volta ao Jupyter é mantida no bloco de anotações.  Trata-se de uma prática recomendada com Jupyter em geral para evitar a execução `.collect()` em grandes RDD ou quadros de molduras; em vez disso, se você quiser inspecionar o conteúdo de um RDD, considere executar `.take()` ou `.sample()` para que a saída não fique muito grande.
* Além disso, quando você salvar um bloco de anotações, limpe todas as células de saída para reduzir o tamanho.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>A inicialização inicial do notebook demora mais do que o esperado

A primeira instrução de código no Jupyter Notebook usando a mágica do Spark pode levar mais de um minuto.  

**Explica**

Isso acontece quando a primeira célula de código é executada. Em segundo plano, isso inicia a configuração de sessão e os contextos de Spark, SQL e Hive são definidos. Depois desses contextos são definidos, a primeira instrução é executada e isso dá a impressão de que a instrução levou muito tempo para ser concluída.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Tempo limite de Jupyter Notebook ao criar a sessão

Quando o cluster Spark está sem recursos, os kernels Spark e PySpark no Jupyter Notebook atingirão o tempo limite ao tentar criar a sessão.

**Mitigações**

1. Libere alguns recursos do cluster Spark:

   * Pare outros notebooks do Spark no menu Fechar e Parar ou clicando em Desligar no gerenciador de notebook.
   * Interrompendo outros aplicativos Spark do YARN.

2. Reinicie o notebook que você está tentando iniciar. Recursos suficientes devem estar disponíveis para você criar uma sessão agora.

## <a name="see-also"></a>Veja também

* [Visão geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários

* [Apache Spark com BI: execute análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: use o Spark no HDInsight para analisar a temperatura do edifício usando dados de HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: use o Spark no HDInsight para prever os resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando o Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicativos

* [Criar um aplicativo autônomo usando Scala](apache-spark-create-standalone-application.md)
* [Execute trabalhos remotamente em um cluster do Apache Spark usando o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Use o Plug-in de Ferramentas do HDInsight para IntelliJ IDEA para criar e enviar aplicativos Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Use o Plugin do HDInsight Tools para o IntelliJ IDEA para depurar os aplicativos do Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Use os blocos de anotações do Apache Zeppelin com um cluster do Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para Jupyter Notebook no cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com notebooks Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos

* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](apache-spark-job-debugging.md)
