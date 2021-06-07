---
title: Criptografia do serviço de Detecção Facial para dados inativos
titleSuffix: Azure Cognitive Services
description: A Microsoft oferece chaves de criptografia gerenciadas pela Microsoft e permite que você gerencie suas assinaturas dos Serviços Cognitivos com as suas chaves, chamadas de CMK (chaves gerenciadas pelo cliente). Este artigo aborda a criptografia de dados inativos para a Detecção Facial e como habilitar e gerenciar a CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: a46253e586aaf90a85bc10e2c62ab6eb238f2ead
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100650806"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Criptografia do serviço de Detecção Facial para dados inativos

O serviço de Detecção Facial criptografa automaticamente seus dados ao persisti-los na nuvem. A criptografia do serviço de Detecção Facial protege seus dados e ajuda você a cumprir os compromissos de conformidade e segurança da sua organização.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> As chaves gerenciadas pelo cliente estão disponíveis somente no tipo de preço E0. Para solicitar a capacidade de usar as chaves gerenciadas pelo cliente, preencha e envie o [Formulário de Solicitação de Chaves Gerenciadas pelo Cliente do Serviço de Detecção Facial](https://aka.ms/cogsvc-cmk). Levará aproximadamente de 3 a 5 dias úteis para você receber um retorno referente ao status da sua solicitação. Dependendo da demanda, você poderá ser colocado em uma fila e ser aprovado quando abrir espaço. Depois de aprovado para usar a CMK com o serviço de Detecção Facial, você precisará criar um recurso de Detecção Facial e selecionar E0 como o Tipo de Preço. Depois que o recurso de Detecção Facial com o tipo de preço E0 for criado, você poderá usar o Azure Key Vault para configurar sua identidade gerenciada.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Próximas etapas

* Para obter uma lista completa de serviços que dão suporte à CMK, confira [Chaves Gerenciadas pelo Cliente para Serviços Cognitivos](../encryption/cognitive-services-encryption-keys-portal.md)
* [O que é o Azure Key Vault](../../key-vault/general/overview.md)?
* [Formulário de Solicitação da Chave Gerenciada pelo Cliente dos Serviços Cognitivos](https://aka.ms/cogsvc-cmk)
