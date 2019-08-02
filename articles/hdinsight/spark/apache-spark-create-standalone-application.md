---
title: 'Tutorial: Criar um aplicativo Scala Maven para Spark no Azure HDInsight usando IntelliJ'
description: Tutorial - Crie um aplicativo do Spark escrito no Scala com o Apache Maven como o sistema de build e um arquétipo Maven existente para Scala fornecido pelo IntelliJ IDEA.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 06/26/2019
ms.openlocfilehash: d057aa162c5554ed6c568fb765ddb2b84ac92fd6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448902"
---
# <a name="tutorial-create-a-scala-maven-application-for-apache-spark-in-hdinsight-using-intellij"></a>Tutorial: Criar um aplicativo Scala Maven para Apache Spark no HDInsight usando IntelliJ

Neste tutorial, você aprenderá a criar um aplicativo [Apache Spark](https://spark.apache.org/) escrito em [Scala](https://www.scala-lang.org/) usando o [Apache Maven](https://maven.apache.org/) com IntelliJ IDEA. O artigo usa o Apache Maven como o sistema de build e começa com um arquétipo Maven existente para Scala fornecido pelo IntelliJ IDEA.  Criar um aplicativo Scala no IntelliJ IDEA envolve as seguintes etapas:

* Usar o Maven como o sistema de compilação.
* Atualize o arquivo do POM (Modelo de Objeto do Projeto) para resolver as dependências do módulo Spark.
* Escreva seu aplicativo em Scala.
* Gere um arquivo jar que pode ser enviado aos clusters HDInsight Spark.
* Executar os aplicativos em um cluster do Spark usando Livy.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Instalar o plug-in Scala para IntelliJ IDEA
> * Use IntelliJ para desenvolver um aplicativo Scala Maven
> * Criar um projeto de Scala autônomo

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [Kit de desenvolvimento Oracle Java](https://www.azul.com/downloads/azure-only/zulu/).  Este tutorial usa o Java versão 8.0.202.

* Um Java IDE. Este artigo usa o [IntelliJ IDEA Community ver.  2018.3.4](https://www.jetbrains.com/idea/download/).

* Azure Toolkit for IntelliJ.  Confira [Installing the Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable) (Instalação do Azure Toolkit for IntelliJ).

## <a name="install-scala-plugin-for-intellij-idea"></a>Instalar o plug-in Scala para IntelliJ IDEA

Siga estas etapas para instalar o plug-in Scala:

1. Abra o IntelliJ IDEA.

2. Na tela de boas-vindas, navegue até **Configurar** > **Plug-ins** para abrir a janela **Plug-ins**.

    ![Habilitar plug-in do scala](./media/apache-spark-create-standalone-application/enable-scala-plugin.png)

3. Selecione **Instalar** para o plug-in Scala caracterizado na nova janela.  

    ![Instalar o plug-in do scala](./media/apache-spark-create-standalone-application/install-scala-plugin.png)

4. Depois que o plug-in foi instalado com êxito, você deve reiniciar o IDE.

## <a name="use-intellij-to-create-application"></a>Use o IntelliJ para criar o aplicativo

1. Inicie o IntelliJ IDEA e selecione **Criar novo projeto** para abrir a janela **Novo projeto**.

2. Selecione **Azure Spark/HDInsight** no painel esquerdo.

3. Selecione **Projeto Spark (Scala)** na janela principal.

4. Na lista suspensa **Ferramenta de build**, selecione uma das seguintes opções:
      * **Maven** para obter suporte ao assistente de criação de projetos Scala.
      * **SBT** para gerenciar as dependências e para criar no projeto Scala.

   ![A caixa de diálogo Novo Projeto](./media/apache-spark-create-standalone-application/create-hdi-scala-app.png)

5. Selecione **Avançar**.

6. Na janela **Novo Projeto**, forneça as seguintes informações:  

  	|  Propriedade   | DESCRIÇÃO   |  
  	| ----- | ----- |  
  	|Nome do projeto| Insira um nome.|  
  	|Local do&nbsp;projeto| Insira o local desejado para salvar o projeto.|
  	|SDK do projeto| Isso ficará em branco na primeira utilização do IDEA.  Selecione **Novo...** e navegue até o JDK.|
  	|Versão do Spark|O assistente de criação integra a versão apropriada para o SDK do Spark e o SDK do Scala. Se a versão do cluster do Spark for inferior a 2.0, selecione **Spark 1.x**. Caso contrário, selecione **Spark 2.x**. Esse exemplo usa o **Spark 2.3.0 (Scala 2.11.8)** .|

    ![Selecionando o SDK do Spark](./media/apache-spark-create-standalone-application/hdi-new-project.png)

7. Selecione **Concluir**.

## <a name="create-a-standalone-scala-project"></a>Criar um projeto de Scala autônomo

1. Inicie o IntelliJ IDEA e selecione **Criar novo projeto** para abrir a janela **Novo projeto**.

2. Selecione **Maven** no painel esquerdo.

3. Especifique um **SDK do projeto**. Se estiver em branco, selecione **Novo…** e navegue até o diretório de instalação do Java.

4. Marque a caixa de seleção **Criar do arquétipo**.  

5. Na lista de arquétipos, selecione **org.scala-tools.archetypes:scala-archetype-simple**. Esse arquétipo cria a estrutura de diretório certa e baixa as dependências padrão necessárias para gravar o programa Scala.

    ![Criar projeto Maven](./media/apache-spark-create-standalone-application/create-maven-project.png)

6. Selecione **Avançar**.

7. Forneça os valores relevantes para **GroupId**, **ArtifactId** e **versão**. Os valores a seguir são usados neste tutorial:

    - **GroupId:** com.microsoft.spark.example
    - **ArtifactId:** SparkSimpleApp

8. Selecione **Avançar**.

9. Verifique as configurações e selecione **Avançar**.

10. Verifique o nome de projeto e local e, em seguida, selecione **Concluir**.  A importação do projeto levará alguns minutos.

11. Após a importação do projeto, no painel esquerdo, navegue até **SparkSimpleApp** > **src** > **test** > **scala** > **com** > **microsoft** > **spark** > **example**.  Clique com o botão direito do mouse em **MySpec** e selecione **Excluir…** . Você não precisa desse arquivo para o aplicativo.  Selecione **OK** na caixa de diálogo.
  
12. Nas etapas subsequentes, você atualizará **pom.xml** para definir as dependências para o aplicativo Spark Scala. Para que essas dependências sejam baixadas e resolvidas automaticamente, você deve configurar o Maven adequadamente.

13. No menu **Arquivo**, selecione **Configurações** para abrir a janela **Configurações**.

14. Na janela **Configurações**, navegue até **Build, Execução, Implantação** > **Ferramentas de Build** > **Maven** > **Importando**.

15. Marque a caixa de seleção **Importar projetos Maven automaticamente**.

16. Selecione **Aplicar** e, depois, **OK**.  Em seguida, você retornará à janela do projeto.

    ![Configurar Maven para downloads automáticos](./media/apache-spark-create-standalone-application/configure-maven.png)


17. No painel esquerdo, navegue até **src** > **main** > **scala** > **com.microsoft.spark.example** e clique duas vezes em **Aplicativo** para abrir App.scala.

18. Substitua o código de exemplo existente pelo código a seguir e salve as alterações. Esse código lê os dados no HVAC.csv (disponível em todos os clusters Spark HDInsight), recupera as linhas com apenas um dígito na sexta coluna e grava a saída em **/HVACOut** no contêiner padrão de armazenamento do cluster.

        package com.microsoft.spark.example
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        /**
          * Test IO to wasb
          */
        object WasbIOTest {
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("WASBIOTest")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows which have only one digit in the 7th column in the CSV
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasb:///HVACout")
          }
        }
19. No painel esquerdo, clique duas vezes em **pom.xml**.  

20. Dentro de `<project>\<properties>`, adicione os seguintes segmentos:

          <scala.version>2.11.8</scala.version>
          <scala.compat.version>2.11.8</scala.compat.version>
          <scala.binary.version>2.11</scala.binary.version>

21. Dentro de `<project>\<dependencies>`, adicione os seguintes segmentos:

           <dependency>
             <groupId>org.apache.spark</groupId>
             <artifactId>spark-core_${scala.binary.version}</artifactId>
             <version>2.3.0</version>
           </dependency>

    Salve as alterações a pom.xml.

22. Crie o arquivo .jar. O IntelliJ IDEA permite a criação de JAR como um artefato de um projeto. Execute as seguintes etapas:

    1. No menu **Arquivo**, selecione **Estrutura do Projeto…** .

    2. Na janela **Estrutura do projeto**, navegue até **Artefatos** > **o símbolo de adição +**  > **JAR** > **De módulos com dependências…** .

        ![Criar JAR](./media/apache-spark-create-standalone-application/create-jar-1.png)

    3. Na janela **Criar JAR de Módulos** janela, selecione o ícone de pasta na **classe principal** caixa de texto.

    4. Na janela **Selecionar classe principal**, selecione a classe exibida por padrão e, em seguida, selecione **OK**.

        ![Criar JAR](./media/apache-spark-create-standalone-application/create-jar-2.png)

    5. Na janela **Criar JAR de Módulos**, verifique se a opção **extrair para o JAR de destino** está selecionada e, em seguida, selecione **OK**.  Essa configuração cria um JAR único com todas as dependências.

        ![Criar JAR](./media/apache-spark-create-standalone-application/create-jar-3.png)

    6. A guia **Layout de Saída** lista todos os jars incluídos como parte do projeto Maven. Você pode selecionar e excluir aqueles dos quais o aplicativo Scala não tem qualquer dependência direta. Para o aplicativo que estamos criando aqui, você pode remover tudo, exceto o último (**Saída de compilação SparkSimpleApp**). Selecione os jars para excluir e, em seguida, selecione o símbolo negativo **-** .

        ![Criar JAR](./media/apache-spark-create-standalone-application/delete-output-jars.png)

        Verifique se a caixa **Incluir no build do projeto** está selecionada, o que garante que o jar seja criado sempre que o projeto for criado ou atualizado. Selecione **Aplicar** e, em seguida, **OK**.

    7. Para criar o arquivo jar, navegue até **Compilar** > **Compilar artefatos** > **Compilar**. O projeto será compilado em cerca de 30 segundos.  O jar de saída é criado em **\out\artifacts**.

        ![Criar JAR](./media/apache-spark-create-standalone-application/output.png)

## <a name="run-the-application-on-the-apache-spark-cluster"></a>Executar o aplicativo no cluster do Apache Spark

Para executar o aplicativo no cluster, você pode usar as seguintes abordagens:

* **Copie o jar do aplicativo para o blob de armazenamento do Azure** associado ao cluster. Você pode usar [**AzCopy**](../../storage/common/storage-use-azcopy.md), um utilitário de linha de comando, para fazer isso. Há muitos outros clientes também que você pode usar para carregar dados. É possível saber mais sobre eles em [Carregar dados para trabalhos do Apache Hadoop no HDInsight](../hdinsight-upload-data.md).

* **Use o Apache Livy para enviar um trabalho de aplicativo remotamente** para o cluster Spark. Os clusters Spark no HDInsight incluem Livy, que expõe pontos de extremidade REST para enviar remotamente trabalhos do Spark. Para saber mais, consulte [Enviar trabalhos do Apache Spark remotamente usando o Apache Livy com clusters Spark no HDInsight](apache-spark-livy-rest-interface.md).

## <a name="clean-up-resources"></a>Limpar recursos

Se não for continuar usando este aplicativo, exclua o cluster que você criou seguindo estas etapas:

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. Na caixa **Pesquisar** na parte superior, digite **HDInsight**.

1. Selecione **Clusters do HDInsight** em **Serviços**.

1. Na lista de clusters do HDInsight que aparece, selecione **…** ao lado do cluster que você criou para este tutorial.

1. Selecione **Excluir**. Selecione **Sim**.

![Excluir um cluster HDInsight](./media/apache-spark-create-standalone-application/hdinsight-azure-portal-delete-cluster.png "Excluir o cluster HDInsight")

## <a name="next-step"></a>Próxima etapa

Neste artigo, você aprendeu a criar um aplicativo Scala do Apache Spark. Avance para o próximo artigo para saber como executar esse aplicativo em um cluster do HDInsight Spark usando Livy.

> [!div class="nextstepaction"]
>[Execute trabalhos remotamente em um cluster do Apache Spark usando o Apache Livy](./apache-spark-livy-rest-interface.md)
