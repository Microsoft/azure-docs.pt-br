---
title: Gerenciar dependências do aplicativo Spark no Azure HDInsight
description: Este artigo fornece uma introdução de como gerenciar as dependências do Spark no cluster HDInsight Spark para aplicativos PySpark e escalares.
author: yanancai
ms.author: yanacai
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: f0673523c74a0ea298e7d2d520952c3e98877e91
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930060"
---
# <a name="manage-spark-application-dependencies"></a>Gerenciar dependências do aplicativo Spark

Neste artigo, você aprenderá a gerenciar dependências para seus aplicativos Spark em execução no HDInsight. Abordamos escalares e PySpark no aplicativo Spark e no escopo do cluster.

Use links rápidos para ir para a seção com base no seu caso de usuário:
* [Configurar dependências do jar do trabalho do Spark usando Jupyter Notebook](#use-jupyter-notebook)
* [Configurar dependências do jar do trabalho do Spark usando usar Azure Toolkit for IntelliJ](#use-azure-toolkit-for-intellij)
* [Configurar dependências do jar para o cluster Spark](#jar-libs-for-cluster)
* [Gerenciar dependências do JAR com segurança](#safely-manage-jar-dependencies)
* [Configurar pacotes python de trabalho do Spark usando Jupyter Notebook](#use-jupyter-notebook-1)
* [Gerenciar com segurança pacotes do Python para o cluster Spark](#python-packages-for-cluster)

## <a name="jar-libs-for-one-spark-job"></a>Bibliotecas jar para um trabalho do Spark
### <a name="use-jupyter-notebook"></a>Usar Jupyter Notebook
Quando uma sessão do Spark é iniciada em Jupyter Notebook no kernel do Spark para escalabilidade, você pode configurar pacotes de:

* [Repositório Maven](https://search.maven.org/)ou pacotes contribuídos pela Comunidade em [pacotes do Spark](https://spark-packages.org/).
* Arquivos jar armazenados no armazenamento primário do cluster.

Você usará a `%%configure` mágica para configurar o bloco de anotações para usar um pacote externo. Em blocos de notas que usam pacotes externos, não deixe de chamar a mágica `%%configure` na primeira célula de código. Isso garante que o kernel está configurado para usar o pacote antes de iniciar a sessão.

>
>[!IMPORTANT]  
>Se esquecer de configurar o kernel na primeira célula, você poderá usar `%%configure` com o parâmetro `-f`, mas isso reiniciará a sessão e todo o progresso será perdido.

**Exemplo de pacotes do repositório do Maven ou pacotes do Spark**

Depois de localizar o pacote do repositório do Maven, reúna os valores para **GroupId**, **artefatoid** e **versão**. Concatene os três valores, separados por dois pontos (**:**).

   ![Concatenar esquema de pacote](./media/apache-spark-manage-dependencies/spark-package-schema.png "Concatenar esquema de pacote")

Verifique se os valores coletados correspondem ao cluster. Nesse caso, estamos usando o pacote do conector do Spark Cosmos DB para escala 2,11 e o Spark 2,3 para cluster HDInsight 3,6 Spark. Se você não tiver certeza, execute `scala.util.Properties.versionString` na célula de código no kernel do Spark para obter a versão escalar do cluster. Execute `sc.version` para obter a versão do Spark do cluster.

```
%%configure { "conf": {"spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.3.0_2.11:1.3.3" }}
```

**Exemplo de JARs armazenados no armazenamento primário**

Use o [esquema de URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) para arquivos jar em seu armazenamento primário de clusters. Isso seria `wasb://` para o Armazenamento do Azure, `abfs://` para o Azure Data Lake Storage Gen2 ou `adl://` para o Azure Data Lake Storage Gen1. Se a transferência segura estiver habilitada para o armazenamento do Azure ou Data Lake Storage Gen2, o URI será `wasbs://` ou `abfss://` . Consulte [transferência segura](../../storage/common/storage-require-secure-transfer.md).

Use a lista separada por vírgulas de caminhos jar para vários arquivos jar, globs são permitidos. Os jars estão incluídos nos classpaths do driver e do executor.

```
%%configure { "conf": {"spark.jars": "wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/azure-cosmosdb-spark_2.3.0_2.11-1.3.3.jar" }}
```

Depois de configurar pacotes externos, você pode executar a importação na célula de código para verificar se os pacotes foram colocados corretamente.

```scala
import com.microsoft.azure.cosmosdb.spark._
```

### <a name="use-azure-toolkit-for-intellij"></a>Usar Azure Toolkit for IntelliJ
[Azure Toolkit for IntelliJ plug-in](./apache-spark-intellij-tool-plugin.md) fornece a experiência de interface do usuário para enviar um aplicativo de escala do Spark para um cluster HDInsight. Ele fornece `Referenced Jars` `Referenced Files` Propriedades e para configurar caminhos de bibliotecas jar ao enviar o aplicativo Spark. Veja mais detalhes sobre [como usar Azure Toolkit for IntelliJ plug-in para o HDInsight](./apache-spark-intellij-tool-plugin.md#run-a-spark-scala-application-on-an-hdinsight-spark-cluster).

![A caixa de diálogo Envio do Spark](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png)

## <a name="jar-libs-for-cluster"></a>Bibliotecas jar para cluster
Em alguns casos, talvez você queira configurar as dependências do JAR no nível do cluster para que cada aplicativo possa ser configurado com as mesmas dependências por padrão. A abordagem é adicionar seus caminhos jar ao driver do Spark e ao caminho da classe do executor.

1. Execute as ações de script de exemplo a seguir para copiar arquivos JAR do armazenamento primário `wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*` para o sistema de arquivos local do cluster `/usr/libs/sparklibs` . A etapa é necessária, pois o Linux usa `:` para separar a lista de caminhos de classe, mas o HDInsight dá suporte apenas a caminhos de armazenamento com esquema como `wasb://` . O caminho de armazenamento remoto não funcionará corretamente se você adicioná-lo diretamente ao caminho de classe.

    ```bash
    sudo mkdir -p /usr/libs/sparklibs
    sudo hadoop fs -copyToLocal wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*.* /usr/libs/sparklibs
    ```

2. Altere a configuração do serviço Spark de Ambari para atualizar o caminho da classe. Vá para **Ambari > > configurações do Spark > padrões de Spark2 personalizados**. **Adicione a propriedade** da seguinte maneira. Use `:` para separar caminhos se você tiver mais de um caminho para adicionar. Globs são permitidos.

    ```
    spark.driver.extraClassPath=/usr/libs/sparklibs/*
    spark.executor.extraClassPath=/usr/libs/sparklibs/*
    ```

   ![Alterar a configuração padrão do Spark](./media/apache-spark-manage-dependencies/change-spark-default-config.png "Alterar a configuração padrão do Spark")

3. Salve as configurações alteradas e reinicie os serviços afetados.

   ![Reiniciar serviços afetados](./media/apache-spark-manage-dependencies/restart-impacted-services.png "Reiniciar serviços afetados")

Você pode automatizar as etapas usando [ações de script](../hdinsight-hadoop-customize-cluster-linux.md). A ação de script para [Adicionar bibliotecas personalizadas do hive](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh) é uma boa referência. Ao alterar as configurações do serviço Spark, certifique-se de usar APIs Ambari em vez de modificar os arquivos de configuração diretamente. 

## <a name="safely-manage-jar-dependencies"></a>Gerenciar dependências do JAR com segurança
O cluster HDInsight tem dependências de jar internas, e as atualizações para essas versões de jar acontecem de tempos em tempos. Para evitar o conflito de versão entre os jars internos e os jars que você leva para referência, considere [o sombreamento das dependências do aplicativo](./safely-manage-jar-dependency.md).

## <a name="python-packages-for-one-spark-job"></a>Pacotes do Python para um trabalho do Spark
### <a name="use-jupyter-notebook"></a>Usar Jupyter Notebook
O HDInsight Jupyter Notebook kernel PySpark não dá suporte à instalação de pacotes python do repositório de pacotes do PyPi ou do Anaconda diretamente. Se você tiver `.zip` , `.egg` ou `.py` dependências, e quiser referenciá-las para uma sessão do Spark, siga as etapas abaixo:

1. Execute as ações de script de exemplo a serem copiadas `.zip` , `.egg` ou `.py` arquivos do armazenamento primário `wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*` para o sistema de arquivos local do cluster `/usr/libs/pylibs` . A etapa é necessária, pois o Linux usa `:` para separar a lista de caminhos de pesquisa, mas o HDInsight dá suporte apenas a caminhos de armazenamento com esquema como `wasb://` . O caminho de armazenamento remoto não funcionará corretamente quando você usar o `sys.path.insert` .

    ```bash
    sudo mkdir -p /usr/libs/pylibs
    sudo hadoop fs -copyToLocal wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*.* /usr/libs/pylibs
    ```

2. No bloco de anotações, execute o código abaixo em uma célula de código com o kernel PySpark:

   ```python
   import sys
   sys.path.insert(0, "/usr/libs/pylibs/pypackage.zip")
   ```

3. Execute `import` para verificar se os pacotes foram incluídos com êxito.  

## <a name="python-packages-for-cluster"></a>Pacotes do Python para cluster
Você pode instalar pacotes do Python de Anaconda para o cluster usando o comando Conda por meio de ações de script. Os pacotes instalados estão no nível do cluster e se aplicam a todos os aplicativos. 

O cluster HDInsight Spark tem duas instalações internas do Python, Anaconda Python 2,7 e Anaconda Python 3,5. Para saber mais sobre as configurações padrão do Python para serviços e como instalar com segurança os pacotes do Python externos sem interromper o cluster, veja mais detalhes em [gerenciar com segurança as dependências do Python para seu cluster](./apache-spark-python-package-installation.md).
