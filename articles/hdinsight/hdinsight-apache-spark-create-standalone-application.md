---
title: "Criar aplicativos do Scala para execução em clusters do Spark – Azure HDInsight | Microsoft Docs"
description: "Crie um aplicativo do Spark escrito no Scala com o Apache Maven como o sistema de build e um arquétipo Maven existente para Scala fornecido pelo IntelliJ IDEA."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: b2467a40-a340-4b80-bb00-f2c3339db57b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: nitinme
ms.openlocfilehash: 95dba08744357f8800b05e3d4b892e3a363d5985
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-scala-maven-application-to-run-on-apache-spark-cluster-on-hdinsight"></a>Criar um aplicativo Maven Scala para execução no cluster do Apache Spark no HDInsight

Saiba como criar um aplicativo Spark escrito em Scala usando o Maven com IntelliJ IDEA. O artigo usa o Apache Maven como o sistema de build e começa com um arquétipo Maven existente para Scala fornecido pelo IntelliJ IDEA.  Criar um aplicativo Scala no IntelliJ IDEA envolve as seguintes etapas:

* Usar o Maven como o sistema de compilação.
* Atualize o arquivo do POM (Modelo de Objeto do Projeto) para resolver as dependências do módulo Spark.
* Escreva seu aplicativo em Scala.
* Gere um arquivo jar que pode ser enviado aos clusters HDInsight Spark.
* Executar os aplicativos em um cluster do Spark usando Livy.

> [!NOTE]
> O HDInsight também fornece uma ferramenta de plug-in do IntelliJ IDEA para facilitar o processo de criação e envio de aplicativos para um cluster HDInsight Spark no Linux. Para obter mais informações, consulte [Usar o plug-in de Ferramentas do HDInsight para IntelliJ IDEA para criar e enviar aplicativos Spark](hdinsight-apache-spark-intellij-tool-plugin.md).
> 
> 

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
* Kit de desenvolvimento Oracle Java. Você pode instalá-lo clicando [aqui](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* Um Java IDE. Este artigo usa IntelliJ IDEA 15.0.1. Você pode instalá-lo clicando [aqui](https://www.jetbrains.com/idea/download/).

## <a name="install-scala-plugin-for-intellij-idea"></a>Instalar o plug-in Scala para IntelliJ IDEA
Se a instalação do IntelliJ IDEA não solicitar para habilitar o plug-in Scala, inicie IntelliJ IDEA e percorra as etapas a seguir para instalar o plug-in:

1. Inicie o IntelliJ IDEA e, na tela de boas-vindas, clique em **Configurar** e, em seguida, clique em **Plug-ins**.
   
    ![Habilitar plug-in do scala](./media/hdinsight-apache-spark-create-standalone-application/enable-scala-plugin.png)
2. Na próxima tela, clique em **Instalar o plugin JetBrains** no canto inferior esquerdo. Na caixa de diálogo **Procurar plug-ins da JetBrains** que é aberta, pesquise por Scala e, em seguida, clique em **Instalar**.
   
    ![Instalar o plug-in do scala](./media/hdinsight-apache-spark-create-standalone-application/install-scala-plugin.png)
3. Depois que o plug-in for instalado com êxito, clique no **botão Reiniciar IntelliJ IDEA** para reiniciar o IDE.

## <a name="create-a-standalone-scala-project"></a>Criar um projeto de Scala autônomo
1. Inicie o IntelliJ IDEA e crie um novo projeto. Na caixa de diálogo Novo Projeto, faça o seguinte e, em seguida, clique em **Avançar**.
   
    ![Criar projeto Maven](./media/hdinsight-apache-spark-create-standalone-application/create-maven-project.png)
   
   * Selecione **Maven** como o tipo de projeto.
   * Especifique um **SDK do projeto**. Clique em Novo e navegue até o diretório de instalação do Java, normalmente `C:\Program Files\Java\jdk1.8.0_66`.
   * Selecione a opção **Criar do Arquétipo** .
   * Na lista de arquétipos, selecione **org.scala-tools.archetypes:scala-archetype-simple**. Isso criará a estrutura de diretório certa e baixará as dependências padrão necessárias para gravar o programa Scala.
2. Forneça os valores relevantes para **GroupId**, **ArtifactId** e **versão**. Clique em **Avançar**.
3. Na caixa de diálogo seguinte, na qual você especificará o diretório inicial do Maven e outras configurações do usuário, aceite os padrões e clique em **Avançar**.
4. Na última caixa de diálogo, especifique um nome de projeto e local e, em seguida, clique em **Concluir**.
5. Exclua o arquivo **MySpec.Scala** em **src\test\scala\com\microsoft\spark\example**. Você não precisa disso para o aplicativo.
6. Se necessário, renomeie os arquivos de origem e de teste padrão. No painel à esquerda, no IntelliJ IDEA, navegue até **src\main\scala\com.microsoft.spark.example**. Clique com botão direito do mouse em **App.scala**, clique em **Refatorar**, clique em Renomear Arquivo, forneça o novo nome para o aplicativo na caixa de diálogo e clique em **Refatorar**.
   
    ![Renomear arquivos](./media/hdinsight-apache-spark-create-standalone-application/rename-scala-files.png)  
7. Nas etapas subsequentes, você irá atualizar o pom.xml para definir as dependências do aplicativo Spark Scala. Para que essas dependências sejam baixadas e resolvidas automaticamente, você deve configurar o Maven adequadamente.
   
    ![Configurar Maven para downloads automáticos](./media/hdinsight-apache-spark-create-standalone-application/configure-maven.png)
   
   1. No menu **Arquivo**, clique em **Configurações**.
   2. Na caixa de diálogo **Configurações**, navegue até **Build, Execução, Implantação** > **Ferramentas de Compilação** > **Maven** > **Importando**.
   3. Selecione a opção para **Importar projetos Maven automaticamente**.
   4. Clique em **Aplicar** e, em seguida, clique em **OK**.
8. Atualize o arquivo de origem Scala para incluir o código do aplicativo. Abra e substitua o código de exemplo existente pelo código a seguir e salve as alterações. Esse código lê os dados no HVAC.csv (disponível em todos os clusters Spark HDInsight), recupera as linhas com apenas um dígito na sexta coluna e grava a saída em **/HVACOut** no contêiner padrão de armazenamento do cluster.
   
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
9. Atualize o pom.xml.
   
   1. Dentro de `<project>\<properties>`, adicione o seguinte:
      
          <scala.version>2.10.4</scala.version>
          <scala.compat.version>2.10.4</scala.compat.version>
          <scala.binary.version>2.10</scala.binary.version>
   2. Dentro de `<project>\<dependencies>`, adicione o seguinte:
      
           <dependency>
             <groupId>org.apache.spark</groupId>
             <artifactId>spark-core_${scala.binary.version}</artifactId>
             <version>1.4.1</version>
           </dependency>
      
      Salve as alterações a pom.xml.
10. Crie o arquivo .jar. O IntelliJ IDEA permite a criação de JAR como um artefato de um projeto. Execute as seguintes etapas:
    
    1. No menu **Arquivo**, clique em **Estrutura do Projeto**.
    2. Na caixa de diálogo **Estrutura do Projeto**, clique em **Artefatos** e, em seguida, clique no sinal de mais. Na caixa de diálogo pop-up, clique em **JAR** e, em seguida, clique em **Dos módulos com dependências**.
       
        ![Criar JAR](./media/hdinsight-apache-spark-create-standalone-application/create-jar-1.png)
    3. Na caixa de diálogo **Criar JAR de Módulos![, clique no botão de reticências (**reticências](./media/hdinsight-apache-spark-create-standalone-application/ellipsis.png)) em relação à **Classe Principal**.
    4. Na caixa de diálogo **Selecionar Classe Principal**, selecione a classe que é exibida por padrão e, em seguida, clique em **OK**.
       
        ![Criar JAR](./media/hdinsight-apache-spark-create-standalone-application/create-jar-2.png)
    5. Na caixa de diálogo **Criar JAR de Módulos**, certifique-se de que a opção **extrair para o JAR de destino** esteja selecionada e, em seguida, clique em **OK**. Isso cria um JAR único com todas as dependências.
       
        ![Criar JAR](./media/hdinsight-apache-spark-create-standalone-application/create-jar-3.png)
    6. A guia layout de saída lista todos os jars incluídos como parte do projeto Maven. Você pode selecionar e excluir aqueles dos quais o aplicativo Scala não tem qualquer dependência direta. Para o aplicativo que estamos criando aqui, você pode remover tudo, exceto o último (**saída de compilação SparkSimpleApp**). Selecione os jars para excluir e, em seguida, clique no ícone **Excluir** .
       
        ![Criar JAR](./media/hdinsight-apache-spark-create-standalone-application/delete-output-jars.png)
       
        Certifique-se de que a caixa **Compilar à criação** esteja marcada, o que garante que o jar seja criado sempre que o projeto for criado ou atualizado. Clique em **Aplicar** e em **OK**.
    7. Na barra de menus, clique em **Compilar** e, em seguida, clique em **Criar Projeto**. Você também pode clicar em **Compilar Artefatos** para criar o jar. O jar de saída é criado em **\out\artifacts**.
       
        ![Criar JAR](./media/hdinsight-apache-spark-create-standalone-application/output.png)

## <a name="run-the-application-on-the-spark-cluster"></a>Executar o aplicativo no cluster do Spark
Para executar o aplicativo no cluster, faça o seguinte:

* **Copie o jar do aplicativo para o blob de armazenamento do Azure** associado ao cluster. Você pode usar [**AzCopy**](../storage/common/storage-use-azcopy.md), um utilitário de linha de comando, para fazer isso. Há muitos outros clientes também que você pode usar para carregar dados. É possível saber mais sobre eles em [Carregar dados para trabalhos do Hadoop no HDInsight](hdinsight-upload-data.md).
* **Use Livy para enviar um trabalho de aplicativo remotamente** para o cluster Spark. Os clusters Spark no HDInsight incluem Livy, que expõe pontos de extremidade REST para enviar remotamente trabalhos do Spark. Para obter mais informações, consulte [Enviar trabalhos do Spark remotamente usando Livy com clusters Spark no HDInsight](hdinsight-apache-spark-livy-rest-interface.md).

## <a name="next-step"></a>Próxima etapa

Neste artigo, você aprendeu como criar um aplicativo Spark Scala. Avance para o próximo artigo para saber como executar esse aplicativo em um cluster do HDInsight Spark usando Livy.

> [!div class="nextstepaction"]
>[Executar trabalhos remotamente em um cluster do Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

