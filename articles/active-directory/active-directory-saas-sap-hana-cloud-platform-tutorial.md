---
title: "Tutorial: Integração do Azure Active Directory com a SAP Cloud Platform | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o SAP Cloud Platform."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bd398225-8bd8-4697-9a44-af6e6679113a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: 6b4b0e3ab126e70e4b27b971b15127aae0486bed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Tutorial: Integração do Active Directory do Azure com a SAP Cloud Platform

Neste tutorial, você aprende a integrar a SAP Cloud Platform ao Azure Active Directory (Azure AD).

A integração da SAP Cloud Platform ao Azure AD oferece os seguintes benefícios:

- No Azure AD, você pode controlar quem tem acesso à SAP Cloud Platform.
- É possível permitir que os usuários se conectem automaticamente à SAP Cloud Platform (Logon Único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD à SAP Cloud Platform, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único da SAP Cloud Platform

Depois de concluir este tutorial, os usuários do Azure AD que você atribuiu à SAP Cloud Platform poderão fazer logon único no aplicativo usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

>[!IMPORTANT]
>Você precisa implantar seu próprio aplicativo ou assinar um aplicativo em sua conta da SAP Cloud Platform para testar o logon único. Neste tutorial, um aplicativo é implantado na conta.
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar a SAP Cloud Platform por meio da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>Adicionar a SAP Cloud Platform por meio da galeria
Para configurar a integração da SAP Cloud Platform ao Azure AD, você precisa adicionar a SAP Cloud Platform à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar a SAP Cloud Platform por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **SAP Cloud Platform**, selecione **SAP Cloud Platform** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![SAP Cloud Platform na lista de resultados](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com a SAP Cloud Platform, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário da SAP Cloud Platform é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado da SAP Cloud Platform.

Na SAP Cloud Platform, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com a SAP Cloud Platform, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste da SAP Cloud Platform](#create-a-sap-cloud-platform-test-user)** – para ter um equivalente de Brenda Fernandes no SAP Cloud Platform que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo SAP Cloud Platform.

**Para configurar o logon único do Azure AD com a SAP Cloud Platform, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo do **SAP Cloud Platform**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_samlbase.png)

3. Na seção **Domínio e URLs da SAP Cloud Platform**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs da SAP Cloud Platform](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_url.png)

    a. Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para entrar no aplicativo **SAP Cloud Platform**. Esta é a URL específica da conta de um recurso protegido em seu aplicativo SAP Cloud Platform. A URL é baseada no seguinte padrão: `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
     >[!NOTE]
     >Esta é a URL em seu aplicativo SAP Cloud Platform que exige que o usuário seja autenticado.
     > 

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |

    b. Na caixa de texto **Identificador** que você irá fornecer à sua SAP Cloud Platform digite uma URL usando um dos seguintes padrões: 

    | |
    |--|
    | `https://hanatrial.ondemand.com/<instancename>` |
    | `https://hana.ondemand.com/<instancename>` |
    | `https://us1.hana.ondemand.com/<instancename>` |
    | `https://ap1.hana.ondemand.com/<instancename>` |

    c. Na caixa de texto **URL de resposta** , digite uma URL no seguinte padrão:

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>` |

    > [!NOTE] 
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Entre em contato com [equipe de suporte do cliente da SAP Cloud Platform](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) para obter a URL de logon e o Identificador. A URL de resposta você pode obter da seção de gerenciamento de confiança que é explicada no tutorial posteriormente.
    > 
     
4. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_400.png)

6. Em uma janela diferente do navegador, inicie uma sessão na ferramenta cockpit da SAP Cloud Platform em `https://account.<landscape host>.ondemand.com/cockpit`(por exemplo: https://account.hanatrial.ondemand.com/cockpit).

7. Clique na guia **Confiar** .
   
    ![Confiança](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790800.png "Confiança")

8. Na seção Gerenciamento de confiança, em **Provedor de serviços local**, execute as seguintes etapas:

    ![Gerenciamento de Confiança](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic793931.png "Gerenciamento de Confiança")
   
    a. Clique em **Editar**.

    b. Para **Tipo de Configuração**, selecione **Personalizado**.

    c. Para **Nome do Provedor Local**, deixe o valor padrão. Copie esse valor e cole-o no campo **Identificador** na configuração do Azure AD para a SAP Cloud Platform.

    d. Para gerar um par de chaves **Chave de Assinatura** e um **Certificado de Assinatura**, clique em **Gerar Par de Chaves**.

    e. Para **Propagação de Entidade**, selecione **Desabilitado**.

    f. Para **Forçar Autenticação**, selecione **Desabilitado**.

    g. Clique em **Salvar**.

9. Depois de salvar as configurações do **Provedor de serviços Llocal**, faça o seguinte para obter a URL de resposta:
   
    ![Obter Metadados](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic793930.png "Obter Metadados")

    a. Baixe o arquivo de metadados da SAP Cloud Platform, clicando em **Obter metadados**.

    b. Abra o arquivo XML de metadados da SAP Cloud Platform baixado e localize a marca **ns3:AssertionConsumerService** .
 
    c. Copie o valor do atributo **Location** e cole-o no campo**URL de Resposta** na configuração do Azure AD da SAP Cloud Platform.

10. Clique na guia **Provedor de Identidade Confiável** e em **Adicionar Provedor de Identidade Confiável**.
   
    ![Gerenciamento de Confiança](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790802.png "Gerenciamento de Confiança")
   
    >[!NOTE]
    >Para gerenciar a lista de provedores de identidade confiáveis, será necessário ter escolhido o Tipo de configuração personalizado na seção Provedor de Serviço Local. Para o tipo de configuração Padrão, você terá uma confiança implícita e não editável para o Serviço de ID do SAP. Para Nenhum, não há configurações de confiança.
    > 
    > 

11. Clique na guia **Geral** e em **Procurar** para carregar o arquivo de metadados baixado.
    
    ![Gerenciamento de Confiança](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic793932.png "Gerenciamento de Confiança")
    
    >[!NOTE]
    >Depois de carregar o arquivo de metadados, os valores de **URL de Logon Único**, **URL de Logoff Único** e o **Certificado de Autenticação** serão automaticamente populados.
    > 
     
12. Clique na guia **Atributos** .

13. Na guia **Atributos**, execute a seguinte etapa:
    
    ![Atributos](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790804.png "Atributos") 

    a. Clique em **Adicionar Atributo Baseado em Declaração** e adicione os seguintes atributos baseados em declaração:
       
    | Atributo de Asserção | Atributo de Entidade |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |nome |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |sobrenome |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |email |
   
     >[!NOTE]
     >A configuração dos Atributos depende de como os aplicativos na HCP são desenvolvidos, isto é, quais atributos eles esperam ter na resposta do SAML e por qual nome (Atributo de Entidade de Segurança) eles acessam esse atributo no código.
     > 
    
    b. O **Atributo Padrão** na captura de tela serve apenas para fins de ilustração. Ele não é necessário para que o cenário funcione.  
 
    c. Os nomes e valores do **Atributo de Entidade** mostrados na captura de tela dependerão de como o aplicativo foi desenvolvido. É possível que o seu aplicativo precise de mapeamentos diferentes.

###<a name="assertion-based-groups"></a>Grupos baseados em asserção

Como uma etapa opcional, você pode configurar grupos com base na asserção para seu Provedor de Identidade do Azure Active Directory.

Usar grupos na SAP Cloud Platform permite que você atribua dinamicamente um ou mais usuários a uma ou mais funções em seus aplicativos da SAP Cloud Platform, determinados pelos valores de atributos na asserção SAML 2.0. 

Por exemplo, se a declaração contém o atributo "*contract=temporary*", talvez seja conveniente que todos os usuários afetados sejam adicionados ao grupo "*TEMPORARY*". O grupo “*TEMPORARY*” pode conter uma ou mais funções de um ou mais aplicativos implantados em sua conta da SAP Cloud Platform.
 
Use os grupos baseados em declarações se desejar atribuir vários usuários simultaneamente a uma ou mais funções de aplicativos em sua conta da SAP Cloud Platform. Se você quer atribuir apenas um único usuário ou alguns deles a funções específicas, recomendamos atribuí-los diretamente na guia "**Autorizações**" da ferramenta cockpit da SAP Cloud Platform.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-sap-cloud-platform-test-user"></a>Criar um usuário da SAP Cloud Platform

Para permitir que os usuários do Azure AD façam logon na SAP Cloud Platform, atribua funções a eles na SAP Cloud Platform.

**Para atribuir uma função a um usuário, execute as seguintes etapas:**

1. Faça logon em sua ferramenta cockpit da **SAP Cloud Platform** .

2. Realize o que é descrito a seguir:
   
    ![Autorizações](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790805.png "Autorizações")
   
    a. Clique em **Autorização**.

    b. Clique na guia **Usuários** .

    c. Na caixa de texto **Usuário** , digite o endereço de email do usuário.

    d. Clique em **Atribuir** para atribuir uma função ao usuário.

    e. Clique em **Salvar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso à SAP Cloud Platform.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes à SAP Cloud Platform, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **SAP Cloud Platform**.

    ![Link da SAP Cloud Platform na lista de Aplicativos](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco da SAP Cloud Platform no Painel de Acesso, você deve ser conectado automaticamente ao aplicativo SAP Cloud Platform.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_203.png

