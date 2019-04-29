---
title: Exportar ou excluir dados de usuário - Content Moderator
titlesuffix: Azure Cognitive Services
description: Saiba como exportar ou excluir seus dados no Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: 1cd70bee22e56e2580b322b93e0f121261d97a94
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60699165"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Exportar ou excluir dados do usuário no Content Moderator

O Content Moderator coleta dados de usuário para operar o serviço, mas os clientes precisam ter controle total sobre a exibição, a exportação e a exclusão dos dados usando a [ferramenta de Revisão](https://contentmoderator.cognitive.microsoft.com/) e as [APIs de Moderação e Revisão](https://docs.microsoft.com/azure/cognitive-services/content-moderator/api-reference).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Para obter mais informações sobre como exportar e excluir dados do usuário em Content Moderator, veja a tabela a seguir.

| Dados | Operação de exportar | Operação de Exclusão |
| ---- | ---------------- | ---------------- |
| Informações da Conta (Chaves de Assinatura) | N/D | Exclua usando o portal do Azure (Assinaturas do Azure). Ou use o botão **Excluir Equipe** na página de configurações da Equipe de [Examinar Interface do Usuário](https://contentmoderator.cognitive.microsoft.com/). |
| Imagens para correspondência personalizada | Chame a [API Obter IDs de imagem](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676). As imagens são armazenadas em um formato de hash proprietário unidirecional e não é possível extrair as imagens reais. | Chame a [API Excluir todas as imagens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686). Ou exclua o recurso Content Moderator usando o portal do Azure. |
| Termos para correspondência personalizada | Chame a [API Obter todos os termos](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) | Chame a [API Excluir todos os termos](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d). Ou exclua o recurso Content Moderator usando o portal do Azure. |
| Marcas | N/D | Use o ícone **Excluir** disponível para cada marcação na página de configurações de Marcação de Interface do Usuário de Análise. Ou use o botão **Excluir Equipe** na página de configurações da Equipe de [Examinar Interface do Usuário](https://contentmoderator.cognitive.microsoft.com/). |
| Análises | Chame a [API Obter revisão](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) | Use o botão **Excluir Equipe** na página de configurações da Equipe de [Interface do Usuário de Análise](https://contentmoderator.cognitive.microsoft.com/).
| Usuários | N/D | Use o ícone **Excluir** disponível para cada usuário na página de configurações de Equipe [Interface do Usuário de Análise](https://contentmoderator.cognitive.microsoft.com/). Ou use o botão **Excluir Equipe** na página de configurações da Equipe de [Examinar Interface do Usuário](https://contentmoderator.cognitive.microsoft.com/). |

