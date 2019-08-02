---
title: Usar o Serviço de Kubernetes do Azure com Kafka no HDInsight
description: Saiba como usar o Kafka no HDInsight a partir de imagens de contêiner hospedadas no AKS (Serviço de Kubernetes do Azure).
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: dc2c3e557f295bd3125c09d9b839716159dcf50b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446467"
---
# <a name="use-azure-kubernetes-service-with-apache-kafka-on-hdinsight"></a>Usar o Serviço de Kubernetes do Azure com Apache Kafka no HDInsight

Saiba como usar o AKS (Serviço de Kubernetes do Azure) com [Apache Kafka](https://kafka.apache.org/) no Cluster HDInsight. As etapas neste documento usam um aplicativo Node.js hospedado em AKS para verificar a conectividade com o Kafka. Esse aplicativo usa o pacote [kafka-node](https://www.npmjs.com/package/kafka-node) para comunicar-se com o Kafka. Ele usa [Socket.io](https://socket.io/) para mensagens direcionadas a eventos entre o cliente do navegador e o back-end hospedado em AKS.

O [Apache Kafka](https://kafka.apache.org) é uma plataforma de streaming distribuída de software livre que pode ser usada para compilar pipelines e aplicativos de dados de streaming em tempo real. O Serviço de Kubernetes do Azure gerencia o ambiente Kubernetes hospedado e torna rápido e fácil a implantação de aplicativos em contêineres. Usando uma Rede Virtual do Microsoft Azure, é possível conectar os dois serviços.

> [!NOTE]  
> O foco deste documento está nas etapas necessárias para permitir que o Serviço de Kubernetes do Azure comunique-se com o Kafka no HDInsight. O próprio exemplo é apenas um cliente Kafka básico para demonstrar que a configuração funciona.

## <a name="prerequisites"></a>Pré-requisitos

* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* Uma assinatura do Azure

Este documento assume que você está familiarizado com a criação e utilização dos seguintes serviços do Azure:

* Kafka no HDInsight
* Serviço de Kubernetes do Azure
* Redes Virtuais do Azure

Este documento também considera que você percorreu o tutorial dos [Serviços de Kubernetes do Azure](../../aks/tutorial-kubernetes-prepare-app.md). Este artigo cria um serviço de contêiner, cria um cluster Kubernetes, um registro de contêiner e configura o `kubectl` utilitário.

## <a name="architecture"></a>Arquitetura

### <a name="network-topology"></a>Topologia de rede

Ambos HDInsight e AKS usam uma Rede Virtual do Azure como um contêiner para recursos de computação. Para habilitar a comunicação entre o HDInsight e o AKS, é necessário habilitar a comunicação entre suas redes. As etapas neste documento usam Emparelhamento de Rede Virtual para as redes. Outras conexões, como a VPN, também devem funcionar. Para obter mais informações sobre emparelhamento, consulte o documento [Emparelhamento de Rede Virtual](../../virtual-network/virtual-network-peering-overview.md).


O diagrama a seguir ilustra a topologia de rede usada neste documento:

![HDInsight em uma rede virtual, AKS em outra, e as redes conectadas usando emparelhamento](./media/apache-kafka-azure-container-services/kafka-aks-architecture.png)

> [!IMPORTANT]  
> A resolução de nomes não está habilitada entre as redes emparelhadas, portanto, o endereçamento IP é usado. Por padrão, o Kafka no HDInsight é configurado para retornar nomes de host em vez de endereços IP quando os clientes se conectam. As etapas neste documento modificam o Kafka para usar Anúncio IP.

## <a name="create-an-azure-kubernetes-service-aks"></a>Criar um Serviço de Kubernetes do Azure (AKS)

Se você ainda não possui um cluster do AKS, use um dos documentos a seguir para saber como criar um:

* [Implantar um cluster do Serviço de Kubernetes do Azure (AKS) - Portal](../../aks/kubernetes-walkthrough-portal.md)
* [Implantar um cluster do Serviço de Kubernetes do Azure (AKS) - CLI](../../aks/kubernetes-walkthrough.md)

> [!NOTE]  
> O AKS cria uma rede virtual durante a instalação. Esta rede é emparelhada àquela criada para o HDInsight na próxima seção.

## <a name="configure-virtual-network-peering"></a>Configure emparelhamento de rede virtual

1. Do [Portal do Azure](https://portal.azure.com), selecione __Grupos de Recursos__ e, em seguida, localize o grupo de recursos que contém a rede virtual para o seu cluster do AKS. O nome do grupo de recursos é `MC_<resourcegroup>_<akscluster>_<location>`. As entradas `resourcegroup` e `akscluster` são o nome do grupo de recursos onde você criou o cluster e o nome do cluster. `location` é o local onde cluster foi criado.

2. No grupo de recursos, selecione o recurso __Rede virtual__.

3. Selecione __Espaço de endereço__. Observe o espaço de endereço listado.

4. Para criar uma rede virtual para HDInsight, selecione __+ Criar um recurso__, __Rede__ e, em seguida, __Rede virtual__.

    > [!IMPORTANT]  
    > Ao inserir os valores para a nova rede virtual, será necessário usar um espaço de endereço que não sobreponha o utilizado pela rede de cluster do AKS.

    Use o mesmo __Local__ da rede virtual utilizada para o cluster do AKS.

    Antes de ir para a próxima etapa, aguarde até que a rede virtual seja criada.

5. Para configurar o emparelhamento entre a rede do HDInsight e a rede de cluster do AKS, selecione a rede virtual e selecione __Emparelhamentos__. Selecione __+ Adicionar__ e use os seguintes valores para preencher o formulário:

   * __Nome__: Insira um nome exclusivo para essa configuração de emparelhamento.
   * __Rede virtual__: Use esse campo para selecionar a rede virtual para o **cluster do AKS**.

     Deixe todos os outros campos no valor padrão, em seguida, selecione __OK__ para configurar o emparelhamento.

6. Para configurar o emparelhamento entre a rede de cluster do AKS e a rede do HDInsight, selecione __rede virtual de cluster do AKS__ e, em seguida, selecione __Emparelhamentos__. Selecione __+ Adicionar__ e use os seguintes valores para preencher o formulário:

   * __Nome__: Insira um nome exclusivo para essa configuração de emparelhamento.
   * __Rede virtual__: Use esse campo para selecionar a rede virtual para o __cluster do HDInsight__.

     Deixe todos os outros campos no valor padrão, em seguida, selecione __OK__ para configurar o emparelhamento.

## <a name="install-apache-kafka-on-hdinsight"></a>Instalar o Apache Kafka no HDInsight

Ao criar o Kafka no Cluster HDInsight, será necessário unir à rede virtual criada anteriormente para o HDInsight. Para obter mais informações sobre como criar um cluster do Kafka, consulte o documento [Criar um cluster do Apache Kafka](apache-kafka-get-started.md).

> [!IMPORTANT]  
> Ao criar o cluster, será necessário usar as __Configurações avançadas__ para unir à rede virtual criada para o HDInsight.

## <a name="configure-apache-kafka-ip-advertising"></a>Configurar Anúncio IP do Apache Kafka

Use as etapas a seguir para configurar o Kafka para anunciar endereços IP em vez de nomes de domínio:

1. Usando um navegador da Web, acesse https://CLUSTERNAME.azurehdinsight.net. Substitua __NOMEDOCLUSTER__ pelo nome do Kafka no cluster HDInsight.

    Quando solicitado, use o nome de usuário e a senha HTTPS para o cluster. A Interface de Usuário Ambari Web para o cluster é exibida.

2. Para exibir informações sobre o Kafka, selecione __Kafka__ na lista à esquerda.

    ![Lista de serviços com Kafka realçado](./media/apache-kafka-azure-container-services/select-kafka-service.png)

3. Para exibir a configuração do Kafka, selecione __Configurações__ na parte central superior.

    ![Links de Configurações para Kafka](./media/apache-kafka-azure-container-services/select-kafka-config.png)

4. Para localizar a configuração __kafka-env__, digite `kafka-env` no campo __Filtro__ na parte superior direita.

    ![Configuração do Kafka, para kafka-env](./media/apache-kafka-azure-container-services/search-for-kafka-env.png)

5. Para configurar o Kafka para anunciar endereços IP, adicione o seguinte texto à parte inferior do campo __kafka-env-template__:

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Para configurar a interface na qual o Kafka escuta, digite `listeners` no campo __Filtro__ na parte superior direita.

7. Para configurar o Kafka para escutar em todas as interfaces de rede, altere o valor no campo __ouvintes__ para `PLAINTEXT://0.0.0.0:9092`.

8. Para salvar as alterações de configuração, use o botão __Salvar__. Digite uma mensagem de texto que descreva as alterações. Selecione __OK__ assim que as alterações tiverem sido salvas.

    ![Botão Salvar configuração](./media/apache-kafka-azure-container-services/save-button.png)

9. Para evitar erros ao reiniciar o Kafka, use o botão __Ações de Serviço__ e selecione __Ativar o Modo de Manutenção__. Selecione OK para concluir essa operação.

    ![Ações de serviço, com ativar manutenção realçada](./media/apache-kafka-azure-container-services/turn-on-maintenance-mode.png)

10. Para reiniciar o Kafka, use o botão __Reiniciar__ e selecione __Reiniciar Todos os Afetados__. Confirme a reinicialização e, em seguida, use o botão __OK__ depois que a operação for concluída.

    ![Botão Reiniciar com reiniciar todos os afetados realçada](./media/apache-kafka-azure-container-services/restart-button.png)

11. Para desabilitar o modo de manutenção, use o botão __Ações de Serviço__ e selecione __Ativar o Modo de Manutenção__. Selecione **OK** para concluir essa operação.

## <a name="test-the-configuration"></a>Testar a configuração

Neste ponto, o Kafka e o Serviço de Kubernetes do Azure estão em comunicação através das redes virtuais emparelhadas. Para testar essa conexão, use as etapas a seguir:

1. Crie um tópico do Kafka que será usado pelo aplicativo de teste. Para obter informações sobre a criação de tópicos do Apache Kafka, consulte o documento [Criar um cluster do Kafka](apache-kafka-get-started.md).

2. Faça o download do aplicativo de exemplo em [https://github.com/Blackmist/Kafka-AKS-Test](https://github.com/Blackmist/Kafka-AKS-Test).

3. Edite o arquivo `index.js` e altere as linhas a seguir:

    * `var topic = 'mytopic'`: Substituir `mytopic` pelo nome do tópico do Kafka usado por esse aplicativo.
    * `var brokerHost = '176.16.0.13:9092`: Substituir `176.16.0.13` pelo endereço IP interno de um dos hosts agentes para o cluster.

        Para localizar o endereço IP interno dos hosts agentes (workernodes) no cluster, consulte o documento [API REST Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-internal-ip-address-of-cluster-nodes). Escolha o endereço IP de uma das entradas onde o nome de domínio inicia com `wn`.

4. A partir de uma linha de comando no diretório `src` instale dependências e use o Docker para compilar uma imagem para implantação:

    ```bash
    docker build -t kafka-aks-test .
    ```

    > [!NOTE]  
    > Os pacotes exigidos por esse aplicativo são verificados no repositório, portanto, não é necessário usar o utilitário `npm` para instalá-los.

5. Faça logon no ACR (Registro de Contêiner do Azure) e localize o nome de loginServer:

    ```bash
    az acr login --name <acrName>
    az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
    ```

    > [!NOTE]  
    > Se você não souber o nome do Registro de Contêiner do Azure ou não estiver familiarizado com o uso da CLI do Azure para trabalhar com o Serviço de Kubernetes do Azure, consulte os [tutoriais do AKS](../../aks/tutorial-kubernetes-prepare-app.md).

6. Marque a imagem local `kafka-aks-test` com o loginServer do seu ACR. Além disso, adicione `:v1` ao final para indicar a versão da imagem:

    ```bash
    docker tag kafka-aks-test <acrLoginServer>/kafka-aks-test:v1
    ```

7. Efetue push da imagem para o registro:

    ```bash
    docker push <acrLoginServer>/kafka-aks-test:v1
    ```
    Essa operação leva vários minutos para ser concluída.

8. Edite o arquivo de manifesto Kubernetes (`kafka-aks-test.yaml`) e substitua `microsoft` pelo nome loginServer do ACR recuperado na etapa 4.

9. Use o comando a seguir para implantar as configurações do aplicativo a partir do manifesto:

    ```bash
    kubectl create -f kafka-aks-test.yaml
    ```

10. Use o seguinte comando para assistir o `EXTERNAL-IP` do aplicativo:

    ```bash
    kubectl get service kafka-aks-test --watch
    ```

    Quando um endereço IP externo for atribuído, use __CTRL + C__ para sair da inspeção

11. Abra um navegador da Web e digite o endereço IP externo para o serviço. Uma página semelhante à imagem a seguir será aberta:

    ![Imagem da página da web](./media/apache-kafka-azure-container-services/test-web-page.png)

12. Digite o texto no campo e selecione o botão __Enviar__. Os dados são enviados para o Kafka. Em seguida, o consumidor do Kafka no aplicativo lê a mensagem e adiciona-a à seção __Mensagens do Kafka__.

    > [!WARNING]  
    > Você pode receber várias cópias de uma mensagem. Esse problema geralmente ocorre ao atualizar o navegador após a conexão ou abrir várias conexões do navegador para o aplicativo.

## <a name="next-steps"></a>Próximas etapas

Use os links a seguir para aprender a usar o Apache Kafka no HDInsight:

* [Introdução ao Apache Kafka no HDInsight](apache-kafka-get-started.md)

* [Use MirrorMaker para criar uma réplica de Apache Kafka no HDInsight](apache-kafka-mirroring.md)

* [Usar o Apache Storm com o Apache Kafka no HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Usar o Apache Spark com o Apache Kafka no HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Conectar-se ao Apache Kafka por meio de uma Rede Virtual do Azure](apache-kafka-connect-vpn-gateway.md)
