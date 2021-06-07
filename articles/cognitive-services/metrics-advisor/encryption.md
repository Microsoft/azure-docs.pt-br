---
title: Criptografia do serviço do revisor de métricas
titleSuffix: Azure Cognitive Services
description: Criptografia de dados do serviço Orientador de métricas em repouso.
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 5d41500a9c53e38cd36f0feba602e0e1baa5da2c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92909736"
---
# <a name="metrics-advisor-service-encryption-of-data-at-rest"></a>Criptografia do serviço do assistente de métricas de dados em repouso

O serviço do revisor de métricas criptografa automaticamente seus dados quando persistidos na nuvem. A criptografia do serviço do revisor de métricas protege seus dados e para ajudá-lo a atender aos compromissos de conformidade e segurança da organização.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> As chaves gerenciadas pelo cliente estão disponíveis somente no tipo de preço E0. Para solicitar a capacidade de usar chaves gerenciadas pelo cliente, preencha e envie o [serviço do supervisor de métricas Customer-Managed formulário de solicitação de chave](https://aka.ms/cogsvc-cmk). Levará aproximadamente 3-5 dias úteis para que o status da solicitação seja reproduzido. Dependendo da demanda, você pode ser colocado em uma fila e aprovado, pois o espaço se torna disponível. Depois de aprovado para usar o CMK com o serviço do assistente de métricas, você precisará criar um novo recurso do revisor de métricas e selecionar E0 como o tipo de preço. Depois que o recurso do supervisor de métricas com o tipo de preço E0 for criado, você poderá usar Azure Key Vault para configurar sua identidade gerenciada.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Próximas etapas

* [Formulário de solicitação de chave Customer-Managed serviço do supervisor de métrica](https://aka.ms/cogsvc-cmk)
* [Saiba mais sobre o Azure Key Vault](../../key-vault/general/overview.md)