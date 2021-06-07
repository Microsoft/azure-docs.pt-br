---
title: Usar o Azure PowerShell para criar uma fila do Barramento de Serviço
description: Neste início rápido, você aprenderá a criar um namespace do Barramento de Serviço e uma fila no namespace usando o Azure PowerShell.
author: spelluru
ms.author: spelluru
ms.date: 08/12/2020
ms.topic: quickstart
ms.devlang: dotnet
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: f7bf9e5435b00ee3076422cccbe689038051499d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537094"
---
# <a name="use-azure-powershell-to-create-a-service-bus-namespace-and-a-queue"></a>Usar o Azure PowerShell para criar um namespace e uma fila do Barramento de Serviço
Este guia de início rápido mostra como criar um namespace e uma fila do Barramento de Serviço usando o Azure PowerShell. Ele também mostra como obter credenciais de autorização que um aplicativo cliente pode usar para enviar/receber mensagens de/para a fila. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisa tem uma assinatura do Azure. Se você não tiver uma assinatura do Azure, poderá criar uma [conta gratuita][] antes de começar. 

Neste guia de início rápido, você usa o Azure Cloud Shell, que pode iniciar depois de entrar no portal do Azure. Para obter detalhes sobre o Azure Cloud Shell, consulte [Visão geral do Azure Cloud Shell](../cloud-shell/overview.md). Você também pode [instalar](/powershell/azure/install-Az-ps) e usar o Azure PowerShell em seu computador. 


## <a name="provision-resources"></a>Provisionar recursos
1. Faça logon no [Portal do Azure](https://portal.azure.com).
2. Inicie o Azure Cloud Shell selecionando o ícone mostrado na seguinte imagem: 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Iniciar o Cloud Shell":::
3. Na janela inferior do Cloud Shell, alterne de **Bash** para **PowerShell**. 

    :::image type="content" source="./media/service-bus-quickstart-powershell/cloud-power-shell.png" alt-text="Alternar para o modo PowerShell":::    
4. Execute o comando a seguir para criar um grupo de recursos do Azure. Atualize o nome e a localização do grupo de recursos, se desejar. 

    ```azurepowershell-interactive
    New-AzResourceGroup –Name ContosoRG –Location eastus
    ```
5. Execute o comando a seguir para criar um namespace de mensagem do Barramento de Serviço. Neste exemplo, `ContosoRG` é o grupo de recursos que você criou na etapa anterior. `ContosoSBusNS` é o nome do namespace do Barramento de Serviço criado nesse grupo de recursos. 

    ```azurepowershell-interactive
    New-AzServiceBusNamespace -ResourceGroupName ContosoRG -Name ContosoSBusNS -Location eastus
    ```
6. Execute o seguinte para criar uma fila no namespace que você criou na etapa anterior. 

    ```azurepowershell-interactive
    New-AzServiceBusQueue -ResourceGroupName ContosoRG -NamespaceName ContosoSBusNS -Name ContosoOrdersQueue 
    ```
7. Obtenha a cadeia de conexão primária para o namespace. Você usa essa cadeia de conexão para se conectar à fila e enviar e receber mensagens. 

    ```azurepowershell-interactive    
    Get-AzServiceBusKey -ResourceGroupName ContosoRG -Namespace ContosoSBusNS -Name RootManageSharedAccessKey
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

[conta gratuita]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
