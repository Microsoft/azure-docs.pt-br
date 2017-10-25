---
title: "Tutorial: Integração do Azure Active Directory com o Workplace by Facebook| Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Workplace by Facebook."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: jeedes
ms.openlocfilehash: 27e62a00832484667117d8718db9f2fc05e2f4e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>Tutorial: Integração do Azure Active Directory com o Workplace by Facebook

Neste tutorial, você aprenderá a integrar o Workplace by Facebook ao Azure AD (Azure Active Directory).

A integração do Workplace by Facebook ao Azure AD oferece os seguintes benefícios:

- No Azure AD, você pode controlar quem tem acesso ao Workplace by Facebook.
- Você pode habilitar o logon automático de seus usuários no Workplace by Facebook (logon único) com as contas do Azure AD.
- Gerencie suas contas em um único local: o portal do Azure.

Para obter mais detalhes sobre a integração de aplicativos de SaaS (software como serviço) ao Azure AD, consulte [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Workplace by Facebook, são necessários os seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Workplace by Facebook habilitada para SSO (logon único)

Para testar as etapas neste tutorial, siga estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testa o SSO do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Workplace by Facebook da galeria.
2. Configurar e testar logon único do Azure AD.

## <a name="add-workplace-by-facebook-from-the-gallery"></a>Adicionar o Workplace by Facebook da galeria
Para configurar a integração do Workplace by Facebook ao Azure AD, adicione o Workplace by Facebook da galeria à sua lista de aplicativos SaaS gerenciados.

1. No [Portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **Aplicativos empresariais** > **Todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Para adicionar o novo aplicativo, selecione **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Workplace by Facebook** e selecione **Workplace by Facebook** nos resultados. Em seguida, selecione **Adicionar** para adicionar o aplicativo.

    ![Workplace by Facebook na lista de resultados](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_search.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD
Nesta seção, você configurará e testará o SSO do Azure AD com o Workplace by Facebook, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o SSO funcione, o Azure AD precisa saber qual usuário do Workplace by Facebook é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Workplace by Facebook.

Estabeleça essa relação atribuindo o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** no Workplace by Facebook.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o SSO do Azure AD no Portal do Azure e configurará o SSO no aplicativo Workplace by Facebook.

1. No Portal do Azure, na página de integração de aplicativos do **Workplace by Facebook**, selecione **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione o **Modo** **Logon baseado em SAML** para habilitar o SSO.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_samlbase.png)

3. Na seção **Domínio e URLs do Workplace by Facebook**, faça o seguinte:

    a. Na caixa de texto **URL de Entrada**, digite uma URL que usa o seguinte padrão: `https://<company subdomain>.facebook.com`

    b. Na caixa de texto **Identificador**, digite uma URL que usa o seguinte padrão: `https://www.facebook.com/company/<scim company id>`

    > [!NOTE]
    > Esses valores são apenas um exemplo. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [Equipe de suporte ao cliente do Workplace by Facebook](https://workplace.fb.com/faq/) para obter esses valores. 

4. Na seção **Certificado de Autenticação SAML**, selecione **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_certificate.png) 

5. Selecione **Salvar**.

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do Workplace by Facebook**, selecione **Configurar o Workplace by Facebook** para abrir a janela **Configurar logon**. Copie a **URL de Saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da seção **Referência Rápida**.

    ![Configuração do Workplace by Facebook](./media/active-directory-saas-facebook-at-work-tutorial/config.png) 

7. Em outra janela do navegador da Web, entre em seu site da empresa do Workplace by Facebook como administrador.
  
   > [!NOTE] 
   > Como parte do processo de autenticação SAML, o Workplace poderá utilizar cadeias de consulta de até 2,5 quilobytes para passar parâmetros para o Azure AD.

8. No **Painel da Empresa**, acesse a guia **Autenticação**.

9. Em **Autenticação SAML**, selecione **Somente SSO** na lista suspensa.

10. Insira os valores copiados da seção **Configuração do Workplace by Facebook** do Portal do Azure nos campos correspondentes:

    *   Na caixa de texto **URL de SAML**, cole o valor da **URL do Serviço de Logon Único** copiado do Portal do Azure.
    *   Na caixa de texto **URL do Emissor SAML**, cole o valor da **ID da Entidade SAML** copiado do Portal do Azure.
    *   Em **Redirecionamento de Logoff SAML (opcional)**, cole o valor da **URL de Saída** que foi copiado do Portal do Azure.
    *   Abra o **Certificado codificado Base 64** no Bloco de notas, baixado do Portal do Azure. Copie o conteúdo dele para sua área de transferência e, em seguida, cole-o na caixa de texto **Certificado SAML**.

11. Talvez seja necessário inserir a URL do público-alvo, a URL do destinatário e a URL do ACS (Serviço do Consumidor de Declaração) listadas na seção **Configuração do SAML**.

12. Role até o final da seção e selecione **Testar SSO**. Uma janela pop-up será exibida, com a página de entrada do Azure AD. Para se autenticar, insira suas credenciais como de costume. Verifique se o endereço de email retornado do Azure AD é o mesmo da conta do Workplace na qual você está conectado.

13. Se o teste for concluído com êxito, role até a parte inferior da página e selecione **Salvar**.

14. Agora, todas as pessoas que usam o Workplace verão a página de entrada do Azure AD para autenticação.

Você pode optar por configurar uma URL de logoff SAML, que pode ser usada para apontar para a página de logoff do Azure AD. Quando essa configuração estiver habilitada e configurada, o usuário não será mais direcionado para a página de logoff do Workplace. Em vez disso, ele será redirecionado para a URL que foi adicionada à configuração de redirecionamento de logoff SAML.


> [!TIP]
> Agora, é possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo. Depois de adicionar esse aplicativo da seção **Active Directory** > **Aplicativos Empresariais**, basta selecionar a guia **Logon Único** e acessar a documentação inserida na seção **Configuração**, na parte inferior. Saiba mais sobre o recurso de documentação inserida na [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="configure-reauthentication-frequency"></a>Configurar a frequência de reautenticação

É possível configurar o Workplace para solicitar uma verificação SAML todos os dias, a cada três dias, toda semana, a cada duas semanas, todos os meses ou nunca.

> [!NOTE] 
>O valor mínimo para a verificação SAML em aplicativos móveis é definido como uma semana.

Você também pode forçar uma redefinição de SAML para todos os usuários. Para fazer isso, use o botão **Exigir autenticação SAML para todos os usuários agora**.


### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

1. No **Portal do Azure**, no painel esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_01.png) 

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e selecione **Todos os usuários**.
    
    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, selecione **Adicionar**.
 
    ![O botão Adicionar](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_03.png) 

4. Na caixa de diálogo **Usuário**, faça o seguinte:
 
    ![A caixa de diálogo Usuário](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **BrendaFernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** de BrendaFernandes.

    c. Selecione **Mostrar Senha** e tome nota.

    d. Selecione **Criar**.
 
### <a name="create-a-workplace-by-facebook-test-user"></a>Criar um usuário de teste do Workplace by Facebook

Nesta seção, um usuário chamado Brenda Fernandes será criado no Workplace by Facebook. O Workplace by Facebook dá suporte ao provisionamento Just-In-Time, que está habilitado por padrão.

Não há nenhuma ação para você nesta seção. Se um usuário ainda não existir no Workplace by Facebook, um novo será criado quando você tentar acessar o Workplace by Facebook.

>[!Note]
>Se precisar criar um usuário manualmente, entre em contato com a [Equipe de suporte ao cliente do Workplace by Facebook](https://workplace.fb.com/faq/).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o SSO do Azure concedendo acesso ao Workplace by Facebook.

![Atribuir usuário][200] 

1. No Portal do Azure, abra o modo de exibição de aplicativos. Vá até o odo de exibição de diretório, vá até **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Workplace by Facebook**.

    ![O link Workplace by Facebook na lista Aplicativos](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_app.png) 

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”][202] 

4. Selecione **Adicionar**. Em seguida, no painel **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![O painel Adicionar Atribuição][203]

5. Na caixa de diálogo **Usuários e grupos**, selecione **Brenda Fernandes** na lista de usuários.

6. Na caixa de diálogo **Usuários e grupos**, selecione **Selecionar**.

7. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.
    
### <a name="test-single-sign-on"></a>Testar logon único

Se você quiser testar suas configurações de SSO, abra o Painel de Acesso.
Para saber mais, confira [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).


## <a name="next-steps"></a>Próximas etapas

* Consulte a [lista de tutoriais sobre como integrar aplicativos de SaaS ao Azure Active Directory](active-directory-saas-tutorial-list.md).
* Leia [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md).
* Saiba mais sobre como [configurar o provisionamento de usuário](active-directory-saas-facebook-at-work-provisioning-tutorial.md).



<!--Image references-->

[1]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_203.png

