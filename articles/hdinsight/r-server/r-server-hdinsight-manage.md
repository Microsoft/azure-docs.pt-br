---
title: Gerenciar o cluster do ML Services no Azure HDInsight
description: Saiba como gerenciar um cluster do ML Services no Microsoft Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: fa838f371607f3c0b0f76f81d6755c842a5901f7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448952"
---
# <a name="manage-ml-services-cluster-on-azure-hdinsight"></a>Gerenciar cluster do ML Services no Microsoft Azure HDInsight

Neste artigo, você aprenderá a gerenciar um cluster existente de serviços de ML no Azure HDInsight para executar tarefas como adicionar vários usuários simultâneos, conectar-se remotamente a um cluster de serviços de ML, alterando o contexto de computação, etc.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster de serviços de ML no HDInsight. Ver [criar Apache clusters Hadoop usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **serviços de ML** para **tipo de Cluster**.


* Um cliente Secure Shell (SSH): Um cliente SSH é usado para se conectar ao cluster HDInsight remotamente e executar comandos diretamente no cluster. Para obter mais informações, confira [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).


## <a name="enable-multiple-concurrent-users"></a>Habilitar múltiplos usuário simultâneos

Você pode habilitar vários usuários simultâneos ao cluster do ML Service no HDInsight adicionando mais usuários ao nó de borda em que a versão da comunidade RStudio é executada. Quando você cria um cluster HDInsight, deve fornecer dois usuários, um usuário HTTP e um usuário SSH:

![Usuários simultâneos 1](./media/r-server-hdinsight-manage/concurrent-users-1.png)

- **Nome de usuário de logon de cluster**: um usuário HTTP para autenticação por meio do gateway de HDInsight que é usado para proteger os clusters HDInsight criados. Esse usuário HTTP é usado para acessar a UI do Apache Ambari, a UI do Apache Hadoop YARN, bem como outros componentes da UI.
- **Nome de usuário SSH (Secure Shell)** : um usuário SSH para acessar o cluster por meio do secure shell. Esse é um usuário no sistema Linux para todos os nós de cabeçalho, nós de trabalho e nós de borda. Portanto, você pode usar o secure shell para acessar qualquer nó em um cluster remoto.

A versão da Comunidade do ML Service usada no cluster do ML Services no HDInsight aceita apenas o nome de usuário e senha do Linux como mecanismo de entrada. Ele não dá suporte a tokens de passagem. Assim, quando você tentar acessar o R Studio pela primeira vez em um cluster do ML Services você precisará entrar duas vezes.

- Primeiro, entre usando as credenciais de usuário HTTP por meio do Gateway do HDInsight. 

- Em seguida, use as credenciais de usuário do SSH para entrar no RStudio.
  
Atualmente, apenas uma conta de usuário SSH pode ser criada durante o provisionamento de um cluster HDInsight. Portanto, para permitir que vários usuários acessem o ML Services em clusters no HDInsight, precisamos criar usuários adicionais no sistema Linux.

Como o RStudio está em execução no nó de borda do cluster, há várias etapas aqui:

1. Use o usuário SSH existente para entrar no nó de borda
2. Adicionar mais usuários do Linux ao nó de borda
3. Use a versão do RStudio Community com o usuário criado

### <a name="step-1-use-the-created-ssh-user-to-sign-in-to-the-edge-node"></a>Etapa 1: Usar o usuário SSH criado para entrar no nó de borda

Siga as instruções em [Conectar-se ao HDInsight (Apache Hadoop) usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) para acessar o nó de borda. É o endereço do nó de borda para o cluster do ML Services no HDInsight `CLUSTERNAME-ed-ssh.azurehdinsight.net`.

### <a name="step-2-add-more-linux-users-in-edge-node"></a>Etapa 2: Adicionar mais usuários do Linux ao nó de borda

Para adicionar um usuário ao nó de borda, execute os comandos:

    # Add a user 
    sudo useradd <yournewusername> -m

    # Set password for the new user
    sudo passwd <yournewusername>

A captura de tela a seguir mostra o resultado.

![Usuários simultâneos 3](./media/r-server-hdinsight-manage/concurrent-users-2.png)

Quando a "Senha atual do Kerberos" for solicitada, basta pressionar **Enter** para ignorar a solicitação. A opção `-m` no comando `useradd` indica que o sistema criará uma pasta base para o usuário, obrigatória para a versão do RStudio Community.

### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>Etapa 3: Use a versão do RStudio Community com o usuário criado

Acessar RStudio a pártir de https://CLUSTERNAME.azurehdinsight.net/rstudio/. Se você estiver fazendo logon pela primeira vez depois de criar o cluster, insira as credenciais de administrador de cluster seguidas as credenciais de usuário SSH que você criou. Se não for o primeiro logon, apenas digite as credenciais de usuário SSH que você criou.

Você também pode entrar usando as credenciais originais (por padrão, ela é *sshuser*) simultaneamente em outra janela do navegador.

Observe também que os usuários adicionados recentemente não têm privilégios de raiz no sistema Linux, mas têm o mesmo acesso a todos os arquivos nos armazenamentos remotos HDFS e WASB.

## <a name="connect-remotely-to-microsoft-ml-services"></a>Conectar remotamente o Microsoft ML Services

Você pode configurar o acesso ao contexto de computação do HDInsight Spark a partir de uma instância remota do ML Client em execução na sua área de trabalho. Para isso, você precisará especificar as seguintes opções (hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches e sshProfileScript), ao definir o contexto de computação RxSpark em seu desktop: Por exemplo:

    myNameNode <- "default"
    myPort <- 0

    mySshHostname  <- '<clustername>-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- '<sshuser>'# HDI SSH username
    mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key

    myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
    myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")

    mySparkCluster <- RxSpark(
      hdfsShareDir = myhdfsShareDir,
      shareDir     = myShareDir,
      sshUsername  = mySshUsername,
      sshHostname  = mySshHostname,
      sshSwitches  = mySshSwitches,
      sshProfileScript = '/etc/profile',
      nameNode     = myNameNode,
      port         = myPort,
      consoleOutput= TRUE
    )

Para obter mais informações, consulte a seção "Usando o Microsoft Machine Learning Server como um Apache Hadoop Client" em [Como usar o RevoScaleR em um contexto de computação do Apache Spark](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios)

## <a name="use-a-compute-context"></a>Use um contexto de computação

Um contexto de computação permite que você controle se o cálculo é executado localmente no nó de borda ou distribuído entre os nós no cluster HDInsight.  Para obter um exemplo de como definir um contexto de computação com o RStudio Server, consulte [executar um script de R em um cluster de serviços de ML no HDInsight do Azure usando o RStudio Server](machine-learning-services-quickstart-job-rstudio.md).

## <a name="distribute-r-code-to-multiple-nodes"></a>Distribua o código R em vários nós

Com o ML Services no HDInsight, você pode facilmente usar o código R existente e executá-lo em vários nós no cluster usando `rxExec`. Essa função é útil ao fazer uma limpeza de parâmetro ou simulações. O código abaixo é um exemplo de como usar o `rxExec`:

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Se você ainda estiver usando o contexto Spark, esse comando retornará o valor do nome do nó para os nós do trabalhador nos quais o código `(Sys.info()["nodename"])` é executado. Por exemplo, em um cluster de quatro nós, você deve receber uma saída semelhante ao snippet de código a seguir:

    $rxElem1
        nodename
    "wn3-mymlser"

    $rxElem2
        nodename
    "wn0-mymlser"

    $rxElem3
        nodename
    "wn3-mymlser"

    $rxElem4
        nodename
    "wn3-mymlser"

## <a name="access-data-in-apache-hive-and-parquet"></a>Acessar dados no Apache Hive e Parquet

O ML Services do HDInsight permite acesso direto aos dados no Hive e Parquet para uso pelas funções do ScaleR no contexto de computação do Spark. Esses recursos estão disponíveis por meio de novas funções de fontes de dados ScalR chamadas RxHiveData e RxParquetData, que funcionam com o uso de Spark SQL para carregar dados diretamente em um DataFrame do Spark para serem analisados pelo ScaleR.

O código abaixo fornece exemplos de código na utilização das novas funções:

    #Create a Spark compute context:
    myHadoopCluster <- rxSparkConnect(reset = TRUE)

    #Retrieve some sample data from Hive and run a model:
    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)

    #Retrieve some sample data from Parquet and run a model:
    rxHadoopMakeDir('/share')
    rxHadoopCopyFromLocal(file.path(rxGetOption('sampleDataDir'), 'claimsParquet/'), '/share/')
    pqData <- RxParquetData('/share/claimsParquet',
                     colInfo = list(
                age    = list(type = "factor"),
               car.age = list(type = "factor"),
                  type = list(type = "factor")
             ) )
    rxGetInfo(pqData, getVarInfo = TRUE)

    rxNaiveBayes(type ~ age + cost, data = pqData)

    #Check on Spark data objects, cleanup, and close the Spark session:
    lsObj <- rxSparkListData() # two data objs are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() # it should show empty list
    rxSparkDisconnect(myHadoopCluster)


Para saber mais sobre o uso dessas novas funções, confira a ajuda online no ML Services usando os comandos `?RxHivedata` e `?RxParquetData`.  

## <a name="install-additional-r-packages-on-the-cluster"></a>Instalar pacotes R adicionais no cluster

### <a name="to-install-r-packages-on-the-edge-node"></a>Para Instalar pacotes R adicionais no nó de borda

Se desejar instalar pacotes R adicionais no nó de borda, você poderá usar o `install.packages()` diretamente do console R quando conectado ao nó de borda por meio do SSH. 

### <a name="to-install-r-packages-on-the-worker-node"></a>Para instalar pacotes R no nó da função de trabalho

Para instalar pacotes R em nós de trabalho do cluster, você deverá usar uma ação de script. As Ações de Script são scripts de Bash que são usados para fazer alterações de configuração no cluster HDInsight ou para instalar um software adicional, como outros pacotes R. 

> [!IMPORTANT]  
> As Ações de Script para instalar pacotes R adicionais só podem ser usadas depois que o cluster foi criado. Não use esse procedimento durante a criação do cluster, pois o script depende da configuração completa do ML Services.

1. Siga as etapas em [Personalizar clusters usando a Ação de script](../hdinsight-hadoop-customize-cluster-linux.md).

3. Para **Enviar ação de script**, forneça as seguintes informações:

   * Para **Tipo de Script**, selecione **Personalizar**.

   * Para **Nome**, forneça um nome para a ação de script.

     * Para uma **URI do script Bash**, insira `https://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`. Este é o script que instala os pacotes R adicionais no nó de trabalho

   * Marque a caixa de seleção apenas para **Trabalho**.

   * **Parâmetros**: Os pacotes R a serem instalados. Por exemplo, `bitops stringr arules`

   * Marque a caixa de seleção para **Manter essa ação de script**.  

   > [!NOTE]
   > 1. Por padrão, todos os pacotes R são instalados por meio de um instantâneo do repositório do Microsoft MRAN consistente com a versão do ML Server que foi instalado. Se você quiser instalar versões mais recentes dos pacotes, há alguns riscos de incompatibilidade. No entanto, esse tipo de instalação é possível especificando `useCRAN` como o primeiro elemento do pacote de lista, por exemplo, `useCRAN bitops, stringr, arules`.  
   > 2. Alguns pacotes R exigem outras bibliotecas do sistema Linux. Por conveniência, o ML Services do HDInsight vem pré-instalado com as dependências necessárias para os 100 pacotes R mais populares. No entanto, se os pacotes R instalados exigirem bibliotecas além dessas, você deverá baixar o script base usado aqui e adicionar etapas para instalar as bibliotecas do sistema. Em seguida, você deverá carregar o script modificado em um contêiner de blob público no armazenamento do Azure e usar o script modificado para instalar os pacotes.
   >    Para saber mais sobre como desenvolver as Ações de Script, confira [Desenvolvimento de ação de script](../hdinsight-hadoop-script-actions-linux.md).  
   >
   >

   ![Adicionando uma ação do script](./media/r-server-hdinsight-manage/submitscriptaction.png)

4. Escolha **Criar** para executar o script. Quando o script é concluído, os pacotes R ficam disponíveis em todos os nós de trabalho.

## <a name="next-steps"></a>Próximas etapas

* [Operacionalizar o cluster do ML Services no HDInsight](r-server-operationalize.md)
* [Opções de contexto de computação para cluster do ML Services no HDInsight](r-server-compute-contexts.md)
* [Opções de Armazenamento do Microsoft Azure para cluster do ML Services no HDInsight](r-server-storage.md)
