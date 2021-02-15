---
title: Content Moderator criptografia de dados em repouso
titleSuffix: Azure Cognitive Services
description: Content Moderator criptografia de dados em repouso.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 1401108a594e30790e842ec379724603f11d493f
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100524384"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>Content Moderator criptografia de dados em repouso

Content Moderator criptografa automaticamente seus dados quando eles são persistidos na nuvem, ajudando a atender às suas metas organizacionais de segurança e conformidade.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> As chaves gerenciadas pelo cliente estão disponíveis somente no tipo de preço E0. Para solicitar a capacidade de usar chaves gerenciadas pelo cliente, preencha e envie o [Content Moderator Customer-Managed formulário de solicitação de chave](https://aka.ms/cogsvc-cmk). Levará aproximadamente 3-5 dias úteis para que o status da solicitação seja reproduzido. Dependendo da demanda, você pode ser colocado em uma fila e aprovado, pois o espaço se torna disponível. Depois de aprovado para usar o CMK com o serviço Content Moderator, você precisará criar um novo recurso de Content Moderator e selecionar E0 como o tipo de preço. Depois que o recurso de Content Moderator com o tipo de preço E0 for criado, você poderá usar Azure Key Vault para configurar sua identidade gerenciada.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>Habilitar a criptografia de dados para sua equipe de Content Moderator

Para habilitar a criptografia de dados para sua equipe de revisão de Content Moderator, consulte o guia de [início rápido: tentar Content moderator na Web](quick-start.md#create-a-review-team).  

> [!NOTE]
> Você precisará fornecer uma _ID de recurso_ com o tipo de preço Content moderator E0.

## <a name="next-steps"></a>Próximas etapas

* Para obter uma lista completa de serviços que dão suporte a CMK, consulte [chaves gerenciadas pelo cliente para serviços cognitivas](../encryption/cognitive-services-encryption-keys-portal.md)
* [O que é Azure Key Vault](../../key-vault/general/overview.md)?
* [Customer-Managed formulário de solicitação de chave de serviços cognitivas](https://aka.ms/cogsvc-cmk)