---
title: "Tutorial: Integração do Azure Active Directory ao iLMS | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o iLMS."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2017
ms.author: jeedes
ms.openlocfilehash: 22c72020200138e78835ed7dd2661f18b824c785
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-ilms"></a>Tutorial: Integração do Azure Active Directory ao iLMS

Neste tutorial, você aprende a integrar o iLMS ao Azure AD (Azure Active Directory).

A integração do iLMS ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao iLMS
- Você pode permitir que os usuários sejam automaticamente conectados ao iLMS (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao iLMS, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do iLMS habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o iLMS por meio da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-ilms-from-the-gallery"></a>Adicionando o iLMS por meio da galeria
Para configurar a integração do iLMS ao Azure AD, você precisa adicionar o iLMS à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o iLMS por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Para adicionar o novo aplicativo, clique no botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **iLMS**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_search.png)

5. No painel de resultados, selecione **iLMS** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configura e testa o logon único do Azure AD com o iLMS, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do iLMS é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do iLMS.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD ao valor de **Nome de Usuário** no iLMS.

Para configurar e testar o logon único do Azure AD com o iLMS, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste do iLMS](#creating-an-ilms-test-user)** – para ter um equivalente de Brenda Fernandes no iLMS que está vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo iLMS.

**Para configurar o logon único do Azure AD com o iLMS, realize as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **iLMS**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_samlbase.png)

3. Na seção **Domínio e URLs do iLMS**, realize as seguintes etapas se desejar configurar o aplicativo no modo iniciado pelo **IdP**:

    ![Configurar o logon único](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_url.png)

    a. Na caixa de texto **Identificador**, cole o valor do **Identificador** copiado da seção **Provedor de Serviços** das configurações do SAML no portal de administração do iLMS.

    b. Na caixa de texto **URL de Resposta**, cole o valor da **(URL) do Ponto de Extremidade** copiado da seção **Provedor de Serviços** das configurações do SAML no portal de administração do iLMS com o seguinte padrão `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

    >[!Note]
    >“123456” é um valor de exemplo de identificador.

4. Marque **Mostrar configurações avançadas de URL**, se quiser configurar o aplicativo no modo iniciado em **SP**:

    ![Configurar o logon único](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_url1.png)

    Na caixa de texto **URL de Logon**, cole o valor da **(URL) do Ponto de Extremidade** copiado da seção **Provedor de Serviços** das configurações do SAML no portal de administração do iLMS como `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`     

5. Para habilitar o provisionamento JIT, o aplicativo iLMS espera as declarações SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. A captura de tela a seguir mostra um exemplo disso.
    
    ![Configurar Logon Único](./media/active-directory-saas-ilms-tutorial/4.png)
    
    Crie os atributos **Department, Region** e **Division** e adicione o nome desses atributos no iLMS. Todos esses atributos mostrados acima são necessários.  

    > [!NOTE] 
    > Você precisa habilitar a opção **Criar Conta de Usuário Não Reconhecido** no iLMS para mapear esses atributos. Siga as instruções [aqui](http://support.inspiredelearning.com/customer/portal/articles/2204526) para ter uma ideia sobre a configuração de atributos.

6. Na seção **Atributos do usuário**, na caixa de diálogo **Logon único**, configure o atributo do token SAML na imagem acima e siga as etapas abaixo:
    
    | Nome do atributo | Valor do atributo |
    | ---------------| --------------- |    
    | division | user.department |
    | region | user.state |
    | department | user.jobtitle |

    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar o logon único](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_04.png)

    ![Configurar Logon Único](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_05.png)
    
    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.
    
    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.
    
    d. Clique em **Ok**

7. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo XML em seu computador.

    ![Configurar Logon Único](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_certificate.png) 

8. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-iLMS-tutorial/tutorial_general_400.png)

9. Em outra janela do navegador da Web, faça logon no **portal de administração do iLMS** como administrador.

10. Clique em **SSO:SAML** na guia **Configurações** para abrir as configurações do SAML e realize as seguintes etapas:
    
    ![Configurar Logon Único](./media/active-directory-saas-ilms-tutorial/1.png) 

    a. Expanda a seção **Provedor de Serviços** e copie o valor do **Identificador** e da **(URL) do Ponto de Extremidade**.

    ![Configurar o logon único](./media/active-directory-saas-ilms-tutorial/2.png) 

    b. Na seção **Provedor de Identidade**, clique em **Importar Metadados**.
    
    c. Selecione o arquivo de **Metadados** baixado no Portal do Azure na seção **Certificado de Autenticação do SAML**.

    ![Configurar Logon Único](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_ssoconfig1.png) 

    d. Se você desejar habilitar o provisionamento JIT para criar contas do iLMS para cancelar o reconhecimento de usuários, realize as seguintes etapas:
        
       - Marque a opção **Criar Conta de Usuário Não Reconhecido**.
       
       ![Configurar o logon único](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_ssoconfig2.png)

       -  Mapeie os atributos no Azure AD com os atributos no iLMS. Na coluna de atributos, especifique o nome dos atributos ou o valor padrão.

    e. Acesse a guia **Regras de Negócios** e realize as seguintes etapas: 
        
       ![Configurar Logon Único](./media/active-directory-saas-ilms-tutorial/5.png)

       - Marque a opção **Criar Regiões, Divisões e Departamentos Não Reconhecidos** para criar Regiões, Divisões e Departamentos que ainda não existem no momento do Logon Único.
        
       - Marque a opção **Atualizar Perfil de Usuário Durante a Conexão** para especificar se o perfil do usuário é atualizado a cada Logon Único. 
        
       - Se a opção **“Atualizar Valores em Branco para Campos Não Obrigatórios no Perfil de Usuário”** estiver marcada, os campos de perfil opcionais que estiverem em branco após a conexão também farão com que o perfil do iLMS do usuário contenha valores em branco nesses campos.
        
       - Marque a opção **Enviar Email de Notificação de Erro** e insira o email do usuário em que você deseja receber o email de notificação de erro.

11. Clique no botão **Salvar** para salvar as alterações.

    ![Configurar Logon Único](./media/active-directory-saas-ilms-tutorial/save.png)

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
    
### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-ilms-tutorial/create_aaduser_01.png) 

2. Vá para **usuários e grupos** e clique em **todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-ilms-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo clique **adicionar** para abrir o **usuário** caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-ilms-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-ilms-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-an-ilms-test-user"></a>Criando um usuário de teste do iLMS

O aplicativo dá suporte ao provisionamento de usuário Just-In-Time e, após a autenticação, os usuários são criados no aplicativo automaticamente. O JIT funcionará se você tiver clicado na caixa de seleção **Criar Conta de Usuário Não Reconhecido** durante a definição da configuração do SAML no portal de administração do iLMS.

Se precisar criar um usuário manualmente, siga as etapas abaixo:

1. Faça logon no site da empresa do iLMS como administrador.

2. Clique em **“Registrar Usuário”** na guia **Usuários** para abrir a página **Registrar Usuário**. 
   
   ![Adicionar Funcionário](./media/active-directory-saas-ilms-tutorial/3.png)

3. Na página **“Registrar Usuário”**, realize as etapas a seguir.

    ![Adicionar Funcionário](./media/active-directory-saas-ilms-tutorial/create_testuser_add.png)

    a. Na caixa de texto **Nome**, digite o nome Brenda.
   
    b. Na caixa de texto **Sobrenome**, digite o sobrenome Fernandes.

    c. Na caixa de texto **ID de Email**, digite o endereço de email da conta de Brenda Fernandes.

    d. Na lista suspensa **Região**, selecione o valor da região.

    e. Na lista suspensa **Divisão**, selecione o valor da divisão.

    f. Na lista suspensa **Departamento**, selecione o valor do departamento.

    g. Clique em **Salvar**.

    > [!NOTE] 
    > Você pode enviar o email de registro para o usuário selecionando a caixa de seleção **Enviar Email de Registro**.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao iLMS.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao iLMS, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **iLMS**.

    ![Configurar o logon único](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco iLMS no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo iLMS.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_203.png

