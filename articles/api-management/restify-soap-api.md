---
title: Importar uma API SOAP e converter em REST usando o Portal do Azure | Microsoft Docs
description: Saiba como importar uma API SOAP, convertê-la em REST com o Gerenciamento de API e, em seguida, testar a API nos portais do Azure e do desenvolvedor.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 11/22/2017
ms.author: apimpm
ms.openlocfilehash: a87f4d0748d14ab995f75279b6a192f350165d6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87843820"
---
# <a name="import-a-soap-api-and-convert-to-rest"></a>Importar uma API SOAP e converter em REST

Este artigo mostra como importar uma API SOAP e convertê-la em REST. O artigo também mostra como testar a API do APIM.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Importar uma API SOAP e converter em REST
> * Testar a API no Portal do Azure
> * Testar a API no Portal do desenvolvedor

## <a name="prerequisites"></a>Pré-requisitos

Conclua o início rápido a seguir: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Importar e publicar uma API de back-end

1. Selecione **APIs** em **GERENCIAMENTO DE API**.
2. Selecione **WSDL** na lista **Adicionar uma nova API**.

    ![API SOAP](./media/restify-soap-api/wsdl-api.png)
3. Na **Especificação WSDL**, digite a URL em que a API SOAP reside.
4. Clique no botão de opção **SOAP para REST**. Quando essa opção é selecionada, o APIM tenta fazer uma transformação automática entre XML e JSON. Nesse caso os consumidores devem chamar a API como uma API restful, que retorna JSON. O APIM está convertendo cada solicitação em uma chamada SOAP.

    ![SOAP para REST](./media/restify-soap-api/soap-to-rest.png)

5. Pressione Tab.

    Os seguintes campos são preenchidos com as informações da API SOAP: Nome de exibição, nome, descrição.
6. Adicione um sufixo da URL da API. O sufixo é um nome que identifica essa API específica nesta instância do APIM. Ele deve ser exclusivo nesta instância de APIM.
9. Publica a API associando-a a um produto. Nesse caso, o produto "*Ilimitado*" é usado.  Se você deseja que a API seja publicada e fique disponível para os desenvolvedores, adicione-a a um produto. Você pode fazer isso durante a criação da API ou configurá-lo mais tarde.

    Os produtos são associações de uma ou mais APIs. Você pode incluir várias APIs e oferecê-las aos desenvolvedores por meio do portal do desenvolvedor. Os Desenvolvedores devem primeiro se inscrever em um produto para obter acesso à API. Com a assinatura, eles obtêm uma chave de assinatura que funciona para qualquer API no produto. Se você criou a instância do APIM, já é um administrador e, portanto, está inscrito em cada produto por padrão.

    Por padrão, cada instância de Gerenciamento de API é fornecida com dois produtos função Web:

    * **Inicial**
    * **Ilimitado**   
10. Selecione **Criar**.

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