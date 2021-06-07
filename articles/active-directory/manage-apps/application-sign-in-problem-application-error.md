---
title: A mensagem de erro aparece na página do aplicativo depois que você entra | Microsoft Docs
description: Como resolver problemas com a entrada do Azure AD quando o aplicativo retorna uma mensagem de erro.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c96209f33491645510d8592997c418472d4f227c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258805"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>Uma página de aplicativo mostra uma mensagem de erro após o usuário entrar

Nesse cenário, o Azure Active Directory (Azure AD) assina o usuário no. Mas o aplicativo exibe uma mensagem de erro e não permite que o usuário conclua o fluxo de entrada. O problema é que o aplicativo não aceitou a resposta emitida pelo AD do Azure.

Há vários motivos possíveis para o aplicativo não aceitar a resposta do Azure AD. Se a mensagem de erro não identificar claramente o que está ausente da resposta, tente o seguinte:

-   Se o aplicativo for a galeria do Azure AD, verifique se você seguiu as etapas em [como depurar o logon único baseado em SAML para aplicativos no Azure ad](./debug-saml-sso-issues.md).

-   Use uma ferramenta como o [Fiddler](https://www.telerik.com/fiddler) para capturar a solicitação, a resposta e o token SAML.

-   Envie a resposta SAML para o fornecedor do aplicativo e pergunte o que está faltando.

## <a name="attributes-are-missing-from-the-saml-response"></a>Atributos ausentes da resposta SAML

Para adicionar um atributo na configuração do Azure AD que será enviada na resposta do Azure AD, siga estas etapas:

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um administrador global ou coadministrador.

2. Na parte superior do painel de navegação no lado esquerdo, selecione **todos os serviços** para abrir a extensão do Azure AD.

3. Digite **Azure Active Directory** na caixa de pesquisa de filtro e, em seguida, selecione **Azure Active Directory**.

4. Selecione **aplicativos empresariais** no painel de navegação do Azure AD.

5. Selecione **todos os aplicativos** para exibir uma lista de seus aplicativos.

   > [!NOTE]
   > Se você não vir o aplicativo desejado, use o controle de **filtro** na parte superior da **lista todos os aplicativos**. Defina a opção **Mostrar** como "todos os aplicativos".

6. Selecione o aplicativo que você deseja configurar para logon único.

7. Depois que o aplicativo for carregado, selecione **logon único** no painel de navegação.

8. Na seção **atributos de usuário** , selecione **Exibir e editar todos os outros atributos de usuário**. Aqui você pode alterar quais atributos enviar para o aplicativo no token SAML quando os usuários entram.

   Para adicionar um atributo:

   1. Selecione **Adicionar atributo**. Insira o **nome** e selecione o **valor** na lista suspensa.

   1.  Clique em **Salvar**. Você verá o novo atributo na tabela.

9. Salve a configuração.

   Na próxima vez que o usuário entrar no aplicativo, o Azure AD enviará o novo atributo na resposta SAML.

## <a name="the-app-doesnt-identify-the-user"></a>O aplicativo não identifica o usuário

A entrada no aplicativo falha porque a resposta SAML não tem um atributo, como uma função. Ou falha porque o aplicativo espera um formato ou valor diferente para o atributo **NameID** (identificador de usuário).

Se você estiver usando o [provisionamento de usuário automatizado do Azure ad](../app-provisioning/user-provisioning.md) para criar, manter e remover usuários no aplicativo, verifique se o usuário foi provisionado para o aplicativo SaaS. Para obter mais informações, consulte [nenhum usuário está sendo provisionado para um aplicativo da galeria do Azure ad](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md).

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Adicionar um atributo à configuração de aplicativo do Azure AD

Para alterar o valor do Identificador de Usuário, siga estas etapas:

1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um administrador global ou coadministrador.

2. Selecione **todos os serviços** na parte superior do painel de navegação no lado esquerdo para abrir a extensão do Azure AD.

3. Digite **Azure Active Directory** na caixa de pesquisa de filtro e, em seguida, selecione **Azure Active Directory**.

4. Selecione **aplicativos empresariais** no painel de navegação do Azure AD.

5. Selecione **todos os aplicativos** para exibir uma lista de seus aplicativos.

   > [!NOTE]
   > Se você não vir o aplicativo desejado, use o controle de **filtro** na parte superior da **lista todos os aplicativos**. Defina a opção **Mostrar** como "todos os aplicativos".

6. Selecione o aplicativo que você deseja configurar para o SSO.

7. Depois que o aplicativo for carregado, selecione **logon único** no painel de navegação.

8. Em **atributos de usuário**, selecione o identificador exclusivo para o usuário na lista suspensa **identificador de usuário** .

## <a name="change-the-nameid-format"></a>Alterar o formato NameID

Se o aplicativo espera outro formato para o atributo **NameID** (identificador de usuário), consulte [editando NameID](../develop/active-directory-saml-claims-customization.md#editing-nameid) para alterar o formato NameID.

O Azure AD seleciona o formato para o atributo **NameID** (identificador de usuário) com base no valor selecionado ou no formato solicitado pelo aplicativo no AUTHREQUEST do SAML. Para obter mais informações, consulte a seção "NameIDPolicy" do [protocolo SAML de logon único](../develop/single-sign-on-saml-protocol.md#nameidpolicy).

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>O aplicativo espera um método de assinatura diferente para a resposta SAML

Para alterar quais partes do token SAML são assinadas digitalmente pelo Azure AD, siga estas etapas:

1. Abra o [portal do Azure](https://portal.azure.com/) e entre como um administrador global ou coadministrador.

2. Selecione **todos os serviços** na parte superior do painel de navegação no lado esquerdo para abrir a extensão do Azure AD.

3. Digite **Azure Active Directory** na caixa de pesquisa de filtro e, em seguida, selecione **Azure Active Directory**.

4. Selecione **aplicativos empresariais** no painel de navegação do Azure AD.

5. Selecione **todos os aplicativos** para exibir uma lista de seus aplicativos.

   > [!NOTE]
   > Se você não vir o aplicativo desejado, use o controle de **filtro** na parte superior da **lista todos os aplicativos**. Defina a opção **Mostrar** como "todos os aplicativos".

6. Selecione o aplicativo que você deseja configurar para logon único.

7. Depois que o aplicativo for carregado, selecione **logon único** no painel de navegação.

8. Em **certificado de autenticação SAML**, selecione  **Mostrar configurações avançadas de assinatura de certificado**.

9. Selecione a **opção de assinatura** que o aplicativo espera entre estas opções:

   * **Assinar resposta SAML**
   * **Assinar resposta SAML e asserção**
   * **Assinar Asserção SAML**

   Na próxima vez que o usuário entrar no aplicativo, o Azure AD assinará a parte da resposta SAML que você selecionou.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>O aplicativo espera o algoritmo de assinatura SHA-1

Por padrão, o Azure AD assina o token SAML usando o algoritmo mais seguro. Recomendamos que você não altere o algoritmo de assinatura para *SHA-1* , a menos que o aplicativo exija SHA-1.

Para alterar o algoritmo de assinatura, siga estas etapas:

1. Abra o [portal do Azure](https://portal.azure.com/) e entre como um administrador global ou coadministrador.

2. Selecione **todos os serviços** na parte superior do painel de navegação no lado esquerdo para abrir a extensão do Azure AD.

3. Digite **Azure Active Directory** na caixa de pesquisa de filtro e, em seguida, selecione **Azure Active Directory**.

4. Selecione **aplicativos empresariais** no painel de navegação do Azure AD.

5. Selecione **todos os aplicativos** para exibir uma lista de seus aplicativos.

   > [!NOTE]
   > Se você não vir o aplicativo desejado, use o controle de **filtro** na parte superior da **lista todos os aplicativos**. Defina a opção **Mostrar** como "todos os aplicativos".

6. Selecione o aplicativo que você deseja configurar para logon único.

7. Depois que o aplicativo for carregado, selecione **logon único** no painel de navegação no lado esquerdo do aplicativo.

8. Em **certificado de autenticação SAML**, selecione **Mostrar configurações avançadas de assinatura de certificado**.

9. Selecione **SHA-1** como o **algoritmo de assinatura**.

   Na próxima vez que o usuário entrar no aplicativo, o Azure AD assinará o token SAML usando o algoritmo SHA-1.

## <a name="next-steps"></a>Próximas etapas
[Como depurar o logon único baseado em SAML para aplicativos no Azure ad](./debug-saml-sso-issues.md).