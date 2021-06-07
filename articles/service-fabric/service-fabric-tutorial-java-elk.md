---
title: Monitorar seus aplicativos no Service Fabric usando ELK no Azure
description: Neste tutorial, saiba como configurar o ELK e monitorar os aplicativos do Service Fabric.
ms.topic: tutorial
ms.date: 02/26/2018
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 5faf26230618161a5b908c9a544a43ec5c33b807
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91532015"
---
# <a name="tutorial-monitor-your-service-fabric-applications-using-elk"></a>Tutorial: Monitorar seus aplicativos do Service Fabric usando ELK

Este tutorial é a parte quatro de uma série. Ele mostra como usar ELK (Elasticsearch, Logstash e Kibana) para monitorar aplicativos do Service Fabric em execução no Azure.

Na primeira parte da série, você aprende a:
> [!div class="checklist"]
> * Configurar o servidor ELK no Azure
> * Configurar Logstash para receber os logs dos Hubs de Eventos
> * Visualizar os logs de aplicativo e de plataforma no Kibana

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * [Compilar um aplicativo Java do Reliable Services no Service Fabric](service-fabric-tutorial-create-java-app.md)
> * [Implantar e depurar o aplicativo em um cluster local](service-fabric-tutorial-debug-log-local-cluster.md)
> * [Implantar o aplicativo em um cluster do Azure](service-fabric-tutorial-java-deploy-azure.md)
> * Configurar o monitoramento e o diagnóstico do aplicativo
> * [Configurar CI/CD](service-fabric-tutorial-java-jenkins.md)

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se você não tem uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Configurar seu aplicativo para emitir logs para o local especificado na [parte dois](service-fabric-tutorial-debug-log-local-cluster.md).
* Complete a [parte três](service-fabric-tutorial-java-deploy-azure.md) e obtenha um cluster do Service Fabric em execução configurado para enviar logs aos Hubs de Eventos.
* A política em Hubs de Eventos que tem a permissão "Escutar" e a chave primária associada da série três.

## <a name="download-the-voting-sample-application"></a>Baixar o aplicativo de exemplo Votação

Se você não tiver criado o aplicativo de exemplo Votação na [parte um esta série de tutoriais](service-fabric-tutorial-create-java-app.md), poderá baixá-lo. Em uma janela de comando, execute o comando a seguir para clonar o repositório de aplicativos de exemplo no computador local.

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart
```

## <a name="create-an-elk-server-in-azure"></a>Criar um servidor ELK no Azure

Você pode usar um ambiente ELK pré-configurado para este tutorial e, se você já tiver um, vá para a seção **Configuração do Logstash**. No entanto, se você não tiver um, as etapas a seguir criarão um no Azure.

1. Crie um ELK certificado pela [Bitnami](https://ms.portal.azure.com/#create/bitnami.elk4-6) no Azure. Para os fins deste tutorial, não especificações a seguir para a criação desse servidor.

2. Vá até o seu recurso no Portal do Azure e entra na guia **Diagnósticos de Inicialização** na seção **Suporte + Solução de problemas**. Em seguida, clique na guia **Log Serial**.

    ![Diagnóstico de inicialização](./media/service-fabric-tutorial-java-elk/bootdiagnostics.png)
3. Faça uma pesquisa nos logs em busca da senha necessária para acessar a instância do Kibana. Ela é semelhante ao snippet a seguir:

    ```bash
    [   25.932766] bitnami[1496]: #########################################################################
    [   25.948656] bitnami[1496]: #                                                                       #
    [   25.962448] bitnami[1496]: #        Setting Bitnami application password to '[PASSWORD]'           #
    [   25.978137] bitnami[1496]: #        (the default application username is 'user')                   #
    [   26.004770] bitnami[1496]: #                                                                       #
    [   26.029413] bitnami[1496]: #########################################################################
    ```

4. Pressione o botão Conectar na página Visão geral do servidor no Portal do Azure para obter os detalhes de logon.

    ![Conexão de VM](./media/service-fabric-tutorial-java-elk/vmconnection.png)

5. Use SSH no servidor que hospeda a imagem ELK usando o seguinte comando

    ```bash
    ssh [USERNAME]@[CONNECTION-IP-OF-SERVER]

    Example: ssh testaccount@104.40.63.157
    ```

## <a name="set-up-elk"></a>Configurar o ELK

1. A primeira etapa é carregar o ambiente ELK

    ```bash
    sudo /opt/bitnami/use_elk
    ```

2. Se você estiver usando um ambiente existente, precisará executar o seguinte comando para interromper o serviço Logstash

    ```bash
    sudo /opt/bitnami/ctlscript.sh stop logstash
    ```

3. Execute o seguinte comando para instalar o plug-in Logstash para Hubs de Eventos.

    ```bash
    logstash-plugin install logstash-input-azureeventhub
    ```

4. Crie ou modifique o arquivo de configuração Logstash existente com o seguinte conteúdo: se você estiver criando o arquivo, ele deverá ser criado em ```/opt/bitnami/logstash/conf/access-log.conf```, se você estiver usando a imagem ELK Bitnami no Azure.

    ```json
    input
    {
        azureeventhub
        {
            key => "[RECEIVER-POLICY-KEY-FOR-EVENT-HUB]"
            username => "[RECEIVER-POLICY-NAME]"
            namespace => "[EVENTHUB-NAMESPACENAME]"
            eventhub => "[EVENTHUB-NAME]"
            partitions => 4
        }
    }

    output {
         elasticsearch {
             hosts => [ "127.0.0.1:9200" ]
         }
     }
    ```

5. Para verificar a configuração, execute o seguinte comando:

    ```bash
    /opt/bitnami/logstash/bin/logstash -f /opt/bitnami/logstash/conf/ --config.test_and_exit
    ```

6. Iniciar o serviço Logstash

    ```bash
    sudo /opt/bitnami/ctlscript.sh start logstash
    ```

7. Verifique o Elasticsearch para certificar-se de que você esteja recebendo dados

    ```bash
    curl 'localhost:9200/_cat/indices?v'
    ```

8. Acesse o painel do Kibana em **http:\//SERVER-IP** e insira o nome de usuário e a senha do Kibana. Se você usou a imagem ELK no Azure, o nome de usuário padrão será "user", e a senha será aquela obtida no **Diagnóstico de Inicialização**.

    ![A captura de tela mostra um painel do Kibana para visualizar a plataforma e os logs do aplicativo.](./media/service-fabric-tutorial-java-elk/kibana.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Executar um servidor ELK no Azure
> * Configurar o servidor para receber informações de diagnóstico do cluster do Service Fabric

Prosseguir para o próximo tutorial:
> [!div class="nextstepaction"]
> [Configurar CI/CD com Jenkins](service-fabric-tutorial-java-jenkins.md)
