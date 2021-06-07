---
title: Importar um aplicativo de API como uma API com o portal do Azure | Microsoft Docs
description: Este artigo mostra como usar o APIM (Gerenciamento de API) para importar um aplicativo de API como uma API.
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
ms.openlocfilehash: 41209233ec59f578db4ff7fd344bb96aefeb975e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95994733"
---
# <a name="import-an-api-app-as-an-api"></a>Importar um aplicativo de API como uma API

Este artigo mostra como importar um aplicativo de API como uma API. O artigo também mostra como testar a API do APIM.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Importar um aplicativo de API como uma API
> * Testar a API no Portal do Azure
> * Testar a API no Portal do desenvolvedor

## <a name="prerequisites"></a>Pré-requisitos

+ Conclua o início rápido a seguir: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md)
+ Verifique se há um aplicativo de API em sua assinatura. Para saber mais, veja a [Documentação do Serviço de Aplicativo](../app-service/index.yml)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Importar e publicar uma API de back-end

1. Navegue até o serviço de Gerenciamento de API no portal do Azure e selecione **APIs** no menu.
2. Selecione **Aplicativo de API** na lista **Adicionar uma nova API**.

    ![Aplicativo de API](./media/import-api-app-as-api/api-app.png)
3. Pressione **Procurar** para ver a lista de aplicativos de API em sua assinatura.
4. Selecione o aplicativo. O APIM localiza o swagger associado ao aplicativo selecionado, busca-o e importa-o. 

    Caso o APIM não encontre o swagger, a API será exposta como uma API de "passagem". 
5. Adicione um sufixo da URL da API. O sufixo é um nome que identifica essa API específica nesta instância do APIM. Ele deve ser exclusivo nesta instância de APIM.
6. Publica a API associando-a a um produto. Nesse caso, o produto "*Ilimitado*" é usado.  Se você deseja que a API seja publicada e fique disponível para os desenvolvedores, adicione-a a um produto. Você pode fazer isso durante a criação da API ou configurá-lo mais tarde.

    Os produtos são associações de uma ou mais APIs. Você pode incluir várias APIs e oferecê-las aos desenvolvedores por meio do portal do desenvolvedor. Os Desenvolvedores devem primeiro se inscrever em um produto para obter acesso à API. Com a assinatura, eles obtêm uma chave de assinatura que funciona para qualquer API no produto. Se você criou a instância do APIM, já é um administrador e, portanto, está inscrito em cada produto por padrão.

    Por padrão, cada instância de Gerenciamento de API é fornecida com dois produtos função Web:

    * **Inicial**
    * **Ilimitado**   
7. Insira outras configurações de API. Você pode definir os valores durante a criação ou configurá-los mais tarde, acessando a guia **Configurações**. As configurações são explicadas no tutorial [Importar e publicar sua primeira API](import-and-publish.md#import-and-publish-a-backend-api).
8. Selecione **Criar**.

## <a name="test-the-new-api-in-the-azure-portal"></a>Testar a nova API no portal do Azure

As operações podem ser chamadas diretamente do portal do Azure, o que oferece uma maneira fácil de exibir e testar as operações de uma API.  

1. Selecione a API que você criou na etapa anterior.
2. Pressione a guia **Testar**.
3. Selecione alguma operação.

    A página exibe os campos dos parâmetros de consulta e os campos dos cabeçalhos. Um dos cabeçalhos é "Ocp-Apim-Subscription-Key", para a chave de assinatura do produto que está associado a essa API. Se você criou a instância do APIM, já é um administrador e, portanto, a chave é preenchida automaticamente. 
1. Pressione **Enviar**.

    O back-end responde com **200 OK** e alguns dados.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Transformar e proteger uma API publicada](transform-api.md)
