---
title: Publicar seu aplicativo na Galeria de aplicativos Azure Active Directory
description: Saiba como listar um aplicativo que dá suporte ao logon único na Galeria de aplicativos Azure Active Directory.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 12/14/2020
ms.author: kenwith
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: f2a04a341239b62290eecac8c0e1ce70b7c927c1
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988912"
---
# <a name="publish-your-app-to-the-azure-ad-app-gallery"></a>Publicar seu aplicativo na Galeria de aplicativos do Azure AD

Você pode publicar seu aplicativo na Galeria de aplicativos do Azure AD. Quando seu aplicativo for publicado, ele será exibido como uma opção para os clientes quando eles estiverem adicionando aplicativos ao seu locatário. 

Alguns dos benefícios de adicionar seu aplicativo à galeria do Azure AD incluem:

- Os clientes encontram a melhor experiência de logon único possível para seu aplicativo.
- A configuração do aplicativo é simples e mínima.
- Uma pesquisa rápida localiza seu aplicativo na galeria.
- Clientes do Azure Active Directory Premium, Básico e Gratuito podem usar essa integração.
- Os clientes mútuos recebem um tutorial de configuração passo a passo.

Além disso, há muitos benefícios quando seus clientes usam o Azure AD como um provedor de identidade para seu aplicativo. Entre eles estão:

- Forneça logon único para seus usuários. Com o SSO, você reduz os custos de suporte, tornando mais fácil para seus clientes o logon único. Se o SSO de um clique estiver habilitado, os administradores de ti de seus clientes não precisarão saber como configurar seu aplicativo para uso em sua organização. Para saber mais sobre o logon único, consulte [O que é logon único?](../manage-apps/what-is-single-sign-on.md).
- Seu aplicativo pode ser detectável na Galeria de aplicativos Microsoft 365, no iniciador de aplicativos Microsoft 365 e no Microsoft Search no Office.com. 
- Gerenciamento de aplicativo integrado. Para saber mais sobre o gerenciamento de aplicativos no Azure AD, consulte [o que é gerenciamento de aplicativos?](../manage-apps/what-is-application-management.md).
- Seu aplicativo pode usar o [API do Graph](/graph/) para acessar os dados que impulsionam a produtividade do usuário no ecossistema da Microsoft.
- A documentação específica do aplicativo coproduzida com a equipe do Azure AD para nossos clientes mútuos facilita a adoção.
- Você fornece aos seus clientes a capacidade de gerenciar completamente sua autenticação e autorização de funcionários e de identidades de convidado.
- Colocar todas as responsabilidades de conformidade e gerenciamento de conta com o proprietário do cliente dessas identidades.
- Fornecendo a capacidade de habilitar ou desabilitar o SSO para provedores de identidade, grupos ou usuários específicos para atender às suas necessidades comerciais.
- Você aumenta seu mercado e sua adoção. Muitas organizações de grande porte exigem que (ou almeje) seus funcionários tenham experiências de SSO contínuos em todos os aplicativos. Tornar o SSO fácil é importante.
- Você reduz o conflito do usuário final, o que pode aumentar o uso do usuário final e aumentar sua receita.
- Os clientes que usam o sistema para[scim](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)(gerenciamento de identidade entre domínios) podem usar o provisionamento para o mesmo aplicativo.
- Adicione segurança e conveniência quando os usuários entram em aplicativos usando o SSO do Azure AD e removendo a necessidade de credenciais separadas.

> [!TIP]
> Quando você oferece seu aplicativo para uso por outras empresas por meio de uma compra ou assinatura, torna seu aplicativo disponível para clientes em seus próprios locatários do Azure. Isso é conhecido como criar um aplicativo multilocatário. Para obter uma visão geral desse conceito, consulte [aplicativos multilocatário no Azure](../../dotnet-develop-multitenant-applications.md) e [locação em Azure Active Directory](single-and-multi-tenant-apps.md).

> [!IMPORTANT]
> Para publicar seu aplicativo na galeria do Azure AD, você deve concordar com termos e condições específicos. Antes de começar, certifique-se de ler e concordar com os [termos e condições](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/).

As etapas para publicar seu aplicativo na Galeria de aplicativos do Azure AD são:
1. Escolha o padrão de logon único certo para seu aplicativo.
2. Implemente o logon único em seu aplicativo.
3. Crie seu locatário do Azure e teste seu aplicativo.
4. Crie e publique a documentação.
5. Envie seu aplicativo.
6. Ingresse no Microsoft Partner Network.

## <a name="what-is-the-azure-ad-application-gallery"></a>O que é a galeria de aplicativos do Azure Active Directory?

- Os clientes encontram a melhor experiência de logon único possível.
- A configuração do aplicativo é simples e mínima.
- Uma pesquisa rápida localiza seu aplicativo na galeria.
- Clientes do Azure Active Directory Premium, Básico e Gratuito podem usar essa integração.
- Os clientes mútuos recebem um tutorial de configuração passo a passo.
- Os clientes que usam o sistema para[scim](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)(gerenciamento de identidade entre domínios) podem usar o provisionamento para o mesmo aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de uma conta permanente para testar com pelo menos dois usuários registrados.

- Para aplicativos federados (Open ID e SAML/WS-enalimentado), o aplicativo deve oferecer suporte ao modelo de software como serviço (SaaS) para ser listado na Galeria de aplicativos do Azure AD. Os aplicativos da Galeria empresarial devem dar suporte a várias configurações de clientes e não a nenhum cliente específico.
- Para Open ID Connect, o aplicativo deve ter multilocatário e a [estrutura de consentimento do Azure ad](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) deve ser implementada corretamente para o aplicativo. O usuário pode enviar a solicitação de entrada para um ponto de extremidade comum para que qualquer cliente possa fornecer consentimento para o aplicativo. É possível controlar acesso de usuário com base na ID do locatário e o UPN do usuário recebido no token.
- Para SAML 2.0/WS-enalimentado, seu aplicativo deve ter a capacidade de fazer a integração de SSO de SAML/WS-alimentada no modo SP ou IDP. Verifique se esse recurso está funcionando corretamente antes de enviar a solicitação.
- Para SSO de senha, certifique-se de que seu aplicativo dá suporte à autenticação de formulário para que o cofre de senha possa ser feito para que o logon único funcione conforme o esperado.
- Você precisa de uma conta permanente para testar com pelo menos dois usuários registrados.

**Como obter o Azure AD para desenvolvedores?**

Você pode obter uma conta de teste gratuita com todos os recursos premium do Azure AD-90 dias gratuitos e pode ser estendido desde que os desenvolvedores trabalhem com ele: https://docs.microsoft.com/office/developer-program/office-365-developer-program

## <a name="step-1---choose-the-right-single-sign-on-standard-for-your-app"></a>Etapa 1-escolher o padrão de logon único certo para seu aplicativo

Para listar um aplicativo na Galeria de aplicativos do Azure AD, implemente pelo menos uma das opções de logon único com suporte. Para entender as opções de logon único e como os clientes irão configurá-las no Azure AD, consulte [Opções de SSO](../manage-apps/sso-options.md).

A tabela a seguir compara os principais padrões: autenticação aberta 2,0 (OAuth 2,0) com OpenID Connect (OIDC), Security Assertion Markup Language (SAML) e especificação Web Services Federation (WS-alimentado).

| Funcionalidade| OAuth/OIDC| SAML/WS-Fed |
| - |-|-|
| Logon único baseado na Web| √| √ |
| Logout único baseado na Web| √| √ |
| Logon único baseado em móvel| √| √* |
| Logout único baseado em celular| √| √* |
| Políticas de acesso condicional para aplicativos móveis| √| √* |
| Experiência de MFA direta para aplicativos móveis| √| √* |
| Provisionamento de SCIM| √| √ |
| Acessar o Microsoft Graph| √| X |

* Possível, mas a Microsoft não fornece exemplos ou diretrizes.

### <a name="oauth-20-and-openid-connect"></a>OAuth 2.0 e OpenID Connect
O OAuth 2,0 é um protocolo [padrão da indústria](https://oauth.net/2/) para autorização. O OpenID Connect (OIDC) é uma camada de autenticação de identidade [padrão do setor](https://openid.net/connect/) criada com base no protocolo OAuth 2,0. 

**Motivos para escolher o OAuth/OIDC**
- A autorização inerente a esses protocolos permite que seu aplicativo acesse e integre com dados avançados de usuário e organizacionais por meio da API de Microsoft Graph.
- Simplifica a experiência do usuário final de seus clientes ao adotar o SSO para seu aplicativo. Você pode definir facilmente os conjuntos de permissões necessários, que são representados automaticamente para o administrador ou o usuário final que está consentido.
- O uso desses protocolos permite que seus clientes usem o acesso condicional e as políticas de MFA (autenticação multifator) para controlar o acesso aos aplicativos. 
- A Microsoft fornece bibliotecas e [exemplos de código em várias plataformas de tecnologia](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) para ajudar seu desenvolvimento.  

**Algumas coisas a serem consideradas**
- Se você já implementou o logon único baseado em SAML para seu aplicativo, talvez não queira implementar um novo padrão para obter seu aplicativo na galeria.

### <a name="saml-20-or-ws-fed"></a>SAML 2,0 ou WS-Fed

O SAML é um [padrão de logon único](https://www.oasis-open.org/standards#samlv2.0) maduro e amplamente adotado para aplicativos Web. Para saber mais sobre como o Azure usa SAML, consulte [como o Azure usa o protocolo SAML](active-directory-saml-protocol-reference.md). 

Especificação Web Services Federation (WS-alimentado) é um [padrão do setor](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) geralmente usado para aplicativos Web desenvolvidos usando a plataforma .net.

**Motivos para escolher o SAML**
- O SAML 2,0 é um padrão maduro e a maioria das plataformas de tecnologia dá suporte a bibliotecas de software livre para SAML 2,0. 
- Você pode fornecer aos seus clientes uma interface de administração para configurar o SSO do SAML. Eles podem configurar o SSO do SAML para Microsoft Azure AD e qualquer outro provedor de identidade que ofereça suporte a SAML.

**Algumas coisas a serem consideradas**
- Ao usar os protocolos SAML 2,0 ou WSFed para aplicativos móveis, determinadas políticas de acesso condicional, incluindo a MFA (autenticação multifator), terão uma experiência degradada.
- Se você quiser acessar o Microsoft Graph, será necessário implementar a autorização por meio do OAuth 2,0 para gerar os tokens necessários. 

### <a name="password-based"></a>Baseado em senha
O SSO baseado em senha, também conhecido como armazenamento de senha em cofre, permite que você gerencie o acesso de usuários e senhas para aplicativos Web que não dão suporte a federação de identidades. Também é útil para cenários em que vários usuários precisam compartilhar uma única conta, como as contas de aplicativo de mídia social da sua organização.


## <a name="step-2---implement-single-sign-on-in-your-app"></a>Etapa 2 – implementar o logon único em seu aplicativo
Cada aplicativo na Galeria deve implementar uma das opções de logon único com suporte. Para saber mais sobre as opções com suporte, consulte [Opções de SSO](../manage-apps/sso-options.md).

Para OAuth e OIDC, consulte [orientação sobre padrões de autenticação](v2-app-types.md) e [exemplos de código do Azure Active Directory](sample-v2-code.md).

Para o SAML e o WS-enalimentado, seu aplicativo deve ter a capacidade de fazer a integração de SSO no modo SP ou IDP. Verifique se esse recurso está funcionando corretamente antes de enviar a solicitação.

Para saber mais sobre autenticação, consulte [o que é autenticação?](../azuread-dev/v1-authentication-scenarios.md).

> [!IMPORTANT]
> Para aplicativos federados (OpenID e SAML/WS-alimentado), o aplicativo deve dar suporte ao modelo SaaS (software como serviço). Os aplicativos da galeria do Azure AD devem dar suporte a várias configurações de clientes e não devem ser específicos de um único cliente.

### <a name="implement-oauth-20-and-openid-connect"></a>Implementar o OAuth 2,0 e o OpenID Connect

Para o OpenID Connect, o aplicativo deve ter vários locatários e a [estrutura de consentimento do Azure ad](consent-framework.md) deve ser implementada corretamente para o aplicativo. O usuário pode enviar a solicitação de entrada para um ponto de extremidade comum para que qualquer cliente possa fornecer consentimento para o aplicativo. É possível controlar acesso de usuário com base na ID do locatário e o UPN do usuário recebido no token.

Para examinar exemplos específicos, consulte os [exemplos de código da plataforma Microsoft Identity](sample-v2-code.md). 

Para examinar os exemplos específicos de dispositivos móveis, consulte: 
* [Android](quickstart-v2-android.md)
* [iOS](quickstart-v2-ios.md)
* [Plataforma Universal do Windows](quickstart-v2-uwp.md)

### <a name="implement-saml-20"></a>Implementar SAML 2,0

Se seu aplicativo der suporte ao SAML 2,0, você poderá integrá-lo diretamente a um locatário do Azure AD. Para saber mais sobre a configuração do SAML com o Azure AD, consulte [Configurar logon único baseado em SAML](../manage-apps/configure-saml-single-sign-on.md).

A Microsoft não fornece, nem recomenda, bibliotecas para implementações de SAML. Há muitas bibliotecas de código-fonte aberto disponíveis.

### <a name="implement-ws-fed"></a>Implementar WS-Fed
Para saber mais sobre WS-Fed em ASP.NET Core, confira [autenticar usuários com WS-Federation no ASP.NET Core](/aspnet/core/security/authentication/ws-federation).

### <a name="implement-password-vaulting"></a>Implementar a compartimentação de senha

Crie um aplicativo Web que tenha uma página de entrada HTML. Certifique-se de que seu aplicativo dá suporte à autenticação de formulário para que o cofre de senha possa ser feito para que o logon único funcione conforme o esperado.

## <a name="step-3---implement-scim-user-provisioning-in-your-app"></a>Etapa 3 – implementar o provisionamento de usuário do SCIM em seu aplicativo
O suporte ao provisionamento do [scim](https://aka.ms/scimoverview) é uma etapa opcional, mas altamente recomendada, na criação do seu aplicativo. O suporte ao padrão SCIM é fácil e permite que os clientes criem e atualizem automaticamente as contas de usuário em seu aplicativo, sem depender de processos manuais, como o carregamento de arquivos CSV. Além disso, os clientes podem automatizar a remoção de usuários e a manutenção de associações de grupo em sincronia, o que não pode ser feito com uma solução como o SAML JIT. 

### <a name="learn-about-scim"></a>Saiba mais sobre o SCIM
Para saber mais sobre os padrões e benefícios do SCIM para seus clientes, consulte [Provisionando com o scim-introdução](https://aka.ms/scimoverview).

### <a name="understand-the-azure-ad-scim-implementation"></a>Entender a implementação de SCIM do Azure AD
Para saber mais sobre a implementação do Azure AD SCIM, consulte [criar um ponto de extremidade scim e configurar o provisionamento de usuário com o Azure ad](../app-provisioning/use-scim-to-provision-users-and-groups.md).

### <a name="implement-scim"></a>Implementar SCIM
O Azure AD fornece o [código de referência](https://aka.ms/scimoverview) para ajudá-lo a criar um ponto de extremidade SCIM. Também há muitas referências/bibliotecas de terceiros que você pode encontrar no GitHub.  

## <a name="step-4---create-your-azure-tenant-and-test-your-app"></a>Etapa 4 – criar seu locatário do Azure e testar seu aplicativo

Você precisará de um locatário do Azure AD para testar seu aplicativo. Para configurar seu ambiente de desenvolvimento, consulte [início rápido: configurar um locatário](quickstart-create-new-tenant.md).

Como alternativa, um locatário do Azure AD vem com cada assinatura Microsoft 365. Para configurar um ambiente de desenvolvimento de Microsoft 365 gratuito, consulte [ingressar no Microsoft 365 programa de desenvolvedor](/office/developer-program/microsoft-365-developer-program).

Quando você tiver um locatário, teste o logon único e o [provisionamento](../app-provisioning/use-scim-to-provision-users-and-groups.md#integrate-your-scim-endpoint-with-the-aad-scim-client). 

**Para aplicativos OIDC ou Oath**, [Registre seu aplicativo](quickstart-register-app.md) como um aplicativo multilocatário. Selecione a opção contas em qualquer diretório organizacional e contas pessoais da Microsoft em tipos de conta com suporte.

**Para aplicativos baseados em SAML e WS-baseded**, você [configura aplicativos de logon único baseados em SAML](../manage-apps/configure-saml-single-sign-on.md) usando um modelo SAML genérico no Azure AD.

Você também pode [converter um aplicativo de locatário único para multilocatário,](howto-convert-app-to-be-multi-tenant.md) se necessário.


## <a name="step-5---create-and-publish-documentation"></a>Etapa 5: criar e publicar a documentação

### <a name="documentation-on-your-site"></a>Documentação no seu site

A facilidade de adoção é um fator significativo nas decisões de software empresarial. A documentação clara e fácil de seguir dá suporte a seus clientes em sua jornada de adoção e reduz os custos de suporte. Trabalhando com milhares de fornecedores de software, a Microsoft observou o que funciona.

É recomendável que sua documentação em seu site, no mínimo, inclua os itens a seguir.

* Introdução à sua funcionalidade de SSO
  * Protocolos compatíveis
  * Versão e SKU
  * Lista de provedores de identidade com suporte com links de documentação
* Informações de licenciamento para seu aplicativo
* Controle de acesso baseado em função para configurar o SSO
* Etapas de configuração de SSO
  * Elementos de configuração da interface do usuário para SAML com valores esperados do provedor
  * Informações do provedor de serviços a serem passadas para provedores de identidade
* Se OIDC/OAuth
  * Lista de permissões necessárias para o consentimento com justificativas de negócios
* Etapas de teste para usuários piloto
* Informações de solução de problemas, incluindo códigos de erro e mensagens
* Mecanismos de suporte para clientes
* Detalhes sobre o ponto de extremidade do SCIM, incluindo os recursos e atributos com suporte

### <a name="documentation-on-the-microsoft-site"></a>Documentação no site da Microsoft

Quando você listar seu aplicativo com a Galeria de aplicativos Azure Active Directory, que também publica seu aplicativo no Azure Marketplace, a Microsoft gerará documentação para nossos clientes mútuos explicando o processo passo a passo. Você pode ver um exemplo [aqui](../saas-apps/tutorial-list.md). Esta documentação é criada com base no seu envio para a galeria e você pode atualizá-la facilmente se fizer alterações em seu aplicativo usando sua conta do GitHub.


## <a name="step-6---submit-your-app"></a>Etapa 6 – enviar seu aplicativo

Depois de testar se a integração de aplicativos funciona com o Azure AD, envie a solicitação de aplicativo no [portal de rede de aplicativos da Microsoft](https://microsoft.sharepoint.com/teams/apponboarding/Apps).

Na primeira vez que você tentar entrar no portal, verá uma das duas telas. 

Se você receber a mensagem "que não funcionou", precisará entrar em contato com a [equipe de integração de SSO do Azure ad](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Forneça a conta de email que você deseja usar para enviar a solicitação. Um endereço de email comercial como `name@yourbusiness.com` é preferencial. A equipe do Azure AD adicionará a conta no portal de rede de aplicativos da Microsoft.

Se você vir uma página "solicitar acesso", preencha a justificativa de negócios e selecione **solicitar acesso**.

Depois que a conta for adicionada, você poderá entrar no portal de rede de aplicativos da Microsoft e enviar a solicitação selecionando o bloco de **solicitação de envio (ISV)** no Home Page.

![Bloco de solicitação de envio (ISV) no home page](./media/howto-app-gallery-listing/homepage.png)

### <a name="issues-on-logging-into-portal"></a>Problemas ao fazer logon no portal

Se você estiver vendo esse erro durante o logon, aqui estão os detalhes sobre o problema e isso é como você pode corrigi-lo.

* Se sua entrada foi bloqueada, conforme mostrado abaixo:

  ![problemas de resolução de aplicativo na Galeria](./media/howto-app-gallery-listing/blocked.png)

**O que está acontecendo:**

O usuário convidado é federado a um locatário inicial que também é um Azure AD. O usuário convidado tem um risco alto. A Microsoft não permite que usuários de alto risco acessem seus recursos. Todos os usuários de alto risco (funcionários ou convidados/fornecedores) devem corrigir/fechar seu risco para acessar os recursos da Microsoft. Para usuários convidados, esse risco de usuário é proveniente do locatário inicial e a política vem do locatário de recursos (a Microsoft, neste caso).
 
**Soluções seguras:**

* Os usuários convidados registrados do MFA recorrem seus próprios riscos de usuário. Isso pode ser feito pelo usuário convidado executando uma alteração ou redefinição de senha segura ( https://aka.ms/sspr) em seu locatário inicial (isso precisa de MFA e SSPR no locatário inicial). A alteração ou redefinição de senha protegida deve ser iniciada no Azure AD e não no local.

* Os usuários convidados têm seus administradores para corrigir seus riscos. Nesse caso, o administrador executará uma redefinição de senha (geração de senha temporária). Isso não precisa de proteção de identidade. O administrador do usuário convidado pode ir para https://aka.ms/RiskyUsers e clicar em ' Redefinir senha '.

* Os usuários convidados têm seus administradores perto/descartam seus riscos. Novamente, isso não precisa de proteção de identidade. O administrador pode ir para https://aka.ms/RiskyUsers e clicar em ' ignorar risco do usuário '. No entanto, o administrador deve fazer a auditoria detalhada para garantir que essa foi uma avaliação de risco positiva de falsos, antes de fechar o risco do usuário. Caso contrário, eles estão colocando os recursos da Microsoft em risco, suprimindo uma avaliação de risco sem investigação.

> [!NOTE]
> Se você tiver problemas com o Access, entre em contato com a [equipe de integração de SSO do Azure ad](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

### <a name="implementation-specific-options"></a>Opções específicas de implementação
Se você quiser adicionar seu aplicativo para listar na Galeria usando o OpenID Connect, selecione **OpenID connect & OAuth 2,0** , conforme mostrado.

![Listando um aplicativo OpenID Connect na Galeria](./media/howto-app-gallery-listing/openid.png)

Se você quiser adicionar seu aplicativo para listar na Galeria usando o **saml 2,0** ou o **WS-enalimentado**, selecione **SAML 2.0/WS-alimentado** , conforme mostrado.

![Listando um aplicativo SAML 2,0 ou WS-Fed na Galeria](./media/howto-app-gallery-listing/saml.png)

Se você quiser adicionar seu aplicativo para listar na Galeria usando o SSO de senha, selecione **SSO de senha (nome de usuário & senha)** , conforme mostrado.

![Listando um aplicativo SSO de senha na Galeria](./media/howto-app-gallery-listing/passwordsso.png)

Se você estiver implementando um ponto de extremidade [SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) 2,0 para provisionamento de usuário, selecione a opção conforme mostrado. Ao fornecer o esquema na solicitação de integração, siga as instruções [aqui](../app-provisioning/export-import-provisioning-configuration.md) para baixar seu esquema. Usaremos o esquema que você configurou ao testar o aplicativo inexistente na galeria para compilar o aplicativo da galeria. 

   ![Solicitação de provisionamento de usuário](./media/howto-app-gallery-listing/user-provisioning.png)

### <a name="update-or-remove-an-existing-listing"></a>Atualizar ou remover uma listagem existente

Você pode atualizar ou remover um aplicativo de galeria existente no [portal de rede de aplicativos da Microsoft](https://microsoft.sharepoint.com/teams/apponboarding/Apps).

![Listando um aplicativo SAML na Galeria](./media/howto-app-gallery-listing/updateorremove.png)

> [!NOTE]
> Se você tiver problemas com o Access, examine a seção anterior sobre como criar sua conta. Se isso não funcionar, entre em contato com a [equipe de integração de SSO do Azure ad](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

### <a name="list-requests-by-customers"></a>Listar solicitações por clientes

Os clientes podem enviar uma solicitação para listar um aplicativo selecionando **solicitações de aplicativo por clientes**  >  **Enviar nova solicitação**.

![Mostra o bloco aplicativos solicitados pelo cliente](./media/howto-app-gallery-listing/customer-submit-request.png)

Este é o fluxo de aplicativos solicitados pelo cliente.

![Mostra o fluxo de aplicativos solicitados pelo cliente](./media/howto-app-gallery-listing/customer-request-2.png)


### <a name="timelines"></a>Linhas do tempo

A linha do tempo para o processo de listagem de um aplicativo SAML 2,0 ou WS-Fed na galeria é de 7 a 10 dias úteis.

![Linha do tempo para listar um aplicativo SAML na Galeria](./media/howto-app-gallery-listing/timeline.png)

A linha do tempo para o processo de listagem de um aplicativo do OpenID Connect na galeria é de 2 a 5 dias úteis.

![Linha do tempo para listar um aplicativo OpenID Connect na Galeria](./media/howto-app-gallery-listing/timeline2.png)

### <a name="escalations"></a>Escalonamentos

Para qualquer escalonamento, envie um email para a [equipe de integração de SSO do Azure ad](mailto:SaaSApplicationIntegrations@service.microsoft.com)e responderemos assim que possível.


## <a name="step-7---join-the-microsoft-partner-network"></a>Etapa 7-ingressar no Microsoft Partner Network
O Microsoft Partner Network fornece acesso instantâneo a recursos, programas, ferramentas e conexões exclusivos. Para ingressar na rede e criar seu plano ir para o mercado, consulte [acessar clientes comerciais](https://partner.microsoft.com/explore/commercial#gtm).


## <a name="next-steps"></a>Próximas etapas
* [Criar um ponto de extremidade SCIM e configurar o provisionamento de usuários](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Cenários de autenticação do Azure AD](authentication-flows-app-scenarios.md)
