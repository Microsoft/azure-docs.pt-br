---
title: "Gerenciamento de logon único para aplicativos empresariais no Azure Active Directory | Microsoft Docs"
description: "Gerenciar configurações de logon único para aplicativos empresariais em sua organização da Galeria de Aplicativos do Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: bcc954d3-ddbe-4ec2-96cc-3df996cbc899
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: curtand
ms.reviewer: asmalser
ms.openlocfilehash: 882180a553fffee05a612cf70ddd9a0f30108415
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="managing-single-sign-on-for-enterprise-apps"></a>Gerenciar o logon único para aplicativos empresariais

Este artigo descreve como usar o [Portal do Azure](https://portal.azure.com) para gerenciar as configurações de logon único para aplicativos corporativos. Aplicativos empresariais são aplicativos que são implantados e usados dentro da sua organização. Este artigo se aplica principalmente aos aplicativos que foram adicionados através da [galeria de aplicativos do Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). 

## <a name="finding-your-apps-in-the-portal"></a>Localizar seus aplicativos no portal
Todos os aplicativos corporativos que estão configurados para o logon único podem ser exibidos e gerenciados no Portal do Azure. Os aplicativos podem ser encontrados na seção **Mais Serviços** &gt; **Aplicativos Empresariais** do Portal. 

![Folha de Aplicativos Empresariais][1]

Selecione **Todos os aplicativos** para exibir uma lista de todos os aplicativos que foram configurados. A seleção de um aplicativo exibe os recursos desse aplicativo, permitindo que os relatórios sejam exibidos para esse aplicativo e que várias configurações possam ser gerenciadas.

Para gerenciar as configurações de logon único, selecione **Logon único**.

![Folha de recursos do aplicativo][2]

## <a name="single-sign-on-modes"></a>Modos de logon único
**Logon único** começa com um menu **Modo**, que permite que o modo de logon único seja configurado. As opções disponíveis incluem:

* **Logon baseado em SAML** – essa opção estará disponível se o aplicativo der suporte ao logon único federado completo com o Azure Active Directory usando o protocolo SAML 2.0, WS-Federation, ou protocolos de conexão OpenID.
* **Logon baseado em senha** – essa opção está disponível se o Azure AD dá suporte ao preenchimento de formulário de senha para este aplicativo.
* **Logon vinculado** – anteriormente conhecida como "Logon único existente", essa opção permite que os administradores coloquem um link para esse aplicativo no painel de acesso do Azure AD ou no iniciador de aplicativo do Office 365 do usuário.

Para obter mais informações sobre esses modos, confira [Como o logon único funciona com o Azure Active Directory](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

## <a name="saml-based-sign-on"></a>Logon único baseado em SAML
A opção **logon baseado em SAML** é dividida em quatro seções:

### <a name="domains-and-urls"></a>Domínios e URLs
É onde todos os detalhes sobre o domínio do aplicativo e as URLs são adicionados ao seu diretório do Azure AD. Todas as entradas necessárias para criar o aplicativo de logon único são exibidas diretamente na tela, enquanto todas as entradas opcionais podem ser exibidas marcando a caixa de seleção **Mostrar configurações de URL avançadas** . A lista completa de entradas com suporte inclui:

* **URL de logon** – o local para o qual o usuário vai para entrar nesse aplicativo. Se o aplicativo estiver configurado para executar logon único iniciado pelo provedor de serviços, quando um usuário abrir essa URL, o provedor de serviços redirecionará para o Azure AD a fim de autenticar e conectar o usuário. 
  * Se esse campo estiver populado, o Azure AD usará a URL para iniciar o aplicativo do Office 365 e o painel de acesso do Azure AD.
  * Se esse campo for omitido, o Azure AD executará, em vez disso, o logon iniciado pelo provedor de identidade quando o aplicativo for iniciado do Office 365, do painel de acesso do Azure AD ou da URL de logon único do Azure AD.
* **Identificador** – esse URI deve identificar exclusivamente o aplicativo para o qual o logon único está sendo configurado. Esse é o valor que o Azure AD envia para o aplicativo como o parâmetro Audiência do token SAML e o aplicativo deve validá-lo. Esse valor também aparece como a ID da entidade em todos os metadados SAML fornecidos pelo aplicativo.
* **URL de resposta** – a URL de resposta é onde o aplicativo espera receber o token SAML. Ela também é chamada de URL de ACS (Serviço do Consumidor de Declaração). Depois que eles tiverem sido inseridos, clique em Avançar para prosseguir para a próxima tela. Esta tela fornece informações sobre o que precisa ser configurado no lado do aplicativo para habilitá-lo a aceitar um token SAML do AD do Azure.
* **Estado de Retransmissão** - o estado de retransmissão é um parâmetro opcional que pode ajudar a informar o aplicativo para onde redirecionar o usuário após a autenticação. Normalmente, o valor é uma URL válida no aplicativo. No entanto, alguns aplicativos usam esse campo de forma diferente (confira a documentação do logon único do aplicativo para obter detalhes). A capacidade de definir o estado de retransmissão é um novo recurso que é exclusivo do novo portal do Azure.

### <a name="user-attributes"></a>Atributos de usuário
É onde administradores podem exibir e editar os atributos que são enviados no token SAML que o Azure AD emite para o aplicativo sempre que os usuários entram.

O único atributo editável com suporte é o atributo **Identificador de Usuário**. O valor desse atributo é o campo no Azure AD que identifica exclusivamente cada usuário no aplicativo. Por exemplo, se o aplicativo foi implantado usando "Endereço de Email" como o nome de usuário e o identificador exclusivo, o valor deve ser definido para o campo "user.mail" no Azure AD.

### <a name="saml-signing-certificate"></a>Certificado de assinatura de SAML
Esta seção mostra os detalhes do certificado que o Azure AD usa para assinar tokens SAML emitidos para o aplicativo sempre que o usuário é autenticado. Ele permite que as propriedades do certificado atual sejam inspecionadas, incluindo a data de validade.

### <a name="application-configuration"></a>Configuração de aplicativo
A seção final fornece a documentação e/ou os controles necessários para configurar o aplicativo para usar o Azure Active Directory como um provedor de identidade.

O menu suspenso **Configurar Aplicativo** fornece novas instruções concisas internas para configurar o aplicativo. Esse é outro novo recurso exclusivo para o novo portal do Azure.

> [!NOTE]
> Para obter um exemplo completo de documentação interna, confira o aplicativo do Salesforce.com. A documentação de aplicativos adicionais está sendo adicionada continuamente.
> 
> 

![Documentos internos][3]

## <a name="password-based-sign-on"></a>Logon baseado em senha
Se houver suporte para o aplicativo, selecionar o modo SSO baseado em senha e selecionar **Salvar** configura-o instantaneamente para fazer o SSO baseado em senha. Para obter mais informações sobre a implantação de SSO baseada em senha, confira [Como o logon único funciona com o Azure Active Directory](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![Logon baseado em senha][4]

## <a name="linked-sign-on"></a>Logon vinculado
Se houver suporte para o aplicativo, selecionar o modo de SSO vinculado permite que você insira a URL para a qual deseja que o painel de acesso do Azure AD ou do Office 365 redirecione quando os usuários clicam nesse aplicativo. Para obter mais informações sobre o SSO vinculado (anteriormente conhecido como "SSO existente"), confira [Como o logon único funciona com o Azure Active Directory](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![Logon vinculado][5]

##<a name="feedback"></a>Comentários

Esperamos que você goste de usar a experiência aprimorada do Azure AD. Continue a fazer seus comentários! Poste seus comentários e suas ideias para aprimoramento na seção **Portal de Administração** do nosso [fórum de comentários](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  Nós estamos empolgados para criar algo novo e interessante diariamente e usar suas diretrizes para formar e definir o que devemos criar a seguir.

[1]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade.PNG
[2]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-sso-blade.PNG
[3]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-embedded-docs.PNG
[4]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-password-sso.PNG
[5]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-linked-sso.PNG
