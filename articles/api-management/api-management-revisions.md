---
title: Revisões no gerenciamento de API do Azure | Microsoft Docs
description: Saiba mais sobre o conceito de revisões no gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 06/12/2020
ms.author: jodowns
ms.custom: fasttrack-new
ms.openlocfilehash: 9a20a7966daff372cf5c0abc9b7b1dbbfd459838
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91403231"
---
# <a name="revisions-in-azure-api-management"></a>Revisões no gerenciamento de API do Azure

As revisões permitem que você faça alterações em suas APIs de maneira segura e controlada. Quando desejar fazer alterações, crie uma nova revisão. Em seguida, você pode editar e testar a API sem perturbar seus consumidores de API. Quando estiver pronto, você tornará sua revisão atual. Ao mesmo tempo, você pode, opcionalmente, postar uma entrada no log de alterações, para manter seus consumidores de API atualizados com o que mudou. O log de alterações é publicado no portal do desenvolvedor.

> [!NOTE]
> O portal do desenvolvedor não está disponível na camada de consumo.

Com as revisões, você pode:

- Faça alterações com segurança nas suas definições de API e políticas, sem perturbar sua API de produção.
- Experimente as alterações antes de publicá-las.
- Documente as alterações feitas, para que os desenvolvedores possam entender o que há de novo.
- Reverta se encontrar problemas.

[Comece a usar as revisões seguindo nosso guia.](./api-management-get-started-revise-api.md)

## <a name="accessing-specific-revisions"></a>Acessando revisões específicas

Cada revisão para sua API pode ser acessada usando uma URL especialmente formada. Acrescente `;rev={revisionNumber}` ao final da URL da API, mas antes da cadeia de caracteres de consulta, para acessar uma revisão específica dessa API. Por exemplo, você pode usar essa URL para acessar a revisão 3 da `customers` API:

`https://apis.contoso.com/customers;rev=3?customerId=123`

Por padrão, cada revisão tem as mesmas configurações de segurança que a revisão atual. Você pode alterar deliberadamente as políticas para uma revisão específica se desejar ter segurança diferente aplicada para cada revisão. Por exemplo, talvez você queira adicionar uma [política de filtragem de IP](./api-management-access-restriction-policies.md#RestrictCallerIPs) para impedir que chamadores externos acessem uma revisão que ainda está em desenvolvimento.

Uma revisão pode ser deixada offline, o que a torna inacessível para os chamadores, mesmo que tente acessar a revisão por meio de sua URL. Você pode marcar uma revisão como offline usando o portal do Azure. Se você usar o PowerShell, poderá usar o `Set-AzApiManagementApiRevision` cmdlet e definir o `Path` argumento como `$null` .

> [!NOTE]
> Sugerimos fazer revisões offline quando você não as estiver usando para teste.

## <a name="current-revision"></a>Revisão atual

Uma única revisão pode ser definida como a revisão *atual* . Essa revisão será a usada para todas as solicitações de API que não especificam um número de revisão explícito na URL. Você pode reverter para uma revisão anterior definindo essa revisão como atual.

Você pode definir uma revisão como atual usando o portal do Azure. Se você usar o PowerShell, poderá usar o `New-AzApiManagementApiRelease` cmdlet.

## <a name="revision-descriptions"></a>Descrições de revisão

Ao criar uma revisão, você pode definir uma descrição para suas próprias finalidades de acompanhamento. As descrições não são reproduzidas aos usuários da API.

Quando você define uma revisão como atual, também pode especificar uma observação de log de alterações públicas. O log de alterações está incluído no portal do desenvolvedor para que os usuários da API o exibam. Você pode modificar a observação do log de alterações usando o `Update-AzApiManagementApiRelease` cmdlet do PowerShell.

## <a name="versions-and-revisions"></a>Versões e revisões

Versões e revisões são recursos distintos. Cada versão pode ter várias revisões, assim como uma API sem controle de versão. Você pode usar revisões sem usar versões, ou o contrário. Normalmente, as versões são usadas para separar as versões de API com alterações significativas, enquanto as revisões podem ser usadas para alterações secundárias e não significativas em uma API.

Se você descobrir que sua revisão tem alterações significativas ou se deseja transformar formalmente sua revisão em uma versão beta/de teste, você pode criar uma versão a partir de uma revisão. Usando o portal do Azure, clique em ' criar versão da revisão ' no menu de contexto revisão na guia revisões.
