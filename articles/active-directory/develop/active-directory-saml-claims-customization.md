---
title: "Personalizando as declarações emitidas no token SAML para aplicativos empresariais no Azure Active Directory | Microsoft Docs"
description: "Saiba como personalizar as declarações emitidas no token SAML para aplicativos empresariais no Azure Active Directory"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: 73f63300e65e1f6bc25edf7f28ec2769acbb7c9e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="customizing-claims-issued-in-the-saml-token-for-enterprise-applications-in-azure-active-directory"></a>Personalizando declarações emitidas no token SAML para aplicativos empresariais no Azure Active Directory
Hoje, o Azure Active Directory dá suporte a logon único com a maioria dos aplicativos empresariais, incluindo os dois aplicativos pré-integrados na galeria de aplicativos do Azure AD, bem como aplicativos personalizados. Quando um usuário é autenticado em um aplicativo por meio do Azure AD usando o protocolo SAML 2.0, o Azure AD envia um token ao aplicativo (por um HTTP POST). Em seguida, o aplicativo é validado e usa o token para conectar o usuário em vez de solicitar um nome de usuário e a senha. Esses tokens SAML contêm partes de informações sobre o usuário conhecidas como "declarações".

Em linguagem de identificação, uma "declaração" são informações que um provedor de identidade declara sobre um usuário dentro do token que ele emite para esse usuário. No [Token SAML](http://en.wikipedia.org/wiki/SAML_2.0), esses dados normalmente estão contidos na Instrução de Atributo SAML. A ID única do usuário é normalmente representada na SAML Subject, também denominada Identificador de Nome.

Por padrão, o Azure Active Directory emite um token SAML ao seu aplicativo que contém uma declaração NameIdentifier com um valor do nome do usuário (também conhecido como nome UPN) no Azure AD. Esse valor pode identificar exclusivamente o usuário. O token SAML também contém declarações adicionais com o endereço de email, nome e sobrenome do usuário.

Para exibir ou editar as declarações emitidas no token SAML para o aplicativo, abra o aplicativo no Portal do Azure. Em seguida, marque a caixa de seleção **Exibir e editar todos os outros atributos de usuário** na seção **Atributos de Usuário** do aplicativo.

![Seção Atributos de usuário][1]

Há dois possíveis motivos para você precisar editar as declarações emitidas no token SAML:
* O aplicativo foi escrito para exigir um conjunto diferente de URIs ou valores de declaração.
* O aplicativo foi implantado de uma forma que exige que a declaração NameIdentifier seja algo diferente do nome de usuário (também conhecido como nome UPN) armazenado no Azure Active Directory.

Você pode editar qualquer um dos valores de declaração padrão. Selecione a linha de declaração na tabela de atributos do token SAML. Isso abre a seção **Editar atributo** e, em seguida, é possível editar o nome, valor e namespace da declaração associado a ela.

![Editar Atributo de Usuário][2]

Também é possível remover declarações (que não sejam NameIdentifier) usando o menu de contexto, aberto clicando no ícone **...**.  Também é possível adicionar novas declarações usando o botão **Adicionar atributo**.

![Editar Atributo de Usuário][3]

## <a name="editing-the-nameidentifier-claim"></a>Editando a declaração NameIdentifier
Para resolver o problema no qual o aplicativo foi implantado usando um nome de usuário diferente, clique na lista suspensa **Identificador de Usuário** na seção **Atributos de Usuário**. Essa ação apresenta uma caixa de diálogo com várias opções diferentes:

![Editar Atributo de Usuário][4]

Na lista suspensa, selecione **user.mail** para definir a declaração NameIdentifier como o endereço de email do usuário no diretório. Ou selecione **user.onpremisessamaccountname** para definir como o Nome da Conta SAM do usuário sincronizado do Azure AD local.

Também é possível usar a função especial **ExtractMailPrefix()** para remover o sufixo de domínio do endereço de email, Nome da Conta SAM ou do nome UPN. Isso extrai somente a primeira parte do nome de usuário que está sendo passada (por exemplo, "joe_smith" em vez de joe_smith@contoso.com).

![Editar Atributo de Usuário][5]

Agora também adicionamos a função **join()** para ingressar no domínio verificado com o valor do identificador de usuário. quando você seleciona a função join() no **Identificador de Usuário**, primeiro selecione o identificador de usuário como o endereço de email ou nome UPN e, em seguida, na segunda lista suspensa, selecione seu domínio verificado. Se você selecionar o endereço de email com o domínio verificado, o Azure AD extrai o nome de usuário do primeiro valor joe_smith de joe_smith@contoso.com e o acrescenta com contoso.onmicrosoft.com. Veja os exemplos a seguir:

![Editar Atributo de Usuário][6]

## <a name="adding-claims"></a>Adicionando declarações
Ao adicionar uma declaração, você pode especificar o nome do atributo (que não precisa seguir rigidamente um padrão de URI de acordo com a especificação SAML). Defina o valor para qualquer atributo de usuário armazenado no diretório.

![Adicionar Atributo de Usuário][7]

Por exemplo, você precisa enviar o departamento ao qual o usuário pertence na organização como uma declaração (por exemplo, Vendas). Insira o nome da declaração conforme esperado pelo aplicativo e, em seguida, selecione **user.department** como o valor.

> [!NOTE]
> Se, para um determinado usuário, não houver valor armazenado para um atributo selecionado, essa declaração não será emitida no token.

> [!TIP]
> Há suporte para **user.onpremisesecurityidentifier** e **user.onpremisesamaccountname** apenas ao sincronizar os dados de usuário do Active Directory local usando a [ferramenta Azure AD Connect](../active-directory-aadconnect.md).

## <a name="restricted-claims"></a>Declarações restritas

Há algumas declarações restritas no SAML. Se você adicionar essas declarações, então o Azure AD não as enviará. O conjunto de declarações restritas do SAML são os seguintes:

    | Tipo de declaração (URI) |
    | ------------------- |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/expired |
    | http://schemas.microsoft.com/identity/claims/accesstoken |
    | http://schemas.microsoft.com/identity/claims/openid2_id |
    | http://schemas.microsoft.com/identity/claims/identityprovider |
    | http://schemas.microsoft.com/identity/claims/objectidentifier |
    | http://schemas.microsoft.com/identity/claims/puid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier[MR1] |
    | http://schemas.microsoft.com/identity/claims/tenantid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod |
    | http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/groups |
    | http://schemas.microsoft.com/claims/groups.link |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/role |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/wids |
    | http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant |
    | http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown |
    | http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged |
    | http://schemas.microsoft.com/2014/03/psso |
    | http://schemas.microsoft.com/claims/authnmethodsreferences |
    | http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier |
    | http://schemas.microsoft.com/identity/claims/scope |

## <a name="next-steps"></a>Próximas etapas
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](../active-directory-apps-index.md)
* [Configurando logon único para aplicativos que não estão na galeria de aplicativo do Active Directory do Azure](../active-directory-saas-custom-apps.md)
* [Solução de problemas de logon único baseado em SAML](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ./media/active-directory-saml-claims-customization/user-attribute-section.png
[2]: ./media/active-directory-saml-claims-customization/edit-claim-name-value.png
[3]: ./media/active-directory-saml-claims-customization/delete-claim.png
[4]: ./media/active-directory-saml-claims-customization/user-identifier.png
[5]: ./media/active-directory-saml-claims-customization/extractemailprefix-function.png
[6]: ./media/active-directory-saml-claims-customization/join-function.png
[7]: ./media/active-directory-saml-claims-customization/add-attribute.png
