---
title: Executar manualmente Azure Functions não disparadas por HTTP
description: Use uma solicitação HTTP para executar Azure Functions não disparadas por HTTP
author: craigshoemaker
ms.topic: article
ms.date: 04/23/2020
ms.author: cshoe
ms.openlocfilehash: 79aebf7ed80fea370ff7a5d5cc40911da4144414
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91537694"
---
# <a name="manually-run-a-non-http-triggered-function"></a>Executar manualmente uma função não disparada por HTTP

Este artigo demonstra como executar manualmente uma função não disparada por HTTP por meio de solicitação HTTP especialmente formatada.

Em alguns contextos, talvez seja necessário executar "sob demanda" uma Função do Azure disparada indiretamente.  Exemplos de gatilhos indiretos incluem [funções em um agendamento](./functions-create-scheduled-function.md) ou funções que são executadas como resultado da [ação do outro recurso](./functions-create-storage-blob-triggered-function.md). 

[Postman](https://www.getpostman.com/) é usado no exemplo a seguir, mas você pode usar [cURL](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler) ou qualquer outro como a ferramenta para enviar solicitações HTTP.

## <a name="define-the-request-location"></a>Definir a localização da solicitação

Para executar uma função não disparada por HTTP, você precisa encontrar uma maneira de enviar uma solicitação ao Azure para executar a função. A URL usada para fazer essa solicitação usa um formulário específico.

![Definir a localização da solicitação: nome de host + caminho da pasta + nome da função](./media/functions-manually-run-non-http/azure-functions-admin-url-anatomy.png)

- **Nome do host:** A localização pública do aplicativo de funções que é composta do nome do aplicativo de funções, mais *azurewebsites.net* ou seu domínio personalizado.
- **Caminho da pasta:** Para acessar funções não disparadas por HTTP por meio de uma solicitação HTTP, você precisa enviar a solicitação por meio das pastas *admin/Functions*.
- **Nome da função:** O nome da função que você deseja executar.

Você pode usar esta localização de solicitação no Postman, juntamente com a chave mestra da função da solicitação para o Azure para executar a função.

> [!NOTE]
> Ao executar localmente, a chave mestra da função não é necessária. É possível [chamar a função](#call-the-function) diretamente omitindo o cabeçalho `x-functions-key`.

## <a name="get-the-functions-master-key"></a>Obter a chave mestra da função

1. Navegue até seu aplicativo de funções no [portal do Azure](https://portal.azure.com), selecione **chaves de aplicativo** e, em seguida, a `_master` chave. 

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key.png" alt-text="Localize a chave mestra a ser copiada." border="true":::

1. Na seção **Editar chave** , copie o valor da chave para a área de transferência e, em seguida, selecione **OK**.

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key-copy.png" alt-text="Copie a chave mestra na área de transferência." border="true":::

1. Depois de copiar a chave de *_master* , selecione **código + teste** e, em seguida, selecione **logs**. Você verá mensagens da função registrada aqui quando executar manualmente a função do Postman.

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-function-log.png" alt-text="Captura de tela que mostra a página ' código + teste ' com uma mensagem dos logs exibidos." border="true":::

> [!CAUTION]  
> Devido às permissões elevadas no aplicativo de funções concedidas pela chave mestra, você não deve compartilhar essa chave com terceiros nem distribuí-la em um aplicativo. A chave só deve ser enviada para um ponto de extremidade HTTPS.

## <a name="call-the-function"></a>Chamar a função

Abra Postman e siga estas etapas:

1. Insira a **localização de solicitação na caixa de texto URL**.
1. Garanta que o método HTTP seja definido como **POST**.
1. Selecione a guia **Cabeçalhos**.
1. Digite **x-Functions-Key** como a primeira chave e cole a chave mestra (da área de transferência) como o valor.
1. Digite **Content-Type** como a segunda chave e digite **Application/JSON** como o valor.

    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-headers.png" alt-text="Configurações de cabeçalhos do postmaster." border="true":::

1. Selecione a guia **Corpo**.
1. Digite **{"Input": "Test"}** como o corpo da solicitação.

    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-body.png" alt-text="Configurações do corpo do postmaster." border="true":::

1. Selecione **Enviar**.
        
    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-send.png" alt-text="Envie uma solicitação com o postmaster." border="true":::

    O Postman então relata um status **202 Aceito**.

1. Em seguida, volte à sua função no portal do Azure. Examine os logs e você verá mensagens provenientes da chamada manual para a função.

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key-logs.png" alt-text="Exiba os logs para ver os resultados do teste de chave mestra." border="true":::

## <a name="next-steps"></a>Próximas etapas

- [Estratégias para testar seu código no Azure Functions](./functions-test-a-function.md)
- [Depuração local do gatilho da Grade de Eventos do Azure Functions](./functions-debug-event-grid-trigger-local.md)
