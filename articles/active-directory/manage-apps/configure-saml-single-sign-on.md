---
title: Entenda o SSO (logon único) baseado em SAML para aplicativos no Azure Active Directory
description: Entenda o SSO (logon único) baseado em SAML para aplicativos no Azure Active Directory
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/28/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: f6422c3f751a7aaa430b93ee68ca1a3520ac915f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257685"
---
# <a name="understand-saml-based-single-sign-on"></a>Entender o logon único baseado em SAML

Na [série de guias de início rápido](view-applications-portal.md) sobre o gerenciamento de aplicativos, você aprendeu a usar o Azure ad como o IDP (provedor de identidade) para um aplicativo. Este artigo apresenta mais detalhes sobre a opção baseada em SAML para logon único. 


## <a name="before-you-begin"></a>Antes de começar

Usar o Azure AD como seu IdP (provedor de identidade) e configurar o logon único (SSO) pode ser simples ou complexo, dependendo do aplicativo que está sendo usado. Alguns aplicativos podem ser configurados com apenas algumas ações. Outros exigem configuração detalhada. Para aprimorar o conhecimento rapidamente, percorra a [série de guias de início rápido](view-applications-portal.md) sobre o gerenciamento de aplicativos. Se o aplicativo que você está adicionando for simples, provavelmente você não precisará ler este artigo. Se o aplicativo que você está adicionando exigir configuração personalizada para SSO baseado em SAML, este artigo será para você.

Na [série de início rápido](add-application-portal-setup-sso.md), há um artigo sobre como configurar o logon único. Nele, você aprende a acessar a página de configuração do SAML para um aplicativo. A página de configuração do SAML inclui cinco seções. Essas seções são discutidas detalhadamente neste artigo.

> [!IMPORTANT] 
> Há alguns cenários em que a opção de **logon único** não estará presente na navegação de um aplicativo em **aplicativos empresariais**. 
>
> Se o aplicativo tiver sido registrado usando **registros de aplicativo** , a funcionalidade de logon único será configurada para usar o OIDC OAuth por padrão. Nesse caso, a opção de **logon único** não será mostrada na navegação em **aplicativos empresariais**. Ao usar **registros de aplicativo** para adicionar seu aplicativo personalizado, você configura opções no arquivo de manifesto. Para saber mais sobre o arquivo de manifesto, consulte [Azure Active Directory manifesto do aplicativo](../develop/reference-app-manifest.md). Para saber mais sobre os padrões de SSO, consulte [autenticação e autorização usando a plataforma de identidade da Microsoft](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform). 
>
> Outros cenários em que o **logon único** ficará ausente da navegação incluem quando um aplicativo é hospedado em outro locatário ou se sua conta não tem as permissões necessárias (administrador global, administrador de aplicativos de nuvem, administrador de aplicativos ou proprietário da entidade de serviço). As permissões também podem causar um cenário em que você pode abrir o **logon único** , mas não poderá salvar. Para saber mais sobre as funções administrativas do Azure AD, consulte ( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .


## <a name="basic-saml-configuration"></a>Configuração básica do SAML

É possível obter esses valores do fornecedor do aplicativo. Pode inserir os valores manualmente ou carregar um arquivo de metadados para extrair o valor dos campos.

> [!TIP]
> Muitos aplicativos já foram pré-configurados para trabalhar com o Azure AD. Esses aplicativos são listados na Galeria de aplicativos que você pode navegar quando adiciona um aplicativo ao seu locatário do Azure AD. A [série de guias de início rápido](add-application-portal-setup-sso.md) orienta você pelo processo. Para os aplicativos na Galeria, você encontrará instruções detalhadas, passo a passo. Para acessar as etapas, você pode clicar no link na página de configuração do SAML para o aplicativo, conforme descrito na série de início rápido, ou pode procurar uma lista de todos os tutoriais de configuração de aplicativo em [tutoriais de configuração do aplicativo SaaS](../saas-apps/tutorial-list.md).

| Configuração Básica do SAML | Iniciado por SP | iniciado por idP | Descrição |
|:--|:--|:--|:--|
| **Identificador (ID da Entidade)** | Obrigatório para alguns aplicativos | Obrigatório para alguns aplicativos | Identifica exclusivamente o aplicativo. O Azure AD envia o identificador para o aplicativo como o parâmetro Audience do token SAML. O aplicativo deve validá-lo. Esse valor também aparece como a ID da entidade em todos os metadados SAML fornecidos pelo aplicativo. Insira uma URL que usa o seguinte padrão: "https://<subdomain>.contoso.com" *Encontre esse valor como o elemento **Issuer** na **AuthnRequest** (solicitação SAML) enviada pelo aplicativo.* |
| **URL de Resposta** | Obrigatório | Obrigatório | Especifica onde o aplicativo espera receber o token SAML. A URL de resposta também é chamada de URL do ACS (Serviço do Consumidor de Declaração). Você pode usar os campos de URL de resposta adicionais para especificar várias URLs de resposta. Por exemplo, você pode precisar de URLs de resposta adicionais para vários subdomínios. Ou, para fins de teste, você pode especificar várias URLs de resposta (host local e URLs públicas) ao mesmo tempo. |
| **URL de logon** | Obrigatório | Não especifique | Quando um usuário abre essa URL, o provedor de serviço redireciona para o Azure AD a fim de autenticar e conectar o usuário. O Azure AD usa a URL para iniciar o aplicativo de Microsoft 365 ou meus aplicativos do Azure AD. Quando em branco, o Azure AD faz um logon iniciado pelo IdP quando um usuário inicia o aplicativo de Microsoft 365, meus aplicativos do Azure AD ou a URL de SSO do Azure AD.|
| **Estado de Retransmissão** | Opcional | Opcional | Especifica para onde o aplicativo deve redirecionar o usuário depois que a autenticação é concluída. Normalmente, o valor é uma URL válida para o aplicativo. No entanto, alguns aplicativos usam esse campo de forma diferente. Para obter mais informações, pergunte ao fornecedor do aplicativo.
| **URL de logoff** | Opcional | Opcional | Usada para enviar as respostas de Logoff do SAML novamente ao aplicativo.

## <a name="user-attributes-and-claims"></a>Atributos e declarações de usuário 

Quando um usuário é autenticado no aplicativo, o Azure AD emite um token SAML para o aplicativo com informações (ou declarações) sobre o usuário que o identifica com exclusividade. Por padrão, essas informações incluem nome de usuário, endereço de email, nome e sobrenome do usuário. Talvez seja necessário personalizar as declarações se, por exemplo, o aplicativo exigir valores de declaração específicos ou um formato **Nome** diferente do nome de usuário. 

> [!IMPORTANT]
> Muitos aplicativos já estão pré-configurados e na Galeria de aplicativos e você não precisa se preocupar em definir declarações de usuário e de grupo. A [série de guias de início rápido](add-application-portal.md) orienta você pela adição e configuração de aplicativos.


O valor do identificador **exclusivo do identificador de usuário (ID de nome)** é uma declaração necessária e é importante. O valor padrão é *User. UserPrincipalName*. O identificador de usuário identifica exclusivamente cada usuário dentro do aplicativo. Por exemplo, se o endereço de email é tanto o nome de usuário quanto o identificador exclusivo, defina o valor como *user.mail*.

Para saber mais sobre como personalizar declarações SAML, confira [como: Personalizar declarações emitidas no token SAML para aplicativos empresariais](../develop/active-directory-saml-claims-customization.md).

Você pode adicionar novas declarações, para obter detalhes, consulte [adicionar declarações específicas do aplicativo](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims) ou adicionar declarações de grupo, consulte [Configurar declarações de grupo](../hybrid/how-to-connect-fed-group-claims.md).


> [!NOTE]
> Para obter outras maneiras de personalizar o token SAML do Azure AD para seu aplicativo, confira os recursos a seguir.
>- Para criar funções personalizadas por meio do portal do Azure, confira [Configurar declarações de função](../develop/active-directory-enterprise-app-role-management.md).
>- Para personalizar as declarações por meio do PowerShell, confira [Personalizar declarações – PowerShell](../develop/active-directory-claims-mapping.md).
>- Para modificar o manifesto do aplicativo a fim de configurar declarações opcionais para seu aplicativo, confira [Configurar declarações opcionais](../develop/active-directory-optional-claims.md).
>- Para definir políticas de tempo de vida de token para tokens de atualização, tokens de acesso, tokens de sessão e tokens de ID, confira [Configurar tempos de vida de tokens](../develop/active-directory-configurable-token-lifetimes.md). Ou para restringir sessões de autenticação por meio do acesso condicional do Azure AD, confira [Funcionalidades do gerenciamento de sessão de autenticação](../conditional-access/howto-conditional-access-session-lifetime.md).

## <a name="saml-signing-certificate"></a>Certificado de autenticação SAML

O Azure AD usa um certificado para assinar os tokens SAML que ele envia para o aplicativo. Você precisa desse certificado para configurar a relação de confiança entre o Azure AD e o aplicativo. Para obter detalhes sobre o formato de certificado, consulte a documentação do aplicativo SAML. Para saber mais, confira [Gerenciar certificados para logon único federado](manage-certificates-for-federated-single-sign-on.md) e [Opções de assinatura de certificado avançado no token SAML](certificate-signing-options.md).

> [!IMPORTANT]
> Muitos aplicativos já estão pré-configurados e na Galeria de aplicativos e você não precisa se aprofundar nos certificados. A [série de guias de início rápido](add-application-portal.md) orienta você pela adição e configuração de aplicativos.

No Azure AD, você pode baixar o certificado ativo no formato Base64 ou Raw diretamente da página principal **Configurar logon único com SAML**. Além disso, você pode obter o certificado ativo baixando o arquivo XML de metadados do aplicativo ou usando a URL de metadados de Federação do aplicativo. Para exibir, criar ou baixar seus certificados (ativos ou inativos), siga estas etapas.

Algumas coisas comuns a serem verificadas para verificar se um certificado incluem: 
   - *A data de validade correta.* Você pode configurar a data de validade para até três anos no futuro.
   - *Um status ativo para o certificado correto.* Se o status for **Inativo**, altere-o para **Ativo**. Para alterar o status, clique com o botão direito do mouse na linha do certificado e selecione **tornar certificado ativo**.
   - *A opção de assinatura e o algoritmo corretos.*
   - *O(s) endereço(s) de email de notificação correto(s).* Quando o certificado ativo estiver próximo da data de validade, o Azure AD enviará uma notificação para o endereço de email configurado neste campo.

Às vezes, talvez seja necessário baixar o certificado. Tenha cuidado ao salvá-lo! Para baixar o certificado, selecione uma das opções de formato Base64, Raw ou XML de metadados de federação. O Azure AD também fornece a **URL de Metadados de Federação do Aplicativo**, na qual você pode acessar os metadados específicos do aplicativo no formato `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`.

> [!NOTE]
> O aplicativo deve ser capaz de lidar com o marcador de ordem de byte presente no XML renderizado ao usar https://login.microsoftonline.com/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={app-id} . A marca de ordem de byte é representada como um caractere ASCII não imprimível» 0 e, em Hex, é representada como EF BB BF ao examinar os dados XML.

Para fazer alterações no certificado, selecione o botão Editar. Há várias coisas que você pode fazer na página **certificado de autenticação SAML** :
   - Criar um novo certificado: selecione **novo certificado**, selecione a **data de validade** e, em seguida, selecione **salvar**. Para ativar o certificado, selecione o menu de contexto ( **...** ) e selecione **Tornar o certificado ativo**.
   - Carregar um certificado com as credenciais de chave privada e pfx: selecione **importar certificado** e navegue até o certificado. Insira a **Senha do PFX** e, em seguida, selecione **Adicionar**.  
   - Configurar a assinatura avançada de certificado. Para obter mais informações sobre essas opções, consulte [Opções avançadas de assinatura de certificado](certificate-signing-options.md).
   - Notifique pessoas adicionais quando o certificado ativo estiver próximo da data de validade: Insira os endereços de email nos campos de **email de notificação** .

## <a name="set-up-the-application-to-use-azure-ad"></a>Configurar o aplicativo para usar o Azure AD

A seção **Configurar \<applicationName>** lista os valores que precisam ser configurados no aplicativo para que ele use o Azure ad como um provedor de identidade SAML. Você define os valores na página configuração no site de aplicativos. Por exemplo, se você estiver configurando o GitHub, vá para o site github.com e defina os valores. Se o aplicativo já tiver sido pré-configurado e estiver na galeria do Azure AD, você encontrará um link para **exibir instruções** passo a passo. Caso contrário, será necessário encontrar a documentação do aplicativo que você está configurando. 

Os valores de URL de **logon** e **URL de logout** são resolvidos para o mesmo ponto de extremidade, que é o ponto de extremidade de tratamento de solicitação SAML para o locatário do Azure AD. 

O **Identificador do Azure AD** é o valor do **Emissor** no token SAML emitido para o aplicativo.

## <a name="test-single-sign-on"></a>Testar logon único

Depois de configurar seu aplicativo para usar o Azure AD como um provedor de identidade baseado em SAML, você poderá testar as configurações para ver se o logon único funciona em sua conta. 

Selecione **teste** e, em seguida, escolha para testar com o usuário conectado no momento ou como outra pessoa. 

Se o logon for bem-sucedido, você estará pronto para atribuir usuários e grupos ao aplicativo SAML. Parabéns!

Se uma mensagem de erro for exibida, conclua as seguintes etapas:

1. Copie e cole as informações específicas na caixa **Como é o erro?** .

    ![Obter diretrizes de resolução](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. Selecione **Obter diretrizes de resolução**. A causa raiz e as orientações para resolução aparecem.  Neste exemplo, o usuário não estava atribuído ao aplicativo.

3. Leia as diretrizes de resolução e tente corrigir o problema.

4. Execute o teste novamente até que ele seja concluído com êxito.

Para saber mais, confira [Depurar logon único baseado em SAML para aplicativos no Azure Active Directory](./debug-saml-sso-issues.md).


## <a name="next-steps"></a>Próximas etapas

- [Série de Início Rápido no Gerenciamento de Aplicativos](view-applications-portal.md)
- [Atribuir usuários e grupos ao aplicativo](./assign-user-or-group-access-portal.md)
- [Configurar o provisionamento automático de conta de usuário](../app-provisioning/configure-automatic-user-provisioning-portal.md)
- [Protocolo SAML de Logon Único](../develop/single-sign-on-saml-protocol.md)
