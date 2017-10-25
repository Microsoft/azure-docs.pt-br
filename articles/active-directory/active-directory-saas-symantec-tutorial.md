---
title: "Tutorial: Integração do Azure Active Directory ao Symantec WSS (Web Security Service) | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Symantec WSS (Web Security Service)."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d6e4d893-1f14-4522-ac20-0c73b18c72a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 61576d3a915d209e7355e04432e586dcf66e7c5a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Tutorial: Integração do Azure Active Directory ao Symantec WSS (Web Security Service)

Neste tutorial, você aprenderá a como integrar sua conta do Symantec Web Security Service (WSS) com sua conta do Azure Active Directory (Azure AD) para que o WSS possa autenticar um usuário final provisionado no Azure AD usando a autenticação do SAML e impor regras de política no nível de usuário ou de grupo.

A integração do Symantec WSS (Web Security Service) ao Azure AD oferece os seguintes benefícios:

- Gerenciar todos os usuários finais e grupos usados pela sua conta do WSS pelo portal do Azure AD. 

- Permitir que os usuários finais se autentiquem no WSS usando suas credenciais do Azure AD.

- Habilitar a imposição de regras de política no nível de usuário e de grupo definidas em sua conta do WSS.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Symantec WSS (Web Security Service), você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma conta do Symantec WSS (Web Security Service)

> [!NOTE]
> Para testar as etapas neste tutorial, não recomendamos o uso de uma conta do WSS que esteja sendo usada para fins de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use sua conta do WSS que esteja sendo usada para fins de produção neste teste, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você vai configurar o Azure AD para habilitar o logon único no WSS usando as credenciais do usuário final definidas em sua conta do Azure AD.
O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o aplicativo Symantec WSS (Web Security Service) por meio da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>Adicionando o Symantec WSS (Web Security Service) por meio da galeria
Para configurar a integração do Symantec WSS (Web Security Service) ao Azure AD, é necessário adicionar o Symantec WSS (Web Security Service) à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Symantec WSS (Web Security Service) por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Symantec Web Security Service (WSS)**, selecione **Symantec Web Security Service (WSS)** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Symantec Web Security Service (WSS) na lista de resultados](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Symantec WSS (Web Security Service), com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Symantec WSS (Web Security Service) é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Symantec WSS (Web Security Service).

No Symantec WSS (Web Security Service), atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Symantec WSS (Web Security Service), você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Symantec WSS (Web Security Service)](#create-a-symantec-web-security-service-wss-test-user)** – para ter um equivalente de Brenda Fernandes no Symantec WSS (Web Security Service) que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Symantec WSS (Web Security Service).

**Para configurar o logon único do Azure AD com o Symantec WSS (Web Security Service), realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Symantec WSS (Web Security Service)**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_samlbase.png)

3. Na seção **Domínio e URLs do Symantec WSS (Web Security Service)**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Symantec Web Security Service (WSS)](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_url.png)

    a. Na caixa de texto **Identificador**, digite a URL: `https://saml.threatpulse.net:8443/saml/saml_realm`

    b. Na caixa de texto **URL de Resposta**, digite a URL: `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Entre em contato com a [equipe de suporte do Cliente do Symantec Web Security Service (WSS)](https://www.symantec.com/contact-us) se os valores de **Identificador** e **URL de Resposta** não estiverem funcionando por algum motivo.

4. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-symantec-tutorial/tutorial_general_400.png)
    
6. Para configurar o logon único no lado do Symantec Web Security Service (WSS), consulte a documentação online do WSS. O arquivo **XML de metadados** baixado precisará ser importado para o portal do WSS. Entre em contato com a [equipe de suporte do Symantec Web Security Service (WSS)](https://www.symantec.com/contact-us) se precisar de ajuda com a configuração no portal do WSS.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-symantec-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-symantec-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-symantec-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-symantec-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-symantec-web-security-service-wss-test-user"></a>Criar um usuário de teste do Symantec WSS (Web Security Service)

Nesta seção, você cria um usuário chamado Brenda Fernandes no Symantec WSS (Web Security Service). O nome de usuário final correspondentes pode ser criado manualmente no portal do WSS ou você pode esperar os usuários/grupos provisionados no Azure AD serem sincronizados ao portal do WSS após alguns minutos (aproximadamente 15 minutos). Os usuários devem ser criados e ativados antes de usar o logon único. O endereço IP público do computador do usuário final, que será usada para navegar em sites, também precisa ser provisionado no portal do Symantec Web Security Service (WSS).

> [!NOTE]
> [Clique aqui](http://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1) para obter o endereço IP público do computador.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Symantec WSS (Web Security Service).

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Symantec WSS (Web Security Service), realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Symantec WSS (Web Security Service)**.

    ![O link do Symantec WSS (Web Security Service) na lista de aplicativos](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você vai testar a funcionalidade de logon único agora que configurou sua conta do WSS para usar o Azure AD para autenticação de SAML.

Depois de configurar seu navegador da Web para o tráfego de proxy para WSS, quando você abre o navegador da Web e tenta navegar para um site, você será redirecionado para a página de logon do Azure. Insira as credenciais do usuário final de teste que foram provisionadas no Azure AD (ou seja, BrendaFernandes) e a senha associada. Uma vez autenticado, você poderá navegar até o site que desejar. Caso você crie uma regra de política no lado do WSS para bloquear a navegação de BrendaFernandes em um site específico, você verá a página de bloqueio do WSS quando tentar navegar ao site como o usuário BrendaFernandes.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_203.png

