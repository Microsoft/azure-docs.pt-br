---
title: Habilidade personalizada do reconhecedor de formulário (C#)
titleSuffix: Azure Cognitive Search
description: Saiba como criar uma habilidade personalizada do reconhecedor de formulário usando C# e Visual Studio.
manager: nitinme
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: a3b073cdb90e0c427bfbca15c1440b9122672610
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880128"
---
# <a name="example-create-a-form-recognizer-custom-skill"></a>Exemplo: criar uma habilidade personalizada do reconhecedor de formulário

Neste exemplo do Azure Pesquisa Cognitiva tipo de habilidade, você aprenderá a criar uma habilidade personalizada do reconhecedor de formulário usando C# e Visual Studio. O reconhecedor de formulário analisa documentos e extrai pares de chave/valor e dados de tabela. Ao encapsular o reconhecedor de formulário na [interface de habilidade personalizada](cognitive-search-custom-skill-interface.md), você pode adicionar esse recurso como uma etapa em um pipeline de enriquecimento de ponta a ponta. O pipeline pode, então, carregar os documentos e fazer outras transformações.

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (qualquer edição).
- Pelo menos cinco formas do mesmo tipo. Você pode usar dados de exemplo fornecidos com este guia.

## <a name="create-a-form-recognizer-resource"></a>Criar um recurso do Reconhecimento de Formulários

[!INCLUDE [create resource](../cognitive-services/form-recognizer/includes/create-resource.md)]

## <a name="train-your-model"></a>Treinar seu modelo

Você precisará treinar um modelo de reconhecimento de formulário com seus formulários de entrada antes de usar essa habilidade. Siga o guia de [início rápido da ondulação](../cognitive-services/form-recognizer/quickstarts/client-library.md?pivots=programming-language-rest-api) para aprender a treinar um modelo. Você pode usar os formulários de exemplo fornecidos nesse guia de início rápido ou pode usar seus próprios dados. Depois que o modelo for treinado, copie seu valor de ID para um local seguro.

## <a name="set-up-the-custom-skill"></a>Configurar a habilidade personalizada

Este tutorial usa o projeto [AnalyzeForm](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Vision/AnalyzeForm) no repositório GitHub do [Azure Search Power Skills](https://github.com/Azure-Samples/azure-search-power-skills) . Clone esse repositório em seu computador local e navegue até **Vision/AnalyzeForm/** para acessar o projeto. Em seguida, abra _AnalyzeForm. csproj_ no Visual Studio. Este projeto cria um recurso de função do Azure que atende à [interface de habilidade personalizada](cognitive-search-custom-skill-interface.md) e pode ser usado para o enriquecimento de pesquisa cognitiva do Azure. Ele usa documentos de formulário como entradas e gera (como texto) os pares de chave/valor que você especificar.

Primeiro, adicione variáveis de ambiente no nível de projeto. Localize o projeto **AnalyzeForm** no painel esquerdo, clique nele com o botão direito do mouse e selecione **Propriedades**. Na janela **Propriedades** , clique na guia **depurar** e localize o campo **variáveis de ambiente** . Clique em **Adicionar** para adicionar as seguintes variáveis:
* `FORMS_RECOGNIZER_ENDPOINT_URL` com o valor definido para a URL do ponto de extremidade.
* `FORMS_RECOGNIZER_API_KEY` com o valor definido para sua chave de assinatura.
* `FORMS_RECOGNIZER_MODEL_ID` com o valor definido como a ID do modelo que você treinou.
* `FORMS_RECOGNIZER_RETRY_DELAY` com o valor definido como 1000. Esse valor é o tempo em milissegundos que o programa aguardará antes de repetir a consulta.
* `FORMS_RECOGNIZER_MAX_ATTEMPTS` com o valor definido como 100. Esse valor é o número de vezes que o programa consultará o serviço ao tentar obter uma resposta bem-sucedida.

Em seguida, abra _AnalyzeForm. cs_ e localize a `fieldMappings` variável, que faz referência à *field-mappings.jsno* arquivo. Esse arquivo (e a variável que faz referência a ele) define a lista de chaves que você deseja extrair de seus formulários e um rótulo personalizado para cada chave. Por exemplo, um valor de `{ "Address:", "address" }, { "Invoice For:", "recipient" }` significa que o script só salvará os valores para os `Address:` campos detectados e `Invoice For:` que rotularão esses valores com `"address"` e `"recipient"` , respectivamente.

Por fim, observe a `contentType` variável. Esse script executa o modelo de reconhecedor de formulário fornecido em documentos remotos que são referenciados pela URL, portanto, o tipo de conteúdo é `application/json` . Se desejar analisar arquivos locais incluindo seus fluxos de bytes nas solicitações HTTP, você precisará alterar o `contentType` para o [tipo MIME](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Complete_list_of_MIME_types) apropriado para o arquivo.

## <a name="test-the-function-from-visual-studio"></a>Testar a função do Visual Studio

Depois de editar o projeto, salve-o e defina o projeto **AnalyzeForm** como o projeto de inicialização no Visual Studio (se ele ainda não estiver definido). Em seguida, pressione **F5** para executar a função em seu ambiente local. Use um serviço REST como o [postmaster](https://www.postman.com/) para chamar a função.

### <a name="http-request"></a>Solicitação HTTP

Você fará a solicitação a seguir para chamar a função.

```HTTP
POST https://localhost:7071/api/analyze-form
```

### <a name="request-body"></a>Corpo da solicitação

Comece com o modelo de corpo da solicitação abaixo.

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": { 
                "formUrl": "<your-form-url>",
                "formSasToken": "<your-sas-token>"
            }
        }
    ]
}
```

Aqui, você precisará fornecer a URL de um formulário que tenha o mesmo tipo que os formulários com os quais você treinou. Para fins de teste, você pode usar um dos seus formulários de treinamento. Se você seguiu o guia de início rápido da ondulação, seus formulários estarão localizados em uma conta de armazenamento de BLOBs do Azure. Abra Gerenciador de Armazenamento do Azure, localize um arquivo de formulário, clique nele com o botão direito do mouse e selecione **obter assinatura de acesso compartilhado**. A próxima janela de diálogo fornecerá uma URL e um token SAS. Insira essas cadeias de caracteres nos `"formUrl"` `"formSasToken"` campos e do corpo da solicitação, respectivamente.

> [!div class="mx-imgBorder"]
> ![Gerenciador de armazenamento do Azure; um documento PDF está selecionado](media/cognitive-search-skill-form/form-sas.png)

Se você quiser analisar um documento remoto que não está no armazenamento de BLOBs do Azure, Cole sua URL no `"formUrl"` campo e deixe o `"formSasToken"` campo em branco.

> [!NOTE]
> Quando a habilidade for integrada em um configurador de habilidades, a URL e o token serão fornecidos pelo Pesquisa Cognitiva.

### <a name="response"></a>Resposta

Você deverá ver uma resposta semelhante ao exemplo a seguir:

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": {
                "address": "1111 8th st. Bellevue, WA 99501 ",
                "recipient": "Southridge Video 1060 Main St. Atlanta, GA 65024 "
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Publicar a função no Azure

Quando estiver satisfeito com o comportamento da função, você poderá publicá-lo.

1. No **Gerenciador de soluções** no Visual Studio, clique com o botão direito do mouse no projeto e selecione **publicar**. Escolha **criar nova**  >  **publicação**.
1. Se você ainda não conectou o Visual Studio à sua conta do Azure, selecione **Adicionar uma conta....**
1. Siga os prompts na tela. Especifique um nome exclusivo para o serviço de aplicativo, a assinatura do Azure, o grupo de recursos, o plano de hospedagem e a conta de armazenamento que você deseja usar. Você pode criar um novo grupo de recursos, um novo plano de hospedagem e uma nova conta de armazenamento, se ainda não os tiver. Ao terminar, selecione **Criar**.
1. Após a conclusão da implantação, observe a URL do site. Essa URL é o endereço do seu aplicativo de funções no Azure. Salve-o em um local temporário.
1. No [portal do Azure](https://portal.azure.com), navegue até o grupo de recursos e procure a `AnalyzeForm` função que você publicou. Na seção **Gerenciar**, você deverá ver as chaves do host. Copie a chave de host *padrão* e salve-a em um local temporário.

## <a name="connect-to-your-pipeline"></a>Conectar-se ao seu pipeline

Para usar essa habilidade em um pipeline Pesquisa Cognitiva, você precisará adicionar uma definição de habilidade ao seu Skill. O bloco JSON a seguir é uma definição de habilidade de exemplo (você deve atualizar as entradas e saídas para refletir seu cenário específico e o ambiente do contratador de habilidades). Substitua `AzureFunctionEndpointUrl` pela URL da função e substitua `AzureFunctionDefaultHostKey` pela sua chave de host.

```json
{ 
  "description":"Skillset that invokes the Form Recognizer custom skill",
  "skills":[ 
    "[... your existing skills go here]",
    { 
      "@odata.type":"#Microsoft.Skills.Custom.WebApiSkill",
      "name":"formrecognizer",
      "description":"Extracts fields from a form using a pre-trained form recognition model",
      "uri":"[AzureFunctionEndpointUrl]/api/analyze-form?code=[AzureFunctionDefaultHostKey]",
      "httpMethod":"POST",
      "timeout":"PT30S",
      "context":"/document",
      "batchSize":1,
      "inputs":[ 
        { 
          "name":"formUrl",
          "source":"/document/metadata_storage_path"
        },
        { 
          "name":"formSasToken",
          "source":"/document/metadata_storage_sas_token"
        }
      ],
      "outputs":[ 
        { 
          "name":"address",
          "targetName":"address"
        },
        { 
          "name":"recipient",
          "targetName":"recipient"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas

Neste guia, você criou uma habilidade personalizada do serviço reconhecedor do Azure Form. Para saber mais sobre habilidades personalizadas, consulte os recursos a seguir. 

* [Azure Search habilidades de energia: um repositório de habilidades personalizadas](https://github.com/Azure-Samples/azure-search-power-skills)
* [Adicionar uma habilidade personalizada a um pipeline de enriquecimento de ia](cognitive-search-custom-skill-interface.md)
* [Definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
* [Criar um qualificable (REST)](/rest/api/searchservice/create-skillset)
* [Mapear campos aprimorados](cognitive-search-output-field-mapping.md)