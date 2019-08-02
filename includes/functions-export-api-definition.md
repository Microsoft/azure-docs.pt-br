---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 49ac1a7585ddf2a6500c7e9382880109c3f7f431
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171693"
---
## <a name="export-an-api-definition"></a>Exportar uma definição de API
Você tem uma definição de OpenAPI para sua função, em [Criar uma definição de OpenAPI para uma função](../articles/azure-functions/functions-openapi-definition.md). A próxima etapa neste processo é exportar a definição de API para que o PowerApps e o Microsoft Flow possam usá-la em uma API personalizada.

> [!IMPORTANT]
> Lembre-se de que você precisa entrar no Azure com as mesmas credenciais que você usa nos locatários do PowerApps e do Microsoft Flow. Isso permite que o Azure crie a API personalizada e disponibilize-a no PowerApps e no Microsoft Flow.

1. No [portal do Azure](https://portal.azure.com), clique no nome do aplicativo de funções (como **function-demo-energy**) > **Recursos de plataforma** > **Definição de API**.

    ![Definição da API](media/functions-export-api-definition/api-definition.png)

1. Clique em **Exportar PowerApps + Flow**.

    ![Fonte de definição da API](media/functions-export-api-definition/export-api-1.png)

1. No painel direito, use as configurações, conforme especificado na tabela.

    |Configuração|DESCRIÇÃO|
    |--------|------------|
    |**Modo de exportação**|Selecione **Express** para gerar automaticamente a API personalizada. Selecionar **Manual** exporta a definição de API, mas, em seguida, você deverá importá-la no PowerApps e no Microsoft Flow manualmente. Para saber mais, veja [Exportar para o PowerApps e Microsoft Flow](../articles/azure-functions/app-service-export-api-to-powerapps-and-flow.md).|
    |**Ambiente**|Selecione o ambiente no qual a API personalizada deve ser salva. Para saber mais, veja [Visão geral de ambientes (PowerApps)](https://powerapps.microsoft.com/tutorials/environments-overview/) ou [Visão geral de ambientes (Microsoft Flow)](https://us.flow.microsoft.com/documentation/environments-overview-admin/).|
    |**Nome da API personalizada**|Insira um nome, como `Turbine Repair`.|
    |**Nome da chave de API**|Insira o nome que os construtores de aplicativo e fluxo devem ver na interface do usuário da API personalizada. Observe que o exemplo inclui informações úteis.|
 
    ![Exportar para o PowerApps e para o Microsoft Flow](media/functions-export-api-definition/export-api-2.png)

1. Clique em **OK**. Agora, a API personalizada é compilada e adicionada ao ambiente especificado.