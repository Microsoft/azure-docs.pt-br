---
title: Usar o Serviço de Kubernetes do Azure com Kafka no HDInsight
description: Saiba como usar o Kafka no HDInsight a partir de imagens de contêiner hospedadas no AKS (Serviço de Kubernetes do Azure).
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: 2216eb5893b77761f4d31c5819d152ceeb985abc
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869643"
---
# <a name="use-azure-kubernetes-service-with-apache-kafka-on-hdinsight"></a>Usar o Serviço de Kubernetes do Azure com Apache Kafka no HDInsight

Saiba como usar o AKS (Serviço de Kubernetes do Azure) com [Apache Kafka](https://kafka.apache.org/) no Cluster HDInsight. As etapas neste documento usam um aplicativo Node.js hospedado em AKS para verificar a conectividade com o Kafka. Esse aplicativo usa o pacote [kafka-node](https://www.npmjs.com/package/kafka-node) para comunicar-se com o Kafka. Ele usa [Socket.io](https://socket.io/) para mensagens direcionadas a eventos entre o cliente do navegador e o back-end hospedado em AKS.

O [Apache Kafka](https://kafka.apache.org) é uma plataforma de streaming distribuída de software livre que pode ser usada para compilar pipelines e aplicativos de dados de streaming em tempo real. O Serviço de Kubernetes do Azure gerencia o ambiente Kubernetes hospedado e torna rápido e fácil a implantação de aplicativos em contêineres. Usando uma Rede Virtual do Microsoft Azure, é possível conectar os dois serviços.

> [!NOTE]  
> O foco deste documento está nas etapas necessárias para permitir que o Serviço de Kubernetes do Azure comunique-se com o Kafka no HDInsight. O próprio exemplo é apenas um cliente Kafka básico para demonstrar que a configuração funciona.

## <a name="prerequisites"></a>Pré-requisitos

* [CLI do Azure](/cli/azure/install-azure-cli)
* Uma assinatura do Azure

Este documento assume que você está familiarizado com a criação e utilização dos seguintes serviços do Azure:

* Kafka no HDInsight
* Serviço de Kubernetes do Azure
* Redes Virtuais do Azure

Este documento também considera que você percorreu o tutorial dos [Serviços de Kubernetes do Azure](../../aks/tutorial-kubernetes-prepare-app.md). Este artigo cria um serviço de contêiner, um cluster Kubernetes e um registro de contêiner, além de configurar o utilitário `kubectl`.

## <a name="architecture"></a>Arquitetura

### <a name="network-topology"></a>Topologia de rede

Ambos HDInsight e AKS usam uma Rede Virtual do Azure como um contêiner para recursos de computação. Para habilitar a comunicação entre o HDInsight e o AKS, é necessário habilitar a comunicação entre suas redes. As etapas neste documento usam Emparelhamento de Rede Virtual para as redes. Outras conexões, como a VPN, também devem funcionar. Para obter mais informações sobre emparelhamento, consulte o documento [Emparelhamento de Rede Virtual](../../virtual-network/virtual-network-peering-overview.md).

O diagrama a seguir ilustra a topologia de rede usada neste documento:

:::image type="content" source="./media/apache-kafka-azure-container-services/kafka-aks-architecture.png" alt-text="HDInsight em uma rede virtual, AKS em outra, usando o emparelhamento" border="false":::

> [!IMPORTANT]  
> A resolução de nomes não está habilitada entre as redes emparelhadas, portanto, o endereçamento IP é usado. Por padrão, o Kafka no HDInsight é configurado para retornar nomes de host em vez de endereços IP quando os clientes se conectam. As etapas neste documento modificam o Kafka para usar Anúncio IP.

## <a name="create-an-azure-kubernetes-service-aks"></a>Criar um Serviço de Kubernetes do Azure (AKS)

Se você ainda não possui um cluster do AKS, use um dos documentos a seguir para saber como criar um:

* [Implantar um cluster do Serviço de Kubernetes do Azure (AKS) - Portal](../../aks/kubernetes-walkthrough-portal.md)
* [Implantar um cluster do Serviço de Kubernetes do Azure (AKS) - CLI](../../aks/kubernetes-walkthrough.md)

> [!IMPORTANT]  
> O AKS cria uma rede virtual durante a instalação em um grupo de recursos **adicional**. O grupo de recursos adicional segue a convenção de nomenclatura do **MC_resourceGroup_AKSclusterName_location**.  
> Esta rede é emparelhada àquela criada para o HDInsight na próxima seção.

## <a name="configure-virtual-network-peering"></a>Configure emparelhamento de rede virtual

### <a name="identify-preliminary-information"></a>Identificar informações preliminares

1. No [portal do Azure](https://portal.azure.com), localize o **Grupo de recursos** adicional que contém a rede virtual para o cluster do AKS.

2. No grupo de recursos, selecione o recurso __Rede virtual__. Anote o nome para usá-lo mais tarde.

3. Em **Configurações**, selecione __Espaço de endereço__. Observe o espaço de endereço listado.

### <a name="create-virtual-network"></a>Criar rede virtual

1. Para criar uma rede virtual para o HDInsight, navegue até __+ Criar um recurso__ > __Rede__ > __Rede virtual__.

1. Crie a rede usando as seguintes diretrizes para determinadas propriedades:

    |Propriedade | Valor |
    |---|---|
    |Espaço de endereço|Você precisará usar um espaço de endereço que não se sobreponha ao usado pela rede de cluster do AKS.|
    |Location|Use o mesmo __Local__ da rede virtual utilizada para o cluster do AKS.|

1. Antes de ir para a próxima etapa, aguarde até que a rede virtual seja criada.

### <a name="configure-peering"></a>Configurar o emparelhamento

1. Para configurar o emparelhamento entre a rede do HDInsight e a rede de cluster do AKS, selecione a rede virtual e selecione __Emparelhamentos__.

1. Selecione __+ Adicionar__ e use os seguintes valores para preencher o formulário:

    |Propriedade |Valor |
    |---|---|
    |Nome do emparelhamento de \<this VN> para rede virtual remota|Insira um nome exclusivo para essa configuração de emparelhamento.|
    |Rede virtual|selecione a rede virtual para o **cluster do AKS**.|
    |Nome do emparelhamento de \<AKS VN> para \<this VN>|Insira um nome exclusivo.|

    Deixe todos os outros campos no valor padrão, em seguida, selecione __OK__ para configurar o emparelhamento.

## <a name="create-apache-kafka-cluster-on-hdinsight"></a>Criar um cluster do Apache Kafka no HDInsight

Ao criar o Kafka no Cluster HDInsight, será necessário unir à rede virtual criada anteriormente para o HDInsight. Para obter mais informações sobre como criar um cluster do Kafka, consulte o documento [Criar um cluster do Apache Kafka](apache-kafka-get-started.md).

## <a name="configure-apache-kafka-ip-advertising"></a>Configurar Anúncio IP do Apache Kafka

Use as etapas a seguir para configurar o Kafka para anunciar endereços IP em vez de nomes de domínio:

1. Usando um navegador da Web, acesse `https://CLUSTERNAME.azurehdinsight.net`. Substitua NOMEDOCLUSTER pelo nome do Kafka no cluster HDInsight.

    Quando solicitado, use o nome de usuário e a senha HTTPS para o cluster. A Interface de Usuário Ambari Web para o cluster é exibida.

2. Para exibir informações sobre o Kafka, selecione __Kafka__ na lista à esquerda.

    :::image type="content" source="./media/apache-kafka-azure-container-services/select-kafka-service.png" alt-text="Lista de serviços com Kafka realçado" border="true":::

3. Para exibir a configuração do Kafka, selecione __Configurações__ na parte central superior.

    :::image type="content" source="./media/apache-kafka-azure-container-services/select-kafka-config1.png" alt-text="Configuração de serviços do Apache Ambari" border="true":::

4. Para localizar a configuração __kafka-env__, digite `kafka-env` no campo __Filtro__ na parte superior direita.

    :::image type="content" source="./media/apache-kafka-azure-container-services/search-for-kafka-env.png" alt-text="Configuração do Kafka, para kafka-env" border="true":::

5. Para configurar o Kafka para anunciar endereços IP, adicione o seguinte texto à parte inferior do campo __kafka-env-template__:

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Para configurar a interface na qual o Kafka escuta, digite `listeners` no campo __Filtro__ na parte superior direita.

7. Para configurar o Kafka para escutar em todas as interfaces de rede, altere o valor no campo __ouvintes__ para `PLAINTEXT://0.0.0.0:9092`.

8. Para salvar as alterações de configuração, use o botão __Salvar__. Digite uma mensagem de texto que descreva as alterações. Selecione __OK__ assim que as alterações tiverem sido salvas.

    :::image type="content" source="./media/apache-kafka-azure-container-services/save-configuration-button.png" alt-text="Salvar configuração do Apache Ambari" border="true":::

9. Para evitar erros ao reiniciar o Kafka, use o botão __Ações de Serviço__ e selecione __Ativar o Modo de Manutenção__. Selecione OK para concluir essa operação.

    :::image type="content" source="./media/apache-kafka-azure-container-services/turn-on-maintenance-mode.png" alt-text="Ações de serviço, com ativar manutenção realçada" border="true":::

10. Para reiniciar o Kafka, use o botão __Reiniciar__ e selecione __Reiniciar Todos os Afetados__. Confirme a reinicialização e, em seguida, use o botão __OK__ depois que a operação for concluída.

    :::image type="content" source="./media/apache-kafka-azure-container-services/restart-required-button.png" alt-text="Botão Reiniciar com reiniciar todos os afetados realçada" border="true":::

11. Para desabilitar o modo de manutenção, use o botão __Ações de Serviço__ e selecione __Ativar o Modo de Manutenção__. Selecione **OK** para concluir essa operação.

## <a name="test-the-configuration"></a>Testar a configuração

Neste ponto, o Kafka e o Serviço de Kubernetes do Azure estão em comunicação através das redes virtuais emparelhadas. Para testar essa conexão, use as etapas a seguir:

1. Crie um tópico do Kafka que será usado pelo aplicativo de teste. Para obter informações sobre a criação de tópicos do Apache Kafka, consulte o documento [Criar um cluster do Kafka](apache-kafka-get-started.md).

2. Faça o download do aplicativo de exemplo em [https://github.com/Blackmist/Kafka-AKS-Test](https://github.com/Blackmist/Kafka-AKS-Test).

3. Edite o arquivo `index.js` e altere as linhas a seguir:

    * `var topic = 'mytopic'`: Substituir `mytopic` pelo nome do tópico do Kafka usado por esse aplicativo.
    * `var brokerHost = '176.16.0.13:9092`: Substituir `176.16.0.13` pelo endereço IP interno de um dos hosts agentes para o cluster.

        Para localizar o endereço IP interno dos hosts agentes (workernodes) no cluster, consulte o documento [API REST Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-internal-ip-address-of-cluster-nodes). Escolha o endereço IP de uma das entradas onde o nome de domínio inicia com `wn`.

4. A partir de uma linha de comando no diretório `src` instale dependências e use o Docker para compilar uma imagem para implantação:

    ```bash
    docker build -t kafka-aks-test .
    ```

    > [!NOTE]  
    > Os pacotes exigidos por esse aplicativo são verificados no repositório, portanto, não é necessário usar o utilitário `npm` para instalá-los.

5. Faça logon no ACR (Registro de Contêiner do Azure) e localize o nome de loginServer:

    ```azurecli
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

    :::image type="content" source="./media/apache-kafka-azure-container-services/test-web-page-image1.png" alt-text="Imagem da página da Web de teste do Apache Kafka" border="true":::

12. Digite o texto no campo e selecione o botão __Enviar__. Os dados são enviados para o Kafka. Em seguida, o consumidor do Kafka no aplicativo lê a mensagem e adiciona-a à seção __Mensagens do Kafka__.

    > [!WARNING]  
    > Você pode receber várias cópias de uma mensagem. Esse problema geralmente ocorre ao atualizar o navegador após a conexão ou abrir várias conexões do navegador para o aplicativo.

## <a name="next-steps"></a>Próximas etapas

Use os links a seguir para aprender a usar o Apache Kafka no HDInsight:

* [Introdução ao Apache Kafka no HDInsight](apache-kafka-get-started.md)

* [Use MirrorMaker para criar uma réplica de Apache Kafka no HDInsight](apache-kafka-mirroring.md)

* [Use o Apache Storm com o Apache Kafka no HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Usar o Apache Spark com o Apache Kafka no HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Conectar-se ao Apache Kafka por meio de uma Rede Virtual do Azure](apache-kafka-connect-vpn-gateway.md)
