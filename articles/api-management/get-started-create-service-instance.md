---
title: "Criar uma instância de Gerenciamento de API do Azure | Microsoft Docs"
description: "Siga as etapas deste tutorial para criar uma nova instância de Gerenciamento de API do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/17/2017
ms.author: apimpm
ms.openlocfilehash: 6433ea1f0eb6ad375402b998b4dfa80bded35c4b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-new-azure-api-management-service-instance"></a>Criar uma nova instância do serviço de Gerenciamento de API do Azure

Este tutorial descreve as etapas para criar uma nova instância de Gerenciamento de API usando o [portal do Azure](https://portal.azure.com/).

## <a name="prerequisites"></a>Pré-requisitos

+ Uma assinatura ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-new-service"></a>Criar um novo serviço

1. No [portal do Azure](https://portal.azure.com/), selecione **Novo** > **Enterprise Integration** > **Gerenciamento de API**.

    Como alternativa, escolha **Novo**, digite `API management` na caixa de pesquisa e pressione Enter. Clique em **Criar**.

2. Na janela **Serviço de Gerenciamento de API**, insira um **nome** exclusivo para seu serviço de Gerenciamento de API. Esse nome não poderá ser alterado posteriormente.

    > [!TIP]
    > O nome do serviço é usado para gerar um nome de domínio padrão na forma de *{name}.azure-api.net.* Se você quiser usar um nome de domínio personalizado, consulte [Configurar um domínio personalizado](configure-custom-domain.md). <br/>
    > O nome do serviço é usado para referir-se ao serviço e ao recurso do Azure correspondente.

5. Selecione uma **assinatura** entre as diferentes assinaturas do Azure às quais você tem acesso.
6. Em **Grupo de Recursos**, selecione o recurso novo ou existente.  Um grupo de recursos é uma coleção de recursos que compartilham o ciclo de vida, as permissões e as políticas. Saiba mais [aqui](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. Em **Local**, selecione a região geográfica na qual o serviço de Gerenciamento de API é criado. Somente as regiões de serviço de Gerenciamento de API disponíveis aparecem na caixa de listagem suspensa. 
9. Insira um **Nome de organização**. Esse nome é usado em vários locais. Por exemplo, o título do portal do desenvolvedor e o remetente de emails de notificação.
10. Em **Email do administrador**, defina o endereço de email para o qual todas as notificações de **Gerenciamento de API** serão enviadas.
11. Em **Tipo de preço**, defina a camada **Desenvolvedor** para avaliar o serviço. Essa camada não é para uso em produção. Para obter mais informações sobre a colocação em escala das camadas de Gerenciamento de API, confira [atualizar e dimensionar](upgrade-and-scale.md).
12. Escolha **Criar**.

    > [!TIP]
    > Geralmente leva entre 20 e 30 minutos para criar um serviço de Gerenciamento de API. Selecionar **Fixar ao painel** torna a localização de um serviço recém-criado mais fácil.

## <a name="next-steps"></a>Próximas etapas

[Publicar uma API com o Gerenciamento de API do Azure](#api-management-getstarted-publish-api.md)