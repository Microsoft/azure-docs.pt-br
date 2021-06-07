---
title: Acesso à API dos serviços de mídia V2 vs v3
description: Este artigo descreve as diferenças de acesso à API entre os serviços de mídia do Azure v2 a v3.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 5f3c6526139389da3bfdbc3c43cf8b6d2a1dbccf
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567960"
---
# <a name="api-access-differences-between-azure-media-services-v2-to-v3-api"></a>Diferenças de acesso à API entre a API dos serviços de mídia do Azure v2 para v3

![logotipo do guia de migração](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![etapas de migração 2](./media/migration-guide/steps-2.svg)

Este artigo descreve as diferenças de acesso à API entre os serviços de mídia do Azure v2 a v3.

## <a name="api-access"></a>Acesso à API

Todas as contas de serviços de mídia terão acesso à API v3. No entanto, é altamente recomendável o desenvolvimento de migração em uma conta nova antes de aplicar o código atualizado a uma conta v2 existente. Isso ocorre porque as entidades v3 não são compatíveis com o v2. Algumas entidades v2, como ativos, são compatíveis com o v3.
Você pode continuar a usar contas existentes se não misturar as APIs V2 e v3 e, em seguida, tentar voltar para a v2, mas isso não é recomendado.

O acesso à API v2 estará disponível até ser desativado no 2024.

## <a name="create-a-v3-account"></a>Criar uma conta v3

Enquanto estiver migrando, você poderá criar uma conta v3 que ainda tenha acesso à v2.  A criação da conta pode ser feita com:

- A API REST e a versão mais antiga
- Marcando a caixa de seleção no Portal.

> [!div class="mx-imgBorder"]
> [![criação de conta no portal ](./media/migration-guide/v-3-v-2-access-account-creation-small.png)](./media/migration-guide/v-3-v-2-access-account-creation.png#lightbox)

Todos os .NET, CLI e outros SDKs serão direcionados para a API mais recente do 2020-05-01, portanto, localize ou configure as versões mais antigas da API.

> [!NOTE]
> Novas contas criadas com a API 2020-05-01 não podem usar APIs v2.
