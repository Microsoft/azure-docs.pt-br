---
title: O que é o Azure Active Directory B2C? | Microsoft Docs
description: Saiba mais sobre como criar e gerenciar experiências de identidade, como inscrição, entrada e gerenciamento de perfil em seu aplicativo usando o Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 02/20/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ca9d8a8373bd73d527862864d436319eb45b5f48
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227159"
---
# <a name="what-is-azure-active-directory-b2c"></a>O que é o Azure Active Directory B2C?

O Azure AD (Azure Active Directory) B2C é um serviço de gerenciamento de identidades entre empresa e consumidor. Esse serviço permite que você personalize e controle como os usuários interagem de maneira segura com aplicativos Web, da área de trabalho, móveis ou de página única. Usando o Azure AD B2C, os usuários podem se inscrever, entrar, redefinir senhas e editar perfis. O Azure AD B2C implementa um formulário dos protocolos OpenID Connect e OAuth 2.0. O aspecto importante na implementação desses protocolos são os tokens de segurança e suas declarações, que permitem fornecer acesso seguro aos recursos.

Um *percurso do usuário* é uma solicitação que especifica uma política, que controla o comportamento de como o usuário e o aplicativo interagem com o Azure AD B2C. Dois caminhos estão disponíveis para você definir os percursos do usuário no Azure AD B2C.

Se você for desenvolvedor de aplicativos com ou sem conhecimento sobre identidades, poderá optar por definir fluxos dos usuários de identidade comuns usando o portal do Azure. Se você for um profissional que trabalha com identidades, integrador de sistemas, consultor ou membro de uma equipe que trabalha com identidades na empresa, estiver familiarizado com os fluxos do OpenID Connect e tiver noções básicas dos provedores de identidade e da autenticação baseada em declarações, poderá escolher políticas personalizadas baseadas em XML.

Antes de começar a definir um percurso do usuário, você precisa criar um locatário do Azure AD B2C e registrar seu aplicativo e a API no locatário. Depois de concluir essas tarefas, comece a definir um percurso do usuário com fluxos dos usuários ou políticas personalizadas. Opcionalmente, você também pode adicionar ou alterar os provedores de identidade ou personalizar a maneira como o usuário experimenta o percurso.

## <a name="protocols-and-tokens"></a>Protocolos e tokens

O Azure AD B2C dá suporte aos [protocolos OpenID Connect e OAuth 2.0](active-directory-b2c-reference-protocols.md) em percursos do usuário. Na implementação Azure AD B2C do OpenID Connect, seu aplicativo inicia esse percurso emitindo solicitações de autenticação para o Azure AD B2C.

O resultado de uma solicitação ao Azure AD B2C é um token de segurança, como um [token de ID ou um token de acesso](active-directory-b2c-reference-tokens.md). Esse token de segurança define a identidade do cliente. Os tokens são recebidos de pontos de extremidade do Azure AD B2C, como um ponto de extremidade `/token` ou `/authorize`. Nesses tokens, você pode acessar declarações que podem ser usadas para validar uma identidade e permitir acesso a recursos seguros.

## <a name="tenants-and-applications"></a>Locatários e aplicativos

No Azure AD B2C, um *locatário* representa a sua organização e é um diretório de usuários. Cada locatário do Azure AD B2C é distinto e separado de outros locatários do Azure AD B2C. Talvez você já tenha um locatário do Azure Active Directory; o locatário do Azure AD B2C é um locatário diferente. Um locatário contém informações sobre os usuários que se inscreveram para usar o aplicativo. Por exemplo, senhas, dados de perfil e permissões. Para obter mais informações, confira [Tutorial: Criar um locatário do Azure Active Directory B2C](tutorial-create-tenant.md).

Antes de configurar seu aplicativo para usar o Azure AD B2C, primeiro você precisa registrá-lo no locatário usando o portal do Azure. O processo de registro do aplicativo coleta e atribui valores ao seu aplicativo. Esses valores incluem uma ID do aplicativo que identifica exclusivamente o aplicativo e um URI de redirecionamento usado para direcionar as respostas novamente para o aplicativo.

A interação de cada aplicativo segue um padrão semelhante de alto nível:

1. O aplicativo direciona o usuário a executar uma política.
2. O usuário conclui a política de acordo com a definição de política.
3. O aplicativo recebe um token.
4. O aplicativo usa o token para tentar acessar um recurso.
5. O servidor de recurso valida o token para verificar se o acesso pode ser permitido.
6. O aplicativo atualiza o token periodicamente.

Para registrar um aplicativo Web, conclua as etapas do [Tutorial: registrar um aplicativo para habilitar a inscrição e entrada usando o Azure AD B2C](tutorial-register-applications.md). Você também pode [adicionar um aplicativo de API Web ao locatário do Azure Active Directory B2C](add-web-application.md) ou [adicionar um aplicativo cliente nativo ao locatário do Azure Active Directory B2C](add-native-application.md).

## <a name="user-journeys"></a>Percursos do usuário

A política em um percurso do usuário pode ser definida como um [fluxo dos usuários](active-directory-b2c-reference-policies.md) ou uma [política personalizada](active-directory-b2c-overview-custom.md). Os fluxos dos usuários predefinidos para as tarefas mais comuns de identidade, como inscrição, entrada e edição de perfil, estão disponíveis no portal do Azure.

Os percursos do usuário permitem controlar comportamentos pela definição das seguintes configurações:

- Contas sociais usadas pelo usuário para se inscrever no aplicativo
- Dados coletados do usuário, como nome ou CEP
- Autenticação multifator
- Aparência das páginas
- Informações retornadas para o aplicativo

As políticas personalizadas são arquivos de configuração que definem o comportamento do [Identity Experience Framework](trustframeworkpolicy.md) no locatário do Azure AD B2C. O Identity Experience Framework é a plataforma subjacente que estabelece a relação de confiança de várias partes e conclui as etapas em um percurso do usuário.

Políticas personalizadas podem ser alteradas totalmente para concluir muitas tarefas. Uma política personalizada é um ou vários arquivos formatados em XML que se referenciam entre si em uma cadeia hierárquica. Um [pacote inicial](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) está disponível para políticas personalizadas, a fim de habilitar tarefas comuns de identidade.

Políticas personalizadas ou fluxos dos usuários de diferentes tipos são usados no locatário do Azure AD B2C, conforme a necessidade, podendo ser reutilizados entre aplicativos. Essa flexibilidade permite definir e modificar experiências de identidade do usuário com pouca ou nenhuma alteração no código. As políticas podem ser executadas com a adição de um parâmetro de consulta especial às solicitações de autenticação HTTP. Para criar sua própria política personalizada, confira [Introdução às políticas personalizadas no Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="identity-providers"></a>Provedores de identidade

Em seus aplicativos, você talvez queira permitir que os usuários entrem com diferentes provedores de identidade. Um *provedor de identidade* cria, mantém e gerencia as informações de identidade, fornecendo serviços de autenticação para aplicativos. Você pode adicionar provedores de identidade compatíveis com o Azure AD B2C usando o portal do Azure.

Normalmente, você usa apenas um provedor de identidade em seu aplicativo, mas tem a opção de adicionar mais. Para configurar um provedor de identidade em seu locatário do Azure AD B2C, primeiro crie um aplicativo no site do desenvolvedor do provedor de identidade e, em seguida, registre o identificador de aplicativo ou o identificador de cliente e a senha ou o segredo do cliente por meio do aplicativo do provedor de identidade criado. O identificador e a senha são então usados para configurar seu aplicativo.

Os seguintes artigos descrevem as etapas usadas para adicionar alguns dos provedores de identidade comuns aos fluxos dos usuários:

- [Amazon](active-directory-b2c-setup-amzn-app.md)
- [Facebook](active-directory-b2c-setup-fb-app.md)
- [Conta da Microsoft](active-directory-b2c-setup-msa-app.md)

Os seguintes artigos descrevem as etapas usadas para adicionar alguns dos provedores de identidade comuns às políticas personalizadas:
- [Amazon](setup-amazon-custom.md)
- [Google](active-directory-b2c-custom-setup-goog-idp.md)
- [Conta da Microsoft](active-directory-b2c-custom-setup-msa-idp.md)

Para obter mais informações, confira [Tutorial: Adicionar provedores de identidade aos aplicativos no Azure Active Directory B2C](tutorial-add-identity-providers.md).


## <a name="page-customization"></a>Personalização de página

A maioria do conteúdo HTML e CSS apresentado aos clientes em um percurso do usuário é controlável. Usando a personalização de página, você pode personalizar a aparência de qualquer política personalizada ou fluxo dos usuários. Você mantém a consistência visual e de marca entre seu aplicativo e o Azure AD B2C usando o recurso de personalização.

O Azure AD B2C executa o código no navegador do usuário e usa uma abordagem moderna chamada CORS (Compartilhamento de Recursos entre Origens). Primeiro, especifique uma URL na política com um conteúdo personalizado em HTML. O Azure AD B2C mescla os elementos da interface do usuário com o conteúdo HTML carregado da URL e exibe a página ao usuário.

Você envia os parâmetros para o Azure AD B2C em uma cadeia de caracteres de consulta. Ao passar o parâmetro para seu ponto de extremidade HTML, o conteúdo da página é alterado dinamicamente. Por exemplo, altere a imagem da tela de fundo na página de inscrição ou de entrada com base em um parâmetro passado de seu aplicativo Web ou móvel.

Para personalizar as páginas em um fluxo dos usuários, confira [Tutorial: Personalizar a interface das experiências do usuário no Azure Active Directory B2C](tutorial-customize-ui.md). Para personalizar as páginas em uma política personalizada, confira [Personalizar a interface do usuário do aplicativo usando uma política personalizada no Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md) ou [Configurar a interface do usuário com conteúdo dinâmico usando políticas personalizadas no Azure Active Directory B2C](active-directory-b2c-ui-customization-custom-dynamic.md).

## <a name="developer-resources"></a>Recursos para desenvolvedores

### <a name="client-applications"></a>Aplicativos cliente

Você tem a opção de aplicativos para [iOS](active-directory-b2c-devquickstarts-ios.md), [Android](active-directory-b2c-devquickstarts-android.md) e .NET, entre outros. O Azure AD B2C permite essas ações e protege as identidades dos usuários ao mesmo tempo.

Se você for desenvolvedor de aplicativo Web ASP.NET, configure seu aplicativo para autenticar contas usando as etapas do [Tutorial: habilitar um aplicativo Web para autenticar com contas usando o Azure AD B2C](active-directory-b2c-tutorials-web-app.md).

Se você for desenvolvedor de aplicativos da área de trabalho, configure seu aplicativo para autenticar contas usando as etapas do [Tutorial: habilitar um aplicativo de desktop para autenticar com contas usando o Azure AD B2C](active-directory-b2c-tutorials-desktop-app.md).

Se você for desenvolvedor de aplicativo de página única que usa o Node.js, configure seu aplicativo para autenticar contas usando as etapas do [Tutorial: habilitar um aplicativo de página única para autenticar com contas usando o Azure AD B2C](active-directory-b2c-tutorials-spa.md).

### <a name="apis"></a>APIs
Se os aplicativos cliente ou Web precisarem chamar APIs, você poderá configurar o acesso seguro a esses recursos no Azure AD B2C.

Se você for desenvolvedor de aplicativo Web ASP.NET, configure seu aplicativo para chamar uma API protegida usando as etapas do [Tutorial: Permitir acesso a um ASP.NET Web API usando o Azure Active Directory B2C](active-directory-b2c-tutorials-web-api.md).

Se você for desenvolvedor de aplicativos da área de trabalho, configure seu aplicativo para chamar uma API protegida usando as etapas do [Tutorial: Permitir acesso a uma API Web do Node.js em um aplicativo da área de trabalho usando o Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app-webapi.md).

Se você for um desenvolvedor de aplicativo de página única usando Node.js, configure seu aplicativo para autenticar contas usando as etapas no [Tutorial: Permitir acesso a uma API Web do ASP.NET Core em um aplicativo de página única usando o Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md).

### <a name="javascript"></a>JavaScript

Adicione seu próprio código JavaScript do lado do cliente a seus aplicativos no Azure AD B2C. Para configurar o JavaScript em seu aplicativo, defina um [layout da página](page-layout.md) e habilite o [JavaScript](javascript-samples.md) em fluxos dos usuários ou políticas personalizadas.

### <a name="user-accounts"></a>Contas de usuário

Muitas tarefas comuns de gerenciamento de locatários precisam ser executadas de forma programática. O principal exemplo é o gerenciamento de usuário. Talvez seja necessário migrar o repositório de usuários existente para um locatário do Azure AD B2C. Talvez seja melhor hospedar o registro de usuário em sua própria página e criar contas de usuário no seu diretório do Azure AD B2C nos bastidores. Esses tipos de tarefas exigem a capacidade de criar, ler, atualizar e excluir contas de usuário. Realize essas tarefas usando a [API do Graph do Azure AD](active-directory-b2c-devquickstarts-graph-dotnet.md).

## <a name="next-steps"></a>Próximas etapas

Começar a configurar seu aplicativo para a experiência de inscrição e entrada ao continuar o tutorial.

> [!div class="nextstepaction"]
> [Tutorial: criar um locatário do Azure Active Directory B2C](tutorial-create-tenant.md)
