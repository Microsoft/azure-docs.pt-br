---
title: Importar a API SOAP usando o Portal do Azure | Microsoft Docs
description: Saiba como importar uma declaração XML padrão de uma API SOAP e, em seguida, testar a API nos portais do Azure e do desenvolvedor.
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
ms.openlocfilehash: 81ded79ee72fb7c2d89898595602cb3e6d7ae5e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011057"
---
# <a name="import-soap-api"></a>Importar a API SOAP

Este artigo mostra como importar uma representação XML padrão da API SOAP. O artigo também mostra como testar a API do Gerenciamento de API.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Importar a API SOAP
> * Testar a API no Portal do Azure
> * Testar a API no Portal do desenvolvedor

## <a name="prerequisites"></a>Pré-requisitos

Conclua o início rápido a seguir: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Importar e publicar uma API de back-end

1. Navegue até o serviço de Gerenciamento de API no portal do Azure e selecione **APIs** no menu.
2. Selecione **WSDL** na lista **Adicionar uma nova API**.

    ![API SOAP](./media/import-soap-api/wsdl-api.png)
3. Na **Especificação WSDL**, digite a URL em que a API SOAP reside.
4. O botão de opção **Passagem SOAP** é selecionado por padrão. Com essa seleção, a API será exposta como SOAP. O consumidor precisa usar regras SOAP. Se você quiser "restify" a API, siga as etapas em [Import a SOAP API and convert it to REST](restify-soap-api.md) (Importar uma API SOAP e convertê-la para REST).

    ![A captura de tela mostra a caixa de diálogo Criar com base no WSDL em que você pode inserir uma especificação do WSDL.](./media/import-soap-api/pass-through.png)
5. Pressione Tab.

    Os seguintes campos são preenchidos com as informações da API SOAP: Nome de exibição, nome, descrição.
6. Adicione um sufixo da URL da API. O sufixo é um nome que identifica a API específica messa instância do Gerenciamento de API. Ele deve ser exclusivo nessa instância do Gerenciamento de API.
7. Publica a API associando-a a um produto. Nesse caso, o produto "*Ilimitado*" é usado.  Se você deseja que a API seja publicada e fique disponível para os desenvolvedores, adicione-a a um produto. Você pode fazer isso durante a criação da API ou configurá-lo mais tarde.

    Os produtos são associações de uma ou mais APIs. Você pode incluir várias APIs e oferecê-las aos desenvolvedores por meio do portal do desenvolvedor. Os Desenvolvedores devem primeiro se inscrever em um produto para obter acesso à API. Com a assinatura, eles obtêm uma chave de assinatura que funciona para qualquer API no produto. Se você criou a instância do Gerenciamento de API, você já é um administrador, portanto, está inscrito em cada produto, por padrão.

    Por padrão, cada instância de Gerenciamento de API é fornecida com dois produtos função Web:

    * **Inicial**
    * **Ilimitado**   
8. Insira outras configurações de API. Você pode definir os valores durante a criação ou configurá-los mais tarde, acessando a guia **Configurações**. As configurações são explicadas no tutorial [Importar e publicar sua primeira API](import-and-publish.md#import-and-publish-a-backend-api).
9. Selecione **Criar**.

### <a name="test-the-new-api-in-the-administrative-portal"></a>Testar a nova API no portal administrativo

As operações podem ser chamadas diretamente do portal administrativo, que fornece uma maneira conveniente de exibir e testar as operações de uma API.  

1. Selecione a API que você criou na etapa anterior.
2. Pressione a guia **Testar**.
3. Selecione alguma operação.

    A página exibe os campos dos parâmetros de consulta e os campos dos cabeçalhos. Um dos cabeçalhos é "Ocp-Apim-Subscription-Key", para a chave de assinatura do produto que está associado a essa API. Se você criou a instância do Gerenciamento de API, já é um administrador e, portanto, a chave é preenchida automaticamente. 
1. Pressione **Enviar**.

    O back-end responde com **200 OK** e alguns dados.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Transformar e proteger uma API publicada](transform-api.md)