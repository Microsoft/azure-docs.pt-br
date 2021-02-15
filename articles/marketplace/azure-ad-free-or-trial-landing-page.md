---
title: Crie a página de aterrissagem para sua oferta de SaaS gratuita ou de avaliação no Marketplace comercial
description: Saiba como criar uma página de aterrissagem para sua oferta de SaaS gratuita ou de avaliação.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/04/2020
ms.openlocfilehash: 699271316eccec1244db886ed2296f87c52f91ae
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348348"
---
# <a name="build-the-landing-page-for-your-free-or-trial-saas-offer-in-the-commercial-marketplace"></a>Crie a página de aterrissagem para sua oferta de SaaS gratuita ou de avaliação no Marketplace comercial

Este artigo orienta você pelo processo de criação de uma página de aterrissagem para um aplicativo SaaS gratuito ou de avaliação que será vendido no Microsoft Commercial Marketplace.

## <a name="overview"></a>Visão geral

Você pode considerar a página de aterrissagem como o "lobby" de sua oferta de SaaS (software como serviço). Depois que o cliente opta por obter seu aplicativo, o Marketplace comercial os direciona para a página de aterrissagem para ativar e configurar sua assinatura para seu aplicativo SaaS. Ao criar uma oferta de SaaS (software como serviço), no Partner Center, você pode escolher se deseja ou não [vender pela Microsoft](plan-saas-offer.md#listing-options). Se você quiser listar apenas sua oferta no Microsoft Commercial Marketplace e não vender pela Microsoft, poderá especificar como os clientes potenciais podem interagir com a oferta. Ao habilitar a opção de listagem **obter agora (gratuito)** ou **avaliação gratuita** , você deve especificar uma URL da página de aterrissagem para a qual o usuário pode acessar a assinatura ou avaliação gratuita.

A finalidade da página de aterrissagem é simplesmente receber o usuário para que eles possam ativar a avaliação gratuita ou assinatura gratuita. Usando o Azure Active Directory (Azure AD) e Microsoft Graph, você habilitará o SSO (logon único) para o usuário e obterá detalhes importantes sobre o usuário que você pode usar para ativar sua avaliação gratuita ou assinatura gratuita, incluindo seu nome, endereço de email e organização.

Como as informações necessárias para ativar a assinatura são limitadas e fornecidas pelo Azure AD e Microsoft Graph, não deve ser necessário solicitar informações que exijam mais do que o consentimento básico. Se você precisar de detalhes do usuário que exigem consentimento adicional para seu aplicativo, solicite essas informações após a conclusão da ativação da assinatura. Isso permite a ativação de assinatura descomplicada para o usuário e diminui o risco de abandonar.

A página de aterrissagem normalmente inclui as seguintes informações e opções de listagem:

- Apresente o nome e os detalhes da avaliação gratuita ou da assinatura gratuita. Por exemplo, especifique os limites de uso ou a duração de uma avaliação.
- Apresente os detalhes da conta do usuário, incluindo nome e sobrenome, organização e email.
- Solicite que o usuário confirme ou substitua detalhes de conta diferentes.
- Oriente o usuário nas próximas etapas após a ativação. Por exemplo, receba um email de boas-vindas, gerencie a assinatura, obtenha suporte ou leia a documentação.

As seções a seguir neste artigo guiarão você pelo processo de criação de uma página de aterrissagem:

1. [Crie um registro de aplicativo do Azure ad](#create-an-azure-ad-app-registration) para a página de aterrissagem.
2. [Use um exemplo de código como um ponto de partida](#use-a-code-sample-as-a-starting-point) para seu aplicativo.
3. [Ler informações de declarações codificadas no token de ID](#read-information-from-claims-encoded-in-the-id-token), recebidas do Azure ad após a entrada, que foi enviada com a solicitação.
4. [Use a API Microsoft Graph](#use-the-microsoft-graph-api) para coletar informações adicionais, conforme necessário.

## <a name="create-an-azure-ad-app-registration"></a>Criar um registro de aplicativo do Azure AD

O Marketplace comercial está totalmente integrado ao Azure AD. Os usuários chegam ao Marketplace autenticado com uma [conta do Azure ad ou conta Microsoft (MSA)](../active-directory/fundamentals/active-directory-whatis.md#terminology). Depois de adquirir uma assinatura de avaliação gratuita ou gratuita por meio de sua oferta somente lista, o usuário passa do mercado comercial para a URL da página de aterrissagem para ativar e gerenciar sua assinatura para seu aplicativo SaaS. Você deve permitir que o usuário entre em seu aplicativo com o SSO do Azure AD. (A URL da página de aterrissagem é especificada na página de [configuração técnica](plan-saas-offer.md#technical-information) da oferta.

A primeira etapa para usar a identidade é verificar se sua página de aterrissagem está registrada como um aplicativo do Azure AD. O registro do aplicativo permite que você use o Azure AD para autenticar usuários e solicitar acesso aos recursos do usuário. Ele pode ser considerado a definição do aplicativo, o que permite que o serviço saiba como emitir tokens para o aplicativo com base nas configurações do aplicativo.

### <a name="register-a-new-application-using-the-azure-portal"></a>Registrar um novo aplicativo usando o portal do Azure

Para começar, siga as instruções para [registrar um novo aplicativo](../active-directory/develop/quickstart-register-app.md). Para permitir que os usuários de outras empresas visitem o aplicativo, você deve escolher **contas em qualquer diretório organizacional (qualquer diretório do Azure ad — multilocatário) e contas pessoais da Microsoft (como o Skype ou o Xbox)** quando perguntado quem pode usar o aplicativo.

Se você pretende consultar a API de Microsoft Graph, [configure seu novo aplicativo para acessar APIs da Web](../active-directory/develop/quickstart-configure-app-access-web-apis.md). Quando você seleciona as permissões de API para esse aplicativo, o padrão de **User. Read** é suficiente para coletar informações básicas sobre o usuário para tornar o processo de integração suave e automático. Não solicite nenhuma permissão de API rotulada **precisa de consentimento do administrador** , pois isso impedirá que todos os usuários não administradores visitem sua página de aterrissagem.

Se você exigir permissões elevadas como parte de seu processo de integração ou de provisionamento, considere usar a funcionalidade de [consentimento incremental](../active-directory/azuread-dev/azure-ad-endpoint-comparison.md) do Azure ad para que todos os usuários enviados do Marketplace possam interagir inicialmente com a página de aterrissagem.

## <a name="use-a-code-sample-as-a-starting-point"></a>Usar um exemplo de código como um ponto de partida

A Microsoft forneceu vários aplicativos de exemplo que implementam um site simples com o logon do Azure AD habilitado. Depois que o aplicativo é registrado no Azure AD, a folha **início rápido** oferece uma lista de tipos de aplicativos e pilhas de desenvolvimento comuns (Figura 1). Escolha aquele que corresponda ao seu ambiente e siga as instruções para baixar e configurar.

**_Figura 1: folha de início rápido na portal do Azure_* _

:::image type="content" source="./media/azure-ad-saas/azure-ad-quickstart-blade.png" alt-text="Ilustra a folha de início rápido no portal do Azure.":::

Depois de baixar o código e configurar seu ambiente de desenvolvimento, altere as definições de configuração no aplicativo para refletir a ID do aplicativo, a ID do locatário e o segredo do cliente que você registrou no procedimento anterior. Observe que as etapas exatas serão diferentes dependendo do exemplo que você estiver usando.

## <a name="read-information-from-claims-encoded-in-the-id-token"></a>Ler informações de declarações codificadas no token de ID

Como parte do fluxo do [OpenID Connect](../active-directory/develop/v2-protocols-oidc.md) , o Azure ad adiciona um [token de ID](../active-directory/develop/id-tokens.md) à solicitação quando o usuário é enviado para a página de aterrissagem. Esse token contém várias partes de informações básicas que podem ser úteis no processo de ativação, incluindo as informações vistas nesta tabela.

| Valor | Descrição |
| ------------ | ------------- |
| aud | Público-alvo para este token. Nesse caso, ele deve corresponder à ID do aplicativo e ser validado. |
| preferred_username | Nome de usuário principal do usuários visitantes. Pode ser um endereço de email, número de telefone ou outro identificador. |
| email | Endereço de email do usuário. Observe que esse campo pode estar vazio. |
| name | Valor legível por humanos que identifica o assunto do token. Nesse caso, será o nome do usuário. |
| oid | Identificador no sistema de identidade da Microsoft que identifica exclusivamente o usuário em aplicativos. Microsoft Graph retornará esse valor como a propriedade ID para uma determinada conta de usuário. |
| tid | Identificador que representa o locatário do Azure AD do qual o usuário é. No caso de uma identidade MSA, isso sempre será `9188040d-6c67-4c5b-b112-36a304b66dad` . Para obter mais informações, consulte a observação na próxima seção: usar Microsoft Graph API. |
| sub | Identificador que identifica exclusivamente o usuário neste aplicativo específico. |
|||

## <a name="use-the-microsoft-graph-api"></a>Usar a API do Microsoft Graph

O token de ID contém informações básicas para identificar o usuário, mas seu processo de ativação pode exigir detalhes adicionais, como a empresa do usuário, para concluir o processo de integração. Use a [API Microsoft Graph](/graph/use-the-api) para solicitar essas informações para evitar forçar o usuário a inserir esses detalhes novamente. As permissões Standard _ *User. Read* * incluem as seguintes informações, por padrão:

| Valor | Descrição |
| ------------ | ------------- |
| displayName | Nome exibido no catálogo de endereços do usuário. |
| givenName | Nome do usuário. |
| jobTitle | Cargo do usuário. |
| mail | Endereço SMTP do usuário. |
| mobilePhone | Número de telefone celular principal do usuário. |
| preferredLanguage | Código ISO 639-1 para o idioma preferencial do usuário. |
| sobrenome | Sobrenome do usuário. |
|||

Propriedades adicionais — como o nome da empresa do usuário ou o local do usuário (país) — podem ser selecionadas para inclusão na solicitação. Para obter mais detalhes, consulte [Propriedades para o tipo de recurso de usuário](/graph/api/resources/user?view=graph-rest-1.0&preserve-view=true#properties).

A maioria dos aplicativos registrados com o Azure AD concede permissões delegadas para ler as informações do usuário do locatário do Azure AD da sua empresa. Qualquer solicitação para Microsoft Graph para essas informações deve ser acompanhada por um token de acesso como autenticação. As etapas específicas para gerar o token de acesso dependerão da pilha de tecnologia que você está usando, mas o código de exemplo conterá um exemplo. Para obter mais informações, consulte [obter acesso em nome de um usuário](/graph/auth-v2-user).

> [!NOTE]
> As contas do locatário MSA (com a ID de locatário `9188040d-6c67-4c5b-b112-36a304b66dad` ) não retornarão mais informações do que já foram coletadas com o token de ID. Portanto, você pode ignorar essa chamada para o API do Graph para essas contas.

## <a name="next-steps"></a>Próximas etapas
- [Como criar uma oferta de SaaS no Marketplace comercial](create-new-saas-offer.md)