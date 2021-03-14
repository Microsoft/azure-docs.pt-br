---
title: Visão Personalizada criptografia de dados em repouso
titleSuffix: Azure Cognitive Services
description: A Microsoft oferece chaves de criptografia gerenciadas pela Microsoft e também permite que você gerencie suas assinaturas de serviços cognitivas com suas próprias chaves, chamadas CMK (chaves gerenciadas pelo cliente). Este artigo aborda a criptografia de dados em repouso para Visão Personalizada e como habilitar e gerenciar CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 822a4249b6ed054f36605d0367803da68bab090b
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652251"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>Visão Personalizada criptografia de dados em repouso

O Azure Visão Personalizada criptografa automaticamente seus dados quando persistidos na nuvem. Visão Personalizada criptografia protege seus dados e para ajudá-lo a atender aos compromissos de segurança e conformidade da organização.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> As chaves gerenciadas pelo cliente só são recursos disponíveis criados após 11 de maio de 2020. Para usar o CMK com Visão Personalizada, será necessário criar um novo recurso de Visão Personalizada. Depois que o recurso for criado, você poderá usar Azure Key Vault para configurar sua identidade gerenciada.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Próximas etapas

* Para obter uma lista completa de serviços que dão suporte a CMK, consulte [chaves gerenciadas pelo cliente para serviços cognitivas](../encryption/cognitive-services-encryption-keys-portal.md)
* [O que é Azure Key Vault](../../key-vault/general/overview.md)?
* [Customer-Managed formulário de solicitação de chave de serviços cognitivas](https://aka.ms/cogsvc-cmk)