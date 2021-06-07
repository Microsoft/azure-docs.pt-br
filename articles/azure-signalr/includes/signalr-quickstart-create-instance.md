---
title: incluir arquivo
description: incluir arquivo
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 09/14/2018
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: f738daab7ddcf0403f546e7c9ffeaeccb66bc6b7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91355629"
---
## <a name="create-an-azure-signalr-service-instance"></a>Criar uma instância do Serviço Azure SignalR

Seu aplicativo vai se conectar a uma instância do Serviço SignalR no Azure.

1. Selecione no botão Novo localizado no canto superior esquerdo do portal do Azure. Na tela Novo, digite *Serviço SignalR* na caixa de pesquisa e pressione Enter.

    ![A captura de tela mostra a pesquisa do Serviço do SignalR no portal do Azure.](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-new.png)

1. Selecione **Serviço SignalR** nos resultados da pesquisa e, em seguida, selecione **Criar**.

1. Insira as configurações a seguir.

    | Configuração      | Valor sugerido  | DESCRIÇÃO                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome do recurso** | Nome globalmente exclusivo | Nome que identifica a nova instância do Serviço SignalR. Os caracteres válidos são `a-z`, `0-9` e `-`.  | 
    | **Assinatura** | Sua assinatura | A assinatura sob a qual essa nova instância do Serviço SignalR será criada. | 
    | **[Grupo de Recursos](../../azure-resource-manager/management/overview.md)** |  myResourceGroup | Nome do novo grupo de recursos no qual criar a instância do Serviço SignalR. | 
    | **Localidade** | Oeste dos EUA | Selecione uma [região](https://azure.microsoft.com/regions/) perto de você. |
    | **Tipo de preços** | Grátis | Experimente o Serviço Azure SignalR gratuitamente. |
    | **Contagem de unidades** |  Não aplicável | A contagem de unidade especifica quantas conexões sua instância do Serviço SignalR pode aceitar. Só é configurável na camada Standard. |
    | **Modo de serviço** |  Sem servidor | Para uso com o Azure Functions ou a API REST. |

    ![A captura de tela mostra a guia Noções Básicas do SignalR com valores.](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-create.png)

1. Selecione **Criar** para começar a implantar a instância do Serviço SignalR.

1. Depois que a instância é implantada, abra-a no portal e localize sua página de Configurações. Altere a configuração do modo de serviço para *Sem servidor* somente se estiver usando o Serviço do Azure SignalR por meio de associação do Azure Functions ou da API REST. Deixe a configuração como *Clássica* ou *Padrão*.