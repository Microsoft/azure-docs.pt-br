---
title: "Tutorial: Integração do Azure Active Directory com o IBM Kenexa Survey Enterprise | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o IBM Kenexa Survey Enterprise."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c7aac6da-f4bf-419e-9e1a-16b460641a52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 5c276c23288292a1c54dd9d57177d5072b90c9e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>Tutorial: Integração do Azure Active Directory com o IBM Kenexa Survey Enterprise

Neste tutorial, você aprenderá como integrar o IBM Kenexa Survey Enterprise ao Azure AD (Azure Active Directory).

A integração do IBM Kenexa Survey Enterprise ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao IBM Kenexa Survey Enterprise.
- É possível permitir que os usuários entrem automaticamente no IBM Kenexa Survey Enterprise com o logon único (SSO) com suas contas do Azure AD.
- Gerencie suas contas em um único local: o portal do Azure.

Se desejar saber mais sobre a integração de aplicativos SaaS (software como serviço) ao Azure AD, consulte [O que é o acesso do aplicativo e o logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o IBM Kenexa Survey Enterprise, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para SSO do IBM Kenexa Survey Enterprise

> [!NOTE]
> Ao testar as etapas deste tutorial, recomendamos que você não use um ambiente de produção.

Para testar as etapas neste tutorial, siga estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testa o SSO do Azure AD em um ambiente de teste. O cenário descrito no tutorial consiste em dois blocos de construção principais:

* Adicionando o IBM Kenexa Survey Enterprise da galeria
* Configurar e testar o SSO do Azure AD

## <a name="add-ibm-kenexa-survey-enterprise-from-the-gallery"></a>Adicionar o IBM Kenexa Survey Enterprise por meio da galeria
Para configurar a integração do IBM Kenexa Survey Enterprise ao Azure AD, adicione o IBM Kenexa Survey Enterprise à lista de aplicativos SaaS gerenciados por meio da galeria.

Para adicionar o IBM Kenexa Survey Enterprise por meio da galeria, faça o seguinte:

1. No [portal do Azure](https://portal.azure.com), no painel esquerdo, clique no botão **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Para adicionar um aplicativo, clique no botão **Novo aplicativo**.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **IBM Kenexa Survey Enterprise**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_search.png)

5. Na lista de resultados, selecione **IBM Kenexa Survey Enterprise** e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![IBM Kenexa Survey Enterprise na lista de resultados](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD
Nesta seção, você configura e testa o SSO do Azure AD com o IBM Kenexa Survey Enterprise, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o SSO funcione, o Azure AD precisa identificar o usuário do IBM Kenexa Survey Enterprise que é equivalente a um usuário do Azure AD. Em outras palavras, o Azure AD precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e um usuário relacionado do IBM Kenexa Survey Enterprise.

Para estabelecer a relação de vínculo, atribua o valor do **nome de usuário** no IBM Kenexa Survey Enterprise como o valor do **Nome de usuário** no Azure AD.

Para configurar e testar o SSO do Azure AD com o IBM Kenexa Survey Enterprise, conclua os blocos de construção nas duas próximas seções.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta seção, você habilita o SSO do Azure AD no portal do Azure e configura o SSO no aplicativo IBM Kenexa Survey Enterprise fazendo o seguinte:

1. No portal do Azure, na página de integração do aplicativo **IBM Kenexa Survey Enterprise**, clique em **Logon único**.

    ![Link Configurar logon único do IBM Kenexa Survey Enterprise][4]

2. Na caixa de diálogo **Logon único**, na caixa **Modo**, selecione **Logon baseado em SAML** para habilitar o SSO.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_samlbase.png)

3. Na seção **Domínio e URLs do IBM Kenexa Survey Enterprise**, realize as seguintes etapas:

    ![Informações de logon único em Domínio e URLs do IBM Kenexa Survey Enterprise](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_url.png)

    a. Na caixa de texto **Identificador**, digite uma URL com o seguinte padrão: `https://surveys.kenexa.com/<companycode>`

    b. Na caixa de texto **URL de Resposta**, digite uma URL com o seguinte padrão: `https://surveys.kenexa.com/<companycode>/tools/sso.asp`

    > [!NOTE] 
    > Os valores anteriores não são reais. Atualize-os com o identificador e a URL de resposta reais. Para obter os valores reais, contate a [equipe de suporte do IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw).

4. Em **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo de certificado no computador.

    ![O link de download do Certificado (Base64)](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_certificate.png) 

    O aplicativo IBM Kenexa Survey Enterprise espera receber as declarações SAML (Security Assertions Markup Language) em um formato específico, o que exige a adição de mapeamentos de atributo personalizados para a configuração dos atributos do token SAML. O valor da declaração do identificador de usuário na resposta deve corresponder à ID de SSO configurada no sistema do Kenexa. Para mapear o identificador de usuário apropriado em sua organização como o protocolo IDP de SSO, trabalhe com a [equipe de suporte do IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw). 

    Por padrão, o Azure AD define o identificador de usuário como o valor de nome UPN. Altere esse valor na guia **Atributo**, conforme mostrado na captura de tela a seguir. A integração funciona somente após a conclusão correta do mapeamento.
    
    ![A caixa de diálogo Atributos de Usuário](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_attribute.png)   

5. Clique em **Salvar**.

    ![O botão Salvar de Configurar Logon Único](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_400.png)

6. Para abrir a janela **Configurar logon**, em **Configuração do IBM Kenexa Survey Enterprise**, clique em **Configurar o IBM Kenexa Survey Enterprise**. 
 
    ![O link Configurar o IBM Kenexa Survey Enterprise](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_configure.png)

7. Copie os valores da **URL de Saída**, **ID da Entidade SAML** e **URL do Serviço de logon único SAML** da seção **Referência Rápida**.

8. Na janela **Configurar logon**, em **Referência Rápida**, copie os valores da **URL de Saída**, da **ID da Entidade SAML** e da **URL do Serviço de Logon Único SAML**.

9. Para configurar o SSO no lado do **IBM Kenexa Survey Enterprise**, envie o **Certificado (Base64)** baixado, a **URL de Saída**, a **ID da Entidade SAML** e a **URL do Serviço de Logon Único SAML** para a [equipe de suporte do IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw).

> [!TIP]
> Consulte uma versão concisa dessas instruções no [portal do Azure](https://portal.azure.com) enquanto estiver configurando o aplicativo. Depois de adicionar o aplicativo na seção **Active Directory** > **Aplicativos Empresariais**, basta clicar na guia **Logon único** e, depois, acessar a documentação inserida por meio da seção **Configuração** ao final. Para saber mais sobre o recurso de documentação inserida, consulte [Documentação inserida do Azure AD](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
Nesta seção, você cria o usuário de teste Brenda Fernandes no portal do Azure fazendo o seguinte:

![Criar um usuário de teste do Azure AD][100]

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_01.png) 

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.
    
    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.
 
    ![O botão Adicionar](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_03.png) 

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:
 
    ![A caixa de diálogo Usuário](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_04.png) 

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-an-ibm-kenexa-survey-enterprise-test-user"></a>Criar um usuário de teste do IBM Kenexa Survey Enterprise

Nesta seção, você criará uma usuária chamada Brenda Fernandes no IBM Kenexa Survey Enterprise. 

Para criar usuários no sistema do IBM Kenexa Survey Enterprise e mapear a ID de SSO para eles, trabalhe com a [equipe de suporte do IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw). Esse valor de ID de SSO também deve ser mapeado para o valor de identificador de usuário do Azure AD. Altere essa configuração padrão na guia **Atributo**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que o usuário Brenda Fernandes use o SSO do Azure concedendo acesso ao IBM Kenexa Survey Enterprise.

![Atribuir a função de usuário][200] 

Para atribuir o usuário Brenda Fernandes ao IBM Kenexa Survey Enterprise, faça o seguinte:

1. No portal do Azure, abra a exibição **Aplicativos**, acesse a exibição **Diretório**, selecione **Aplicativos empresariais** e, depois, clique em **Todos os aplicativos**.

    ![Os links “Aplicativos empresariais” e “Todos os aplicativos”][201] 

2. Na lista **Aplicativos**, selecione **IBM Kenexa Survey Enterprise**.

    ![O link do IBM Kenexa Survey Enterprise na lista de Aplicativos](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_app.png) 

3. No painel esquerdo, clique em **Usuários e grupos**.

    ![O link “Usuários e grupos”][202] 

4. Clique no botão **Adicionar** e, em seguida, no painel **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![O painel Adicionar Atribuição][203]

5. Na caixa de diálogo **Usuários e grupos**, na lista **Usuários**, selecione **Brenda Fernandes**.

6. Na caixa de diálogo **Usuários e grupos**, clique no botão **Selecionar**.

7. Na caixa de diálogo **Adicionar Atribuição**, clique no botão **Atribuir**.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de SSO do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco do **IBM Kenexa Survey Enterprise** no Painel de Acesso, deverá ser conectado automaticamente ao aplicativo IBM Kenexa Survey Enterprise.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS ao Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_203.png

 
