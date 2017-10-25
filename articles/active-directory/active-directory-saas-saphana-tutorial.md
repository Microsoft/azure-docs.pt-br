---
title: "Tutorial: integração do Azure Active Directory com o SAP HANA | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o SAP HANA."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: cef4a146-f4b0-4e94-82de-f5227a4b462c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: a7e73f6ee763d1005ad85935cf2d8f6b24ecf116
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Tutorial: integração do Azure Active Directory com o SAP HANA

Neste tutorial, você aprenderá a integrar o SAP HANA ao Azure AD (Azure Active Directory).

A integração do SAP HANA ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao SAP HANA
- Você pode permitir que os usuários façam logon automaticamente no SAP HANA (logon único) com as respectivas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao SAP HANA, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do SAP HANA habilitada para logon único
- Uma instância do HANA em execução, seja em qualquer IaaS público, local, em VMs do Azure ou em grandes instâncias SAP no Azure
- A Interface da Web de Administração do XSA, bem como o HANA Studio instalado na instância do HANA.

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção do SAP HANA. Teste a integração primeiro no ambiente de desenvolvimento ou de preparo do aplicativo e, em seguida, use o ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o SAP HANA da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-sap-hana-from-the-gallery"></a>Adicionar o SAP HANA da galeria
Para configurar a integração do SAP HANA ao Azure AD, você precisará adicionar o SAP HANA da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o SAP HANA da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **SAP HANA**, selecione **SAP HANA** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo. 

    ![O novo aplicativo](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o SAP HANA, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do SAP HANA é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no SAP HANA.

No SAP HANA, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o SAP HANA, você precisará concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste do SAP HANA](#creating-a-sap-hana-test-user)** – para ter um equivalente de Brenda Fernandes no SAP HANA vinculado à representação do usuário no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo SAP HANA.

**Para configurar o logon único do Azure AD com o SAP HANA, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **SAP HANA**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_samlbase.png)

3. Na seção **Domínio e URLs do SAP HANA**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_url.png)

    a. Na caixa de texto **Identificador**, digite como: `HA100` 

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Contate a [equipe de suporte ao cliente do SAP HANA](https://cloudplatform.sap.com/contact.html) para obter esses valores. 

4. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_certificate.png) 

    >[!Note]
    >Se o certificado não está ativo, ative-o clicando na caixa de seleção "Tornar o novo certificado ativo" no Azure AD. 

5. O aplicativo SAP HANA espera que as declarações SAML estejam em um formato específico. A captura de tela a seguir mostra um exemplo disso. Aqui nós mapeamos o **Identificador de Usuário** com a função **ExtractMailPrefix()** função de **user.mail**. Isso retorna o valor de prefixo de email do usuário que é a ID de usuário único. Isto é enviado para o aplicativo SAP HANA em cada resposta bem-sucedida.

    ![Configurar Logon Único](./media/active-directory-saas-saphana-tutorial/attribute.png)

6. Na seção **Atributos de Usuário**, na caixa de diálogo **Logon único**:

    a. Na lista suspensa **Identificador de Usuário**, selecione **ExtractMailPrefix**.
    
    b. Na lista suspensa **Email**, selecione **user.mail**.

7. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-saphana-tutorial/tutorial_general_400.png)
    
8. Para configurar o logon único no lado do **SAP HANA**, faça logon no seu **Console Web XSA do HANA** navegando até o respectivo ponto de extremidade HTTPS.

    > [!Note]
    > Na configuração padrão, a URL redireciona a solicitação para uma tela de logon, o que exige as credenciais de um usuário de banco de dados SAP HANA autenticado para concluir o processo de logon. O usuário que fizer logon deverá ter os privilégios necessários para executar tarefas de administração de SAML.

9. Na Interface da Web XSA, navegue até **Provedor de identidade SAML** e clique no botão **"+"** na parte inferior da tela para exibir o painel Adicionar Informações de Provedor de Identidade e execute as seguintes etapas:

    ![Adicionar Provedor de Identidade](./media/active-directory-saas-saphana-tutorial/sap1.png)

    a. No painel **Adicionar Informações do Provedor de Identidade**, cole o conteúdo do XML de metadados que você baixou do Portal do Azure na caixa de texto **Metadados**.

    ![Configurações para Adicionar Provedor de Identidade](./media/active-directory-saas-saphana-tutorial/sap2.png)

    b. Se o conteúdo do documento XML é válido, o processo de análise extrai as informações necessárias para inserir os campos **Assunto, ID de Entidade e Emissor** na área Dados Gerais da tela e os campos de URL na área Destino da tela, por exemplo, **URL Base e a URL SingleSignOn (*)**.

    ![Configurações para Adicionar Provedor de Identidade](./media/active-directory-saas-saphana-tutorial/sap3.png)

    c. Na caixa Nome da área Dados Gerais da tela, insira um nome para o novo provedor de identidade SSO de SAML.

    > [!Note]
    > O nome do IdP de SAML é obrigatório e deve ser exclusivo. Ele aparecerá na lista de IdPs de SAML disponíveis que é exibida, se você selecionar SAML como o método de autenticação para aplicativos XS do SAP HANA a usar, por exemplo, na área de Autenticação da tela da ferramenta de Administração de Artefato XS.

10. Salve os detalhes do novo provedor de identidade SAML. Escolha **Salvar** para salvar os detalhes do provedor de identidade SAML e adicione o novo IdP de SAML à lista de IdPs de SAML conhecidos.

    ![botão salvar](./media/active-directory-saas-saphana-tutorial/sap4.png)

11. No HANA Studio, dentro das propriedades do sistema da guia **Configuração**, apenas filtre as configurações por **saml** e ajuste o **assertion_timeout** de **10 s** para **120 s**.

    ![configuração assertion_timeout](./media/active-directory-saas-saphana-tutorial/sap7.png)

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-saphana-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-saphana-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![O botão Adicionar](./media/active-directory-saas-saphana-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![A caixa de diálogo Usuário](./media/active-directory-saas-saphana-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-sap-hana-test-user"></a>Criação de um usuário de teste SAP HANA

Para permitir que os usuários do Azure AD façam logon no SAP HANA, eles devem ser provisionados no SAP HANA.
O SAP HANA dá suporte ao provisionamento just-in-time, que está habilitado por padrão.

Se você precisar criar um usuário manualmente, execute as seguintes etapas:

>[!Note]
>Você pode alterar a autenticação externa usada pelo usuário.
Os usuários externos são autenticados usando um sistema externo, por exemplo, um sistema Kerberos. Para obter informações detalhadas sobre identidades externas, contate seu [administrador de domínio](https://cloudplatform.sap.com/contact.html).

1. Abra o [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) como administrador e habilite o usuário do BD para o SSO do SAML.

    ![criar usuário](./media/active-directory-saas-saphana-tutorial/sap5.png)

2. Marque a caixa de seleção invisível à esquerda do **SAML** e siga o link Configurar.

3. Clique em **Adicionar** para adicionar o IdP de SAML e clique em **OK** selecionando o IdP de SAML apropriado.

4. Adicione a **Identidade Externa** (por ex., BrendaFernandes aqui) ou escolha **"Qualquer"** e clique em **OK**.

    >[!Note]
    >Se a caixa de seleção "QUALQUER" não estiver marcada, o nome de usuário no HANA deverá corresponder exatamente ao nome do usuário no nome UPN antes do sufixo de domínio (ou seja, BrittaSimon@contoso.com se tornaria BrendaFernandes no HANA).

5. Para fins de teste, atribua todas as funções **"XS"** ao usuário.

    ![atribuição de funções](./media/active-directory-saas-saphana-tutorial/sap6.png)

    > [!TIP]
    > Você deve conceder apenas as permissões apropriadas para seus casos de uso.

6. Salve o usuário.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao SAP HANA.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao SAP HANA, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **SAP HANA**.

    ![Atribuir usuário](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do SAP HANA no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo SAP HANA.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_203.png

