---
title: Início Rápido – Usar a CLI do Azure para criar uma fila do Barramento de Serviço | Microsoft Docs
description: Neste início rápido, você aprenderá a usar a CLI do Azure para criar um namespace do Barramento de Serviço e uma fila nesse namespace.
author: spelluru
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: spelluru
ms.openlocfilehash: 14892b114f3bb8a0bb6c07f7cdd01a174b931cf9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95810623"
---
# <a name="use-the-azure-cli-to-create-a-service-bus-namespace-and-a-queue"></a>Usar a CLI do Azure para criar um namespace e uma fila do Barramento de Serviço
Este guia de início rápido mostra como criar um namespace e uma fila do Barramento de Serviço usando a CLI do Azure. Ele também mostra como obter credenciais de autorização que um aplicativo cliente pode usar para enviar/receber mensagens de/para a fila. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>Pré-requisitos
Se você não tiver uma assinatura do Azure, poderá criar uma [conta gratuita][free account] antes de começar.

Neste guia de início rápido, você usa o Azure Cloud Shell, que pode iniciar depois de entrar no portal do Azure. Para obter detalhes sobre o Azure Cloud Shell, consulte [Visão geral do Azure Cloud Shell](../cloud-shell/overview.md). Você também pode [instalar](/cli/azure/install-azure-cli) e usar o Azure PowerShell em seu computador. 

## <a name="provision-resources"></a>Provisionar recursos
1. Faça logon no [Portal do Azure](https://portal.azure.com).
2. Inicie o Azure Cloud Shell selecionando o ícone mostrado na imagem a seguir. Mude para o modo **Bash** se o Cloud Shell estiver no modo **PowerShell**. 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Iniciar o Cloud Shell":::
3. Execute o comando a seguir para criar um grupo de recursos do Azure. Atualize o nome e a localização do grupo de recursos, se desejar. 

    ```azurecli-interactive
    az group create --name ContosoRG --location eastus
    ```
4. Execute o comando a seguir para criar um namespace de mensagem do Barramento de Serviço.

    ```azurecli-interactive
    az servicebus namespace create --resource-group ContosoRG --name ContosoSBusNS --location eastus
    ```
5. Execute o comando a seguir para criar uma fila no namespace que você criou na etapa anterior. Neste exemplo, `ContosoRG` é o grupo de recursos que você criou na etapa anterior. `ContosoSBusNS` é o nome do namespace do Barramento de Serviço criado nesse grupo de recursos. 

    ```azurecli-interactive
    az servicebus queue create --resource-group ContosoRG --namespace-name ContosoSBusNS --name ContosoOrdersQueue
    ```
6. Execute o comando a seguir para obter a cadeia de conexão primária para o namespace. Você usa essa cadeia de conexão para se conectar à fila e enviar e receber mensagens. 

    ```azurecli-interactive
    az servicebus namespace authorization-rule keys list --resource-group ContosoRG --namespace-name ContosoSBusNS --name RootManageSharedAccessKey --query primaryConnectionString --output tsv    
    ```

    Anote a cadeia de conexão e o nome da fila. Você os usará para enviar e receber mensagens. 


## <a name="next-steps"></a>Próximas etapas
Neste artigo, você criou um namespace do Barramento de Serviço e uma fila no namespace. Para saber como enviar/receber mensagens de/para a fila, consulte um dos guias de início rápido a seguir na seção **Enviar e receber mensagens**. 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

