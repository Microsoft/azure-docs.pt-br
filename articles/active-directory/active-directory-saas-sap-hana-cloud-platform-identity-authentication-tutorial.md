---
title: "Tutorial: Integração do Azure Active Directory com a Autenticação de Identidade da SAP Cloud Platform | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e a Autenticação de Identidade da SAP Cloud Platform."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: jeedes
ms.openlocfilehash: e4e7c7273acc216ae82a52b3e6dcd530a6ad1bd7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Tutorial: Integração do Azure Active Directory com a Autenticação de Identidade da SAP Cloud Platform

Neste tutorial, você aprenderá a integrar a Autenticação de Identidade da SAP Cloud Platform ao Azure Active Directory (Azure AD). A Autenticação de Identidade da SAP Cloud Platform é usada como um proxy IdP para acessar aplicativos SAP usando o Azure AD como o IdP principal.

Integrar a Autenticação de Identidade da SAP Cloud Platform ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao aplicativo SAP.
- Você pode permitir que os usuários se conectem automaticamente ao logon único (SSO) de aplicativos SAP com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Autenticação de Identidade da SAP Cloud Platform, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura da Autenticação de Identidade da SAP Cloud Platform com logon único habilitado

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar Autenticação de Identidade da SAP Cloud Platform a partir da galeria
2. Configurar e testar o logon único do AD do Azure

Antes de nos aprofundarmos nos detalhes técnicos, é essencial entender os conceitos que serão examinados. A federação da Autenticação de Identidade da SAP Cloud Platform e do Azure Active Directory permite que você implemente o SSO em aplicativos ou serviços protegidos pelo AAD (como um IdP) com aplicativos SAP e serviços protegidos pela Autenticação de Identidade da SAP Cloud Platform.

Atualmente, a Autenticação de Identidade da SAP Cloud Platform atua como um provedor de identidade de proxy para aplicativos SAP. O Azure Active Directory por sua vez atua como o principal provedor de identidade nessa configuração. 

O seguinte diagrama ilustra isso:

![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sapcloudauth-tutorial/architecture-01.png)

Com essa configuração, seu locatário de Autenticação de Identidade da SAP Cloud Platform será configurado como um aplicativo confiável no Azure Active Directory. 

Todos os serviços e aplicativos SAP que você deseja proteger dessa maneira são posteriormente configurados no console de gerenciamento de Autenticação de Identidade da SAP Cloud Platform!

Isso significa que a autorização para conceder acesso aos serviços e aplicativos SAP precisa ocorrer na Autenticação de Identidade da SAP Cloud Platform para uma configuração como essa (em vez de configurar a autorização no Azure Active Directory).

Configurando a Autenticação de Identidade da SAP Cloud Platform como um aplicativo por meio do Azure Active Directory Marketplace, você não precisa cuidar da configuração de declarações individuais/transformações e asserções SAML necessárias para gerar um token de autenticação válido para aplicativos SAP.

>[!NOTE] 
>Atualmente, o SSO da Web foi testado somente por ambas as partes. Fluxos necessários para a comunicação de aplicativo a API ou de API para API devem funcionar, mas ainda não foram testados. Eles serão testados como parte das atividades subsequentes.
>

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Adicionar Autenticação de Identidade da SAP Cloud Platform a partir da galeria
Para configurar a integração da Autenticação de Identidade da SAP Cloud Platform no Azure AD, você precisa adicionar a Autenticação de Identidade da SAP Cloud Platform da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar a Autenticação de Identidade da SAP Cloud Platform da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Autenticação de Identidade da SAP Cloud Platform**, selecione **Autenticação de Identidade da SAP Cloud Platform** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Adicionar Autenticação de Identidade da SAP Cloud Platform na lista de resultados](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com a Autenticação de Identidade da SAP Cloud Platform, com base em um usuário de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Autenticação de Identidade da SAP Cloud Platform é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado da Autenticação de Identidade da SAP Cloud Platform.

Na Autenticação de Identidade da SAP Cloud Platform, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com a Autenticação de Identidade da SAP Cloud Platform, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste de Autenticação de Identidade da SAP Cloud Platform](#create-an-sap-cloud-platform-identity-authentication-test-user)** – para ter um equivalente de Brenda Fernandes na Autenticação de Identidade da SAP Cloud Platform que é vinculado à representação dela no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único em seu aplicativo da Autenticação de Identidade da SAP Cloud Platform.

**Para configurar o logon único do Azure AD com Autenticação de Identidade da SAP Cloud Platform, realize as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos de **Autenticação de Identidade da SAP Cloud Platform**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_samlbase.png)

3. Na seção **Domínio e URLs da Autenticação de Identidade da SAP Cloud Platform**, se você deseja configurar o aplicativo no modo iniciado pelo **IDP**:

    ![Informações sobre logon único de Domínio e URLs da Autenticação de Identidade da SAP Cloud Platform](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_url.png)

    Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<entity-id>.accounts.ondemand.com`

    > [!NOTE] 
    > Esse valor não é real. Atualize esse valor com o Identificador real. Entre em contato com a [equipe de suporte do Cliente de Autenticação de Identidade da SAP Cloud Platform](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) para obter esse valor. Se você não souber esse valor, siga a documentação da Autenticação de Identidade da SAP Cloud Platform em [Configuração do SAML 2.0 do Locatário](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

4. Marque **Mostrar configurações de URL avançadas**. Se quiser configurar o aplicativo no modo iniciado em **SP**:

    ![Informações sobre logon único de Domínio e URLs da Autenticação de Identidade da SAP Cloud Platform](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<entity-id>.accounts.ondemand.com/admin`

    > [!NOTE] 
    > Esse valor não é real. Atualize esse valor com a URL de Logon real. Entre em contato com a [equipe de suporte do Cliente de Autenticação de Identidade da SAP Cloud Platform](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) para obter esse valor.

5. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_certificate.png)

6. O aplicativo de Autenticação de Identidade da SAP Cloud Platform espera as asserções SAML em um formato específico. Você pode gerenciar os valores desses atributos da seção "**Atributos de Usuário**" na página de integração do aplicativo. A captura de tela a seguir mostra um exemplo disso. 

    ![Configurar Logon Único](./media/active-directory-saas-sapcloudauth-tutorial/attribute.png)

7. Na seção **Atributos de Usuário** da caixa de diálogo **Logon único**, se seu aplicativo SAP espera um atributo, por exemplo "firstName". No diálogo de atributos de token SAML, adicione o atributo "firstName".

    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.
    
    ![Configurar o logon único](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_attribute_04.png)
    
    ![Configurar Logon Único](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_attribute_05.png)
    
    b. Na caixa de texto **Nome**, digite o nome do atributo "firstName".
    
    c. Na lista **Valor**, selecione o valor do atributo "user.givenname".
    
    d. Clique em **OK**.

8. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_400.png)

9. Na seção **Configuração da Autenticação de Identidade da SAP Cloud Platform**, clique em **Configurar Autenticação de Identidade da SAP Cloud Platform** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configuração da Autenticação de Identidade da SAP Cloud Platform](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_configure.png) 

10. Para configurar o SSO para o seu aplicativo, vá para o Console de administração da Autenticação de Identidade da SAP Cloud Platform. A URL tem o seguinte padrão: `https://<tenant-id>.accounts.ondemand.com/admin`. Depois, siga a documentação sobre a Autenticação de Identidade da SAP Cloud Platform para [Configurar o Microsoft Azure AD como provedor de identidade corporativa na Autenticação de Identidade da SAP Cloud Platform](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html). 

11. No portal do Azure, clique no botão **Salvar**.

12. Continue as etapas a seguir somente se você quiser adicionar e habilitar o SSO para outro aplicativo SAP. Repita as etapas na seção "Adicionar Autenticação de Identidade da SAP Cloud Platform" para adicionar outra instância de Autenticação de identidade da SAP Cloud Platform.

13. No portal do Azure, na página de integração de aplicativos de **Autenticação de Identidade da SAP Cloud Platform**, clique em **Logon vinculado**.

    ![Configurar o logon vinculado](./media/active-directory-saas-sapcloudauth-tutorial/linked_sign_on.png)

14. Salve a configuração.

>[!NOTE] 
>O novo aplicativo aproveitará a configuração de SSO para o aplicativo SAP anterior. Certifique-se de usar os mesmos Provedores de Identidade Corporativa no Console de Administração de Autenticação de Identidade da SAP Cloud Platform.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-an-sap-cloud-platform-identity-authentication-test-user"></a>Criar um usuário de teste da Autenticação de Identidade da SAP Cloud Platform

Você não precisa criar um usuário na Autenticação de Identidade da SAP Cloud Platform. Os usuários que estão no repositório de usuários do Azure AD podem usar a funcionalidade de SSO.

A Autenticação de Identidade da SAP Cloud Platform dá suporte à opção de Federação de Identidades. Essa opção permite que o aplicativo verifique se os usuários autenticados pelo provedor de identidade corporativa existem no repositório de usuários da Autenticação de Identidade da SAP Cloud Platform. 

Na configuração padrão, a opção de Federação de Identidades é desabilitada. Se a Federação de Identidades é habilitada, somente os usuários que são importados na Autenticação de Identidade da SAP Cloud Platform são capazes de acessar o aplicativo. 

Para obter mais informações sobre como habilitar ou desabilitar a Federação de Identidades com a Autenticação de Identidade da SAP Cloud Platform, consulte Habilitar a Federação de Identidades com a Autenticação de Identidade da SAP Cloud Platform em [Configurar a Federação de Identidades com o repositório de usuários da Autenticação de Identidade da SAP Cloud Platform](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilitará Brenda Fernandes a usar o logon único do Azure concedendo-lhe acesso à Autenticação de Identidade da SAP Cloud Platform.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes à Autenticação de Identidade da SAP Cloud Platform, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Autenticação de Identidade da SAP Cloud Platform**.

    ![Link da Autenticação de Identidade da SAP Cloud Platform na lista de Aplicativos](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco de Autenticação de Identidade da SAP Cloud Platform no Painel de Acesso, seu logon deve ser realizado automaticamente no seu aplicativo de Autenticação de Identidade da SAP Cloud Platform.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_203.png

