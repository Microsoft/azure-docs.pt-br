---
title: "Tutorial: integração do Azure Active Directory ao SAP Business Object Cloud | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o SAP Business Object Cloud."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.openlocfilehash: 6d517c5e302ac36e5bba2053998c75f8f4d42683
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>Tutorial: integração do Azure Active Directory ao SAP Business Object Cloud

Neste tutorial, você aprenderá a integrar o SAP Business Object Cloud ao Azure AD (Azure Active Directory).

Você obtém os seguintes benefícios quando integra o SAP Business Object Cloud ao Azure AD:

- No Azure AD, você pode controlar quem tem acesso ao SAP Business Object Cloud.
- Você pode conectar automaticamente seus usuários ao SAP Business Object Cloud usando o logon único e a conta do Azure AD de um usuário.
- É possível gerenciar suas contas em uma, um local central e no portal do Azure.

Para saber mais sobre a integração de aplicativos de SaaS (software como serviço) ao Azure AD, confira [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao SAP Business Object Cloud, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Um SAP Business Object Cloud, com logon único habilitado

> [!NOTE]
> Em caso de testar as etapas deste tutorial, recomendamos não testá-las em um ambiente de produção.

Recomendações para testar as etapas deste tutorial:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você poderá [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. 

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o SAP Business Object Cloud da galeria.
2. Configurar e testar o logon único do Azure AD.

## <a name="add-sap-business-object-cloud-from-the-gallery"></a>Adicionar o SAP Business Object Cloud da galeria
Para configurar a integração do SAP Business Object Cloud ao Azure AD, na galeria, adicione o SAP Business Object Cloud à sua lista de aplicativos SaaS gerenciados.

Para adicionar o SAP Business Object Cloud da galeria:

1. No [portal do Azure](https://portal.azure.com), no menu esquerdo, selecione **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.

    ![A página de aplicativos empresariais][2]
    
3. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, insira **SAP Business Object Cloud**.

    ![A caixa de pesquisa](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_search.png)

5. No painel de resultados, selecione **SAP Business Object Cloud** e o botão **Adicionar**.

    ![SAP Business Object Cloud na lista de resultados](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_addfromgallery.png)

##  <a name="set-up-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você vai configurar e testar o logon único do Azure AD com o SAP Business Object Cloud, com base em uma usuária de teste chamada *Brenda Fernandes*.

Para que o logon único funcione, o Azure AD precisa conhecer o usuário equivalente do Azure AD no SAP Business Object Cloud. É necessário estabelecer uma relação de vinculação entre um usuário do Azure AD e o usuário relacionado no SAP Business Object Cloud.

Para estabelecer a relação de vinculação, no SAP Business Object Cloud, para **Nome de usuário**, atribua o valor de **nome de usuário** no Azure AD.

Para configurar e testar o logon único do Azure AD com o SAP Business Object Cloud, complete as seguintes tarefas:

1. [Configurar o logon único do Azure AD](#set-up-azure-ad-single-sign-on). Configura um usuário para usar esse recurso.
2. [Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user). Testa o logon único do Azure AD com a usuária Brenda Fernandes.
3. [Criar um usuário de teste do SAP Business Object Cloud](#create-an-sap-business-object-cloud-test-user). Cria um equivalente de Brenda Fernandes no SAP Business Object Cloud que é vinculado à representação do usuário no Azure AD.
4. [Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user). Configura Brenda Fernandes para usar o logon único do Azure AD.
5. [Testar o logon único](#test-single-sign-on). Verifica se a configuração funciona.

### <a name="set-up-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você ativa o logon único do Azure AD no portal do Azure. Em seguida, configura o logon único no seu aplicativo SAP Business Object Cloud.

Para configurar o logon único do Azure AD com o SAP Business Object Cloud:

1. No portal do Azure, na página de integração do aplicativo do **SAP Business Object Cloud**, selecione **Logon único**.

    ![Selecionar Logon Único][4]

2. Na página **Logon único**, para **Modo**, selecione **Logon com base em SAML**.
 
    ![Selecionar Logon com base em SAML](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_samlbase.png)

3. Em **Domínio e URLs do SAP Business Object Cloud**, conclua as seguintes etapas:

    1. Na caixa **URL de Entrada**, digite uma URL com o seguinte padrão: 
    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    2. Na caixa **Identificador**, digite uma URL com o seguinte padrão:
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    ![URLs da página Domínio e URLs do SAP Business Object Cloud](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_url.png)
 
    > [!NOTE] 
    > Os valores nessas URLs são apenas para demonstração. Atualize os valores com a verdadeira URL de entrada e a URL do identificador. Para obter a URL de entrada, entre em contato com a [equipe de Suporte ao cliente do SAP Business Object Cloud](https://www.sap.com/product/analytics/cloud-analytics.support.html). Você pode obter a URL do identificador ao baixar os metadados do SAP Business Object Cloud do console do administrador. Isso é explicado mais adiante no tutorial. 

4. Em **Certificado de Autenticação SAML**, selecione **XML de Metadados**. Em seguida, salve o arquivo de metadados no computador.

    ![Selecionar XML de Metadados](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_certificate.png) 

5. Selecione **Salvar**.

    ![Selecionar Salvar](./media/active-directory-saas-sapboc-tutorial/tutorial_general_400.png)

6. Em outra janela do navegador da Web, entre no site da empresa do seu SAP Business Object Cloud como um administrador.

7. Selecione **Menu** > **Sistema** > **Administração**.
    
    ![Selecionar Menu, Sistema e Administração](./media/active-directory-saas-sapboc-tutorial/config1.png)

8. Na guia **Segurança**, selecione o ícone **Editar** (caneta).
    
    ![Na guia Segurança, selecionar o ícone Editar](./media/active-directory-saas-sapboc-tutorial/config2.png)  

9. Para **Método de Autenticação**, selecione **SSO (Logon Único) do SAML**.

    ![Selecionar Logon Único do SAML para o método de autenticação](./media/active-directory-saas-sapboc-tutorial/config3.png)  

10. Para baixar os metadados do provedor de serviços (Etapa 1), selecione **Baixar**. No arquivo de metadados, localize e copie o valor **entityID**. No portal do Azure, em **Domínio e URLs do SAP Business Object Cloud**, cole o valor na caixa **Identificador**.

    ![Copiar e colar o valor de entityID](./media/active-directory-saas-sapboc-tutorial/config4.png)  

11. Para fazer upload dos metadados do provedor de serviços (Etapa 2) no arquivo que você baixou do portal do Azure, em **Fazer upload dos metadados do Provedor de Identidade**, selecione **Fazer Upload**.  

    ![Em Fazer upload dos metadados do Provedor de Identidade, selecionar Fazer Upload](./media/active-directory-saas-sapboc-tutorial/config5.png)

12. Na lista **Atributo de Usuário**, selecione o atributo de usuário (Etapa 3) que você quer usar na sua implementação. Esse atributo de usuário é mapeado para o provedor de identidade. Para inserir um atributo personalizado na página do usuário, use a opção **Mapeamento de SAML Personalizado**. Ou você pode selecionar o **Email** ou a **ID DE USUÁRIO** como o atributo de usuário. Em nosso exemplo, selecionamos **Email** porque mapeamos a declaração da identificação de usuário com o atributo **userprincipalname** na seção **Atributos de Usuário** do portal do Azure. Isso fornece um email de usuário exclusivo, que é enviado ao aplicativo SAP Business Object Cloud em cada resposta bem-sucedida de SAML.

    ![Selecionar Atributo de Usuário](./media/active-directory-saas-sapboc-tutorial/config6.png)

13. Para verificar a conta com o provedor de identidade (Etapa 4), na caixa **Credencial de Logon (Email)**, insira o endereço de email do usuário. Em seguida, selecione **Verificar Conta**. O sistema adiciona credenciais de entrada à conta de usuário.

    ![Inserir email e selecionar Verificar Conta](./media/active-directory-saas-sapboc-tutorial/config7.png)

14. Selecione o ícone **Salvar**.

    ![Ícone Salvar](./media/active-directory-saas-sapboc-tutorial/save.png)

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo! Depois de adicionar o aplicativo selecionando **Active Directory** > **Aplicativos Empresariais**, selecione a guia **Logon Único**. Você pode acessar a documentação inserida na seção **Configuração**, na parte inferior da página. Para saber mais, confira a [documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
Nesta seção, você criará uma usuária de teste no portal do Azure chamada Brenda Fernandes.

Para criar um usuário de teste no Azure AD:

1. No portal do Azure, no menu esquerdo, selecione **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sapboc-tutorial/create_aaduser_01.png) 

2. Para exibir a lista de usuários, selecione **Usuários e grupos** e, em seguida, selecione **Todos os usuários**.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sapboc-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, selecione **Adicionar**.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sapboc-tutorial/create_aaduser_03.png) 

4. Na caixa de diálogo **Usuário**, conclua as seguintes etapas:
 
    1. Na caixa **Nome**, insira **BrendaFernandes**.

    2. Na caixa **Nome de usuário**, insira o endereço de email da usuária Brenda Fernandes.

    3. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    4. Selecione **Criar**.

        ![A caixa de diálogo Usuário](./media/active-directory-saas-sapboc-tutorial/create_aaduser_04.png) 

    ![Criar um usuário do AD do Azure][100]

### <a name="create-an-sap-business-object-cloud-test-user"></a>Criar um usuário de teste do SAP Business Object Cloud

Os usuários do Azure AD devem ser provisionados no SAP Business Object Cloud para que possam entrar no SAP Business Object Cloud. No SAP Business Object Cloud, o provisionamento é uma tarefa manual.

Para provisionar uma conta de usuário:

1. Entre no site da empresa do seu SAP Business Object Cloud como administrador.

2. Selecione **Menu** > **Segurança** > **Usuários**.

    ![Adicionar Funcionário](./media/active-directory-saas-sapboc-tutorial/user1.png)

3. Na página **Usuários**, para adicionar detalhes do novo usuário, selecione **+**. 

    ![Página Adicionar Usuários](./media/active-directory-saas-sapboc-tutorial/user4.png)

    Em seguida, conclua as seguintes etapas:

    1. Na caixa **ID DE USUÁRIO**, insira a ID do usuário, como **Brenda**.

    2. Na caixa **NOME**, insira o nome do usuário, como **Brenda**.

    3. Na caixa **SOBRENOME**, insira o sobrenome do usuário, como **Fernandes**.

    4. Na caixa **NOME DE EXIBIÇÃO**, insira o nome completo do usuário, como **Brenda Fernandes**.

    5. Na caixa **EMAIL**, insira o endereço de email do usuário, como **brittasimon@contoso.com**.

    6. Na página **Selecionar Funções**, selecione a função apropriada para o usuário e selecione **OK**.

      ![Escolher função](./media/active-directory-saas-sapboc-tutorial/user3.png)

    7. Selecione o ícone **Salvar**.    


### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que a usuária Brenda Fernandes use o logon único do Azure AD concedendo acesso de conta de usuário ao SAP Business Object Cloud.

Para atribuir Brenda Fernandes ao SAP Business Object Cloud:

1. No portal do Azure, abra a exibição de aplicativos e vá para a exibição de diretório. Selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **SAP Business Object Cloud**.

    ![Configurar Logon Único](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_app.png) 

3. No menu esquerdo, selecione **Usuários e grupos**.

    ![Selecionar Usuários e grupos][202] 

4. Selecione **Adicionar**. Na página **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![A página Adicionar Atribuição][203]

5. Na página **Usuários e grupos**, na lista de usuários, selecione **Brenda Fernandes**.

6. Na página **Usuários e grupos**, selecione **Selecionar**.

7. Na página **Adicionar Atribuição**, selecione **Atribuir**.

![Atribuir a função de usuário][200] 
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao selecionar o bloco do SAP Business Object Cloud no painel de acesso, você deve ser conectado automaticamente ao aplicativo SAP Business Object Cloud.

Para saber mais sobre o painel de acesso, veja [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS ao Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_203.png

