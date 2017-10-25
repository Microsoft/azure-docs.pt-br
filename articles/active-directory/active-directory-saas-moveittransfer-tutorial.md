---
title: "Tutorial: Integração do Azure Active Directory com o MOVEit Transfer - Azure AD integration | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o MOVEit Transfer - Azure AD integration."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: jeedes
ms.openlocfilehash: d35aceb9be2d0ff49f86a00cc84f5deb198d88f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Tutorial: Integração do Azure Active Directory com o MOVEit Transfer - Azure AD integration

Neste tutorial, você aprenderá a integrar o MOVEit Transfer - Azure AD integration ao Azure Active Directory (Azure AD).

A integração do MOVEit Transfer- Azure AD integration ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao MOVEit Transfer - Azure AD integration.
- Você pode permitir que seus usuários faça logon automaticamente no MOVEit Transfer - Azure AD integration (logon único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao MOVEit Transfer - Azure AD integration, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do MOVEit Transfer - Azure AD integration habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar MOVEit Transfer - Azure AD integration pela galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Adicionar MOVEit Transfer - Azure AD integration pela galeria
Para configurar a integração do MOVEit Transfer - Azure AD integration ao Azure AD, você precisa adicionar o MOVEit Transfer - Azure AD integration pela galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o MOVEit Transfer - Azure AD integration pela galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **MOVEit Transfer - Azure AD integration**, selecione **MOVEit Transfer - Azure AD integration** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![MOVEit Transfer - Azure AD integration na lista de resultados](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o MOVEit Transfer - Azure AD integration, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do MOVEit Transfer - Azure AD integration é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do Azure AD e o usuário relacionado no MOVEit Transfer - Azure AD integration.

No MOVEit Transfer - Azure AD integration, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o MOVEit Transfer - Azure AD integration, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do MOVEit Transfer - Azure AD integration](#create-a-moveit-transfer---azure-ad-integration-test-user)** para ter um equivalente de Brenda Fernandes no MOVEit Transfer - Azure AD integration que seja vinculado à representação do usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único em seu aplicativo do MOVEit Transfer - Azure AD integration.

**Para configurar o logon único do Azure AD com o MOVEit Transfer - Azure AD integration, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **MOVEit Transfer - Azure AD integration**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_samlbase.png)

3. Na seção **Domínio e URLs do MOVEit Transfer - Azure AD integration**, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://contoso.com`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://contoso.com/<tenatid>`

    c. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://contoso.com/<tenatid>/SAML/SSO/HTTP-Post`    
     
    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. Você pode consultar esses valores posteriormente na seção **URL de metadados do provedor de serviço** ou entrar em contato com a [equipe de suporte do Cliente do MOVEit Transfer - Azure AD integration](https://community.ipswitch.com/s/support) para obter esses valores.

4. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_400.png)
    
6. Faça logon em seu locatário do MOVEit Transfer como administrador.

7. No painel de navegação esquerdo, clique em **Configurações**.

    ![Seção Configurações no lado do aplicativo](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_000.png)

8. Clique no link **Logon Único** que está em **Políticas de Segurança -> Autenticação de Usuário**.

    ![Políticas de segurança no lado do aplicativo](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_001.png)

9. Clique no link de URL de Metadados para baixar o documento de metadados.

    ![URL de metadados do provedor de serviço](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
    * Verifique se **entityID** corresponde ao **Identificador** na seção **Domínio e URLs do MOVEit Transfer - Azure AD integration**.
    * Verifique se a URL de local **AssertionConsumerService** corresponde à **URL DE RESPOSTA** na seção **Domínio e URLs do MOVEit Transfer - Azure AD integration**.
    
    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_007.png)

10. Clique no botão **Adicionar Provedor de Identidade** para adicionar um novo Provedor de Identidade Federada.

    ![Adicionar Provedor de Identidade](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_003.png)

11. Clique em **Procurar...** para selecionar o arquivo de metadados que você baixou do portal do Azure e clique em **Adicionar Provedor de Identidade** para carregar o arquivo baixado.

    ![Provedor de identidade SAML](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_004.png)

12. Selecione "**Sim**" como **Habilitado** na página **Editar Provedor de Identidade Federado...** e clique em **Salvar**.

    ![Configurações de Provedor de Identidade Federado](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_005.png)

13. Na página **Editar Configurações de usuário de Provedor de Identidade Federado**, realize as ações a seguir:
    
    ![Edite Configurações de Provedor de Identidade Federado](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. Selecione **SAML NameID** como **Nome de logon**.
    
    b. Selecione **Outros** como **Nome completo** e, na caixa de texto **Nome do atributo**, coloque o valor: `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. Selecione **Outros** como **Email** e, na caixa de texto **Nome do atributo**, coloque o valor: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    d. Selecione **Sim** para **Criação automática de conta no momento da conexão**.
    
    e. Clique no botão **Salvar** .

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-moveit-transfer---azure-ad-integration-test-user"></a>Crie um usuário de teste do MOVEit Transfer - Azure AD integration

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no MOVEit Transfer - Azure AD integration. O MOVEit Transfer - Azure AD integration dá suporte ao provisionamento just-in-time, que você habilitou. Não há itens de ação para você nesta seção. Um novo usuário é criado durante uma tentativa de acessar o MOVEit Transfer - Azure AD integration, caso ele ainda não exista.

>[!NOTE]
>Se precisar criar um usuário manualmente, você precisará contatar a [equipe de suporte do MOVEit Transfer - Azure AD integration](https://community.ipswitch.com/s/support).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao MOVEit Transfer - Azure AD integration.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao MOVEit Transfer - Azure AD integration, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **MOVEit Transfer - Azure AD integration**.

    ![Link do MOVEit Transfer - Azure AD integration na lista de aplicativos](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

O objetivo desta seção é testar sua configuração de SSO do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do MOVEit Transfer - Azure AD integration no painel de acesso, você deverá ser conectado automaticamente ao aplicativo do MOVEit Transfer - Azure AD integration. 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_203.png

