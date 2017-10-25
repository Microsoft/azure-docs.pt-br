---
title: "Tutorial: integração do Azure Active Directory ao SuccessFactors | Microsoft Docs"
description: "Saiba como usar o SuccessFactors com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/21/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: e85a38ccbe25263ac42bc76351416b023fb77c87
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>Tutorial: Integração do Azure Active Directory com o SuccessFactors
O objetivo desse tutorial é mostrar como integrar o SuccessFactors ao Azure AD (Azure Active Directory).

A integração do SuccessFactors ao Azure AD oferece os seguintes benefícios:

* No Azure AD, você pode controlar quem tem acesso ao SuccessFactors
* Você pode habilitar seus usuários a fazerem logon automaticamente no SuccessFactors (Logon Único) com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD ao SuccessFactors, você precisará dos seguintes itens:

* Uma assinatura válida do Azure
* Um locatário no SuccessFactors

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
> 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do SuccessFactors da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-successfactors-from-the-gallery"></a>Adição do SuccessFactors da galeria
Para configurar a integração do SuccessFactors ao Azure AD, você precisará adicionar o SuccessFactors da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o SuccessFactors da galeria, execute as seguintes etapas:**

1. No portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Configurando o logon único][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Configurando o logon único][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Configurando o logon único][4]
6. Na **caixa de pesquisa**, digite **SuccessFactors**.
   
    ![Configurando o logon único][5]
7. No painel de resultados, selecione **SuccessFactors** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Configurando o logon único][6]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar logon único do Azure AD com o SuccessFactors, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do SuccessFactors é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SuccessFactors.

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD ao valor do **Nome de Usuário** no SuccessFactors.

Para configurar e testar o logon único do Azure AD com o SuccessFactors, é preciso concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do SuccessFactors](#creating-a-successfactors-test-user)** : para ter um equivalente de Brenda Fernandes no SuccessFactors que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD
Nesta seção, você habilitará o logon único do Azure AD no portal clássico e configurará o logon único em seu aplicativo SuccessFactors.

**Para configurar o logon único do Azure AD com o SuccessFactors, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração do aplicativo **SuccessFactors**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
    ![Configurando o logon único][7]
2. Na página **Como você deseja que os usuários façam logon no SuccessFactors**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
    ![Configurando o logon único][8]
3. Na página **Configurar URL do Aplicativo**, realize as etapas a seguir e clique em **Avançar**.
   
    ![Configurando o logon único][9]
   
    a. Na caixa de texto **URL de Entrada** , digite uma URL usando os seguintes padrões: 
   
    |  |
    | --- |
    | `https://<company name>.successfactors.com/<company name>` |
    | `https://<company name>.sapsf.com/<company name>` |
    | `https://<company name>.successfactors.eu/<company name>` |
    | `https://<company name>.sapsf.eu` |
   
    b. Na caixa de texto **URL de Resposta** , digite uma URL nos seguintes padrões: 
   
    |  |
    | --- |
    | `https://<company name>.successfactors.com/<company name>` |
    | `https://<company name>.sapsf.com/<company name>` |
    | `https://<company name>.successfactors.eu/<company name>` |
    | `https://<company name>.sapsf.eu` |
    | `https://<company name>.sapsf.eu/<company name>` |
   
    c. Clique em **Avançar**. 

    > [!NOTE]
    > Observe que esses não são os valores reais. Você precisa atualizar esses valores com a URL de Entrada e a URL de Resposta reais. Para obter esses valores, entre em contato com a [equipe de suporte do SuccessFactors](https://www.successfactors.com/en_us/support.html).

1. Na página **Configurar logon único no SuccessFactors**, clique em **Baixar certificado** e salve o arquivo de certificado no computador.
   
    ![Configurando o logon único][10]

2. Em uma janela de navegador da Web diferente, faça logon no site de sua empresa do **portal de administração do SuccessFactors** como administrador.

3. Visite **Segurança de Aplicativo** e nativo do **Recurso Logon Único**. 

4. Coloque qualquer valor em **Redefinir Token** e clique em **Salvar Token** para habilitar SSO do SAML.
   
    ![Configurar o logon único no lado do aplicativo][11]

    > [!NOTE] 
    > Esse valor é usado apenas como chave liga/desliga. Se nenhum valor for salvo, o SSO do SAML será ATIVADO. Se um valor em branco for salvo, o SSO do SAML será DESATIVADO.

1. Nativo da captura de tela abaixo e execute as ações a seguir.
   
    ![Configurar o logon único no lado do aplicativo][12]
   
    a. Selecione o botão de opção **SSO do SAML v2**
   
    b. Defina o Nome da Parte de Declaração SAML (por exemplo, emissor SAml + nome da empresa).
   
    c. Na caixa de texto **Emissor SAML**, coloque o valor de **URL do Emissor** do assistente de configuração de aplicativo do Azure AD.
   
    d. Selecione **Resposta(Gerada pelo Cliente/IdP/AP)** como **Exigir Assinatura Obrigatória**.
   
    e. Selecione **Habilitado** como **Habilitar Sinalizador SAML**.
   
    f. Selecione **Não** como **Assinatura da Solicitação de Logon (Gerado por SF/SP/RP)**.
   
    g. Selecione **Perfil de Navegador/Postagem** como **Perfil SAML**.
   
    h. Selecione **Não** como **Impor Período de Certificado Válido**.
   
    i. Copie o conteúdo do arquivo do certificado baixado e, então, cole-o na caixa de texto **Certificado de Verificação SAML** .

    > [!NOTE] 
    > O conteúdo do certificado deve ter começar marcas de certificado do certificado e de fim.

1. Navegue até SAML V2 e então execute as seguintes etapas:
   
    ![Configurar o logon único no lado do aplicativo][13]
   
    a. Selecione **Sim** como **Logoff Global iniciado por SP de suporte**.
   
    b. Na caixa de texto **URL de Serviço Logoff Global (destino de LogoutRequest)**, coloque o valor de **URL de Logoff Remoto** do assistente de configuração de aplicativo do Azure AD.
   
    c. Selecione **Não** como **Exigir que sp criptografe todos os elementos NameID**.
   
    d. Selecione **não especificado** como **Formato de NameID**.
   
    e. Selecione **Sim** como **Habilitar logon iniciado por sp (AuthnRequest)**.
   
    f. Na caixa de texto **Enviar solicitação como emissor para Toda a Empresa**, insira o valor de **URL de Logon Remoto** do assistente de configuração de aplicativo do Azure AD.
2. Execute estas etapas se quiser fazer com os nomes de usuário de logon diferenciem maiúsculas de minúsculas.
   
    a. Visite **Configurações da Empresa**(próximo à parte inferior).
   
    b. marque a caixa de seleção ao lado de **Habilitar Nome de Usuário que Não Diferencia Maiúsculas de Minúsculas**.
   
    c.Clique em **Salvar**.
   
    ![Configurar o logon único][29]

    > [!NOTE] 
    > Se você tentar habilitar essa opção, o sistema verificará se ele criará um nome de logon SAML duplicado. Por exemplo, se o cliente tiver os nomes de usuário Usuário1 e usuário1. Parar de diferenciar maiúsculas e minúsculas cria essas duplicatas. O sistema fornecerá a você uma mensagem de erro e não habilitará o recurso. O cliente precisará alterar um dos nomes de usuário para que ele realmente sela digitado diferentes. 

1. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
    ![Aplicativos][14]
2. Na página **Confirmação de logon único**, clique em **Concluir**.
   
    ![Aplicativos][15]

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][16]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure][17]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure][18]
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure][19]
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure][20]
   
    a. Em Tipo de Usuário, selecione Novo usuário na organização.
   
    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    c. Clique em **Avançar**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure][21]
   
    a. Na caixa de texto **Nome**, digite **Brenda**.  
   
    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
    c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   
    d. Na lista **Função**, selecione **Usuário**.
   
    e. Clique em **Avançar**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure][22]
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure][23]
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.  

### <a name="creating-a-successfactors-test-user"></a>Criação de um usuário de teste do SuccessFactors
Para permitir que os usuários do AD do Azure façam logon no SuccessFactors, eles deverão ser provisionados no SuccessFactors.  
No caso do SuccessFactors, o provisionamento será uma tarefa manual.

Para obter os usuários criados no SuccessFactors, você precisará entrar em contato com a [equipe de suporte do SuccessFactors](https://www.successfactors.com/en_us/support.html).

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure
O objetivo desta seção é permitir que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao SuccessFactors.

![Atribuir usuário][24]

**Para atribuir Brenda Fernandes ao SuccessFactors, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][25]
2. Na lista de aplicativos, selecione **SuccessFactors**.
   
    ![Configurar o logon único][26]
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][27]
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][28]

### <a name="testing-single-sign-on"></a>Teste do logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco SuccessFactors no Painel de Acesso, deverá fazer logon automaticamente no seu aplicativo SuccessFactors.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_00.png
[1]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_01.png
[6]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_02.png
[7]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_03.png
[8]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_04.png
[9]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_05.png
[10]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_06.png

[11]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_09.png
[14]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_05.png
[15]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_06.png

[16]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_00.png
[17]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_01.png
[18]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_02.png
[19]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_03.png
[20]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_04.png
[21]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_05.png
[22]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_06.png
[23]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_07.png

[24]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_07.png
[25]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_08.png
[26]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_11.png
[27]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_09.png
[28]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_10.png
[29]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_10.png
