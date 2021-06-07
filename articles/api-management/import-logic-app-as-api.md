---
title: Importar um aplicativo lógico como uma API com o Portal do Azure | Microsoft Docs
description: Este artigo mostra como usar o APIM (Gerenciamento de API) para importar um aplicativo lógico como uma API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/22/2020
ms.author: apimpm
ms.openlocfilehash: 0dd460bce01b86c72d0b8fd70351fdcc85c69bd4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93146953"
---
# <a name="import-a-logic-app-as-an-api"></a>Importar um aplicativo lógico como uma API

Este artigo mostra como importar um Aplicativo Lógico como uma API e testar a API importada.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
>
> -   Importar um aplicativo lógico como uma API
> -   Testar a API no Portal do Azure
> -   Testar a API no Portal do desenvolvedor

## <a name="prerequisites"></a>Pré-requisitos

-   Conclua o início rápido a seguir: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md)
-   Verifique se há um aplicativo lógico em sua assinatura, que expõe um ponto de extremidade HTTP. Para obter mais informações, confira [Disparar fluxos de trabalho com pontos de extremidade HTTP](../logic-apps/logic-apps-http-endpoint.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Importar e publicar uma API de back-end

1. Navegue até o serviço de Gerenciamento de API no portal do Azure e selecione **APIs** no menu.
2. Selecione **Aplicativo Lógico** na lista **Adicionar uma nova API**.

    ![Aplicativo lógico](./media/import-logic-app-as-api/logic-app-api.png)

3. Pressione **Procurar** para ver a lista de Aplicativos Lógicos com gatilho HTTP em sua assinatura. (Observe que os Aplicativos Lógicos sem gatilho HTTP não serão exibidos na lista.)
4. Selecione o aplicativo. O Gerenciamento de API localiza o Swagger associado ao aplicativo selecionado, busca-o e importa-o.
5. Adicione um sufixo da URL da API. O sufixo é um nome que identifica a API específica messa instância do Gerenciamento de API. Ele deve ser exclusivo nessa instância do Gerenciamento de API.
6. Publica a API associando-a a um produto. Nesse caso, o produto "_Ilimitado_" é usado. Se você deseja que a API seja publicada e fique disponível para os desenvolvedores, adicione-a a um produto. Você pode fazer isso durante a criação da API ou configurá-lo mais tarde.

    Os produtos são associações de uma ou mais APIs. Você pode incluir várias APIs e oferecê-las aos desenvolvedores por meio do portal do desenvolvedor. Os Desenvolvedores devem primeiro se inscrever em um produto para obter acesso à API. Com a assinatura, eles obtêm uma chave de assinatura que funciona para qualquer API no produto. Se você criou a instância do Gerenciamento de API, você já é um administrador, portanto, está inscrito em cada produto, por padrão.

    Por padrão, cada instância de Gerenciamento de API é fornecida com dois produtos função Web:

    - **Inicial**
    - **Ilimitado**

7. Insira outras configurações de API. Você pode definir os valores durante a criação ou configurá-los mais tarde, acessando a guia **Configurações**. As configurações são explicadas no tutorial [Importar e publicar sua primeira API](import-and-publish.md#import-and-publish-a-backend-api).
8. Selecione **Criar**.

## <a name="test-the-api-in-the-azure-portal"></a>Testar a API no Portal do Azure

As operações podem ser chamadas diretamente do portal do Azure, o que oferece uma maneira fácil de exibir e testar as operações de uma API.

1. Selecione a API que você criou na etapa anterior.
2. Pressione a guia **Testar**.
3. Selecione alguma operação.

    A página exibe os campos dos parâmetros de consulta e os campos dos cabeçalhos. Um dos cabeçalhos é "Ocp-Apim-Subscription-Key", para a chave de assinatura do produto que está associado a essa API. Se você criou a instância do Gerenciamento de API, já é um administrador e, portanto, a chave é preenchida automaticamente.

4. Pressione **Enviar**.

    O back-end responde com **200 OK** e alguns dados.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

>[!NOTE]
>Cada aplicativo lógico tem uma operação **manual-invoke**. Se você desejar compor sua API de vários aplicativos lógicos para não ter uma colisão, será necessário renomear a função.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
>
> [Transformar e proteger uma API publicada](transform-api.md)
