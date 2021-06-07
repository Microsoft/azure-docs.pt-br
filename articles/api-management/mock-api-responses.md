---
title: Tutorial – Simular respostas da API no Gerenciamento de API – portal do Azure | Microsoft Docs
description: Neste tutorial, você usa o Gerenciamento de API para definir uma política em uma API para que ela retorne uma resposta fictícia se o back-end não está disponível para enviar respostas reais.
author: vladvino
ms.service: api-management
ms.custom: mvc, devx-track-azurecli
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: a7617a36ed800f1765ed7723568a4b612fcb6518
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587571"
---
# <a name="tutorial-mock-api-responses"></a>Tutorial: Simular respostas de API

As APIs de back-end podem ser importadas para uma API do APIM (Gerenciamento de API) ou criadas e gerenciadas manualmente. As etapas neste tutorial mostram como usar o APIM para criar uma API em branco e gerenciá-la manualmente e, depois, definir uma política em uma API para que ela retorne uma resposta fictícia. Este método permite que os desenvolvedores prossigam com a implementação e teste da instância de APIM mesmo se o back-end não está disponível para enviar respostas reais. 

A capacidade de simular respostas pode ser útil em diversos cenários:

+ Quando a fachada da API é criada primeiro e a implementação do back-end ocorre posteriormente. Ou, o back-end está sendo desenvolvido paralelamente.
+ Quando o back-end temporariamente não está operacional ou não pode ser escalado.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma API de teste 
> * Adicionar uma operação à API de teste
> * Habilitar a simulação de resposta
> * Testar a API fictícia


:::image type="content" source="media/mock-api-responses/mock-api-responses01.png" alt-text="Resposta de API fictícia":::

## <a name="prerequisites"></a>Pré-requisitos

+ Conheça a [terminologia do Gerenciamento de API do Azure](api-management-terminology.md).
+ Compreenda o [conceito de políticas no Gerenciamento de API do Azure](api-management-howto-policies.md).
+ Conclua o início rápido a seguir: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).

## <a name="create-a-test-api"></a>Criar uma API de teste 

As etapas nesta seção mostram como criar uma API em branco sem back-end. 


1. Entre no portal do Azure e navegue até sua instância do Gerenciamento de API.
1. Selecione **APIs** >  **+ Adicionar API** > **API em branco**.
1. Na janela **Criar uma API em branco**, selecione **Completo**.
1. Insira *API de Teste* como **Nome de exibição**.
1. Insira **Ilimitado** para **Produtos**.
1. Verifique se **Gerenciado** está selecionado em **Gateways**.
1. Selecione **Criar**.

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-01-create-test-api.png" alt-text="Criar API em branco":::

## <a name="add-an-operation-to-the-test-api"></a>Adicionar uma operação à API de teste

Uma API expõe uma ou mais operações. Nesta seção, adicione uma operação à API em branco que você criou. Chamar a operação depois de concluir as etapas nesta seção produz um erro. Você não receberá erros após concluir as etapas posteriormente na seção [Habilitar a simulação de resposta](#enable-response-mocking).

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecione a API que você criou na etapa anterior.
1. Selecione **+ Adicionar Operação**.
1. Na janela **Front-end**, insira os valores a seguir.

     | Configuração             | Valor                             | DESCRIÇÃO                                                                                                                                                                                   |
    |---------------------|-----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Nome de exibição**    | *Chamada de teste*                       | O nome exibido no [portal do desenvolvedor](api-management-howto-developer-portal.md).                                                                                                                                       |
    | **URL** (verbo HTTP) | GET                               | Selecione um dos verbos HTTP predefinidos.                                                                                                                                         |
    | **URL**             | */test*                           | Um caminho de URL para a API.                                                                                                                                                                       |
    | **Descrição**     |                                   |  Descrição opcional da operação, usada para fornecer a documentação no portal do desenvolvedor para os desenvolvedores que usarão a API.                                                    |
    
1. Selecione a guia **Respostas**, localizada sob os campos URL, Nome de exibição e Descrição. Insira as configurações nessa guia para definir os códigos de status de resposta, os tipos de conteúdo, os exemplos e os esquemas.
1. Selecione **+ Adicionar resposta** e selecione **200 OK** na lista.
1. Sob o cabeçalho **Representações** à direita, selecione **+ Adicionar representação**.
1. Insira *application/json* na caixa de pesquisa e selecione o tipo de conteúdo **application/json**.
1. Na caixa de texto **Exemplo**, insira `{ "sampleField" : "test" }`.
1. Clique em **Salvar**.

:::image type="content" source="media/mock-api-responses/03-mock-api-responses-02-add-operation.png" alt-text="Adicionar operação de API" border="false":::

Embora não seja necessário para este exemplo, configurações adicionais de uma operação de API podem ser definidas em outras guias, incluindo:


|Tab      |Descrição  |
|---------|---------|
|**Consulta**     |  Adicione os parâmetros de consulta. Além de fornecer um nome e uma descrição, você pode fornecer valores que são atribuídos a um parâmetro de consulta. Um dos valores pode ser marcado como padrão (opcional).        |
|**Solicitação**     |  Defina esquemas, exemplos e tipos de conteúdo de solicitação.       |

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para começar a usar a CLI do Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Para adicionar uma operação à API de teste, execute o comando [az apim api operation create](/cli/azure/apim/api/operation#az_apim_api_operation_create):

```azurecli
az apim api operation create --resource-group apim-hello-word-resource-group \
    --display-name "Test call" --api-id test-api --method GET \
    --url-template /test --service-name apim-hello-world 
```

Execute o comando [az apim api operation list](/cli/azure/apim/api/operation#az_apim_api_operation_list) para ver todas as operações de uma API:

```azurecli
az apim api operation list --resource-group apim-hello-word-resource-group \
    --api-id test-api --service-name apim-hello-world --output table
```

Para remover uma operação, use o comando [az apim api operation delete](/cli/azure/apim/api/operation#az_apim_api_operation_delete). Obtenha a ID da operação do comando anterior.

```azurecli
az apim api operation delete --resource-group apim-hello-word-resource-group \
    --api-id test-api --operation-id 00000000000000000000000000000000 \
    --service-name apim-hello-world
```

Mantenha essa operação para uso no restante deste artigo.

---

## <a name="enable-response-mocking"></a>Habilitar a simulação de resposta

1. Selecione a API criada em [Criar uma API de teste](#create-a-test-api).
1. Selecione a operação de teste que você adicionou.
1. Na janela à direita, verifique se a guia **Design** está selecionada.
1. Na janela **Processamento de entrada**, selecione **+ Adicionar política**.

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-03-enable-mocking.png" alt-text="Adicionar política de processamento" border="false":::

1. Selecione **Respostas fictícias** da galeria.

    :::image type="content" source="media/mock-api-responses/mock-responses-policy-tile.png" alt-text="Bloco de política de respostas fictícias" border="false":::

1. Na caixa de texto **Resposta de Gerenciamento de API**, digite **200 OK, application/json**. Essa seleção indica que a sua API deve retornar o exemplo de resposta definido na seção anterior.

    :::image type="content" source="media/mock-api-responses/mock-api-responses-set-mocking.png" alt-text="Definir resposta fictícia":::

1. Clique em **Salvar**.

    > [!TIP]
    > Uma barra amarela com o texto **A simulação está habilitada** para sua API indica que as respostas retornadas do Gerenciamento de API são simuladas pela [política de simulação](api-management-advanced-policies.md#mock-response) e não são produzidas pelo back-end.

## <a name="test-the-mocked-api"></a>Testar a API fictícia

1. Selecione a API criada em [Criar uma API de teste](#create-a-test-api).
1. Selecione a guia **Testar**.
1. Certifique-se de que a API **Chamada de teste** esteja selecionada. Selecione **Enviar** para fazer uma chamada de teste.

   :::image type="content" source="media/mock-api-responses/03-mock-api-responses-04-test-mocking.png" alt-text="Testar a API fictícia":::

1. A **Resposta HTTP** exibe o JSON fornecido como um exemplo na primeira seção desse tutorial.

    :::image type="content" source="media/mock-api-responses/mock-api-responses-test-response.png" alt-text="Resposta HTTP fictícia":::

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar uma API de teste
> * Adicionar uma operação à API de teste
> * Habilitar a simulação de resposta
> * Testar a API fictícia

Prosseguir para o próximo tutorial:

> [!div class="nextstepaction"]
> [Transformar e proteger uma API publicada](transform-api.md)
