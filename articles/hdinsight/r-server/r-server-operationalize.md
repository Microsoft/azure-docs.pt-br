---
title: Operacionalizar o ML Services no HDInsight - Azure
description: Saiba como colocar seu modelo de dados em operação para fazer previsões com os serviços de ML no Azure HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/27/2018
ms.openlocfilehash: c90642e58c026c78ce854e7fe74dd36963d48b67
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944002"
---
# <a name="operationalize-ml-services-cluster-on-azure-hdinsight"></a>Operacionalizar o cluster do ML Services no Azure HDInsight

Após usar o cluster do ML Services no HDInsight para concluir a modelagem de dados, você poderá operacionalizar o modelo para fazer previsões. Esse artigo fornece instruções sobre como fazer essa tarefa.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster dos Serviços de ML no HDInsight. Veja [Criar clusters Apache Hadoop usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **Serviços de ML** como **Tipo de cluster**.

* Um cliente Secure Shell (SSH): um cliente SSH é usado para se conectar ao cluster HDInsight remotamente e executar comandos diretamente no cluster. Para obter mais informações, confira [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="operationalize-ml-services-cluster-with-one-box-configuration"></a>Operacionalizar o cluster do ML Services com configuração de uma caixa

> [!NOTE]  
> As etapas abaixo aplicam-se ao Microsoft R Server 9.0 e ML Server 9.1. Para o ML Server 9.3, consulte [Usar a ferramenta de administração para gerenciar a configuração de operacionalização](/machine-learning-server/operationalize/configure-admin-cli-launch).

1. SSH no nó de borda.

    ```bash
    ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net
    ```

    Para obter instruções sobre como usar SSH com o Azure HDInsight, consulte [usar SSH com HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Altere o diretório para a versão relevante e sudo do dll dot net: 

    - Para Microsoft ML Server 9.1:

        ```bash
        cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
        sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll
        ```

    - Para o Microsoft R Server 9.0:

        ```bash
        cd /usr/lib64/microsoft-deployr/9.0.1
        sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll
        ```

1. São apresentadas as opções para escolher. Escolha a primeira opção, conforme mostrado na captura de tela a seguir, para **Configurar o ML Server para operacionalização**.

    ![Seleção do utilitário de administração do R Server](./media/r-server-operationalize/admin-util-one-box-1.png)

1. Agora, é possível escolher como você quer operacionalizar o ML Server. A partir das opções apresentadas, escolha a primeiro inserindo **A**.

    ![Operacionalização do utilitário de administração do R Server](./media/r-server-operationalize/admin-util-one-box-2.png)

1. Quando solicitado, digite e insira novamente a senha para um usuário de administrador local.

1. Você deve ver sugestões de saídas que a operação foi bem-sucedida. Você também precisará selecionar outra opção de menu. Selecione E para retornar ao menu principal.

    ![Êxito do utilitário de administração do R Server](./media/r-server-operationalize/admin-util-one-box-3.png)

1. Como opção, você pode executar verificações de diagnóstico com um teste de diagnóstico da seguinte forma:

    a. No menu principal, selecione **6** para executar testes de diagnóstico.

    ![Diagnóstico do utilitário de administração do R Server](./media/r-server-operationalize/hdinsight-diagnostic1.png)

    b. No menu testes de diagnóstico, selecione **um**. Quando solicitado, insira a senha que você forneceu para o usuário administrador local.

    ![Teste do utilitário de administração do servidor R](./media/r-server-operationalize/hdinsight-diagnostic2.png)

    c. Verifique se a saída mostra que a integridade geral é uma passagem.

    ![Passagem do utilitário de administração do R Server](./media/r-server-operationalize/hdinsight-diagnostic3.png)

    d. Nas opções de menu apresentadas, insira **E** para retornar ao menu principal e, em seguida, digite **8** para sair do utilitário de administração.

### <a name="long-delays-when-consuming-web-service-on-apache-spark"></a>Atrasos longos ao consumir um serviço web no Apache Spark

Se você tiver longos atrasos ao tentar consumir um serviço web criado com as funções mrsdeploy no contexto de computação do Apache Spark, talvez seja necessário adicionar algumas pastas ausentes. O aplicativo Spark pertence a um usuário chamado '*rserve2*' sempre que ele é chamado de um serviço web usando as funções mrsdeploy. Para contornar este problema:

```r
# Create these required folders for user 'rserve2' in local and hdfs:

hadoop fs -mkdir /user/RevoShare/rserve2
hadoop fs -chmod 777 /user/RevoShare/rserve2

mkdir /var/RevoShare/rserve2
chmod 777 /var/RevoShare/rserve2


# Next, create a new Spark compute context:

rxSparkConnect(reset = TRUE)
```

Nesse estágio, a configuração de operacionalização foi concluída. Agora, você pode usar o pacote `mrsdeploy`em seu RClient para conectar a operacionalização no nó de borda e começar a usar seus recursos como a [execução remota](/machine-learning-server/r/how-to-execute-code-remotely) e os [serviços web](/machine-learning-server/operationalize/concept-what-are-web-services). A depender se o cluster está configurado em uma rede virtual ou não, você pode precisar configurar o túnel de encaminhamento da porta por meio de logon de SSH. As seções a seguir explicam como configurar esse túnel.

### <a name="ml-services-cluster-on-virtual-network"></a>Cluster do ML Service na rede virtual

Lembre-se de permitir o tráfego pela porta 12800 para o nó de borda. Dessa forma, você pode usar o nó de borda para se conectar ao recurso de operacionalização.

```r
library(mrsdeploy)

remoteLogin(
    deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
    username = "admin",
    password = "xxxxxxx"
)
```

Se o `remoteLogin()` não puder se conectar ao nó de borda, mas for possível executar o SSH para o nó de borda, você precisará verificar se a regra que permite o tráfego na porta 12800 foi configurada corretamente. Caso o problema persista, você pode usar uma solução alternativa configurando o túnel de encaminhamento da porta por meio de SSH. Para obter instruções, consulte a seção a seguir:

### <a name="ml-services-cluster-not-set-up-on-virtual-network"></a>Cluster do ML Services não configurado na rede virtual

Se o cluster não estiver configurado na rede virtual ou se você estiver tendo problemas com a conectividade por meio da rede virtual, use o túnel SSH de encaminhamento da porta:

```bash
ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net
```

Depois que a sessão SSH estiver ativa, o tráfego da porta local 12800 da máquina será encaminhado para a porta 12800 do nó de borda por meio de uma sessão SSH. Use `127.0.0.1:12800` no seu método `remoteLogin()`. Isso faz logon na operacionalização do nó de borda por meio do encaminhamento de porta.

```r
library(mrsdeploy)

remoteLogin(
    deployr_endpoint = "http://127.0.0.1:12800",
    username = "admin",
    password = "xxxxxxx"
)
```

## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>Nós de computação operacional de dimensionamento nos nós de trabalho do HDInsight

Para expandir os nós de computação, desativa os nós de trabalho e, em seguida, configure os nós de computação nos nós de trabalho desativados.

### <a name="step-1-decommission-the-worker-nodes"></a>Etapa 1: Desativar os nós de trabalho

O cluster do ML Services não é gerenciado pelo [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html). Se os nós de trabalho não forem encerrados, o Gerenciador de Recursos YARN não funcionará como esperado porque não estará ciente dos recursos consumidos pelo servidor. Para evitar essa situação,recomendamos desativar os nós de trabalho antes de expandir os nós de computação.

Siga estas etapas para desativar os nós de trabalho:

1. Faça logon no console de Ambari do cluster e clique na guia **Hosts**.

1. Selecione nós de trabalho (para ser desativado).

1. Clique em **ações**  >  **hosts selecionados**  >  **hosts**  >  **ativar o modo de manutenção**. Por exemplo, na imagem a seguir, selecionamos wn3 e wn4 para desativar.  

   ![O Apache Ambari ativa o modo de manutenção](./media/r-server-operationalize/get-started-operationalization.png)  

* Selecione **ações**  >  **hosts selecionados**  >  **datanodes** > clique em **desativar**.
* Selecione **ações**  >  **hosts selecionados**  >  **NodeManagers** > clique em **desativar**.
* Selecione **ações**  >  **hosts selecionados**  >  **nós** de > clique em **parar**.
* Selecione **ações**  >  **hosts selecionados**  >  **NodeManagers** > clique em **parar**.
* Selecione **ações**  >  **hosts selecionados**  >  **hosts** > clique em **parar todos os componentes**.
* Desmarque os nós de trabalho e selecione os nós de cabeçalho.
* Selecione **ações**  >  **hosts selecionados** > "**hosts**  >  **reiniciar todos os componentes**.

### <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Etapa 2: Configurar nós de computação em cada nó de trabalho encerrado

1. SSH em cada nó de trabalho desativado.

1. Execute o utilitário admin usando a DLL relevante para o cluster do ML Services existente. Para ML Server 9.1, execute o seguinte:

    ```bash
    dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll
    ```

1. Insira **1** para selecionar a opção **Configurar o ML Server para operacionalização**.

1. Insira **C** para selecionar a opção `C. Compute node`. Isso configura o nó de computação no nó de trabalho.

1. Saia do utilitário de administração.

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>Etapa 3: adicionar detalhes de nós de computação no nó da Web

Depois que todos os nós de trabalho encerrados forem configurados para executar o nó de computação, volte ao nó de borda e adicione os endereços IP dos nós de trabalho encerrados na configuração do nó de web do ML Server:

1. SSH no nó de borda.

1. Execute `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.

1. Procure a seção "URIs" e adicione o IP do nó de trabalho e detalhes de porta.

    ```json
    "Uris": {
        "Description": "Update 'Values' section to point to your backend machines. Using HTTPS is highly recommended",
        "Values": [
            "http://localhost:12805", "http://[worker-node1-ip]:12805", "http://[workder-node2-ip]:12805"
        ]
    }
    ```

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar o cluster do ML Services no HDInsight](r-server-hdinsight-manage.md)
* [Opções de contexto de computação para cluster do ML Services no HDInsight](r-server-compute-contexts.md)
* [Opções de Armazenamento do Microsoft Azure para cluster do ML Services no HDInsight](r-server-storage.md)