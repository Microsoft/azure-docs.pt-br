---
title: Importar uma especificação de OpenAPI usando o Portal do Azure | Microsoft Docs
description: Saiba como importar uma especificação de OpenAPI com o Gerenciamento de API e, em seguida, testar a API nos portais do Azure e do desenvolvedor.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 05295efec3d7651c3a77dd5ad6e2c72b57a6c5a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95994668"
---
# <a name="import-an-openapi-specification"></a>Importar uma especificação de OpenAPI

Este artigo mostra como importar uma API de back-end de “especificação OpenAPI” residindo em https://conferenceapi.azurewebsites.net?format=json. Esta API de back-end é fornecida pela Microsoft e hospedada no Azure. O artigo também mostra como testar a API do APIM.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Importar uma API de back-end de “Especificação de OpenAPI”
> * Testar a API no Portal do Azure
> * Testar a API no Portal do desenvolvedor

## <a name="prerequisites"></a>Pré-requisitos

Conclua o início rápido a seguir: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Importar e publicar uma API de back-end

1. Navegue até o serviço de Gerenciamento de API no portal do Azure e selecione **APIs** no menu.
2. Selecione **Especificação de OpenAPI** na lista **Adicionar uma nova API**.

    ![Especificação de OpenAPI](./media/import-api-from-oas/oas-api.png)
3. Insira as configurações de API. Você pode definir os valores durante a criação ou configurá-los mais tarde, acessando a guia **Configurações**. As configurações são explicadas no tutorial [Importar e publicar sua primeira API](import-and-publish.md#import-and-publish-a-backend-api).
4. Selecione **Criar**.

> [!NOTE]
> As limitações de importação da API estão documentadas em [outro artigo](api-management-api-import-restrictions.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Transformar e proteger uma API publicada](transform-api.md)
