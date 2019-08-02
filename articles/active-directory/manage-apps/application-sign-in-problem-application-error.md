---
title: Mensagem de erro é exibida na página de aplicativo depois de entrar no | Microsoft Docs
description: Como resolver problemas de entrada do Azure AD quando o aplicativo retornar uma mensagem de erro.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23e6a3d0b533dccc3c3111382b014907d5c026ab
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612657"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>Uma página de aplicativo mostra uma mensagem de erro depois que o usuário faz logon

Nesse cenário, Azure Active Directory (Azure AD) conecta o usuário. Mas o aplicativo exibe uma mensagem de erro e não permite que o usuário concluir o fluxo de entrada. O problema é que o aplicativo não aceitou a resposta que emitido do AD do Azure.

Há várias razões possíveis por que o aplicativo não aceitou a resposta do AD do Azure. Se a mensagem de erro não identificar claramente o que está faltando da resposta, tente o seguinte:

-   Se o aplicativo de galeria do Azure AD, verifique se que você seguiu as etapas em [como depurar baseado em SAML SSO para aplicativos no Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).

-   Use uma ferramenta como [Fiddler](https://www.telerik.com/fiddler) para capturar a solicitação SAML, a resposta e o token.

-   Enviar a resposta SAML para o fornecedor do aplicativo e peça-lhe o que está faltando.

## <a name="attributes-are-missing-from-the-saml-response"></a>Atributos estão faltando da resposta SAML

Para adicionar um atributo na configuração do AD do Azure que será enviada na resposta do Azure AD, siga estas etapas:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e entre como um administrador global ou um coadministrador.

2. Na parte superior do painel de navegação à esquerda, selecione **todos os serviços** para abrir a extensão do AD do Azure.

3. Tipo de **Azure Active Directory** na caixa de pesquisa do filtro e selecione **Azure Active Directory**.

4. Selecione **aplicativos empresariais** no painel de navegação do Azure AD.

5. Selecione **todos os aplicativos** para exibir uma lista dos seus aplicativos.

   > [!NOTE]
   > Se você não vir o aplicativo que você deseja, use o **filtro** controle na parte superior das **lista de todos os aplicativos**. Defina as **Mostrar** opção para "Todos os aplicativos".

6. Selecione o aplicativo que você deseja configurar para logon único.

7. Depois que o aplicativo for carregado, selecione **logon único** no painel de navegação.

8. No **atributos de usuário** seção, selecione **exibir e editar todos os outros atributos de usuário**. Aqui você pode alterar quais atributos devem enviar para o aplicativo no token SAML quando os usuários entrarem.

   Para adicionar um atributo:

   1. Selecione **Adicionar Atributo**. Insira o **nome**e selecione o **valor** na lista suspensa.

   1.  Clique em **Salvar**. Você verá o novo atributo na tabela.

9. Salve a configuração.

   Na próxima vez que o usuário faz logon para o aplicativo do Azure AD enviará o novo atributo na resposta SAML.

## <a name="the-app-doesnt-identify-the-user"></a>O aplicativo não identifica o usuário

Entrar aplicativo falhará porque a resposta SAML está faltando um atributo, como uma função. Ou ele falhará porque o aplicativo espera um formato diferente ou o valor para o **NameID** atributo (identificador de usuário).

Se você estiver usando [do Azure AD provisionamento automatizado de usuários](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) para criar, manter e remover usuários no aplicativo, verifique se que o usuário foi provisionado para o aplicativo SaaS. Para obter mais informações, consulte [nenhum usuário está sendo provisionado para um aplicativo de galeria do Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-no-users-provisioned).

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Adicionar um atributo a configuração de aplicativo do Azure AD

Para alterar o valor do Identificador de Usuário, siga estas etapas:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e entre como um administrador global ou um coadministrador.

2. Selecione **todos os serviços** na parte superior do painel de navegação à esquerda para abrir a extensão do AD do Azure.

3. Tipo de **Azure Active Directory** na caixa de pesquisa do filtro e selecione **Azure Active Directory**.

4. Selecione **aplicativos empresariais** no painel de navegação do Azure AD.

5. Selecione **todos os aplicativos** para exibir uma lista dos seus aplicativos.

   > [!NOTE]
   > Se você não vir o aplicativo que você deseja, use o **filtro** controle na parte superior das **lista de todos os aplicativos**. Defina as **Mostrar** opção para "Todos os aplicativos".

6. Selecione o aplicativo que você deseja configurar para que o SSO.

7. Depois que o aplicativo for carregado, selecione **logon único** no painel de navegação.

8. Sob **atributos de usuário**, selecione o identificador exclusivo para o usuário da **identificador de usuário** lista suspensa.

## <a name="change-the-nameid-format"></a>Alterar o formato de NameID

Se o aplicativo espera outro formato para o **NameID** atributo (identificador de usuário), consulte [edição nameID](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization#editing-nameid) para alterar o formato de NameID.

Azure AD seleciona o formato para o **NameID** (identificador de usuário) do atributo com base no valor selecionado ou o formato solicitado pelo aplicativo no AuthRequest do SAML. Para obter mais informações, consulte a seção "NameIDPolicy" [único protocolo de logon SAML](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol#nameidpolicy).

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>O aplicativo espera um método de assinatura diferente para a resposta SAML

Para alterar quais partes do token SAML são assinados digitalmente pelo AD do Azure, siga estas etapas:

1. Abra o [portal do Azure](https://portal.azure.com/) e entre como um administrador global ou um coadministrador.

2. Selecione **todos os serviços** na parte superior do painel de navegação à esquerda para abrir a extensão do AD do Azure.

3. Tipo de **Azure Active Directory** na caixa de pesquisa do filtro e selecione **Azure Active Directory**.

4. Selecione **aplicativos empresariais** no painel de navegação do Azure AD.

5. Selecione **todos os aplicativos** para exibir uma lista dos seus aplicativos.

   > [!NOTE]
   > Se você não vir o aplicativo que você deseja, use o **filtro** controle na parte superior das **lista de todos os aplicativos**. Defina as **Mostrar** opção para "Todos os aplicativos".

6. Selecione o aplicativo que você deseja configurar para logon único.

7. Depois que o aplicativo é carregado, selecione **logon único** no painel de navegação.

8. Sob **certificado de autenticação SAML**, selecione **Mostrar configurações avançadas de certificado assinatura**.

9. Selecione o **opção de assinatura** que o aplicativo espera dentre estas opções:

   * **Assinar resposta SAML**
   * **Assinar resposta SAML e declaração**
   * **Assinar declaração SAML**

   Na próxima vez que o usuário faz logon para o aplicativo do Azure AD assinará a parte da resposta SAML que você selecionou.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>O aplicativo espera que o algoritmo de assinatura SHA-1

Por padrão, o Azure AD assina o token SAML usando o algoritmo mais segura. É recomendável que você não altere o algoritmo de assinatura *SHA-1* , a menos que o aplicativo exige o SHA-1.

Para alterar o algoritmo de assinatura, siga estas etapas:

1. Abra o [portal do Azure](https://portal.azure.com/) e entre como um administrador global ou um coadministrador.

2. Selecione **todos os serviços** na parte superior do painel de navegação à esquerda para abrir a extensão do AD do Azure.

3. Tipo de **Azure Active Directory** na caixa de pesquisa do filtro e selecione **Azure Active Directory**.

4. Selecione **aplicativos empresariais** no painel de navegação do Azure AD.

5. Selecione **todos os aplicativos** para exibir uma lista dos seus aplicativos.

   > [!NOTE]
   > Se você não vir o aplicativo que você deseja, use o **filtro** controle na parte superior das **lista de todos os aplicativos**. Defina as **Mostrar** opção para "Todos os aplicativos".

6. Selecione o aplicativo que você deseja configurar para logon único.

7. Depois que o aplicativo for carregado, selecione **logon único** no painel de navegação no lado esquerdo do aplicativo.

8. Sob **certificado de autenticação SAML**, selecione **Mostrar configurações avançadas de certificado assinatura**.

9. Selecione **SHA-1** como o **algoritmo de assinatura**.

   Na próxima vez que o usuário faz logon para o aplicativo do Azure AD assinará o token SAML usando o algoritmo SHA-1.

## <a name="next-steps"></a>Próximas etapas
[Como depurar baseado em SAML SSO para aplicativos no Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).
