---
title: Editar uma API com o Portal do Azure | Microsoft Docs
description: Saiba como usar o APIM (Gerenciamento de API) para editar uma API. Adicione, exclua ou renomeie operações na instância APIM ou edite o swagger da API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/08/2017
ms.author: apimpm
ms.openlocfilehash: 1c4e64251390936e8a63ee904ec69f173cac6114
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93146698"
---
# <a name="edit-an-api"></a>Editar uma API

As etapas neste tutorial mostram como usar o APIM (Gerenciamento de API) para editar uma API. 

+ Você pode fazer isso adicionando, excluindo ou renomeando operações na instância de APIM. 
+ Você pode editar o swagger da API.

## <a name="prerequisites"></a>Pré-requisitos

+ [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md)
+ [Importar e publicar sua primeira API](import-and-publish.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="edit-an-api-in-apim"></a>Editar uma API no APIM

![Captura de tela que realça o processo de edição de uma API no APIM.](./media/edit-api/edit-api001.png)

1. Clique na guia **APIs**.
2. Selecione uma das APIs que você importou anteriormente.
3. Selecione a guia **Design**.
4. Selecione uma operação que você deseja editar.
5. Para renomear a operação, selecione um **lápis** na janela **Front-end**.

## <a name="update-the-swagger"></a>Atualizar o swagger

Você pode atualizar sua API de back-end do Portal do Azure seguindo estas etapas:

1. Selecione **Todas as operações**
2. Clique no lápis na janela **Front-end**.

    ![Captura de tela que realça o ícone de lápis na tela Front-end.](./media/edit-api/edit-api002.png)

    O swagger da sua API é exibido.

    ![Editar uma API](./media/edit-api/edit-api003.png)

3. Atualize o swagger.
4. Pressione **Salvar**.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Exemplos de política de APIM](./policy-reference.md)
> [Transformar e proteger uma API publicada](transform-api.md)