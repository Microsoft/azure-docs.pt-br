---
title: Usar o Azure Toolkit for IntelliJ com a Área Restrita do Hortonworks
description: Saiba como usar as ferramentas do HDInsight no Kit de Ferramentas do Azure para IntelliJ com a Área Restrita do Hortonworks.
keywords: ferramentas do hadoop,consulta do hive,intellij,área restrita do hortonworks,kit de ferramentas do azure para intellij
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 5234835f5fbfd5617e6b0440ff38fa900051470a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450119"
---
# <a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Usar ferramentas de HDInsight para IntelliJ com a área restrita do Hortonworks

Saiba como usar as Ferramentas do HDInsight para IntelliJ para desenvolver aplicativos Apache Scala e testá-los na [Área Restrita do Hortonworks](https://hortonworks.com/products/sandbox/) em execução no seu computador. 

O [IntelliJ IDEA](https://www.jetbrains.com/idea/) é um ambiente de desenvolvimento integrado Java (IDE) para o desenvolvimento de software de computador. Depois de desenvolver e testar os aplicativos na Área Restrita do Hortonworks, é possível movê-los para o [Azure HDInsight](apache-hadoop-introduction.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, você deve ter os seguintes itens:

- HDP (Plataforma de Dados do Hortonworks) 2.4 na Área Restrita do Hortonworks em execução no seu computador local. Para configurar o HDP, consulte [Introdução ao ecossistema do Apache Hadoop com uma área restrita do Hadoop em uma máquina virtual](apache-hadoop-emulator-get-started.md). 
    > [!NOTE]
    > As Ferramentas do HDInsight para IntelliJ foram testadas somente com HDP 2.4. Para obter o HDP 2.4, expanda **Arquivamento da Área Restrita da Hortonworks** no [site de downloads da Área Restrita do Hortonworks](https://hortonworks.com/downloads/#sandbox).

- [Java Developer Kit (JDK) versão 1.8 ou posterior](https://aka.ms/azure-jdks). O Kit de Ferramentas do Azure para IntelliJ exige um JDK.

- [Edição de comunidade do IntelliJ IDEA](https://www.jetbrains.com/idea/download) com o plug-in do [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) e o plug-in do [Kit de Ferramentas do Azure para IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij). As Ferramentas do HDInsight para IntelliJ estão disponíveis como parte do Kit de Ferramentas do Azure para IntelliJ. 

Para instalar os plug-ins:

  1. Abra o IntelliJ IDEA.
  2. Na página **inicial**, selecione **Configurar** e, em seguida, selecione **Plug-ins**.
  3. Selecione **Instalar o plug-in JetBrains** no canto inferior esquerdo.
  4. Use a função de pesquisa para pesquisar pelo **Scala** e, em seguida, selecione **Instalar**.
  5. Selecione **Reiniciar IntelliJ IDEA** para concluir a instalação.
  6. Repita as etapas 4 e 5 para instalar o **Kit de Ferramentas do Azure para IntelliJ**. Para mais informações, consulte [Instalar o Kit de Ferramentas do Azure para IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="create-an-apache-spark-scala-application"></a>Criar um aplicativo Scala do Apache Spark

Nesta seção, você cria um projeto de exemplo do Scala usando o IntelliJ IDEA. Na próxima seção, você vincular IntelliJ IDEA à área restrita Hortonworks (emulador) antes de enviar o projeto.

1. Abra o IntelliJ IDEA no computador. Na caixa de diálogo **Novo Projeto**, siga as etapas:

   1. Selecione **HDInsight** > **Spark no HDInsight (Scala)** .
   2. Na lista **Ferramenta de build**, selecione uma das opções a seguir, com base no seu cenário:

      * **Maven**: Para suporte ao assistente de criação de projetos do Scala.
      * **SBT**: Para gerenciar dependências e compilar para o projeto do Scala.

   ![A caixa de diálogo Novo Projeto](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)

2. Selecione **Avançar**.
3. Na próxima caixa de diálogo **Novo Projeto**, siga as seguintes etapas:

   1. Na caixa **Nome do projeto**, digite um nome para o projeto.
   2. Na caixa **Local do projeto**, digite um local para o projeto.
   3. Ao lado da lista suspensa **SDK do Projeto**, selecione **Novo**, **JDK** e, em seguida, especifique a pasta do JDK Java versão 1.7 ou posterior. Selecione **Java 1.8** para o cluster Spark 2.x. Selecione **Java 1.7** para o cluster Spark 1.x. O local padrão é C:\Program Files\Java\jdk1.8.x_xxx.
   4. Na lista suspensa **Versão do Spark**, o assistente de criação de projeto Scala integra a versão correta do SDK do Spark e do SDK do Scala. Se a versão do cluster do Spark for inferior a 2.0, selecione **Spark 1.x**. Caso contrário, selecione **Spark2.x**. Esse exemplo usa o Spark 1.6.2 (Scala 2.10.5). Verifique se está usando o repositório marcado como **Scala 2.10.x**. Não use o repositório marcado como Scala 2.11.x.
    
      ![Criar IntelliJ Scala propriedades do projeto](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)


4. Selecione **Concluir**.
5. Se a exibição **Projeto** ainda não estiver aberta, pressione **Alt + 1** para abri-la.
6. Em **Explorador de Projeto**, expanda o projeto e, em seguida, selecione **src**.
7. Clique com o botão direito do mouse em **src**, aponte para **Novo** e, em seguida, selecione **Classe Scala**.
8. Na caixa **Nome**, insira um nome. Na caixa **Tipo**, selecione **Objeto**. Depois, selecione **OK**.

    ![A caixa de diálogo Criar Nova Classe Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)

9. No arquivo .scala, cole este código:

        import java.util.Random
        import org.apache.spark.{SparkConf, SparkContext}
        import org.apache.spark.SparkContext._

        /**
        * Usage: GroupByTest [numMappers] [numKVPairs] [valSize] [numReducers]
        */
        object GroupByTest {
            def main(args: Array[String]) {
                val sparkConf = new SparkConf().setAppName("GroupBy Test")
                var numMappers = 3
                var numKVPairs = 10
                var valSize = 10
                var numReducers = 2

                val sc = new SparkContext(sparkConf)

                val pairs1 = sc.parallelize(0 until numMappers, numMappers).flatMap { p =>
                val ranGen = new Random
                var arr1 = new Array[(Int, Array[Byte])](numKVPairs)
                for (i <- 0 until numKVPairs) {
                    val byteArr = new Array[Byte](valSize)
                    ranGen.nextBytes(byteArr)
                    arr1(i) = (ranGen.nextInt(Int.MaxValue), byteArr)
                }
                arr1
                }.cache
                // Enforce that everything has been calculated and in cache.
                pairs1.count

                println(pairs1.groupByKey(numReducers).count)
            }
        }

10. No menu **Compilar**, selecione **Compilar projeto**. Certifique-se de que a compilação foi concluída com êxito.


## <a name="link-to-the-hortonworks-sandbox"></a>Link para a Área Restrita do Hortonworks

Antes que possa estabelecer um vínculo com uma Área Restrita do Sandbox (emulador), você precisa ter um aplicativo IntelliJ existente.

Para vincular a um emulador:

1. Abra o projeto no IntelliJ.
2. No menu **Exibir**, selecione **Janelas de Ferramentas** e selecione o **Azure Explorer**.
3. Expanda **Azure**, clique com o botão direito do mouse em **HDInsight** e, em seguida, selecione **Vincular um Emulador**.
4. Na caixa de diálogo **Vincular um Novo Emulador**, insira a senha definida para a conta raiz da Área Restrita do Hortonworks. Em seguida, insira valores semelhantes àqueles usados na captura de tela a seguir. Depois, selecione **OK**. 

   ![A caixa de diálogo Vincular um Novo Emulador](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Para configurar o emulador, selecione **Sim**.

Quando for conectado com êxito, o emulador (Área Restrita do Hortonworks) será listado no nó do HDInsight.

## <a name="submit-the-spark-scala-application-to-the-hortonworks-sandbox"></a>Enviar o aplicativo Scala Spark à Área Restrita do Hortonworks

Após vincular o IntelliJ IDEA ao emulador, você pode enviar seu projeto.

Para enviar um projeto a um emulador:

1. No **Explorador de Projetos**, clique com o botão direito do mouse no projeto e selecione **Enviar Aplicativo Spark ao HDInsight**.
2. Conclua as seguintes etapas:

    1. Na lista suspensa **Cluster Spark (somente no Linux)** , selecione a Área Restrita do Hortonworks local.
    2. Na caixa **Nome da classe principal**, escolha ou digite o nome da classe principal. Neste artigo, o nome é **GroupByTest**.

3. Selecione **Enviar**. Os logs de envio de trabalho são mostrados na janela da ferramenta de envio Spark.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [usar as Ferramentas do HDInsight no Azure Toolkit for IntelliJ criar aplicativos Apache Spark para um cluster do Linux do HDInsight Spark](../spark/apache-spark-intellij-tool-plugin.md).

- Para assistir a um vídeo sobre as Ferramentas do HDInsight para IntelliJ, consulte [Introduzir Ferramentas do HDInsight para IntelliJ para desenvolvimento do Apache Spark](https://www.youtube.com/watch?v=YTZzYVgut6c).

- Saiba como [depurar aplicativos Apache Spark remotamente em um cluster HDInsight com o Azure Toolkit for IntelliJ por meio do SSH](../spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md).

- Saiba como [usar as ferramentas do HDInsight no Azure Toolkit for IntelliJ para depurar aplicativos Apache Spark remotamente em um cluster do Linux do HDInsight Spark](../spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md).

- Saiba como [Usar as Ferramentas do HDInsight no Azure Toolkit for Eclipse para criar aplicativos Apache Spark](../spark/apache-spark-eclipse-tool-plugin.md).

- Para assistir a um vídeo sobre as Ferramentas do HDInsight para Eclipse, veja [Usar a Ferramentas do HDInsight para Eclipse para criar aplicativos Spark](https://mix.office.com/watch/1rau2mopb6fha).
