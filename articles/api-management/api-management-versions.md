---
title: Versões no gerenciamento de API do Azure | Microsoft Docs
description: Saiba mais sobre o conceito de versões no gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 02/10/2021
ms.author: jodowns
ms.custom: fasttrack-new
ms.openlocfilehash: 55951f288314d92cf5057e7d5c1e988f65cb3e14
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102040372"
---
# <a name="versions-in-azure-api-management"></a>Versões no gerenciamento de API do Azure

As versões permitem que você apresente grupos de APIs relacionadas aos seus desenvolvedores. Você pode usar versões para manipular alterações significativas em sua API com segurança. Os clientes podem optar por usar sua nova versão de API quando estiverem prontos, enquanto os clientes existentes continuam a usar uma versão mais antiga. As versões são diferenciadas por meio de um identificador de versão (que é qualquer valor de cadeia de caracteres que você escolher), e um esquema de controle de versão permite que os clientes identifiquem qual versão de uma API eles desejam usar.

Para a maioria das finalidades, cada versão de API pode ser considerada sua própria API independente. Duas versões de API diferentes podem ter diferentes conjuntos de operações e políticas diferentes.

Com as versões, você pode:

- Publicar várias versões de sua API ao mesmo tempo.
- Use um caminho, uma cadeia de caracteres de consulta ou um cabeçalho para diferenciar as versões.
- Use qualquer valor de cadeia de caracteres que você queira identificar sua versão, que pode ser um número, uma data ou um nome.
- Mostre suas versões de API agrupadas no portal do desenvolvedor.
- Pegue uma API (sem versão) existente e crie uma nova versão sem interromper os clientes existentes.

[Comece a usar as versões seguindo nosso passo a passos.](./api-management-get-started-publish-versions.md)

## <a name="versioning-schemes"></a>Esquemas de controle de versão

Diferentes desenvolvedores de API têm requisitos diferentes para o controle de versão. O gerenciamento de API do Azure não prescreve uma abordagem única para controle de versão, mas, em vez disso, fornece várias opções.

### <a name="path-based-versioning"></a>Controle de versão baseado em caminho

Quando o esquema de controle de versão do caminho é usado, o identificador de versão precisa ser incluído no caminho da URL para qualquer solicitação de API.

Por exemplo, `https://apis.contoso.com/products/v1` e `https://apis.contoso.com/products/v2` pode se referir à mesma `products` API, mas às versões `v1` e, `v2` respectivamente.

O formato de uma URL de solicitação de API ao usar o controle de versão baseado em caminho é: `https://{yourDomain}/{apiName}/{versionIdentifier}/{operationId}` .

### <a name="header-based-versioning"></a>Controle de versão baseado em cabeçalho

Quando o esquema de controle de versão do cabeçalho é usado, o identificador de versão precisa ser incluído em um cabeçalho de solicitação HTTP para qualquer solicitação de API. Você pode especificar o nome do cabeçalho de solicitação HTTP.

Por exemplo, você pode criar um cabeçalho personalizado chamado `Api-Version` , e os clientes podem especificar `v1` ou `v2` no valor desse cabeçalho.

### <a name="query-string-based-versioning"></a>Controle de versão baseado em cadeia de caracteres de consulta

Quando o esquema de controle de versão da cadeia de caracteres de consulta é usado, o identificador de versão precisa ser incluído em um parâmetro de cadeia de caracteres de consulta para qualquer solicitação de API. Você pode especificar o nome do parâmetro de cadeia de caracteres de consulta.

O formato de uma URL de solicitação de API ao usar o controle de versão baseado em cadeia de caracteres de consulta é: `https://{yourDomain}/{apiName}/{operationId}?{queryStringParameterName}={versionIdentifier}` .

Por exemplo, `https://apis.contoso.com/products?api-version=v1` e `https://apis.contoso.com/products/api-version=v2` pode se referir à mesma `products` API, mas às versões `v1` e, `v2` respectivamente.

## <a name="original-versions"></a>Versões originais

Se você adicionar uma versão a uma API sem controle de versão, uma `Original` versão será criada automaticamente e responderá à URL padrão, sem um identificador de versão especificado. A `Original` versão garante que os chamadores existentes não sejam desfeitos pelo processo de adição de uma versão. Se você criar uma nova API com versões habilitadas no início, uma `Original` versão não será criada.

## <a name="how-versions-are-represented"></a>Como as versões são representadas

O gerenciamento de API do Azure mantém um recurso chamado um *conjunto* de versões, que representa um conjunto de versões para uma única API lógica. Um conjunto de versões contém o nome de exibição da API com versão e o [esquema de controle de versão usados](#versioning-schemes) para direcionar solicitações para versões especificadas.

Cada versão de uma API é mantida como seu próprio recurso de API, que é então associado a um conjunto de versões. Um conjunto de versões pode conter APIs com operações ou políticas diferentes. Você pode fazer alterações significativas entre as versões em um conjunto.

O portal do Azure cria conjuntos de versão para você. Você pode modificar o nome e a descrição de um conjunto de versões no portal do Azure.

Você pode exibir e gerenciar conjuntos de versão diretamente usando [CLI do Azure](/cli/azure/apim/api/versionset), [Azure PowerShell](/powershell/module/az.apimanagement/#api-management), [modelos do Resource Manager](/azure/templates/microsoft.apimanagement/service/apiversionsets)ou a [API do Azure Resource Manager](/rest/api/apimanagement/2020-06-01-preview/apiversionset).

### <a name="migrating-a-non-versioned-api-to-a-versioned-api"></a>Migrando uma API sem controle de versão para uma API com versão

Quando você usa o portal do Azure para habilitar o controle de versão em uma API existente, as seguintes alterações são feitas nos recursos de gerenciamento de API:

 * Um novo conjunto de versões é criado.
 * A versão existente é mantida e [configurada como a `Original` versão da API](#original-versions). A API está vinculada ao conjunto de versões, mas não requer que um identificador de versão seja especificado.
 * A nova versão é criada como uma nova API e está vinculada ao conjunto de versões. Essa nova API deve ser acessada usando o esquema e o identificador de controle de versão.

## <a name="versions-and-revisions"></a>Versões e revisões

Versões e revisões são recursos distintos. Cada versão pode ter várias revisões, assim como uma API sem controle de versão. Você pode usar revisões sem usar versões, ou o contrário. Normalmente, as versões são usadas para separar as versões de API com alterações significativas, enquanto as revisões podem ser usadas para alterações secundárias e não significativas em uma API.

Se você descobrir que sua revisão tem alterações significativas ou se deseja transformar formalmente sua revisão em uma versão beta/de teste, você pode criar uma versão a partir de uma revisão. Usando o portal do Azure, clique em ' criar versão da revisão ' no menu de contexto revisão na guia revisões.

## <a name="developer-portal"></a>Portal do desenvolvedor

O [portal do desenvolvedor](./api-management-howto-developer-portal.md) lista cada versão de uma API separadamente.

![Portal do desenvolvedor de gerenciamento de API exibindo uma lista de APIs com versão](media/api-management-versions/portal-list.png)

Os detalhes de uma API também mostram uma lista de todas as versões dessa API. Uma `Original` versão é exibida sem um identificador de versão.

![Portal do desenvolvedor de gerenciamento de API exibindo os detalhes de uma API e uma lista de versões para essa API](media/api-management-versions/portal-details.png)

> [!TIP]
> As versões de API precisam ser adicionadas a um produto antes de serem visíveis no portal do desenvolvedor.
