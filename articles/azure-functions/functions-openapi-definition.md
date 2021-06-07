---
title: Expor suas funções com a OpenAPI usando o Gerenciamento de API do Azure
description: Crie uma definição de OpenAPI que permite que outros aplicativos e serviços chamem sua função no Azure.
ms.topic: tutorial
ms.date: 04/21/2020
ms.reviewer: sunayv
ms.custom: devx-track-csharp, mvc, cc996988-fb4f-47, references_regions
ms.openlocfilehash: 9083ff7d8f65c68ce8d173973a4eda650ac355aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88212915"
---
# <a name="create-an-openapi-definition-for-a-serverless-api-using-azure-api-management"></a>Crie uma definição de OpenAPI para uma API sem servidor usando o Gerenciamento de API do Azure

APIs REST geralmente são descritas usando uma definição de OpenAPI. Esta definição contém informações sobre as operações que estão disponíveis em uma API e como os dados de solicitação e resposta para a API devem ser estruturados.

Neste tutorial, você deve criar uma função que determina se um reparo de emergência em uma turbina eólica é eficaz em termos de custo. Em seguida, crie uma definição de OpenAPI para o aplicativo de funções usando o [Gerenciamento de API do Azure](../api-management/api-management-key-concepts.md) para que a função possa ser chamada de outros aplicativos e serviços.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma função no Azure
> * Gerar uma definição de OpenAPI usando o Gerenciamento de API do Azure
> * Testar a definição chamando a função
> * Baixar a definição de OpenAPI

## <a name="create-a-function-app"></a>Criar um aplicativo de funções

Você deve ter um aplicativo de funções para hospedar a execução de suas funções. Um aplicativo de funções lhe permite agrupar funções como uma unidade lógica para facilitar o gerenciamento, a implantação, o dimensionamento e o compartilhamento de recursos.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-the-function"></a>Criar a função

Este tutorial usa uma função disparada por HTTP que usa dois parâmetros:

* O tempo estimado para reparar uma turbina, em horas.
* A capacidade da turbina, em quilowatts. 

Depois, a função calculará o custo do reparo e o valor da receita que a turbina proporcionaria em um período de 24 horas. Para criar uma função disparada por HTTP no [portal do Azure](https://portal.azure.com):

1. No menu esquerdo do seu aplicativo de funções, selecione **Funções** e **Adicionar** no menu superior.

1. Na janela **Nova Função**, selecione **Gatilho Http**.

1. Para **Nova Função**, insira `TurbineRepair`. 

1. Escolha **Função** na lista suspensa **[Nível de autorização](functions-bindings-http-webhook-trigger.md#http-auth)** e, em seguida, selecione **Criar Função**.

    :::image type="content" source="media/functions-openapi-definition/select-http-trigger-openapi.png" alt-text="Criar função HTTP para OpenAPI":::

1. Selecione **Código + Teste** e, em seguida, selecione **run.csx** na lista suspensa. Substitua o conteúdo do arquivo do script C# run.csx pelo código abaixo e selecione **Salvar**:

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    const double revenuePerkW = 0.12;
    const double technicianCost = 250;
    const double turbineCost = 100;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        // Get query strings if they exist
        int tempVal;
        int? hours = Int32.TryParse(req.Query["hours"], out tempVal) ? tempVal : (int?)null;
        int? capacity = Int32.TryParse(req.Query["capacity"], out tempVal) ? tempVal : (int?)null;
    
        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);
    
        // Use request body if a query was not sent
        capacity = capacity ?? data?.capacity;
        hours = hours ?? data?.hours;
    
        // Return bad request if capacity or hours are not passed in
        if (capacity == null || hours == null){
            return new BadRequestObjectResult("Please pass capacity and hours on the query string or in the request body");
        }
        // Formulas to calculate revenue and cost
        double? revenueOpportunity = capacity * revenuePerkW * 24;  
        double? costToFix = (hours * technicianCost) +  turbineCost;
        string repairTurbine;
    
        if (revenueOpportunity > costToFix){
            repairTurbine = "Yes";
        }
        else {
            repairTurbine = "No";
        };
    
        return (ActionResult)new OkObjectResult(new{
            message = repairTurbine,
            revenueOpportunity = "$"+ revenueOpportunity,
            costToFix = "$"+ costToFix
        });
    }
    ```

    Esse código de função retorna uma mensagem de `Yes` ou `No` para indicar se um reparo de emergência é econômico. Ele também retorna a oportunidade de receita que a turbina representa e o custo para reparar a turbina.

1. Para testar a função, selecione **Testar**, selecione a guia **Entrada**, insira a seguinte entrada no **Corpo** e, em seguida, selecione **Executar**:

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

    :::image type="content" source="media/functions-openapi-definition/test-function.png" alt-text="Testar a função no portal do Azure":::

    A seguinte saída será retornada na guia **Saída**:

    ```json
    {"message":"Yes","revenueOpportunity":"$7200","costToFix":"$1600"}
    ```

Agora você tem uma função que determina o custo-benefício de reparos de emergências. Em seguida, você pode gerar uma definição de OpenAPI para o aplicativo de funções.

## <a name="generate-the-openapi-definition"></a>Gerar a definição de OpenAPI

Para gerar a definição de OpenAPI:

1. Selecione o aplicativo de funções, escolha **Gerenciamento de API** no menu esquerdo e, em seguida, selecione **Criar** em **Gerenciamento de API**.

    :::image type="content" source="media/functions-openapi-definition/select-all-settings-openapi.png" alt-text="Escolha Gerenciamento de API":::


1. Use as configurações do Gerenciamento de API conforme especificado na seguinte tabela:

    | Configuração      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | Nome globalmente exclusivo | Um nome é gerado com base no nome do seu aplicativo de funções. |
    | **Assinatura** | Sua assinatura | A assinatura na qual este novo recurso será criado. |  
    | **[Grupo de recursos](../azure-resource-manager/management/overview.md)** |  myResourceGroup | O mesmo recurso que seu aplicativo de funções, que deve estar definido para você. |
    | **Localidade** | Oeste dos EUA | Escolha o local Oeste dos EUA. |
    | **Nome da organização** | Contoso | O nome da organização usado no portal do desenvolvedor e para notificações por email. |
    | **Email do administrador** | seu email | Email que recebeu notificações do sistema de Gerenciamento de API. |
    | **Tipo de preços** | Consumo | A camada de consumo não está disponível em todas as regiões. Para obter detalhes completos sobre preços, confira a [Página de preços de Gerenciamento de API](https://azure.microsoft.com/pricing/details/api-management/) |

    ![Criar um novo serviço de Gerenciamento de API](media/functions-openapi-definition/new-apim-service-openapi.png)

1. Escolha **Criar** para criar a instância de Gerenciamento de API, o que pode levar vários minutos.

1. Depois que o Azure cria a instância, ele habilita a opção **Habilitar Application Insights** na página. Selecione-a para enviar logs para o mesmo local que o aplicativo de funções e, em seguida, selecione **Vincular API**.

1. **Importar Funções do Azure** é exibido com a função **TurbineRepair** realçada. Clique em **Selecionar** para continuar.

    ![Importar Funções do Azure para o Gerenciamento de API](media/functions-openapi-definition/import-function-openapi.png)

1. Na página **Criar com base no Aplicativo de Funções**, aceite os padrões e selecione **Criar**.

    :::image type="content" source="media/functions-openapi-definition/create-function-openapi.png" alt-text="Criar com base no Aplicativo de Funções":::

    O Azure cria a API para a função.

## <a name="test-the-api"></a>Testar a API

Antes de usar a definição de OpenAPI, você deve verificar se a API funciona.

1. Na página do aplicativo de funções, selecione **Gerenciamento de API**, selecione a guia **Teste** e, em seguida, selecione **POST TurbineRepair**. 

1. Insira o seguinte código no **Corpo da solicitação**:

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

1. Selecione **Enviar** e veja a **Resposta HTTP**.

    :::image type="content" source="media/functions-openapi-definition/test-function-api-openapi.png" alt-text="Testar API de função":::

## <a name="download-the-openapi-definition"></a>Baixar a definição de OpenAPI

Se sua API funcionar conforme esperado, será possível baixar a definição de OpenAPI.

1. Selecione **Baixar definição de OpenAPI** na parte superior da página.
   
   ![Baixar definição de OpenAPI](media/functions-openapi-definition/download-definition.png)

2. Salve o arquivo JSON baixado e abra-o. Examine a definição.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Próximas etapas

Você usou a integração do Gerenciamento de API para gerar uma definição de OpenAPI de suas funções. Agora você pode editar a definição no Gerenciamento de API no portal. Também é possível [saber mais sobre o Gerenciamento de API](../api-management/api-management-key-concepts.md).

> [!div class="nextstepaction"]
> [Editar a definição de OpenAPI no Gerenciamento de API](../api-management/edit-api.md)
