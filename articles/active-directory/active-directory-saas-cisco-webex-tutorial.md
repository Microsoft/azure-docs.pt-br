---
title: "Tutorial: integração do Azure Active Directory com o Cisco Webex | Microsoft Docs"
description: "Saiba como usar o Cisco Webex com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
ms.openlocfilehash: b44b1a5b3e988a51db3325ec8a181651fa84e768
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Tutorial: integração do Active Directory do Azure ao Cisco Webex
O objetivo deste tutorial é mostrar a integração do Azure ao Cisco Webex.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Um locatário do Cisco Webex

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Cisco Webex poderão fazer logon único no aplicativo em seu site de empresa do Cisco Webex (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

* Habilitando a integração de aplicativos para o Cisco Webex
* Configuração do SSO (logon único)
* Configurando o provisionamento de usuários
* Atribuindo usuários

![Cenário](./media/active-directory-saas-cisco-webex-tutorial/IC777614.png "Cenário")

## <a name="enable-the-application-integration-for-cisco-webex"></a>Habilitar a integração de aplicativos para o Cisco Webex
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Cisco Webex.

**Para habilitar a integração de aplicativos para o Cisco Webex, execute as seguintes etapas:**

1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-cisco-webex-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-cisco-webex-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-cisco-webex-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-cisco-webex-tutorial/IC749322.png "Adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **Cisco Webex**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-cisco-webex-tutorial/IC777615.png "Galeria de Aplicativos")
7. No painel de resultados, selecione **Cisco Webex** e clique em **Concluir** para adicionar o aplicativo.
   
   ![Cisco Webex](./media/active-directory-saas-cisco-webex-tutorial/IC777616.png "Cisco Webex")
   
## <a name="configure-single-sign-on"></a>Configurar o logon único

O objetivo desta seção é descrever como permitir que os usuários autentiquem no Cisco Webex com a própria conta do Azure AD usando a federação baseada em protocolo SAML.  

Como parte deste procedimento, será necessário criar um certificado codificado em base-64. Se você não estiver familiarizado com esse procedimento, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

**Para configurar o logon único, execute as seguintes etapas:**

1. No Portal Clássico do Azure, na página de integração do aplicativo **Cisco Webex**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar logon único](./media/active-directory-saas-cisco-webex-tutorial/IC777617.png "Configurar logon único")
2. Na página **Como você deseja que os usuários façam logon no Cisco Webex**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar logon único](./media/active-directory-saas-cisco-webex-tutorial/IC777618.png "Configurar logon único")
3. Na página **Configurar URL do Aplicativo**, realize as etapas a seguir e clique em **Avançar**.
   
   ![Configurar URL do Aplicativo](./media/active-directory-saas-cisco-webex-tutorial/IC777619.png "Configurar URL do Aplicativo")   
   1. Na caixa de texto **URL de Logon**, digite a URL de locatário do Cisco Webex (por exemplo, *http://contoso.webex.com*).
   2. Na caixa de texto **URL de resposta do Cisco Webex**, digite sua **URL do AssertionConsumerService do Cisco Webex** (por exemplo, *https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company*).
4. Na página **Configurar logon único no Cisco Webex**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado no computador.
   
   ![Configurar logon único](./media/active-directory-saas-cisco-webex-tutorial/IC777620.png "Configurar logon único")
5. Em uma janela diferente do navegador da Web, faça logon em seu site de empresa do Cisco Webex como administrador.
6. No menu na parte superior, clique em **Administração de Site**.
   
   ![Administração de Site](./media/active-directory-saas-cisco-webex-tutorial/IC777621.png "Administração de Site")
7. Na seção **Gerenciar Site**, clique em **Configuração de SSO**.
   
   ![Configuração de SSO](./media/active-directory-saas-cisco-webex-tutorial/IC777622.png "Configuração de SSO")
8. Na seção Configuração de SSO da Web Federado, execute as seguintes etapas:
   
   ![Configuração de SSO Federado](./media/active-directory-saas-cisco-webex-tutorial/IC777623.png "Configuração de SSO Federado")  
   1. Na lista **Protocolo de Federação**, selecione **SAML 2.0**.
   2. Crie um arquivo **codificado em base 64** usando o certificado baixado.  
    >[!TIP]
    >Para obter mais detalhes, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)
    >  
   3. Abra seu certificado codificado em base-64 no bloco de notas e copie o conteúdo dele.
   4. Clique em **Importar Metadados do SAML**e cole o certificado codificado em Base 64.
   5. No portal clássico do Azure, na página do diálogo **Configurar logon único no Cisco Webex**, copie o valor da **URL do Emissor** e cole-o na caixa de texto **Emissor para SAML (ID do IdP)**.
   6. No portal clássico do Azure, na página do diálogo **Configurar logon único no Cisco Webex**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon do Serviço SSO do Cliente**.
   7. Na lista **Formato de NameID**, selecione **Endereço de Email**.
   8. Na caixa de texto **AuthnContextClassRef**, digite **urn:oasis:names:tc:SAML:2.0:ac:classes:Password**.
   9. No portal clássico do Azure, na página do diálogo **Configurar logon único no Cisco Webex**, copie o valor da **URL de Logout Remoto** e cole-o na caixa de texto **URL de Logout do Serviço SSO do Cliente**.
   10. Clique em **Atualizar**.
9. No portal clássico do Azure, na página do diálogo **Configurar logon único no Cisco Webex**, escolha a confirmação de configuração de logon único e clique em **Concluir**.
   
   ![Configurar logon único](./media/active-directory-saas-cisco-webex-tutorial/IC777624.png "Configurar logon único")
   
## <a name="configure-user-provisioning"></a>Configurar provisionamento do usuário

Para permitir que os usuários do Azure AD façam logon no Cisco Webex, eles devem ser provisionados no Cisco Webex.  

* No caso do Cisco Webex, o provisionamento é uma tarefa manual.

**Para provisionar contas de usuário, execute as seguintes etapas:**

1. Faça logon em seu locatário do **Cisco Webex** .
2. Vá para **Gerenciar Usuários \> Adicionar Usuário**.
   
   ![Adicionar Usuários](./media/active-directory-saas-cisco-webex-tutorial/IC777625.png "Adicionar Usuários")
3. Na seção Adicionar Usuário, execute as seguintes etapas:
   
   ![Adicionar Usuário](./media/active-directory-saas-cisco-webex-tutorial/IC777626.png "Adicionar Usuário")   
   1. Para **Tipo de Conta**, selecione **Host**.
   2. Digite as informações de um usuário existente do Azure AD nas seguintes caixas de texto: **Nome, Sobrenome**, **Nome de usuário**, **Email**, **Senha** e **Confirmar Senha**.
   3. Clique em **Adicionar**.

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do Cisco Webex ou APIs fornecidas pelo Cisco Webex para provisionar as contas de usuário do AAD. 
> 

## <a name="assign-users"></a>Atribuir usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

**Para atribuir usuários ao Cisco Webex, execute as etapas a seguir:**

1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração do aplicativo **Cisco Webex**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-cisco-webex-tutorial/IC777627.png "Atribuir usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-cisco-webex-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

