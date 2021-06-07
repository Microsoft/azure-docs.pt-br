---
title: Tutorial – Importar e gerenciar APIs – Gerenciamento de API do Azure e Visual Studio Code   Microsoft Docs
description: Neste tutorial, saiba como usar a extensão de Gerenciamento de API do Azure para Visual Studio Code a fim de importar, testar e gerenciar APIs.
ms.service: api-management
author: dlepow
ms.author: apimpm
ms.topic: tutorial
ms.date: 12/10/2020
ms.openlocfilehash: 0dea8e43d5f09b84c5795bc257cf3331ad919fcb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649539"
---
# <a name="tutorial-use-the-api-management-extension-for-visual-studio-code-to-import-and-manage-apis"></a>Tutorial: Usar a extensão de Gerenciamento de API para Visual Studio Code a fim de importar e gerenciar APIs

Neste tutorial, você aprenderá a usar a Extensão de Gerenciamento de API para Visual Studio Code para executar operações comuns no Gerenciamento de API. Use o ambiente do Visual Studio Code que você já conhece para importar, atualizar, testar e gerenciar APIs.

Você aprenderá como:

> [!div class="checklist"]
> * Importar uma API para o Gerenciamento de API
> * Editar a API
> * Aplicar políticas de Gerenciamento de API
> * Testar a API


:::image type="content" source="media/visual-studio-code-tutorial/tutorial-api-result.png" alt-text="API na extensão de Gerenciamento de API":::

Para obter uma introdução aos recursos adicionais do Gerenciamento de API, confira os tutoriais do Gerenciamento de API usando o [portal do Azure](import-and-publish.md).

## <a name="prerequisites"></a>Pré-requisitos
- Noções básicas sobre a [terminologia do Gerenciamento de API do Azure](api-management-terminology.md)
- Verifique se você instalou o [Visual Studio Code](https://code.visualstudio.com/) e a última [Extensão do Gerenciamento de API do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement&ssr=false#overview)
- [Criar uma instância de Gerenciamento de API](vscode-create-service-instance.md)

## <a name="import-an-api"></a>Importar uma API

O exemplo a seguir importa uma Especificação OpenAPI no formato JSON para o Gerenciamento de API. A Microsoft fornece a API de back-end usada neste exemplo e a hospeda no Azure em `https://conferenceapi.azurewebsites.net?format=json`.

1. No Visual Studio Code, selecione o ícone do Azure na barra de atividades.
1. No painel do Explorer, expanda a instância do Gerenciamento de API que você criou.
1. Clique com o botão direito do mouse em **APIs** e selecione **Importar do Link de OpenAPI**. 
1. Quando precisar, insira os seguintes valores:
    1. Um **link de OpenAPI** para o conteúdo no formato JSON. Para este exemplo: *https://conferenceapi.azurewebsites.net?format=json* .
    Essa URL é o serviço que implementa a API de exemplo. O Gerenciamento de API envia as solicitações para esse endereço.
    1. Um **nome de API**, como *demo-conference-api*, que seja exclusivo na instância do Gerenciamento de API. Esse nome só pode conter letras, números e hifens. O primeiro e o último caracteres precisam ser alfanuméricos. Esse nome é usado no caminho para chamar a API.

Depois que a API for importada com êxito, ela será exibida no painel do Explorer e as operações de API disponíveis serão mostradas no nó **Operações**.

:::image type="content" source="media/visual-studio-code-tutorial/tutorial-api-operations.png" alt-text="API importada no painel do Explorer":::

## <a name="edit-the-api"></a>Editar a API

Edite a API no Visual Studio Code. Por exemplo, edite a descrição em JSON do Resource Manager da API na janela do editor para remover o protocolo **HTTP** usado para acessar a API. Selecione **Arquivo** > **Salvar**.

:::image type="content" source="media/visual-studio-code-tutorial/import-demo-api.png" alt-text="Editar descrição em JSON":::

Para editar o formato OpenAPI, clique com o botão direito do mouse no nome da API no painel do Explorer e selecione **Editar OpenAPI**. Faça as alterações e selecione **Arquivo** > **Salvar**.

## <a name="apply-policies-to-the-api"></a>Aplicar políticas à API 

O Gerenciamento de API fornece [políticas](api-management-policies.md) que você pode configurar para suas APIs. As políticas são um conjunto de instruções executadas em sequência, na solicitação ou na resposta de uma API. As políticas podem ser globais, que se aplicam a todas as APIs na sua instância do Gerenciamento de API, ou podem ter o escopo definido para uma API ou uma operação de API específica.

Esta seção mostra como aplicar algumas políticas de saída comuns à API que transforma a resposta da API. As políticas deste exemplo alteram os cabeçalhos de resposta e ocultam as URLs de back-end originais exibidas no corpo da resposta.

1. No painel do Explorer, selecione **Política** na *demo-conference-api* importada. O arquivo de política será aberto na janela do editor. Esse arquivo configura políticas para todas as operações na API. 

1. Atualize o arquivo com o seguinte conteúdo no elemento `<outbound>`:
    ```html
    [...]
    <outbound>
        <set-header name="Custom" exists-action="override">
            <value>"My custom value"</value>
        </set-header>
        <set-header name="X-Powered-By" exists-action="delete" />
        <redirect-content-urls />
        <base />
    </outbound>
    [...]
    ```

    * A primeira política `set-header` adiciona um cabeçalho de resposta personalizado para fins de demonstração.
    * A segunda política `set-header` exclui o cabeçalho **X-Powered-By**, caso ele exista. Esse cabeçalho pode revelar a estrutura do aplicativo usada no back-end da API, e os editores geralmente o removem.
    * A política `redirect-content-urls` reescreve (mascara) os links no corpo da resposta de modo que eles apontem para os links equivalentes por meio do gateway do Gerenciamento de API.
    
1. Salve o arquivo. Se precisar, escolha **Carregar** para carregar o arquivo na nuvem.

## <a name="test-the-api"></a>Testar a API

### <a name="get-the-subscription-key"></a>Obter a chave de assinatura

Para testar a API importada que você importou e as políticas aplicadas, você precisará obter uma chave de assinatura para a instância do Gerenciamento de API.

1. No painel do Explorer, clique com o botão direito do mouse no nome da instância do Gerenciamento de API.
1. Selecione **Copiar Chave de Assinatura**.

    :::image type="content" source="media/visual-studio-code-tutorial/copy-subscription-key.png" alt-text="Copiar chave de assinatura":::

### <a name="test-an-api-operation"></a>Testar uma operação de API

1. No painel do Explorer, expanda o nó **Operações** na *demo-conference-api* importada.
1. Escolha uma operação como *GetSpeakers*.
1. Na janela do editor, ao lado de **Ocp-Apim-Subscription-Key**, substitua `{{SubscriptionKey}}` pela chave de assinatura copiada.
1. Selecione **Enviar solicitação**. 

:::image type="content" source="media/visual-studio-code-tutorial/test-api.png" alt-text="Enviar uma solicitação de API por meio do Visual Studio Code":::

Quando a solicitação for realizada com sucesso, o back-end responderá com **200 OK** e alguns dados.

:::image type="content" source="media/visual-studio-code-tutorial/test-api-policies.png" alt-text="Operação de teste de API":::

Observe os seguintes detalhes na resposta:
* O cabeçalho **Custom** é adicionado à resposta.
* O cabeçalho **X-Powered-By** não é exibido na resposta.
* As URLs para o back-end de API são redirecionadas para o gateway do Gerenciamento de API, neste caso, `https://apim-hello-world.azure-api.net/demo-conference-api`.

### <a name="trace-the-api-operation"></a>Rastrear a operação de API

Para obter informações detalhadas de rastreamento para ajudar você a depurar a operação de API, selecione o link exibido ao lado de **Ocp-APIM-Trace-Location**. 

O arquivo JSON nessa localização contém informações de rastreamento de entrada, back-end e saída, de modo que você possa determinar quando os problemas ocorrem depois que a solicitação é feita.

> [!TIP]
> Quando você testa as operações de API, a extensão do Gerenciamento de API permite a [depuração de política](api-management-debug-policies.md) opcional (disponível na camada de serviço do Desenvolvedor).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, remova a instância de Gerenciamento de API clicando com o botão direito do mouse e selecionando **Abrir no Portal** para [excluir o serviço de Gerenciamento de API](get-started-create-service-instance.md#clean-up-resources) e o grupo de recursos dele.

Como alternativa, você pode selecionar **Excluir o Gerenciamento de API** para excluir apenas a instância de Gerenciamento de API (essa operação não exclui o grupo de recursos dela).

:::image type="content" source="media/visual-studio-code-tutorial/vscode-apim-delete.png" alt-text="Excluir a instância de Gerenciamento de API do VS Code":::

## <a name="next-steps"></a>Próximas etapas

Este tutorial apresentou vários recursos da extensão de Gerenciamento de API para Visual Studio Code que podem ser usados para importar e gerenciar APIs. Você aprendeu a:

> [!div class="checklist"]
> * Importar uma API para o Gerenciamento de API
> * Editar a API
> * Aplicar políticas de Gerenciamento de API
> * Testar a API

A extensão de Gerenciamento de API fornece recursos adicionais para trabalhar com suas APIs. Por exemplo, [depurar políticas](api-management-debug-policies.md) (disponível na camada de serviço do Desenvolvedor) ou criar e gerenciar [valores nomeados](api-management-howto-properties.md).