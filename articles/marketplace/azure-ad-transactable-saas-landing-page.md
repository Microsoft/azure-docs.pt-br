---
title: Crie a página de aterrissagem para sua oferta de SaaS transactável no Marketplace comercial
description: Saiba como criar uma página de aterrissagem para sua oferta de SaaS.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 4bfc29472373a53bcebb2ba59134d1f3702d4793
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102549865"
---
# <a name="build-the-landing-page-for-your-transactable-saas-offer-in-the-commercial-marketplace"></a>Crie a página de aterrissagem para sua oferta de SaaS transactável no Marketplace comercial

Este artigo orienta você pelo processo de criação de uma página de aterrissagem para um aplicativo SaaS que será vendido no Microsoft Commercial Marketplace.

## <a name="overview"></a>Visão geral

Você pode considerar a página de aterrissagem como o "lobby" de sua oferta de SaaS (software como serviço). Depois que o comprador assina uma oferta, o Marketplace comercial a direciona para a página de aterrissagem para ativar e configurar sua assinatura para seu aplicativo SaaS. Considere isso como uma etapa de confirmação de pedido que permite que o comprador veja o que comprou e confirme os detalhes da conta. Usando o Azure Active Directory (Azure AD) e Microsoft Graph, você habilitará o SSO (logon único) para o comprador e obterá detalhes importantes sobre o comprador que você pode usar para confirmar e ativar sua assinatura, incluindo seu nome, endereço de email e organização.

Como as informações necessárias para ativar a assinatura são limitadas e fornecidas pelo Azure AD e Microsoft Graph, não deve ser necessário solicitar informações que exijam mais do que o consentimento básico. Se você precisar de detalhes do usuário que exigem consentimento adicional para seu aplicativo, solicite essas informações após a conclusão da ativação da assinatura. Isso permite a ativação de assinatura descomplicada para o comprador e diminui o risco de abandonar.

A página de aterrissagem normalmente inclui o seguinte:

- Apresente o nome da oferta e do plano adquiridos, bem como os termos de cobrança.
- Apresente os detalhes da conta do comprador, incluindo o nome e o sobrenome, a organização e o email.
- Solicite ao comprador que confirme ou substitua detalhes de conta diferentes.
- Oriente o comprador nas próximas etapas após a ativação. Por exemplo, receba um email de boas-vindas, gerencie a assinatura, obtenha suporte ou leia a documentação.

> [!NOTE]
> O comprador também será direcionado para a página de aterrissagem ao gerenciar sua assinatura após a ativação. Depois que a assinatura do comprador for ativada, você deverá usar o SSO para permitir que o usuário entre. É recomendável direcionar o usuário para um perfil de conta ou página de configuração.

As seções a seguir irão orientá-lo pelo processo de criação de uma página de aterrissagem:

1. [Crie um registro de aplicativo do Azure ad](#create-an-azure-ad-app-registration) para a página de aterrissagem.
1. [Use um exemplo de código como um ponto de partida](#use-a-code-sample-as-a-starting-point) para seu aplicativo.
1. [Use dois aplicativos do Azure ad para melhorar a segurança na produção](#use-two-azure-ad-apps-to-improve-security-in-production).
1. [Resolva o token de identificação de compra do Marketplace](#resolve-the-marketplace-purchase-identification-token) adicionado à URL pelo Marketplace comercial.
1. [Ler informações de declarações codificadas no token de ID](#read-information-from-claims-encoded-in-the-id-token), que foi recebido do Azure ad depois de entrar, que foi enviado com a solicitação.
1. [Use a API Microsoft Graph](#use-the-microsoft-graph-api) para coletar informações adicionais, conforme necessário.

## <a name="create-an-azure-ad-app-registration"></a>Criar um registro de aplicativo do Azure AD

O Marketplace comercial está totalmente integrado ao Azure AD. Os compradores chegam ao Marketplace autenticado com uma [conta do Azure ad ou conta Microsoft (MSA)](../active-directory/fundamentals/active-directory-whatis.md#terminology). Após a compra, o comprador vai do mercado comercial para a URL da página de aterrissagem para ativar e gerenciar sua assinatura de seu aplicativo SaaS. Você deve permitir que o comprador entre no seu aplicativo com o SSO do Azure AD. (A URL da página de aterrissagem é especificada na página de [configuração técnica](plan-saas-offer.md#technical-information) da oferta.

A primeira etapa para usar a identidade é verificar se sua página de aterrissagem está registrada como um aplicativo do Azure AD. O registro do aplicativo permite que você use o Azure AD para autenticar usuários e solicitar acesso aos recursos do usuário. Ele pode ser considerado a definição do aplicativo, o que permite que o serviço saiba como emitir tokens para o aplicativo com base nas configurações do aplicativo.

### <a name="register-a-new-application-using-the-azure-portal"></a>Registrar um novo aplicativo usando o portal do Azure

Para começar, siga as instruções para [registrar um novo aplicativo](../active-directory/develop/quickstart-register-app.md). Para permitir que os usuários de outras empresas visitem o aplicativo, você deve escolher uma das opções de multilocatário quando for perguntado quem pode usar o aplicativo.

Se você pretende consultar a API de Microsoft Graph, [configure seu novo aplicativo para acessar APIs da Web](../active-directory/develop/quickstart-configure-app-access-web-apis.md). Quando você seleciona as permissões de API para este aplicativo, o padrão de **User. Read** é suficiente para reunir informações básicas sobre o comprador para tornar o processo de integração suave e automático. Não solicite nenhuma permissão de API rotulada **precisa de consentimento do administrador**, pois isso impedirá que todos os usuários não administradores visitem sua página de aterrissagem.

Se você precisar de permissões elevadas como parte de sua integração ou processo de provisionamento, considere usar a funcionalidade de [consentimento incremental](../active-directory/azuread-dev/azure-ad-endpoint-comparison.md) do Azure ad para que todos os compradores enviados do Marketplace possam interagir inicialmente com a página de aterrissagem.

## <a name="use-a-code-sample-as-a-starting-point"></a>Usar um exemplo de código como um ponto de partida

Fornecemos vários aplicativos de exemplo que implementam um site simples com o logon do Azure AD habilitado. Depois que o aplicativo é registrado no Azure AD, a folha **início rápido** oferece uma lista de tipos de aplicativos comuns e pilhas de desenvolvimento, como mostrado na Figura 1. Escolha aquele que corresponda ao seu ambiente e siga as instruções para baixar e configurar.

***Figura 1: folha de início rápido no portal do Azure***

:::image type="content" source="./media/azure-ad-saas/azure-ad-quickstart-blade.png" alt-text="Ilustra a folha de início rápido no portal do Azure.":::

Depois de baixar o código e configurar seu ambiente de desenvolvimento, altere as definições de configuração no aplicativo para refletir a ID do aplicativo, a ID do locatário e o segredo do cliente que você registrou no procedimento anterior. Observe que as etapas exatas serão diferentes dependendo do exemplo que você estiver usando.

## <a name="use-two-azure-ad-apps-to-improve-security-in-production"></a>Use dois aplicativos do Azure AD para melhorar a segurança na produção

Este artigo apresenta uma versão simplificada da arquitetura para implementar uma página de aterrissagem para sua oferta de SaaS do Marketplace comercial. Ao executar a página em produção, recomendamos que você melhore a segurança comunicando-se com as APIs de preenchimento de SaaS somente por meio de um aplicativo protegido e diferente. Isso requer a criação de dois novos aplicativos:

- Primeiro, o aplicativo de página de aterrissagem multilocatário foi descrito até este ponto, exceto sem a funcionalidade de entrar em contato com as APIs de preenchimento de SaaS. Essa funcionalidade será descarregada para outro aplicativo, conforme descrito abaixo.
- Em segundo lugar, um aplicativo para possuir as comunicações com as APIs de preenchimento de SaaS. Esse aplicativo deve ser um único locatário, apenas para ser usado pela sua organização, e uma lista de controle de acesso pode ser estabelecida para limitar o acesso às APIs somente a partir desse aplicativo.

Isso permite que a solução funcione em cenários que observam o princípio [da separação de preocupações](/dotnet/architecture/modern-web-apps-azure/architectural-principles#separation-of-concerns) . Por exemplo, a página de aterrissagem usa o primeiro aplicativo registrado do Azure AD para conectar o usuário. Depois que o usuário estiver conectado, a página de aterrissagem usará o segundo Azure AD para solicitar um token de acesso para chamar a API de preenchimento de SaaS e chamar a operação de resolução.

## <a name="resolve-the-marketplace-purchase-identification-token"></a>Resolver o token de identificação de compra do Marketplace

Quando o comprador é enviado para a página de aterrissagem, um token é adicionado ao parâmetro de URL. Esse token é diferente do token emitido pelo Azure AD e do token de acesso usado para autenticação de serviço a serviço e é usado como uma entrada para as [APIs de preenchimento de SaaS](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) resolverem chamada para obter os detalhes da assinatura. Assim como acontece com todas as chamadas para as APIs de preenchimento de SaaS, sua solicitação de serviço a serviço será autenticada com um token de acesso baseado no usuário da ID de aplicativo do Azure AD do aplicativo para autenticação de serviço a serviço.

> [!NOTE]
> Na maioria dos casos, é preferível fazer essa chamada a partir de um segundo aplicativo de locatário único. Consulte [usar dois aplicativos do Azure ad para melhorar a segurança na produção](#use-two-azure-ad-apps-to-improve-security-in-production) anteriormente neste artigo.

### <a name="request-an-access-token"></a>Solicitar um token de acesso

Para autenticar seu aplicativo com as APIs de preenchimento de SaaS, você precisa de um token de acesso, que pode ser gerado chamando o ponto de extremidade OAuth do Azure AD. Consulte [como obter o token de autorização do editor](./partner-center-portal/pc-saas-registration.md#how-to-get-the-publishers-authorization-token).

### <a name="call-the-resolve-endpoint"></a>Chamar o ponto de extremidade de resolução

As APIs de preenchimento de SaaS implementam o ponto de extremidade de [resolução](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) que pode ser chamado para confirmar a validade do token do Marketplace e retornar informações sobre a assinatura.

## <a name="read-information-from-claims-encoded-in-the-id-token"></a>Ler informações de declarações codificadas no token de ID

Como parte do fluxo do [OpenID Connect](../active-directory/develop/v2-protocols-oidc.md) , o Azure ad adiciona um [token de ID](../active-directory/develop/id-tokens.md) à solicitação quando o comprador é enviado para a página de aterrissagem. Esse token contém várias partes de informações básicas que podem ser úteis no processo de ativação, incluindo as informações vistas nesta tabela.

| Valor | Descrição |
| ------------ | ------------- |
| aud | Público-alvo para este token. Nesse caso, ele deve corresponder à ID do aplicativo e ser validado. |
| preferred_username | Nome de usuário principal do usuários visitantes. Pode ser um endereço de email, número de telefone ou outro identificador. |
| email | Endereço de email do usuário. Observe que esse campo pode estar vazio. |
| name | Valor legível por humanos que identifica o assunto do token. Nesse caso, será o nome do comprador. |
| oid | Identificador no sistema de identidade da Microsoft que identifica exclusivamente o usuário em aplicativos. Microsoft Graph retornará esse valor como a propriedade ID para uma determinada conta de usuário. |
| tid | Identificador que representa o locatário do Azure AD do qual o comprador é. No caso de uma identidade MSA, isso sempre será ``9188040d-6c67-4c5b-b112-36a304b66dad`` . Para obter mais informações, consulte a observação na próxima seção: usar a API de Microsoft Graph. |
| sub | Identificador que identifica exclusivamente o usuário neste aplicativo específico. |
|||

## <a name="use-the-microsoft-graph-api"></a>Usar a API do Microsoft Graph

O token de ID contém informações básicas para identificar o comprador, mas seu processo de ativação pode exigir detalhes adicionais, como a empresa do comprador, para concluir o processo de integração. Use a [API Microsoft Graph](/graph/use-the-api) para solicitar essas informações para evitar forçar o usuário a inserir esses detalhes novamente. As permissões **User. Read** padrão incluem as informações a seguir, por padrão.

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

Propriedades adicionais — como o nome da empresa do usuário ou o local do usuário (país) — podem ser selecionadas para inclusão na solicitação. Consulte [Propriedades para o tipo de recurso de usuário](/graph/api/resources/user#properties) para obter mais detalhes.

A maioria dos aplicativos registrados com o Azure AD concede permissões delegadas para ler as informações do usuário do locatário do Azure AD da sua empresa. Qualquer solicitação para Microsoft Graph para essas informações deve ser acompanhada por um token de acesso para autenticação. As etapas específicas para gerar o token de acesso dependerão da pilha de tecnologia que você está usando, mas o código de exemplo conterá um exemplo. Para obter mais informações, consulte [obter acesso em nome de um usuário](/graph/auth-v2-user).

> [!NOTE]
> As contas do locatário MSA (com a ID de locatário ``9188040d-6c67-4c5b-b112-36a304b66dad`` ) não retornarão mais informações do que já foram coletadas com o token de ID. Portanto, você pode ignorar essa chamada para o API do Graph para essas contas.

## <a name="next-steps"></a>Próximas etapas

- [Como criar uma oferta de SaaS no Marketplace comercial](create-new-saas-offer.md)