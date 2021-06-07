---
title: Criptografia de Visão Personalizada de dados inativos
titleSuffix: Azure Cognitive Services
description: A Microsoft oferece chaves de criptografia gerenciadas pela Microsoft e permite que você gerencie suas assinaturas dos Serviços Cognitivos com as suas chaves, chamadas de CMK (chaves gerenciadas pelo cliente). Este artigo aborda a criptografia de dados inativos para a Visão Personalizada e como habilitar e gerenciar a CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 822a4249b6ed054f36605d0367803da68bab090b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100652251"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>Criptografia de Visão Personalizada de dados inativos

A Visão Personalizada do Azure criptografa automaticamente seus dados ao persisti-los na nuvem. A criptografia da Visão Personalizada protege seus dados e ajuda você a cumprir os compromissos de conformidade e de segurança da sua organização.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> As chaves gerenciadas pelo cliente são recursos disponíveis se criadas depois de 11 de maio de 2020. Para usar a CMK com a Visão Personalizada, será necessário criar um recurso de Visão Personalizada. Depois que o recurso for criado, você poderá usar o Azure Key Vault para configurar sua identidade gerenciada.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Próximas etapas

* Para obter uma lista completa de serviços que dão suporte à CMK, confira [Chaves Gerenciadas pelo Cliente para Serviços Cognitivos](../encryption/cognitive-services-encryption-keys-portal.md)
* [O que é o Azure Key Vault](../../key-vault/general/overview.md)?
* [Formulário de Solicitação da Chave Gerenciada pelo Cliente dos Serviços Cognitivos](https://aka.ms/cogsvc-cmk)