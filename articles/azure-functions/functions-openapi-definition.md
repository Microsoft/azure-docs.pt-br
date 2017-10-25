---
title: "Criar uma definição de OpenAPI para uma função | Microsoft Docs"
description: "Crie uma definição de OpenAPI que permite que outros aplicativos e serviços chamem sua função no Azure."
services: functions
keywords: "OpenAPI, Swagger, aplicativos de nuvem, serviços de nuvem,"
documentationcenter: 
author: mgblythe
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/25/2017
ms.author: mblythe; glenga
ms.custom: mvc
ms.openlocfilehash: a196df5b4ab47b234b48594da45cd4d72f604086
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-openapi-definition-for-a-function"></a>Criar uma definição de OpenAPI para uma função
APIs REST geralmente são descritas usando uma definição de OpenAPI (anteriormente conhecida como um arquivo [Swagger](http://swagger.io/)). Esta definição contém informações sobre as operações que estão disponíveis em uma API e como os dados de solicitação e resposta para a API devem ser estruturados.

Neste tutorial, você deve criar uma função que determina se um reparo de emergência em uma turbina eólica é eficaz em termos de custo. Em seguida, crie uma definição de OpenAPI para o aplicativo de funções para que a função possa ser chamada de outros aplicativos e serviços.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma função no Azure
> * Gerar uma definição de OpenAPI usando as ferramentas de OpenAPI
> * Modificar a definição para fornecer metadados adicionais
> * Testar a definição chamando a função

## <a name="create-a-function-app"></a>Criar um aplicativo de funções

Você deve ter um aplicativo de funções para hospedar a execução de suas funções. Um aplicativo de funções lhe permite agrupar funções como uma unidade lógica para facilitar o gerenciamento, a implantação, o dimensionamento e o compartilhamento de recursos. 

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]


## <a name="create-the-function"></a>Criar a função

Este tutorial usa uma função acionada por HTTP que usa dois parâmetros: o tempo estimado para criar um reparo da turbina (em horas) e a capacidade de turbina (em quilowatts). A função, em seguida, calcula o custo do reparo e o valor da receita que a turbina proporcionaria em um período de 24 horas.

1. Expanda seu aplicativo de funções, clique no botão **+** ao lado de **Functions** e clique no modelo **HTTPTrigger**. Digite `TurbineRepair` para a função **Nome** e clique em **Criar**.

    ![Folha Aplicativos de Funções, Funções +](media/functions-openapi-definition/add-function.png)

1. Substitua o conteúdo do arquivo run.csx pelo código abaixo e clique em **Salvar**:

    ```c#
    using System.Net;

    const double revenuePerkW = 0.12; 
    const double technicianCost = 250; 
    const double turbineCost = 100;

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {   

        //Get request body
        dynamic data = await req.Content.ReadAsAsync<object>();
        int hours = data.hours;
        int capacity = data.capacity;

        //Formulas to calculate revenue and cost
        double revenueOpportunity = capacity * revenuePerkW * 24;  
        double costToFix = (hours * technicianCost) +  turbineCost;
        string repairTurbine;

        if (revenueOpportunity > costToFix){
            repairTurbine = "Yes";
        }
        else {
            repairTurbine = "No";
        }

        return req.CreateResponse(HttpStatusCode.OK, new{
            message = repairTurbine,
            revenueOpportunity = "$"+ revenueOpportunity,
            costToFix = "$"+ costToFix         
        }); 
    }
    ```
    Esse código de função retorna uma mensagem de `Yes` ou `No` para indicar se um reparo de emergência é econômico, bem como as oportunidades de receita que representa a turbina e o custo para reparar a turbina. 

1. Para testar a função, clique em **Testar** na extremidade direita para expandir a guia Teste. Digite o seguinte valor para o **Corpo da solicitação** e clique em **Executar**.

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

    ![Testar a função no portal do Azure](media/functions-openapi-definition/test-function.png)

    O valor a seguir é retornado no corpo da resposta.

    ```json
    {"message":"Yes","revenueOpportunity":"$7200","costToFix":"$1600"}
    ```

Agora você tem uma função que determina o custo-benefício de reparos de emergências. Em seguida, você pode gerar e modificar uma definição de OpenAPI para o aplicativo de funções.

## <a name="generate-the-openapi-definition"></a>Gerar a definição de OpenAPI

Agora você está pronto para gerar a definição de OpenAPI. Essa definição pode ser usada por outras tecnologias da Microsoft, como aplicativos de API, [PowerApps](functions-powerapps-scenario.md) e [Microsoft Flow](../azure-functions/app-service-export-api-to-powerapps-and-flow.md), bem como ferramentas de desenvolvedores terceiros como [Postman](https://www.getpostman.com/docs/importing_swagger) e [muitos outros pacotes](http://swagger.io/tools/).

1. Selecione apenas os *verbos* que sua API dá suporte (neste caso, POST). Isso torna a definição de API gerada mais clara.

    1. Na guia **Integrar** da sua nova função do Gatilho de HTTP, altere **Métodos HTTP permitidos** para **Métodos selecionados**

    1. Em **Métodos HTTP selecionados**, desmarque todas as opções, exceto **POST**.

        ![Métodos HTTP selecionados](media/functions-openapi-definition/selected-http-methods.png)
        
1. Clique no nome do aplicativo de funções (como **function-demo-energy**) > **Recursos de plataforma** > **Definição de API**.

    ![Definição da API](media/functions-openapi-definition/api-definition.png)

1. Na guia **Definição de API**, clique em **Função**.

    ![Fonte de definição da API](media/functions-openapi-definition/api-definition-source.png)

    Esta etapa habilita um pacote de opções OpenAPI para seu aplicativo de funções, incluindo um ponto de extremidade para hospedar um arquivo OpenAPI de domínio do seu aplicativo de funções, uma cópia embutida do [Editor OpenAPI](http://editor.swagger.io) e um gerador de modelo de definição da API.

1. Clique em **Gerar modelo de definição da API** > **Salvar**.

    ![Gerar modelo de definição da API](media/functions-openapi-definition/generate-template.png)

    O Azure verifica seu aplicativo de funções para funções de Gatilho de HTTP e usa as informações no functions.json para gerar uma definição de OpenAPI. Aqui está a definição que é gerada:

    ```yaml
    swagger: '2.0'
    info:
    title: function-demo-energy.azurewebsites.net
    version: 1.0.0
    host: function-demo-energy.azurewebsites.net
    basePath: /
    schemes:
    - https
    - http
    paths:
    /api/TurbineRepair:
        post:
        operationId: /api/TurbineRepair/post
        produces: []
        consumes: []
        parameters: []
        description: >-
            Replace with Operation Object
            #http://swagger.io/specification/#operationObject
        responses:
            '200':
            description: Success operation
        security:
            - apikeyQuery: []
    definitions: {}
    securityDefinitions:
    apikeyQuery:
        type: apiKey
        name: code
        in: query
    ```

    Essa definição é descrita como um _modelo_ porque ela requer mais metadados para ser uma definição de OpenAPI completa. Você modificará a definição na próxima etapa.

## <a name="modify-the-openapi-definition"></a>Modificar a definição de OpenAPI
Agora que tem uma definição de modelo, você faz a modificação para fornecer metadados adicionais sobre estruturas de dados e operações da API. Em **definição de API**, exclua a definição gerada de `post` até a parte inferior da definição, cole o conteúdo abaixo e clique em **Salvar**.

```yaml
    post:
      operationId: CalculateCosts
      description: Determines if a technician should be sent for repair
      summary: Calculates costs
      x-ms-summary: Calculates costs
      x-ms-visibility: important
      produces:
        - application/json
      consumes:
        - application/json
      parameters:
        - name: body
          in: body
          description: Hours and capacity used to calculate costs
          x-ms-summary: Hours and capacity
          x-ms-visibility: important
          required: true
          schema:
            type: object
            properties:
              hours:
                description: The amount of effort in hours required to conduct repair
                type: number
                x-ms-summary: Hours
                x-ms-visibility: important
              capacity:
                description: The max output of a turbine in kilowatts
                type: number
                x-ms-summary: Capacity
                x-ms-visibility: important
      responses:
        200:
          description: Message with cost and revenue numbers
          x-ms-summary: Message
          schema:
           type: object
           properties:
            message:
              type: string
              description: Returns Yes or No depending on calculations
              x-ms-summary: Message 
            revenueOpportunity:
              type: string
              description: The revenue opportunity cost
              x-ms-summary: RevenueOpportunity 
            costToFix:
              type: string
              description: The cost in $ to fix the turbine
              x-ms-summary: CostToFix
      security:
        - apikeyQuery: []
definitions: {}
securityDefinitions:
  apikeyQuery:
    type: apiKey
    name: code
    in: query
```

Nesse caso você poderia apenas colar os metadados carregados, mas é importante entender os tipos de modificações que fizemos ao modelo padrão:

+ Foi especificado que a API produz e consome dados em um formato JSON.

+ Especificados os parâmetros necessários, com seus nomes e tipos de dados.

+ Especificados os valores retornados para uma resposta bem-sucedida, com seus nomes e tipos de dados.

+ Fornecidos resumos e descrições fáceis para a API, suas operações e seus parâmetros. Isso é importante para as pessoas que usarão essa função.

+ Adicionados x-ms-summary e x-ms-visibility, que são usados na interface do usuário para Aplicativos Lógicos e Microsoft Flow. Para obter mais informações, consulte [Extensões do OpenAPI para APIs personalizadas no Microsoft Flow](https://preview.flow.microsoft.com/documentation/customapi-how-to-swagger/).

> [!NOTE]
> Deixamos a definição de segurança com o método de autenticação padrão da chave de API. Esta seção da definição deverá ser alterada se você tiver usado um tipo diferente de autenticação.

Para obter mais informações sobre como definir as operações da API, consulte a [Especificação da API Open](https://swagger.io/specification/#operationObject).

## <a name="test-the-openapi-definition"></a>Testar a definição de OpenAPI
Antes de usar a definição de API, é uma boa ideia testá-la na interface do usuário do Azure Functions.

1. Na guia **Gerenciar** de sua função, em **Chaves de Host**, copie a chave **padrão**.

    ![Copiar chave de API](media/functions-openapi-definition/copy-api-key.png)

    > [!NOTE]
    >Use essa chave para teste. Você também usá-la quando chamar a API de um aplicativo ou serviço.

1. Volte para a definição de API: **function-demo-energy** > **Recursos de plataforma** > **Definição de API**.

1. No painel direito, clique em **Alterar autenticação**, insira a chave de API que você copiou e clique em **Autenticar**.

    ![Autenticar com a chave de API](media/functions-openapi-definition/authenticate-api-key.png)

1. Role para baixo e clique em **Testar esta operação**.

    ![Teste essa operação](media/functions-openapi-definition/try-operation.png)

1. Insira valores para **horas** e **capacidade**.

    ![Inserir parâmetros](media/functions-openapi-definition/parameters.png)

    Observe como a interface do usuário usa as descrições da definição de API.

1. Clique em **Enviar uma solicitação**, em seguida, clique na guia **Muito** para ver a saída.

    ![Enviar uma solicitação](media/functions-openapi-definition/send-request.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar uma função no Azure
> * Gerar uma definição de OpenAPI usando as ferramentas de OpenAPI
> * Modificar a definição para fornecer metadados adicionais
> * Testar a definição chamando a função

Avance para o próximo tópico para aprender a criar um aplicativo de PowerApps que usa a definição de OpenAPI que você criou.
> [!div class="nextstepaction"]
> [Chamar uma função do PowerApps](functions-powerapps-scenario.md)
