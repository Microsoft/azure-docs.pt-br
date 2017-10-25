---
title: Analisar dados do sensor com o Apache Storm e o HBase | Microsoft Docs
description: Saiba como conectar-se ao Apache Storm com uma rede virtual. Use o Storm com o HBase para processar dados de sensor de um hub de eventos e visualize-o com D3.js.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: a9a1ac8e-5708-4833-b965-e453815e671f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/09/2017
ms.author: larryfr
ms.openlocfilehash: 3c66f9ea025a2d245cdf907be9f3c586f1ed45ba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-sensor-data-with-apache-storm-event-hub-and-hbase-in-hdinsight-hadoop"></a>Analisar dados de sensor com o Apache Storm e com o HBase no HDInsight (Hadoop)

Saiba como usar o Apache Storm no HDInsight para processar dados de sensor por meio dos Hubs de Eventos do Azure. Os dados são então armazenados no Apache HBase no HDInsight e visualizados com o D3.js.

O modelo do Azure Resource Manager usado neste documento demonstra como criar vários recursos do Azure em um grupo de recursos. O modelo cria uma Rede Virtual do Azure, dois clusters HDInsight (Storm e HBase) e um Aplicativo Web do Azure. Uma implementação node.js de um painel da Web em tempo real é automaticamente implantada no aplicativo Web.

> [!NOTE]
> As informações descritas e o exemplo deste documento exigem o HDInsight versão 3.6.
>
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure.
* [Node.js](http://nodejs.org/): usado para visualizar o painel da Web localmente no ambiente de desenvolvimento.
* [Java e JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/index.html): usado para desenvolver a topologia do Storm.
* [Maven](http://maven.apache.org/what-is-maven.html): usado para criar e compilar o projeto.
* [Git](http://git-scm.com/): usado para baixar o projeto do GitHub.
* Um cliente **SSH** : usado para conectar-se aos clusters HDInsight baseados em Linux. Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).


> [!IMPORTANT]
> Não é necessário um cluster HDInsight existente. As etapas descritas neste documento criam os seguintes recursos:
> 
> * Uma Rede virtual do Azure
> * Um cluster Storm no HDInsight (baseado em Linux, dois nós de trabalho)
> * Um cluster HBase no HDInsight (baseado em Linux, dois nós de trabalho)
> * Um Aplicativo Web do Azure que hospeda o painel da Web

## <a name="architecture"></a>Arquitetura

![diagrama da arquitetura](./media/hdinsight-storm-sensor-data-analysis/devicesarchitecture.png)

Esse exemplo consiste nos seguintes componentes:

* **Hub de Eventos do Azure**: contém os dados coletados dos sensores.
* **Storm no HDInsight**: fornece processamento em tempo real dos dados do Hub de Eventos.
* **HBase no HDInsight**: fornece um armazenamento de dados NoSQL persistente para dados depois que eles são processados pelo Storm.
* **Serviço de Rede Virtual do Azure**: permite a comunicação segura entre o Storm no HDInsight e o HBase em clusters HDInsight.
  
  > [!NOTE]
  > Uma rede virtual é necessária ao usar a API de cliente HBase Java. Ela não é exposta por meio do gateway público para clusters HBase. A instalação de clusters HBase e Storm na mesma rede virtual permite que o cluster Storm (ou qualquer outro sistema na rede virtual) acesse o HBase diretamente usando a API do cliente.

* **Site do painel**: um painel de exemplo que traça gráficos de dados em tempo real.
  
  * O site é implementado em Node.js.
  * [Socket.io](http://socket.io/) é usada para comunicação em tempo real entre a topologia Storm e o site.
    
    > [!NOTE]
    > O uso do Socket.io para a comunicação é um detalhe de implementação. Você pode usar qualquer estrutura de comunicação, como SignalR ou WebSockets brutos.

  * [D3.js](http://d3js.org/) é usada para representar graficamente os dados enviados para o site.

> [!IMPORTANT]
> São necessários dois clusters, pois não há método compatível para criar um cluster HDInsight para Storm e HBase.

A topologia lê dados do Hub de Eventos usando a classe [org.apache.storm.eventhubs.spout.EventHubSpout](http://storm.apache.org/releases/0.10.1/javadocs/org/apache/storm/eventhubs/spout/class-use/EventHubSpout.html) e grava dados no HBase usando a classe [org.apache.storm.hbase.bolt.HBaseBolt](https://storm.apache.org/releases/1.0.1/javadocs/org/apache/storm/hbase/bolt/HBaseBolt.html). A comunicação com o site é realizada usando [socket.io-client.java](https://github.com/nkzawa/socket.io-client.java).

O seguinte diagrama explica o layout da topologia:

![diagrama de topologia](./media/hdinsight-storm-sensor-data-analysis/sensoranalysis.png)

> [!NOTE]
> Esse diagrama é uma visão simplificada da topologia. Uma instância de cada componente é criada para cada partição no Hub de Eventos. Essas instâncias são distribuídas pelos nós do cluster e os dados são roteados entre elas da seguinte forma:
> 
> * Os dados do spout para o analisador têm a carga balanceada.
> * Os dados do analisador para o Painel e para o HBase são agrupados por ID de Dispositivo para que as mensagens do mesmo dispositivo fluam sempre para o mesmo componente.

### <a name="topology-components"></a>Componentes da topologia

* **Spout do Hub de Eventos**: o spout é fornecido como parte do Apache Storm versão 0.10.0 e superior.
  
  > [!NOTE]
  > O spout do Hub de Eventos usado neste exemplo exige um Storm no cluster HDInsight versão 3.5 ou 3.6.

* **ParserBolt.java**: os dados emitidos pelo spout são JSON brutos e, às vezes, mais de um evento é emitido por vez. Esse bolt lê os dados emitidos pelo spout e analisa a mensagem JSON. O bolt então emite os dados como uma tupla que contém vários campos.
* **DashboardBolt.java**: esse componente demonstra como usar a biblioteca de cliente Socket.io para Java para enviar dados em tempo real para o painel da Web.
* **no-hbase.yaml**: a definição de topologia usada ao executar no modo local. Não usa componentes HBase.
* **with-hbase.yaml**: a definição de topologia usada ao executar a topologia no cluster. Usa componentes HBase.
* **dev.properties**: as informações de configuração para o spout do Hub de Eventos, o bolt HBase, e os componentes do painel.

## <a name="prepare-your-environment"></a>Prepare o seu ambiente

Antes de usar este exemplo, você deve criar um Hub de Eventos do Azure que é lido pela topologia do Storm.

### <a name="configure-event-hub"></a>Configurar o Hub de Eventos

O Hub de Eventos é a fonte de dados para este exemplo. Use as etapas a seguir para criar um Hub de Eventos.

1. No [portal do Azure](https://portal.azure.com), selecione **+ Novo** -> **Internet das Coisas** -> **Hubs de Eventos**.
2. Na seção **Criar Namespace**, execute as seguintes tarefas:
   
   1. Insira um **Nome** para o namespace.
   2. Selecione um tipo de preço. **Básico** é suficiente para este exemplo.
   3. Selecione a **Assinatura** do Azure a utilizar.
   4. Selecione um grupo de recursos existente ou crie um novo.
   5. Selecione a **Localização** para o Hub de Eventos.
   6. Selecione **Fixar no painel** e clique em **Criar**.

3. Quando o processo de criação é concluído, as informações dos Hubs de Eventos para seu namespace são exibidas. Desse local, selecione **+ Adicionar Hub de Eventos**. Na seção **Criar Hub de Eventos**, insira o nome **sensordata** e selecione **Criar**. Mantenha os valores padrão nos outros campos.
4. Na exibição Hubs de Eventos do seu namespace, selecione **Hubs de Eventos**. Selecione a entrada **sensordata** .
5. No Hub de Eventos sensordata, selecione **Políticas de acesso compartilhado**. Use o link **+ Adicionar** para adicionar as políticas a seguir:

    | Nome da política | Declarações |
    | ----- | ----- |
    | dispositivos | Enviar |
    | storm | Escutar |

1. Selecione ambas as políticas e anote o valor **CHAVE PRIMÁRIA** . Você precisará do valor para as duas políticas em etapas futuras.

## <a name="download-and-configure-the-project"></a>Baixe e configure o projeto.

Use o seguinte comando para baixar o projeto do GitHub.

    git clone https://github.com/Blackmist/hdinsight-eventhub-example

Depois que o comando for concluído, você terá a seguinte estrutura de diretório:

    hdinsight-eventhub-example/
        TemperatureMonitor/ - this contains the topology
            resources/
                log4j2.xml - set logging to minimal.
                no-hbase.yaml - topology definition without hbase components.
                with-hbase.yaml - topology definition with hbase components.
            src/main/java/com/microsoft/examples/bolts/
                ParserBolt.java - parses JSON data into tuples
                DashboardBolt.java - sends data over Socket.IO to the web dashboard.
        dashboard/nodejs/ - this is the node.js web dashboard.
        SendEvents/ - utilities to send fake sensor data.

> [!NOTE]
> Este documento não apresenta os detalhes completos do código incluído nesse exemplo. No entanto, o código é totalmente comentado.

Para configurar o projeto para ler no Hub de Eventos, abra o arquivo `hdinsight-eventhub-example/TemperatureMonitor/dev.properties` e adicione as informações do Hub de Eventos às seguintes linhas:

```bash
eventhub.read.policy.name: your_read_policy_name
eventhub.read.policy.key: your_key_here
eventhub.namespace: your_namespace_here
eventhub.name: your_event_hub_name
eventhub.partitions: 2
```

## <a name="compile-and-test-locally"></a>Compile e teste localmente

> [!IMPORTANT]
> Usar a topologia localmente requer um ambiente de desenvolvimento Storm ativo. Para saber mais, confira [Setting up a Storm development environment](http://storm.apache.org/releases/1.1.0/Setting-up-development-environment.html) (Configurando um ambiente de desenvolvimento Storm) em Apache.org.

> [!WARNING]
> Se estiver usando um ambiente de desenvolvimento Windows, você poderá receber uma `java.io.IOException` ao executar a topologia localmente. Nesse caso, passe a executar a topologia no HDInsight.

Antes de testar, você deve iniciar o painel de controle para exibir a saída da topologia e gerar dados para armazenar no Hub de Eventos.

> [!IMPORTANT]
> O componente do HBase dessa topologia não estará ativo durante o teste local. A API do Java para o cluster HBase não pode ser acessada de fora da Rede Virtual do Azure que contém os clusters.

### <a name="start-the-web-application"></a>Iniciar o aplicativo Web

1. Abra um prompt de comando e altere os diretórios para `hdinsight-eventhub-example/dashboard`. Use o seguinte comando para instalar as dependências exigidas pelo aplicativo Web:
   
    ```bash
    npm install
    ```

2. Use o comando abaixo para iniciar o aplicativo Web:
   
    ```bash
    node server.js
    ```
   
    Você verá uma mensagem semelhante ao seguinte texto:
   
        Server listening at port 3000

3. Abra um navegador da Web e insira `http://localhost:3000/` como o endereço. Uma página semelhante à seguinte imagem será exibida:
   
    ![Painel da Web](./media/hdinsight-storm-sensor-data-analysis/emptydashboard.png)
   
    Deixe esse prompt de comando aberto. Após o teste, use Ctrl+C para parar o servidor Web.

### <a name="generate-data"></a>Gerar dados

> [!NOTE]
> As etapas desta seção usam o Node.js para que possam ser usadas em qualquer plataforma. Para obter outros exemplos de linguagem, confira o diretório `SendEvents`.

1. Abra um novo prompt, shell ou terminal e altere os diretórios para `hdinsight-eventhub-example/SendEvents/nodejs`. Para instalar as dependências exigidas pelo aplicativo, use o seguinte comando:

    ```bash
    npm install
    ```

2. Abra o arquivo `app.js` em um editor de texto e adicione as informações do Hub de Eventos obtidas anteriormente:
   
    ```javascript
    // ServiceBus Namespace
    var namespace = 'YourNamespace';
    // Event Hub Name
    var hubname ='sensordata';
    // Shared access Policy name and key (from Event Hub configuration)
    var my_key_name = 'devices';
    var my_key = 'YourKey';
    ```
   
   > [!NOTE]
   > Este exemplo supõe que você usou `sensordata` como o nome do seu Hub de Eventos. E `devices` como o nome da política que tem uma declaração `Send`.

3. Use o seguinte comando para inserir novas entradas no Hub de Eventos:
   
    ```bash
    node app.js
    ```
   
    Você verá várias linhas de saída que contêm os dados enviados para o Hub de Eventos:
   
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"0","Temperature":7}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"1","Temperature":39}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"2","Temperature":86}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"3","Temperature":29}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"4","Temperature":30}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"5","Temperature":5}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"6","Temperature":24}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"7","Temperature":40}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"8","Temperature":43}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"9","Temperature":84}

### <a name="build-and-start-the-topology"></a>Criar e iniciar a topologia

1. Abra um novo prompt de comando e altere os diretórios para `hdinsight-eventhub-example/TemperatureMonitor`. Para criar e empacotar a topologia, use o seguinte comando: 

    ```bash
    mvn clean package
    ```

2. Para iniciar a topologia no modo local, use o seguinte comando:

    ```bash
    storm jar target/TemperatureMonitor-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local --filter dev.properties resources/no-hbase.yaml
    ```

    * `--local` inicia a topologia no modo local.
    * `--filter` usa o arquivo `dev.properties` para popular os parâmetros na definição de topologia.
    * `resources/no-hbase.yaml` usa a definição de topologia `no-hbase.yaml`.
 
   Uma vez iniciada, a topologia lê as entradas do Hub de Eventos e as envia para o painel em execução no computador local. Você deverá ver linhas exibidas no painel da Web semelhantes à seguinte imagem:
   
    ![Painel com dados](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

2. Enquanto o painel é executado, use o comando de `node app.js` das etapas anteriores para enviar novos dados para os Hubs de Eventos. Como os valores de temperatura são gerados aleatoriamente, o gráfico será atualizado para mostrar grandes mudanças de temperatura.
   
   > [!NOTE]
   > Você deve estar no diretório **hdinsight-eventhub-example/SendEvents/Nodejs** ao usar o comando `node app.js`.

3. Depois de verificar se o painel é atualizado, interrompa a topologia usando Ctrl+C. Você também pode usar Ctrl+C para parar o servidor Web local.

## <a name="create-a-storm-and-hbase-cluster"></a>Criar um cluster Storm e HBase

As etapas descritas nesta seção usam um [modelo do Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md) para criar uma Rede Virtual do Azure, bem como um cluster Storm e HBase na rede virtual. O modelo também cria um aplicativo Web e implanta uma cópia do painel nele.

> [!NOTE]
> Uma rede virtual é usada para que a topologia em execução no cluster Storm possa se comunicar diretamente com o cluster HBase usando a API Java do HBase.

O modelo do Resource Manager usado neste documento está localizado em um contêiner de blob público, em **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hbase-storm-cluster-in-vnet-3.6.json**.

1. Clique no botão a seguir para entrar no Azure e abra o modelo do Resource Manager no portal do Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-storm-cluster-in-vnet-3.6.json" target="_blank"><img src="./media/hdinsight-storm-sensor-data-analysis/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Na seção **Implantação personalizada**, insira os seguintes valores:
   
    ![Parâmetros do HDInsight](./media/hdinsight-storm-sensor-data-analysis/parameters.png)
   
   * **Nome do Cluster Base**: esse valor é usado como o nome base para os clusters Storm e HBase. Por exemplo, a inserção de **abc** cria um cluster Storm chamado **storm-abc** e um cluster HBase chamado **hbase-abc**.
   * **Nome de Usuário de Logon do Cluster**: o nome de usuário do administrador para os clusters Storm e HBase.
   * **Senha de Logon do Cluster**: a senha de usuário do administrador para os clusters Storm e HBase.
   * **Nome de Usuário do SSH**: o usuário do SSH a ser criado para os clusters Storm e HBase.
   * **Senha do SSH**: a senha do usuário do SSH para os clusters Storm e HBase.
   * **Local**: a região na qual os clusters são criados.
     
     Clique em **OK** para salvar os parâmetros.

3. Use a seção **Conceitos básicos** para criar um grupo de recursos ou selecione um existente.
4. No menu suspenso **Localização do grupo de recursos**, selecione o mesmo local selecionado para o parâmetro **Localização** na seção **Configurações**.
5. Leia os termos e condições e depois selecione **Eu concordo com os termos e condições declarados acima**.
6. Por fim, marque **Fixar no painel** e selecione **Comprar**. Demora cerca de 20 minutos para criar os clusters.

Depois que os recursos forem criados, as informações sobre o grupo de recursos serão exibidas.

![Grupo de recursos para rede virtual e clusters](./media/hdinsight-storm-sensor-data-analysis/groupblade.png)

> [!IMPORTANT]
> Observe que os nomes dos clusters HDInsight são **storm-BASENAME** e **hbase-BASENAME**, onde BASENAME é o nome fornecido para o modelo. Esses nomes são usados em uma etapa posterior durante a conexão aos clusters. Observe também que o nome do site do painel é **basename-dashboard**. Esse valor é usado posteriormente neste documento.

## <a name="configure-the-dashboard-bolt"></a>Configurar o bolt do Painel

Para enviar dados ao painel implantado como um aplicativo Web, é necessário modificar a seguinte linha no arquivo `dev.properties`:

```yaml
dashboard.uri: http://localhost:3000
```

Altere `http://localhost:3000` para `http://BASENAME-dashboard.azurewebsites.net` e salve o arquivo. Substitua **BASENAME** pelo nome de base que você forneceu na etapa anterior. Você também pode usar o grupo de recursos criado anteriormente para selecionar o painel e exibir o URL.

## <a name="create-the-hbase-table"></a>Criar uma tabela do HBase

Para armazenar dados no HBase, primeiro devemos criar uma tabela. Pré-crie os recursos nos quais o Storm precisa para gravar, pois a tentativa de criação de recursos em uma topologia Storm pode resultar na tentativa de várias cópias criando o mesmo recurso. Crie os recursos fora da topologia e use o Storm para leitura/gravação e análise.

1. Use o SSH para se conectar ao cluster HBase usando o usuário SSH e a senha fornecidos ao modelo durante a criação do cluster. Por exemplo, caso esteja se conectando usando o comando `ssh` , você deve usar a sintaxe a seguir:
   
    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```
   
    Substitua `sshuser` pelo nome de usuário do SSH fornecido na criação do cluster. Substitua `clustername` pelo nome do cluster HBase.

2. Na sessão do SSH, inicie o shell do HBase.
   
    ```bash
    hbase shell
    ```
   
    Depois que o shell for carregado, você verá um prompt `hbase(main):001:0>`.

3. No shell do HBase, insira o seguinte comando para criar uma tabela na qual os dados do sensor serão armazenados:
   
    ```hbase
    create 'SensorData', 'cf'
    ```

4. Verifique se a tabela foi criada usando o seguinte comando:
   
    ```hbase
    scan 'SensorData'
    ```
   
    Isso retorna informações semelhantes ao exemplo a seguir, indicando que há 0 linhas na tabela.
   
        ROW                   COLUMN+CELL                                       0 row(s) in 0.1900 seconds

5. Insira `exit` para sair do shell do HBase:

## <a name="configure-the-hbase-bolt"></a>Configurar o bolt do HBase

Para gravar no HBase usando o cluster Storm, você deve fornecer o bolt do HBase com os detalhes de configuração do cluster HBase.

1. Use um dos seguintes exemplos a fim de recuperar o quorum Zookeeper para o cluster HBase:

    ```bash
    CLUSTERNAME='your_HDInsight_cluster_name'
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/HBASE/components/HBASE_MASTER" | jq '.metrics.hbase.master.ZookeeperQuorum'
    ```

    > [!NOTE]
    > Substitua `your_HDInsight_cluster_name` pelo nome do cluster HDInsight. Para saber mais sobre como instalar o utilitário `jq`, confira [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).
    >
    > Quando solicitado, insira a senha para o logon de administrador do HDInsight.

    ```powershell
    $clusterName = 'your_HDInsight_cluster_name'
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HBASE/components/HBASE_MASTER" -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.metrics.hbase.master.ZookeeperQuorum
    ```

    > [!NOTE]
    > Substitua 'nome_do_cluster_do_HDInsight' pelo nome do seu cluster HDInsight. Quando solicitado, insira a senha para o logon de administrador do HDInsight.
    >
    > Este exemplo exige o Azure PowerShell. Para saber mais sobre como usar o Azure PowerShell, confira [Introdução ao Azure PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/Getting-Started-with-Windows-PowerShell?view=powershell-6)

    As informações retornadas por esses exemplos são semelhantes ao seguinte texto:

    `zk2-hbase.mf0yeg255m4ubit1auvj1tutvh.ex.internal.cloudapp.net:2181,zk0-hbase.mf0yeg255m4ubit1auvj1tutvh.ex.internal.cloudapp.net:2181,zk3-hbase.mf0yeg255m4ubit1auvj1tutvh.ex.internal.cloudapp.net:2181`

    Essas informações são usadas pelo Storm para se comunicar com o cluster HBase.

2. Modifique o arquivo `dev.properties` e adicione as informações do quorum Zookeeper à seguinte linha:

    ```yaml
    hbase.zookeeper.quorum: your_hbase_quorum
    ```

## <a name="build-package-and-deploy-the-solution-to-hdinsight"></a>Criar, empacotar e implantar a solução no HDInsight

No ambiente de desenvolvimento, use as etapas a seguir para implantar a topologia Storm no cluster storm.

1. No diretório `TemperatureMonitor`, use o seguinte comando para executar um novo build e criar um pacote JAR no seu projeto:
   
        mvn clean package
   
    Este comando cria um arquivo chamado `TemperatureMonitor-1.0-SNAPSHOT.jar in the `diretório de destino do projeto.

2. Use scp para fazer upload dos arquivo `TemperatureMonitor-1.0-SNAPSHOT.jar` e `dev.properties` no cluster Storm. No exemplo a seguir, substitua `sshuser` pelo usuário SSH fornecido na criação do cluster e `clustername` pelo nome do cluster Storm. Quando solicitado, insira a senha do usuário SSH.
   
    ```bash
    scp target/TemperatureMonitor-1.0-SNAPSHOT.jar dev.properties sshuser@clustername-ssh.azurehdinsight.net:
    ```

   > [!NOTE]
   > O upload dos arquivos pode levar vários minutos para ser concluído.

    Para saber mais sobre como usar os comandos `scp` e `ssh` com o HDInsight, confira [Usar SSH com HDInsight](./hdinsight-hadoop-linux-use-ssh-unix.md)

3. Depois que o upload do arquivo for concluído, conecte-se ao cluster Storm usando SSH.
   
    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Substitua `sshuser` pelo nome de usuário SSH. Substitua `clustername` pelo nome do cluster Storm.

4. Para iniciar a topologia, use o seguinte comando na sessão do SSH:
   
    ```bash
    storm jar TemperatureMonitor-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote --filter dev.properties -R /with-hbase.yaml
    ```

    * `--remote` envia a topologia ao serviço Nimbus, que a distribui aos nós do supervisor no cluster.
    * `--filter` usa o arquivo `dev.properties` para popular os parâmetros na definição de topologia.
    * `-R /with-hbase.yaml` usa a topologia `with-hbase.yaml` incluída no pacote.

5. Depois que a topologia for iniciada, abra um navegador no site publicado no Azure e use o comando `node app.js` para enviar dados ao Hub de Eventos. Você deve ver a atualização do painel da Web para exibir as informações.
   
    ![painel Transações da Web](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

## <a name="view-hbase-data"></a>Exibir dados do HBase

Use as seguintes etapas para se conectar ao HBase e verificar se os dados foram gravados na tabela:

1. Use SSH para conectar-se ao cluster HBase.
   
    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Substitua `sshuser` pelo nome de usuário SSH. Substitua `clustername` pelo nome do cluster HBase.

2. Na sessão do SSH, inicie o shell do HBase.
   
    ```bash
    hbase shell
    ```
   
    Depois que o shell for carregado, você verá um prompt `hbase(main):001:0>`.

3. Exiba as linhas da tabela:
   
    ```hbase
    scan 'SensorData'
    ```
   
    Esse comando retorna informações semelhantes ao texto a seguir, indicando que há dados na tabela.
   
        hbase(main):002:0> scan 'SensorData'
        ROW                             COLUMN+CELL
        \x00\x00\x00\x00               column=cf:temperature, timestamp=1467290788277, value=\x00\x00\x00\x04
        \x00\x00\x00\x00               column=cf:timestamp, timestamp=1467290788277, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x01               column=cf:temperature, timestamp=1467290788348, value=\x00\x00\x00M
        \x00\x00\x00\x01               column=cf:timestamp, timestamp=1467290788348, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x02               column=cf:temperature, timestamp=1467290788268, value=\x00\x00\x00R
        \x00\x00\x00\x02               column=cf:timestamp, timestamp=1467290788268, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x03               column=cf:temperature, timestamp=1467290788269, value=\x00\x00\x00#
        \x00\x00\x00\x03               column=cf:timestamp, timestamp=1467290788269, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x04               column=cf:temperature, timestamp=1467290788356, value=\x00\x00\x00>
        \x00\x00\x00\x04               column=cf:timestamp, timestamp=1467290788356, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x05               column=cf:temperature, timestamp=1467290788326, value=\x00\x00\x00\x0D
        \x00\x00\x00\x05               column=cf:timestamp, timestamp=1467290788326, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x06               column=cf:temperature, timestamp=1467290788253, value=\x00\x00\x009
        \x00\x00\x00\x06               column=cf:timestamp, timestamp=1467290788253, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x07               column=cf:temperature, timestamp=1467290788229, value=\x00\x00\x00\x12
        \x00\x00\x00\x07               column=cf:timestamp, timestamp=1467290788229, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x08               column=cf:temperature, timestamp=1467290788336, value=\x00\x00\x00\x16
        \x00\x00\x00\x08               column=cf:timestamp, timestamp=1467290788336, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x09               column=cf:temperature, timestamp=1467290788246, value=\x00\x00\x001
        \x00\x00\x00\x09               column=cf:timestamp, timestamp=1467290788246, value=2015-02-10T14:43.05.00320Z
        10 row(s) in 0.1800 seconds
   
   > [!NOTE]
   > Essa operação de exame retorna um máximo de 10 linhas da tabela.

## <a name="delete-your-clusters"></a>Excluir os clusters

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Para excluir os clusters, o repositório e o aplicativo Web de uma vez, exclua o grupo de recursos que os contém.

## <a name="next-steps"></a>Próximas etapas

Para obter mais exemplos de topologias Storm com o HDInsight, consulte [Topologias de exemplo para o Storm no HDInsight](hdinsight-storm-example-topology.md)

Para saber mais sobre o Apache Storm, confira o site [Apache Storm](https://storm.incubator.apache.org/) .

Para saber mais sobre HBase com HDInsight, confira a [Visão geral do HBase com HDInsight](hdinsight-hbase-overview.md)

Para saber mais sobre o Socket.IO, acesse o site [socket.io](http://socket.io/) .

Para saber mais sobre D3.js, confira [D3.js - Documentos controlados por dados](http://d3js.org/)

Para saber mais sobre como criar topologias em Java, confira [Desenvolver topologias Java para Apache Storm no HDInsight](hdinsight-storm-develop-java-topology.md).

Para saber mais sobre como se conectar ao cluster Storm, confira [Desenvolver topologias C# para Apache Storm no HDInsight usando o Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

[azure-portal]: https://portal.azure.com
