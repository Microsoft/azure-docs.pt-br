---
title: Usar a CLI do Azure para criar tópicos e assinaturas do Barramento de Serviço
description: Neste início rápido, você aprenderá a criar um tópico do Barramento de Serviço e assinaturas para esse tópico usando a CLI do Azure
ms.date: 06/23/2020
ms.topic: quickstart
author: spelluru
ms.author: spelluru
ms.openlocfilehash: 080b089efa276779420f6d9bc8e76272f1e65788
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90069689"
---
# <a name="use-azure-cli-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Usar a CLI do Azure para criar um tópico do Barramento de Serviço e assinaturas para o tópico
Neste Início rápido, você usará a CLI do Azure para criar um tópico do Barramento de Serviço e, em seguida, criará assinaturas para esse tópico. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>O que são os tópicos e as assinaturas do Barramento de Serviço?
Os tópicos e assinaturas do Barramento de Serviço dão suporte a um modelo de comunicação de mensagens de *publicação/assinatura* . Durante o uso de tópicos e assinaturas, os componentes de um aplicativo distribuído não se comunicam diretamente uns com os outros, eles trocam mensagens por meio de um tópico, que atua como um intermediário.

![Conceitos de tópico](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

Ao contrário do que ocorre com as filas do Barramento de Serviço, em que cada mensagem é processada por um único consumidor, tópicos e assinaturas fornecem uma forma de comunicação de um para muitos usando um padrão de publicação/assinatura. É possível registrar várias assinaturas para um tópico. Quando uma mensagem é enviada a um tópico, é disponibilizada para cada assinatura para ser manipulada/processada de forma independente. Uma assinatura de tópico é semelhante a uma fila virtual que recebe cópias das mensagens enviadas para o tópico. Outra opção é registrar regras de filtro para um tópico por assinatura, o que permite que você filtre ou restrinja quais mensagens para um tópico são recebidas por quais assinaturas de tópico.

As assinaturas e os tópicos do Barramento de Serviço permitem o dimensionamento para processar um grande número de mensagens entre muitos usuários e aplicativos.

## <a name="prerequisites"></a>Pré-requisitos
Se você não tiver uma assinatura do Azure, poderá criar uma [conta gratuita][free account] antes de começar.

Neste guia de início rápido, você usa o Azure Cloud Shell, que pode iniciar depois de entrar no portal do Azure. Para obter detalhes sobre o Azure Cloud Shell, consulte [Visão geral do Azure Cloud Shell](../cloud-shell/overview.md). Você também pode [instalar](/cli/azure/install-azure-cli) e usar o Azure PowerShell em seu computador. 

## <a name="create-a-service-bus-topic-and-subscriptions"></a>Criar assinaturas e um tópico do Barramento de Serviço
Cada [assinatura a um tópico](service-bus-messaging-overview.md#topics) pode receber uma cópia de cada mensagem. Os tópicos tem total compatibilidade de protocolo e semântica com filas do Barramento de Serviço. Os tópicos do Barramento de Serviço dão suporte a uma ampla matriz de regras de seleção com condições de filtro, com ações opcionais que definem ou modificam as propriedades da mensagem. Cada vez que uma regra corresponde, é produzida uma mensagem. Para saber mais sobre regras, filtros e ações, consulte este [link](topic-filters.md).

1. Faça logon no [Portal do Azure](https://portal.azure.com).
2. Inicie o Azure Cloud Shell selecionando o ícone mostrado na imagem a seguir. Mude para o modo **Bash** se o Cloud Shell estiver no modo **PowerShell**. 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Iniciar o Cloud Shell":::
3. Execute o comando a seguir para criar um grupo de recursos do Azure. Atualize o nome e a localização do grupo de recursos, se desejar. 

    ```azurecli-interactive
    az group create --name MyResourceGroup --location eastus
    ```
4. Execute o comando a seguir para criar um namespace de mensagem do Barramento de Serviço. Atualize o nome do namespace para que seja exclusivo. 

    ```azurecli-interactive
    namespaceName=MyNameSpace$RANDOM
    az servicebus namespace create --resource-group MyResourceGroup --name $namespaceName --location eastus
    ```
5. Execute o comando a seguir para criar um tópico no namespace. 

    ```azurecli-interactive
    az servicebus topic create --resource-group MyResourceGroup   --namespace-name $namespaceName --name MyTopic
    ```
6. Criar a primeira assinatura do tópico
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S1    
    ```
6. Criar a segunda assinatura do tópico
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S2    
    ```
6. Criar a terceira assinatura do tópico
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S3    
    ```
7. Crie um filtro na primeira assinatura com um filtro usando as propriedades personalizadas (`StoreId` é uma das `Store1`, `Store2` e `Store3`).

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --subscription-name S1 --name MyFilter --filter-sql-expression "StoreId IN ('Store1','Store2','Store3')"    
    ```
8. Criar um filtro na segunda assinatura com um filtro usando as propriedades do cliente (`StoreId = Store4`)

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S2 --name MySecondFilter --filter-sql-expression "StoreId = 'Store4'"    
    ```
9. Crie um filtro na terceira assinatura com um filtro usando as propriedades do cliente (`StoreId` não está em `Store1`, `Store2`, `Store3` ou `Store4`).

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --subscription-name S3 --name MyThirdFilter --filter-sql-expression "StoreId NOT IN ('Store1','Store2','Store3', 'Store4')"     
    ```
10. Execute o comando a seguir para obter a cadeia de conexão primária para o namespace. Você usa essa cadeia de conexão para se conectar à fila e enviar e receber mensagens. 

    ```azurecli-interactive
    az servicebus namespace authorization-rule keys list --resource-group MyResourceGroup --namespace-name $namespaceName --name RootManageSharedAccessKey --query primaryConnectionString --output tsv    
    ```

    Anote a cadeia de conexão e o nome do tópico. Você os usará para enviar e receber mensagens. 
    

## <a name="next-steps"></a>Próximas etapas
Para saber como enviar mensagens para um tópico e receber essas mensagens por meio de uma assinatura, confira o seguinte artigo: selecione a linguagem de programação no sumário. 

> [!div class="nextstepaction"]
> [Publicar e assinar mensagens](service-bus-dotnet-how-to-use-topics-subscriptions.md)


[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create
