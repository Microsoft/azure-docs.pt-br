---
title: "Tutorial: Integração do Azure Active Directory com o Qlik Sense Enterprise | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Qlik Sense Enterprise."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8c27e340-2b25-47b6-bf1f-438be4c14f93
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: jeedes
ms.openlocfilehash: 4964634cd5aaf0dbb98c766f5e12700c4d118750
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>Tutorial: integração do Azure Active Directory ao Qlik Sense Enterprise

Neste tutorial, você aprenderá como integrar o Qlik Sense Enterprise ao Azure Active Directory (Azure AD).

Integrar o Qlik Sense Enterprise ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD que tenha acesso ao Qlik Sense Enterprise.
- Você pode habilitar seus usuários a fazerem logon automaticamente no Qlik Sense Enterprise (logon único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Qlik Sense Enterprise, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Qlik Sense Enterprise habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, será possível obter uma versão de avaliação de um mês aqui: [Oferta de avaliação](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar Qlik Sense Enterprise da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Adicionar Qlik Sense Enterprise da galeria
Para configurar a integração do Qlik Sense Enterprise com o Azure AD, você precisará adicionar o Qlik Sense Enterprise à sua lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Qlik Sense Enterprise por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Qlik Sense Enterprise**, selecione **Qlik Sense Enterprise** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Qlik Sense Enterprise na lista de resultados](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Qlik Sense Enterprise, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Qlik Sense Enterprise é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Qlik Sense Enterprise.

No Qlik Sense Enterprise, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Qlik Sense Enterprise, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar de um usuário de teste do Qlik Sense Enterprise](#create-a-qlik-sense-enterprise-test-user)**: para ter um equivalente de Brenda Fernandes no Qlik Sense Enterprise que esteja vinculado à representação do usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único em seu aplicativo Qlik Sense Enterprise.

**Para configurar o logon único do Azure AD com Qlik Sense Enterprise, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **Qlik Sense Enterprise**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_samlbase.png)

3. Na seção **URLs e Domínio do Qlik Sense Enterprise**, execute as seguintes etapas:

    ![Informações de logon único em Domínio e URLs do Qlik Sense Enterprise](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<Qlik Sense Fully Qualifed Hostname>:443//samlauthn/`
    
    > [!NOTE]
    > Observe a barra à direita no fim desse URI. Ela é necessária.
    
    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:
    | |
    |--|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qlikpoc.com`|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qliksense.com`|

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Logon e o Identificador, explicados adiante no tutorial, ou entre em contato com a [equipe de suporte do Cliente do Qlik Sense Enterprise](https://www.qlik.com/us/services/support) para obter esses valores. 

4. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_400.png)

6. Prepare o arquivo XML de Metadados de Federação para que você possa carregar no servidor Qlik Sense.
   
    > [!NOTE]
    > Antes de carregar os metadados IdP para o servidor Qlik Sense, o arquivo precisa ser editado para remover as informações e garantir a operação correta entre o Azure AD e o servidor Qlik Sense.
    
    ![QlikSense][qs24]
   
    a. Abra o arquivo FederationMetaData.xml baixado do portal do Azure em um editor de texto.
   
    b. Procure o valor **RoleDescriptor**.  Há quatro entradas (dois pares de marcas de elemento de abertura e fechamento).
   
    c. Exclua todas as informações e as marcas RoleDescriptor do arquivo.
   
    d. Salve o arquivo e mantenha-o à mão para uso posterior neste documento.

7. Navegue para o QMC (Qlik Sense Qlik Management Console) como um usuário que pode criar configurações de proxy virtual.

8. No QMC, clique no item de menu **Virtual Proxies**.
   
    ![QlikSense][qs6] 

9. Na parte inferior da tela, clique no botão **Criar novo**.
   
    ![QlikSense][qs7]

10. A tela de Edição de proxy virtual é exibida.  No lado direito da tela, há um menu para tornar as opções de configuração visíveis.
   
    ![QlikSense][qs9]

11. Com a opção de menu identificação marcada, insira as informações de identificação para a configuração de proxy virtual do Azure.
    
    ![QlikSense][qs8]  
    
    a. O campo **Descrição** é um nome amigável para a configuração do proxy virtual.  Insira um valor para uma descrição.
    
    b. O campo **Prefixo** identifica o ponto de extremidade do proxy virtual para se conectar ao Qlik Sense com o Logon Único do Azure AD.  Insira um nome de prefixo exclusivo para esse proxy virtual.
    
    c. **Tempo limite de inatividade de sessão (minutos)** é o tempo limite para conexões por meio desse proxy virtual.
    
    d. O **Nome de cabeçalho de cookie de sessão** é o nome do cookie que armazena o identificador de sessão para a sessão do Qlik Sense que um usuário recebe após uma autenticação bem-sucedida.  Esse nome deve ser exclusivo.

12. Clique na opção de menu de autenticação para torná-la visível.  Aparece a tela de autenticação.
    
    ![QlikSense][qs10]
    
    a. O menu suspenso **Modo de acesso anônimo** determina se os usuários anônimos podem acessar o Qlik Sense por meio do proxy virtual.  A opção padrão é Nenhum usuário anônimo.
    
    b. O menu suspenso **Método de autenticação** determina o esquema de autenticação que o proxy virtual usará.  Selecione SAML na lista suspensa.  Mais opções são exibidas como resultado.
    
    c. No **campo URI de host SAML**, insira o nome de host que os usuários precisam usar para acessar o Qlik Sense por meio desse proxy virtual SAML.  O nome do host é o uri do servidor Qlik Sense.
    
    d. Na **ID da entidade SAML**, insira o mesmo valor inserido para o campo URI de host SAML.
    
    e. **Metadados IdP SAML** é o arquivo editado anteriormente na seção **Editar metadados de federação de configuração do Azure AD**.  **Antes de carregar os metadados IdP, o arquivo precisa ser editado** para remover as informações e garantir a operação correta entre o Azure AD e servidor Qlik Sense.  **Veja as instruções acima se o arquivo ainda precisar ser editado.**  Se o arquivo foi editado, clique no botão Procurar e selecione o arquivo de metadados editado para carregá-lo para a configuração do proxy virtual.
    
    f. Insira a referência de nome ou o esquema de atributo para o atributo SAML que representa o **UserID** que o Azure AD envia ao servidor Qlik Sense.  Informações de referência de esquema estão disponíveis na configuração de postagem de telas do aplicativo do Azure.  Para usar o atributo de nome, insira `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    g. Insira o valor para o **diretório de usuário** que será anexado aos usuários quando eles se autenticarem no servidor Qlik Sense por meio do Azure AD.  Valores embutidos em código devem estar entre **colchetes []**.  Para usar um atributo enviado na asserção SAML do Azure AD, digite o nome do atributo na caixa de texto **sem** colchetes.
    
    h. O **algoritmo de assinatura SAML** define o certificado de provedor (nesse caso, o servidor Qlik Sense) do serviço de assinatura para a configuração de proxy virtual.  Se o servidor Qlik Sense usar um certificado confiável gerado usando o Microsoft Enhanced RSA e o Provedor de Criptografia AES, altere o algoritmo de assinatura de SAML para **SHA-256**.
    
    i. A seção de mapeamento de atributo SAML permite que atributos adicionais como grupos sejam enviados ao Qlik Sense para uso em regras de segurança.

13. Clique na opção de menu **BALANCEAMENTO DE CARGA** para torná-la visível.  Aparece a tela Balanceamento de Carga.
    
    ![QlikSense][qs11]

14. Clique no botão **Adicionar novo nó de servidor**, selecione o(s) nó(s) de mecanismo aos quais o Qlik Sense enviará sessões para fins de balanceamento de carga e clique no botão **Adicionar**.
    
    ![QlikSense][qs12]

15. Clique na opção de menu Avançado para torná-la visível. Aparece a tela Avançado.
    
    ![QlikSense][qs13]
    
    A lista de permissões de Host identifica os nomes de host que são aceitos durante a conexão com o servidor Qlik Sense.  **Insira o nome do host que os usuários especificarão ao se conectar ao servidor Qlik Sense.** O nome do host é o mesmo valor que o uri de host SAML, sem https://.

16. Clique no botão **Aplicar**.
    
    ![QlikSense][qs14]

17. Clique em OK para aceitar a mensagem de aviso informando que proxies vinculados ao proxy virtual serão reiniciados.
    
    ![QlikSense][qs15]

18. No lado direito da tela, o menu Itens Associados é exibido.  Clique na opção de menu **Proxies**.
    
    ![QlikSense][qs16]

19. Aparece a tela de proxy.  Clique no botão **Vincular** na parte inferior para vincular um proxy ao proxy virtual.
    
    ![QlikSense][qs17]

20. Selecione o nó de proxy que dará suporte a essa conexão de proxy virtual e clique no botão **Vincular**.  Depois da vinculação, o proxy será listado nos proxies associados.
    
    ![QlikSense][qs18]
  
    ![QlikSense][qs19]

21. Após cerca de cinco a dez segundos, será exibida a mensagem Atualizar QMC.  Clique no botão **Atualizar QMC**.
    
    ![QlikSense][qs20]

22. Quando o QMC for atualizado, clique no item de menu **Virtual proxies**. A nova entrada de proxy virtual SAML é listada na tabela na tela.  Clique na entrada de proxy virtual.
    
    ![QlikSense][qs51]

23. Na parte inferior da tela, o botão Baixar metadados SP será ativado.  Clique no botão **Baixar metadados SP** para salvar os metadados em um arquivo.
    
    ![QlikSense][qs52]

24. Abra o arquivo de metadados sp.  Observe a entrada **entityID** e a entrada **AssertionConsumerService**.  Esses valores são equivalentes ao **Identificador** e à **URL de logon** na configuração do aplicativo do Azure AD. Cole esses valores na seção **Domínio e URLs do Qlik Sense Enterprise** na configuração do aplicativo do Azure AD se não forem correspondentes e, em seguida, você deverá substituí-los no assistente de configuração de aplicativo do Azure AD.
    
    ![QlikSense][qs53]

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

   ![O botão Azure Active Directory](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

   ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

   ![O botão Adicionar](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

   ![A caixa de diálogo Usuário](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_04.png)

   a. Na caixa **Nome**, digite **BrendaFernandes**.

   b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

   c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

   d. Clique em **Criar**.
 
### <a name="create-a-qlik-sense-enterprise-test-user"></a>Criar um usuário de teste do Qlik Sense Enterprise

Nesta seção, você deve criar uma usuária chamada Brenda Fernandes no Qlik Sense Enterprise. Trabalhe com a [equipe de suporte do Cliente do Qlik Sense Enterprise](https://www.qlik.com/us/services/support) para adicionar os usuários na plataforma Qlik Sense Enterprise. Os usuários devem ser criados e ativados antes de usar o logon único. 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Qlik Sense Enterprise.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Qlik Sense Enterprise, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Qlik Sense Enterprise**.

    ![O link do Qlik Sense Enterprise na lista de Aplicativos](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksense-enterprise_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Qlik Sense Enterprise no Painel de Acesso, você deverá ser conectado automaticamente a seu aplicativo Qlik Sense Enterprise. 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_203.png

[qs6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs24]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs51]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png

