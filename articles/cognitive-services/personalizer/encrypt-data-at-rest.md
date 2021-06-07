---
title: Criptografia do serviço de personalização de dados em repouso
titleSuffix: Azure Cognitive Services
description: A Microsoft oferece chaves de criptografia gerenciadas pela Microsoft e também permite que você gerencie suas assinaturas de serviços cognitivas com suas próprias chaves, chamadas CMK (chaves gerenciadas pelo cliente). Este artigo aborda a criptografia de dados em repouso para o personalizador e como habilitar e gerenciar o CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 1a27199930587c1a096dd99462ebd0c9d65054ee
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100524374"
---
# <a name="personalizer-service-encryption-of-data-at-rest"></a>Criptografia do serviço de personalização de dados em repouso

O serviço personalizado criptografa automaticamente os dados quando persistidos na nuvem. A criptografia do serviço personalizado protege seus dados e para ajudá-lo a atender aos compromissos de conformidade e segurança da organização.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> As chaves gerenciadas pelo cliente estão disponíveis somente no tipo de preço E0. Para solicitar a capacidade de usar chaves gerenciadas pelo cliente, preencha e envie o [serviço personalizado Customer-Managed formulário de solicitação de chave](https://aka.ms/cogsvc-cmk). Levará aproximadamente 3-5 dias úteis para que o status da solicitação seja reproduzido. Dependendo da demanda, você pode ser colocado em uma fila e aprovado, pois o espaço se torna disponível. Depois de aprovado para usar o CMK com o serviço personalizador, será necessário criar um novo recurso personalizado e selecionar E0 como o tipo de preço. Depois que o recurso personalizador com o tipo de preço E0 for criado, você poderá usar Azure Key Vault para configurar sua identidade gerenciada.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Próximas etapas

* [Formulário de solicitação de chave de Customer-Managed de serviço personalizado](https://aka.ms/cogsvc-cmk)
* [Saiba mais sobre o Azure Key Vault](../../key-vault/general/overview.md)