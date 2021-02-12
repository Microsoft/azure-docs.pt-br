---
title: Problemas ao entrar em aplicativos configurados para logon único baseado em SAML
description: Diretrizes para os erros específicos ao entrar em um aplicativo que você configurou para logon único federado baseado em SAML com o Azure Active Directory
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 02/18/2019
ms.author: kenwith
ms.reviewer: luleon, asteen
ms.custom: contperf-fy21q2
ms.openlocfilehash: d5a90909345599b2389d1752e00a7bc917d06822
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430404"
---
# <a name="problems-signing-in-to-saml-based-single-sign-on-configured-apps"></a>Problemas ao entrar em aplicativos configurados para logon único baseado em SAML
Para solucionar os problemas de entrada abaixo, recomendamos o seguinte para diagnosticar melhor e automatizar as etapas de resolução:

- Instale a [extensão do navegador My Apps Secure](my-apps-deployment-plan.md) para ajudar o Azure Active Directory (AAD) a fornecer melhores diagnósticos e resoluções ao usar a experiência de teste no portal do Azure.
- Reproduza o erro usando a experiência de teste na página de configuração do aplicativo no portal do Azure. Saiba mais em [depurar aplicativos de logon único baseados em SAML](./debug-saml-sso-issues.md)

Se você usar a [experiência de teste](./debug-saml-sso-issues.md) no portal do Azure com a extensão de navegador seguro meus aplicativos, não precisará seguir as etapas abaixo para abrir a página de configuração de logon único baseada em SAML.

Para abrir a página de configuração de logon único baseado em SAML:
1.  Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou **coadministrador**.
1.  Abra a **extensão Azure Active Directory** selecionando **todos os serviços** na parte superior do menu de navegação esquerdo principal.
1.  Digite **"Azure Active Directory"** na caixa de pesquisa de filtro e selecione o item de **Azure Active Directory** .
1.  Escolha **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.
1.  Marque **Todos os aplicativos** para exibir uma lista com todos os seus aplicativos.

    Se você não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos**.

1.  Selecione o aplicativo para o qual você deseja configurar o logon único.
1. Depois que o aplicativo for carregado, selecione **logon único** no menu de navegação esquerdo do aplicativo.
1. Escolha SSO baseado em SAML.

## <a name="application-not-found-in-directory"></a>Aplicativo não encontrado no diretório
`Error AADSTS70001: Application with Identifier 'https:\//contoso.com' was not found in the directory.`

**Causa possível**

O `Issuer` atributo enviado do aplicativo para o Azure ad na solicitação SAML não corresponde ao valor do identificador configurado para o aplicativo no Azure AD.

**Resolução**

Verifique se o `Issuer` atributo na solicitação SAML corresponde ao valor do identificador configurado no Azure AD.

Na página configuração do SSO baseado em SAML, na seção **configuração básica do SAML** , verifique se o valor na caixa de texto identificador corresponde ao valor do valor do identificador exibido no erro.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>O endereço de resposta não corresponde aos endereços de resposta configurados para o aplicativo
`Error AADSTS50011: The reply URL specified in the request does not match the reply URLs configured for the application: '{application identifier}'.`

**Causa possível**

O `AssertionConsumerServiceURL` valor na solicitação SAML não corresponde ao padrão ou ao valor da URL de resposta configurado no Azure AD. O `AssertionConsumerServiceURL` valor na solicitação SAML é a URL que você vê no erro.

**Resolução**

Verifique se o `AssertionConsumerServiceURL` valor na solicitação SAML corresponde ao valor da URL de resposta configurado no Azure AD. 

Verifique ou atualize o valor na caixa de texto URL de resposta para corresponder ao `AssertionConsumerServiceURL` valor na solicitação SAML.   

Depois de atualizar o valor da URL de resposta no Azure AD, e ele corresponder ao valor enviado pelo aplicativo na solicitação SAML, você deverá ser capaz de entrar no aplicativo.

## <a name="user-not-assigned-a-role"></a>Usuário não atribuído a uma função
`Error AADSTS50105: The signed in user 'brian\@contoso.com' is not assigned to a role for the application.`

**Causa possível**

O usuário não teve acesso concedido para o aplicativo no Azure AD.

**Resolução**

Para atribuir um ou mais usuários diretamente a um aplicativo, consulte [início rápido: atribuir usuários a um aplicativo](add-application-portal-assign-users.md).

## <a name="not-a-valid-saml-request"></a>Não é uma solicitação SAML válida
`Error AADSTS75005: The request is not a valid Saml2 protocol message.`

**Causa possível**

O Azure Active Directory não oferece suporte para a solicitação SAML enviada pelo aplicativo para logon único. Alguns problemas comuns são:
- Campos obrigatórios ausentes na solicitação SAML
- Método codificado de solicitação SAML

**Resolução**

1. Capture a solicitação SAML. Siga o tutorial [como depurar o logon único baseado em SAML para aplicativos no Azure ad](./debug-saml-sso-issues.md) para saber como capturar a solicitação SAML.
1. Entre em contato com o fornecedor do aplicativo e compartilhe as seguintes informações:
    - Solicitação SAML
    - [Requisitos de protocolo SAML de logon único do Azure AD](../develop/single-sign-on-saml-protocol.md)

O fornecedor do aplicativo deve validar que eles dão suporte à implementação SAML do Azure AD para logon único.

## <a name="misconfigured-application"></a>Aplicativo configurado incorretamente
`Error AADSTS650056: Misconfigured application. This could be due to one of the following: The client has not listed any permissions in the requested permissions in the client's application registration. Or, The admin has not consented in the tenant. Or, Check the application identifier in the request to ensure it matches the configured client application identifier. Please contact your admin to fix the configuration or consent on behalf of the tenant.`

**Causa possível**

O `Issuer` atributo enviado do aplicativo para o Azure ad na solicitação SAML não corresponde ao valor do identificador configurado para o aplicativo no Azure AD.

**Resolução**

Verifique se o `Issuer` atributo na solicitação SAML corresponde ao valor do identificador configurado no Azure AD. 

Verifique se o valor na caixa de texto identificador corresponde ao valor do valor do identificador exibido no erro.

## <a name="certificate-or-key-not-configured"></a>Chave ou certificado não configurado
`Error AADSTS50003: No signing key configured.`

**Causa possível**

O objeto de aplicativo está corrompido e o Azure AD não reconhece o certificado configurado para o aplicativo.

**Resolução**

Para excluir e criar um novo certificado, siga as etapas abaixo:
1. Na tela de configuração do SSO baseado em SAML, selecione **criar novo certificado** na seção **certificado de autenticação SAML** .
1. Selecione data de validade e clique em **salvar**.
1. Marque **Tornar o novo certificado ativo** para substituir o certificado ative. Em seguida, clique em **Salvar** na parte superior do painel e aceite para ativar o certificado de substituição.
1. Na seção **Certificado de Autenticação SAML**, clique em **remover** para remover o certificado **Não Usado**.

## <a name="saml-request-not-present-in-the-request"></a>A solicitação SAML não está presente na solicitação
`Error AADSTS750054: SAMLRequest or SAMLResponse must be present as query string parameters in HTTP request for SAML Redirect binding.`

**Causa possível**

O Azure AD não pôde identificar a solicitação SAML dentro dos parâmetros de URL na solicitação HTTP. Isso pode acontecer se o aplicativo não estiver usando a associação de redirecionamento HTTP ao enviar a solicitação SAML ao Azure AD.

**Resolução**

O aplicativo precisa enviar a solicitação SAML codificada para o cabeçalho Location usando a associação de redirecionamento HTTP. Para obter mais informações sobre como implementá-la, leia a seção sobre Associação de redirecionamento HTTP no [Documento de especificação de protocolo SAML](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf).

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>O Azure AD está enviando o token para um ponto de extremidade incorreto
**Causa possível**

Durante o logon único, se a solicitação de entrada não contiver uma URL de resposta explícita (URL de serviço do consumidor de asserção), o Azure AD selecionará qualquer uma das URLs de resposta configuradas para esse aplicativo. Mesmo que o aplicativo tenha uma URL de resposta explícita configurada, o usuário pode ser redirecionado https://127.0.0.1:444 . 

Quando o aplicativo foi adicionado como um aplicativo inexistente na galeria, o Azure Active Directory criou essa URL de resposta como um valor padrão. Esse comportamento mudou e o Azure Active Directory não adiciona mais essa URL por padrão. 

**Resolução**

Exclua as URLs de resposta não utilizadas configuradas para o aplicativo.

Na página configuração do SSO baseado em SAML, na seção **URL de resposta (URL do serviço do consumidor de asserção)** , exclua as URLs de resposta não usadas ou padrão criadas pelo sistema. Por exemplo, `https://127.0.0.1:444/applications/default.aspx`.


## <a name="authentication-method-by-which-the-user-authenticated-with-the-service-doesnt-match-requested-authentication-method"></a>Método de autenticação pelo qual o usuário autenticado com o serviço não corresponde ao método de autenticação solicitado
`Error: AADSTS75011 Authentication method by which the user authenticated with the service doesn't match requested authentication method 'AuthnContextClassRef'. `

**Causa possível**

O `RequestedAuthnContext` está na solicitação SAML. Isso significa que o aplicativo está esperando o `AuthnContext` especificado pelo `AuthnContextClassRef` . No entanto, o usuário já foi autenticado antes de acessar o aplicativo e o `AuthnContext` (método de autenticação) usado para a autenticação anterior é diferente daquele que está sendo solicitado. Por exemplo, ocorreu um acesso de usuário federado a myapps e WIA. O `AuthnContextClassRef` será `urn:federation:authentication:windows` . O AAD não executará uma nova solicitação de autenticação, ele usará o contexto de autenticação que foi passado por ele pelo IdP (ADFS ou qualquer outro serviço de Federação, neste caso). Portanto, haverá uma incompatibilidade se o aplicativo solicitar outros `urn:federation:authentication:windows` . Outro cenário é quando multifator foi usado: `'X509, MultiFactor` .

**Resolução**


`RequestedAuthnContext` é um valor opcional. Em seguida, se possível, pergunte ao aplicativo se ele pode ser removido.

Outra opção é verificar se o `RequestedAuthnContext` será respeitado. Isso será feito solicitando uma nova autenticação. Ao fazer isso, quando a solicitação SAML é processada, uma nova autenticação será feita e o `AuthnContext` será cumprido. Para solicitar uma nova autenticação, a solicitação SAML mais contém o valor `forceAuthn="true"` . 



## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problema ao personalizar as declarações SAML enviadas para um aplicativo
Para saber como personalizar as declarações de atributo SAML enviadas ao seu aplicativo, consulte [mapeamento de declarações em Azure Active Directory](../develop/active-directory-claims-mapping.md).

## <a name="errors-related-to-misconfigured-apps"></a>Erros relacionados a aplicativos configurados incorretamente
Verifique se as configurações do portal correspondem às que você tem no seu aplicativo. Especificamente, compare o ID do Aplicativo/Cliente, URLs de Resposta, Chaves/Segredos do Cliente e URI da ID do Aplicativo.

Compare o recurso que você está solicitando acesso no código com as permissões configuradas na guia **Recursos Necessários** para certificar-se de que está solicitando somente os recursos que você configurou.

## <a name="next-steps"></a>Próximas etapas
- [Série de Início Rápido no Gerenciamento de Aplicativos](add-application-portal-assign-users.md)
- [Como depurar o logon único baseado em SAML em aplicativos no do Azure AD](./debug-saml-sso-issues.md)
- [Requisitos de protocolo SAML de logon único do Azure AD](../develop/single-sign-on-saml-protocol.md)
