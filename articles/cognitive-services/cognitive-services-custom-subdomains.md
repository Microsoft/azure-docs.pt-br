---
title: Subdomínios personalizados
titleSuffix: Azure Cognitive Services
description: Os serviços cognitivas do Azure usam nomes de subdomínio personalizados para cada recurso criado por meio do portal do Azure, Azure Cloud Shell ou CLI do Azure. Ao contrário dos pontos de extremidade regionais, que eram comuns para todos os clientes em uma região específica do Azure, os nomes de subdomínio personalizados são exclusivos para o recurso. Os nomes de subdomínio personalizados são necessários para habilitar recursos como o Azure Active Directory (Azure AD) para autenticação.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: erhopf
ms.openlocfilehash: 7153735052f96bef65bf3daaccde4eab3e61b0f9
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68473046"
---
# <a name="custom-subdomain-names-for-cognitive-services"></a>Nomes de subdomínio personalizados para serviços cognitivas

Os serviços cognitivas do Azure usam nomes de subdomínio personalizados para cada recurso criado por meio do [portal do Azure](https://portal.azure.com), [Azure cloud Shell](https://azure.microsoft.com/features/cloud-shell/)ou [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Ao contrário dos pontos de extremidade regionais, que eram comuns para todos os clientes em uma região específica do Azure, os nomes de subdomínio personalizados são exclusivos para o recurso. Os nomes de subdomínio personalizados são necessários para habilitar recursos como o Azure Active Directory (Azure AD) para autenticação.

## <a name="how-does-this-impact-existing-resources"></a>Como isso afeta os recursos existentes?

Os recursos de serviços cognitivas criados antes de 1º de julho de 2019 usarão os pontos de extremidade regionais para o serviço associado. Esses pontos de extremidade funcionarão com recursos novos e existentes.

Se você quiser migrar um recurso existente para aproveitar os nomes de subdomínio personalizados, para que possa habilitar recursos como o Azure AD, siga estas instruções:

1. Entre no portal do Azure e localize o recurso serviços cognitivas ao qual você gostaria de adicionar um nome de subdomínio personalizado.
2. Na folha **visão geral** , localize e selecione **gerar nome de domínio personalizado**.
3. Isso abre um painel com instruções para criar um subdomínio personalizado exclusivo para seu recurso.
   > [!WARNING]
   > Depois de criar um nome de subdomínio personalizado, ele **não pode** ser alterado.

## <a name="do-i-need-to-update-my-existing-resources"></a>É necessário atualizar meus recursos existentes?

Nº O ponto de extremidade Regional continuará a funcionar para serviços cognitivas novos e existentes, e o nome de subdomínio personalizado é opcional. Mesmo que um nome de subdomínio personalizado seja adicionado, o ponto de extremidade Regional continuará funcionando com o recurso.

## <a name="what-if-an-sdk-asks-me-for-the-region-for-a-resource"></a>E se um SDK solicitar a região de um recurso?

> [!WARNING]
> Os serviços de fala **não** dão suporte a subdomínios personalizados no momento. Use os pontos de extremidade regionais ao usar os serviços de fala e SDKs associados.

Os pontos de extremidade regionais e os nomes de subdomínio personalizados têm suporte e podem ser usados de forma intercambiável. No entanto, o ponto de extremidade completo é necessário.

As informações de região estão disponíveis na folha de **visão geral** do recurso no [portal do Azure](https://portal.azure.com). Para obter a lista completa de pontos de extremidade regionais, consulte [há uma lista de pontos de extremidade regionais?](#is-there-a-list-of-regional-endpoints)

## <a name="are-custom-subdomain-names-regional"></a>Os nomes de subdomínio personalizados são regionais?

Sim. O uso de um nome de subdomínio personalizado não altera nenhum dos aspectos regionais de seu recurso de serviços cognitivas.

## <a name="what-are-the-requirements-for-a-custom-subdomain-name"></a>Quais são os requisitos para um nome de subdomínio personalizado?

Um nome de subdomínio personalizado é exclusivo para seu recurso. O nome só pode incluir caracteres alfanuméricos e `-` o caractere; ele deve ter entre 2 e 64 caracteres de comprimento e não pode terminar `-`com um.

## <a name="can-i-change-a-custom-domain-name"></a>Posso alterar um nome de domínio personalizado?

Nº Depois que um nome de subdomínio personalizado é criado e associado a um recurso, ele não pode ser alterado.

## <a name="can-i-reuse-a-custom-domain-name"></a>Posso reutilizar um nome de domínio personalizado?

Cada nome de subdomínio personalizado é exclusivo, portanto, para reutilizar um nome de subdomínio personalizado que você atribuiu a um recurso de serviços cognitivas, você precisará excluir o recurso existente. Depois que o recurso tiver sido excluído, você poderá reutilizar o nome de subdomínio personalizado.

## <a name="is-there-a-list-of-regional-endpoints"></a>Há uma lista de pontos de extremidade regionais?

Sim. Esta é uma lista de pontos de extremidade regionais que você pode usar com os recursos de serviços cognitivas do Azure.

> [!NOTE]
> O API de Tradução de Texto e APIs de Pesquisa do Bing usar pontos de extremidade globais.

| Tipo de ponto de extremidade | Região | Ponto de extremidade |
|---------------|--------|----------|
| Público | Global (Tradução de Texto & Bing) | `https://api.cognitive.microsoft.com` |
| | Leste da Austrália | `https://australiaeast.api.cognitive.microsoft.com` |
| | Sul do Brasil | `https://brazilsouth.api.cognitive.microsoft.com` |
| | Canadá Central | `https://canadacentral.api.cognitive.microsoft.com` |
| | EUA Central | `https://centralus.api.cognitive.microsoft.com` |
| | Ásia Oriental | `https://eastasia.api.cognitive.microsoft.com` |
| | East US | `https://eastus.api.cognitive.microsoft.com` |
| | Leste dos EUA 2 | `https://eastus2.api.cognitive.microsoft.com` |
| | Centro da França | `https://francecentral.api.cognitive.microsoft.com` |
| | Índia Central | `https://centralindia.api.cognitive.microsoft.com` |
| | Leste do Japão | `https://japaneast.api.cognitive.microsoft.com` |
| | Coreia Central | `https://koreacentral.api.cognitive.microsoft.com` |
| | Centro-Norte dos EUA | `https://northcentralus.api.cognitive.microsoft.com` |
| | Europa Setentrional | `https://northeurope.api.cognitive.microsoft.com` |
| | Norte da África do Sul | `https://southafricanorth.api.cognitive.microsoft.com` |
| | Centro-Sul dos EUA | `https://southcentralus.api.cognitive.microsoft.com` |
| | Sudeste da Ásia | `https://southeastasia.api.cognitive.microsoft.com` |
| | Sul do Reino Unido | `https://uksouth.api.cognitive.microsoft.com` |
| | Centro-oeste dos EUA | `https://westcentralus.api.cognitive.microsoft.com` |
| | Europa Ocidental | `https://westeurope.api.cognitive.microsoft.com` |
| | Oeste dos EUA | `https://westus.api.cognitive.microsoft.com` |
| | Oeste dos EUA 2 | `https://westus2.api.cognitive.microsoft.com` |
| US Gov | US Gov - Virgínia | `https://virginia.api.cognitive.microsoft.us` |
| China | Leste da China 2 | `https://chinaeast2.api.cognitive.azure.cn` |
| | Norte da China | `https://chinanorth.api.cognitive.azure.cn` |

## <a name="see-also"></a>Consulte também

* [Quais são os serviços cognitivas?](Welcome.md)
* [Autenticação](authentication.md)
