---
title: Migrar para o novo portal do desenvolvedor do portal do desenvolvedor herdado
titleSuffix: Azure API Management
description: Saiba como migrar do portal do desenvolvedor herdado para o novo portal do desenvolvedor no gerenciamento de API.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/15/2020
ms.author: apimpm
ms.openlocfilehash: f5105c685de4b3ccdffe69eec8ee8eeb32976c1f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92325961"
---
# <a name="migrate-to-the-new-developer-portal"></a>Migrar para o novo portal do desenvolvedor

Este artigo descreve as etapas que você precisa seguir para migrar do portal herdado preterido para o novo portal do desenvolvedor no gerenciamento de API.

> [!IMPORTANT]
> O portal do desenvolvedor herdado foi preterido e receberá apenas atualizações de segurança. Você pode continuar usando-o, como de costume, até a desativação dele em outubro de 2023, quando ele será removido de todos os serviços de Gerenciamento de API.

![Portal do desenvolvedor do Gerenciamento da API](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="improvements-in-new-developer-portal"></a>Aprimoramentos no novo portal do desenvolvedor

O novo portal do desenvolvedor atende a muitas limitações do portal preterido. Ele apresenta um [Editor de arrastar e soltar Visual para edição de conteúdo](api-management-howto-developer-portal-customize.md) e um painel dedicado para designers para estilizar o site. As páginas, personalizações e configurações são salvas como Azure Resource Manager recursos no serviço de gerenciamento de API, o que permite [automatizar implantações do portal](api-management-howto-developer-portal.md#automate). Por fim, a base de código do portal é de código-fonte aberto, para [que você possa estendê-la com a funcionalidade personalizada](api-management-howto-developer-portal.md#managed-vs-self-hosted).

## <a name="how-to-migrate-to-new-developer-portal"></a>Como migrar para o novo portal do desenvolvedor

O novo portal do desenvolvedor é incompatível com o portal preterido e a migração automatizada não é possível. Você precisa recriar manualmente o conteúdo (páginas, texto, arquivos de mídia) e personalizar a aparência do novo Portal. As etapas precisas variam de acordo com as personalizações e a complexidade do Portal. Consulte [o tutorial portal do desenvolvedor](api-management-howto-developer-portal-customize.md) para obter diretrizes. A configuração restante, como a lista de APIs, produtos, usuários, provedores de identidade, é compartilhada automaticamente entre os dois portais.

> [!IMPORTANT]
> Se você iniciou o novo portal do desenvolvedor antes, mas não fez nenhuma alteração, [redefina o conteúdo padrão](api-management-howto-developer-portal.md#preview-to-ga) para atualizá-lo para a versão mais recente.

Ao migrar do portal preterido, tenha em mente as seguintes alterações:

- Se você expor o portal do desenvolvedor por meio de um domínio personalizado, [atribua um domínio](configure-custom-domain.md) ao novo portal do desenvolvedor. Use a opção **portal do desenvolvedor** na lista suspensa na portal do Azure.
- [Aplique uma política CORS](api-management-howto-developer-portal.md#cors) em suas APIs para habilitar o console de teste interativo.
- Se você injetar CSS personalizado para estilizar o portal, precisará [replicar o estilo usando o painel de design interno](api-management-howto-developer-portal-customize.md). A injeção de CSS não é permitida no novo Portal.
- Você pode injetar JavaScript personalizado somente na [versão hospedada internamente do novo portal](api-management-howto-developer-portal.md#managed-vs-self-hosted).
- Se o gerenciamento de API estiver em uma rede virtual e estiver exposto à Internet por meio do gateway de aplicativo, [consulte este artigo de documentação](api-management-howto-integrate-internal-vnet-appgateway.md) para obter as etapas exatas de configuração. Você precisa:

    - Habilite a conectividade com o ponto de extremidade de gerenciamento do gerenciamento de API.
    - Habilite a conectividade com o novo ponto de extremidade do Portal.
    - Desabilite as regras de firewall do aplicativo Web selecionadas.

- Se você alterou os modelos de notificação por email padrão para incluir uma URL do portal preterida explicitamente definida, altere-as para usar o parâmetro URL do portal ou aponte para a nova URL do Portal. Se os modelos usarem o parâmetro de URL interno do portal, nenhuma alteração será necessária.
- Não há suporte para *problemas* e *aplicativos* no novo portal do desenvolvedor.
- A integração direta com o Facebook, Microsoft, Twitter e Google como provedores de identidade não tem suporte no novo portal do desenvolvedor. Você pode integrar com esses provedores por meio de Azure AD B2C.
- Se você usar a delegação, altere a URL de retorno em seus aplicativos e use o [ponto de extremidade obter API de *token de acesso compartilhado*](/rest/api/apimanagement/2019-12-01/user/getsharedaccesstoken) em vez do ponto de extremidade *gerar URL de SSO* .
- Se você usar o Azure AD como um provedor de identidade:

    - Altere a URL de retorno em seu aplicativo para apontar para o novo domínio do portal do desenvolvedor.
    - Modifique o sufixo da URL de retorno em seu aplicativo de `/signin-aad` para `/signin` .

- Se você usar Azure AD B2C como um provedor de identidade:

    - Altere a URL de retorno em seu aplicativo para apontar para o novo domínio do portal do desenvolvedor.
    - Modifique o sufixo da URL de retorno em seu aplicativo de `/signin-aad` para `/signin` .
    - Inclua o *nome*, o *sobrenome* e a *ID de objeto do usuário* nas declarações do aplicativo.

- Se você usar o OAuth 2,0 no console de teste interativo, altere a URL de retorno em seu aplicativo para apontar para o novo domínio do portal do desenvolvedor e modificar o sufixo:

    - De `/docs/services/[serverName]/console/oauth2/authorizationcode/callback` para `/signin-oauth/code/callback/[serverName]` para o fluxo de concessão de código de autorização.
    - De `/docs/services/[serverName]/console/oauth2/implicit/callback` para `/signin-oauth/implicit/callback` para o fluxo de concessão implícita.
- Se você usar o OpenID Connect no console de teste interativo, altere a URL de retorno em seu aplicativo para apontar para o novo domínio do portal do desenvolvedor e modificar o sufixo:

    - De `/docs/services/[serverName]/console/openidconnect/authorizationcode/callback` para `/signin-oauth/code/callback/[serverName]` para o fluxo de concessão de código de autorização.
    - De `/docs/services/[serverName]/console/openidconnect/implicit/callback` para `/signin-oauth/implicit/callback` para o fluxo de concessão implícita.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o portal do desenvolvedor:

- [Visão Geral do portal do desenvolvedor do Gerenciamento de API do Azure](api-management-howto-developer-portal.md)
- [Acessar e personalizar o portal do desenvolvedor](api-management-howto-developer-portal-customize.md)