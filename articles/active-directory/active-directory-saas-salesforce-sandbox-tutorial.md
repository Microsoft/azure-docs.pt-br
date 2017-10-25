---
title: "Tutorial: integração do Azure Active Directory com a Área Restrita do Salesforce | Microsoft Docs"
description: "Saiba como usar a Área Restrita Salesforce com o Active Directory do Azure para habilitar o logon único, o provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/21/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 32835e79188806bb2ff319eea23b1b52ab585ab1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Tutorial: Integração do Active Directory do Azure com a Área Restrita Salesforce

O objetivo deste tutorial é mostrar a integração do Azure com a Área Restrita Salesforce.  

>[!TIP]
>Para enviar comentários, consulte a [página de suporte do Azure](http://go.microsoft.com/fwlink/?LinkId=521878). 
> 

As áreas restritas oferecem a capacidade de criar várias cópias da sua organização em ambientes separados por uma variedade de finalidades, como desenvolvimento, testes e treinamento, sem comprometer os dados e os aplicativos em sua organização de produção do Salesforce.  

Para obter mais detalhes, confira [Visão geral da área restrita](https://help.salesforce.com/HTViewHelpDoc?id=create_test_instance.htm&language=en_US)

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma área restrita no Salesforce.com

Se você não tiver uma área restrita válida no Salesforce.com, precisará entrar em contato com o Salesforce.

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para a Área Restrita Salesforce
2. Configuração do SSO (logon único)
3. Habilitando seu domínio
4. Configurando o provisionamento de usuários
5. Atribuindo usuários

![Cenário](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769571.png "Cenário")

## <a name="enable-the-application-integration-for-salesforce-sandbox"></a>Habilitar a integração de aplicativos para a Área Restrita do Salesforce
O objetivo desta seção é descrever como habilitar a integração de aplicativos para a área restrita Salesforce.

**Para habilitar a integração de aplicativos para a área restrita Salesforce, execute as seguintes etapas:**

1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700994.png "Aplicativos")
4. Para abrir a **Galeria de Aplicativos**, clique em **Adicionar um Aplicativo** e em **Adicionar um aplicativo a ser utilizado pela minha organização**.
   
   ![O que você deseja fazer? ] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC700995.png "O que você deseja fazer?")
5. Na **caixa de pesquisa**, digite **Área Restrita Salesforce**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-salesforce-sandbox-tutorial/IC710978.png "Galeria de Aplicativos")
6. No painel de resultados, selecione **Área Restrita Salesforce** e clique em **Concluir** para adicionar o aplicativo.
   
   ![Área restrita do SalesForce](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746474.png "Área restrita do Salesforce")
   
## <a name="configur-single-sign-on-sso"></a>Configurar o SSO (logon único)

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Salesforce com a conta do AD do Azure usando federação baseada em protocolo SAML.

**Para configurar o logon único, execute as seguintes etapas:**

1. No Portal Clássico do Azure, na página de integração de aplicativos da **Área Restrita do Salesforce**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar logon único](./media/active-directory-saas-salesforce-sandbox-tutorial/IC749323.png "Configurar logon único")
2. Na página **Como você deseja que os usuários façam logon na Área Restrita Salesforce**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Área restrita do SalesForce](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746479.png "Área restrita do Salesforce")
3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de logon**, digite sua URL usando o seguinte padrão, `http://company.my.salesforce.com` e, em seguida, clique em **Avançar**.
   
   ![Configurar URL do Aplicativo](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781022.png "Configurar URL do Aplicativo")
4. Se já tiver configurado o logon único para outra instância de Área restrita do Salesforce em seu diretório, você também deve configurar o **Identificador** para ter o mesmo valor que a **URL de Logon**. 
 * O campo **Identificador** pode ser encontrado marcando a caixa de seleção **Mostrar configurações avançadas** na página **Configurar URL do Aplicativo** do diálogo.
5. Na página **Configurar logon único na Área Restrita Salesforce**, clique em **Baixar o certificado** e salve o arquivo de certificado em seu computador.
   
   ![Configurar Logon Único](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781023.png "Configurar Logon Único")
6. Em outra janela do navegador da Web, faça logon em sua área restrita Salesforce como um administrador.
7. No menu na parte superior, clique em **Configuração**.
   
   ![Configuração](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781024.png "Configuração")
8. No painel de navegação à esquerda, clique em **Controles de Segurança** e clique em **Configurações de Logon Único**.
   
   ![Configurações de Logon Único](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781025.png "Configurações de Logon Único")
9. Na seção de Configurações de Logon Único, execute as seguintes etapas:
   
   ![Configurações de Logon Único](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781026.png "Configurações de Logon Único")  
 1.  Selecione **SAML Habilitado**. 
 2.  Clique em **Novo**.
10. Na seção Configurações de Logon Único de SAML, execute as seguintes etapas:
    
    ![Configurações de Logon Único do SAML](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781027.png "Configurações de Logon Único do SAML")  
 1. Na caixa de texto Nome, digite o nome da configuração (por exemplo:*SPSSOWAAD\_Teste*). 
 2. No portal clássico do Azure, na página de diálogo **Configurar logon único na Área Restrita do Salesforce**, copie o valor de **URL do Emissor** e cole-o na caixa de texto **Emissor**.
 3. Na caixa de texto **Id da Entidade**, digite **https://test.salesforce.com** se esta for a primeira instância de área restrita do Salesforce que você está adicionando ao seu diretório. Se você já tiver adicionado uma instância da Área restrita do Salesforce, para a **ID da Entidade**, digite a **URL de Logon** que deve estar no seguinte formato: `http://company.my.salesforce.com`   
 4. Clique em **Procurar** para carregar o certificado baixado.  
 5. Para o **Tipo de Identidade SAML**, selecione **A declaração contém a ID de Federação do objeto de Usuário**. 
 6. Para **Local de Identidade SAML**, selecione **A identidade está no elemento NameIdentifier da instrução Subject**.
 7. No portal clássico do Azure, na página de diálogo **Configurar logon único na Área Restrita do Salesforce**, copie o valor de **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon do Provedor de Identidade**. 
 8. O SFDC não dá suporte a logout SAML.  Como alternativa, cole “https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0” na caixa de texto **URL de Logoff do Provedor de Identidade**.
 9. Para **Associação de Solicitação Iniciada pelo Provedor de Serviços**, selecione **HTTP Post**. 
 10. Clique em **Salvar**.
11. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
    
    ![Configurar Logon Único](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781028.png "Configurar Logon Único")

## <a name="enable-your-domain"></a>Habilitar seu domínio
Esta seção pressupõe que você já tenha criado um domínio.  Para obter mais detalhes, confira [Definindo seu nome de domínio](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US).

**Para habilitar seu domínio, execute as seguintes etapas:**

1. No painel de navegação esquerdo, clique em **Gerenciamento de Domínio** e clique em **Meu Domínio.**
   
   ![Meu Domínio](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781029.png "Meu Domínio")
   
   >[!NOTE]
   >Verifique se o domínio foi configurado corretamente. 
   > 
2. Na seção **Configurações de Página de Logon**, clique em **Editar** e, para o **Serviço de Autenticação**, selecione o nome da Configuração de Logon Único SAML da seção anterior e, por fim, clique em **Salvar**.
   
   ![Meu Domínio](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781030.png "Meu Domínio")

Assim que você tiver um domínio configurado, seus usuários deverão usar a URL do domínio para fazer logon na área restrita Salesforce.  

Para obter o valor da URL, clique no perfil SSO criado na seção anterior.

## <a name="configure-user-provisioning"></a>Configurar provisionamento do usuário
O objetivo desta seção é descrever como habilitar o provisionamento de contas de usuário do Active Directory na Área Restrita Salesforce.

**Para configurar o provisionamento de usuários, execute as seguintes etapas:**

1. No portal do Salesforce, na barra de navegação superior, selecione seu nome para expandir o seu menu de usuário:
   
   ![Minhas configurações](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698773.png "Minhas configurações")
2. Do seu menu de usuário, selecione **Minhas Configurações** para abrir a página **Minhas Configurações**.
3. No painel esquerdo, clique em **Pessoal** para expandir a seção Pessoal e clique em **Redefinir Meu Token de Segurança**:
   
   ![Minhas configurações](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698774.png "Minhas configurações")
4. Na página **Redefinir meu Token de Segurança**, clique em **Redefinir Token de Segurança** para solicitar um email com seu token de segurança do Salesforce.com.
   
   ![Novo Token](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698776.png "Novo token")
5. Marque a caixa de entrada de um email do Salesforce.com usando "**confirmação de segurança de salesforce.com.com**" como o assunto.
6. Leia o email e copie o valor do token de segurança.
7. No portal clássico do Azure, na página de integração do aplicativo **Área restrita do salesforce**, clique em **Configurar provisionamento do usuário** para abrir a caixa de diálogo **Configurar Provisionamento do Usuário**.
   
   ![Configurar provisionamento do usuário](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769573.png "configurar provisionamento do usuário")
8. Na página **Inserir suas credenciais da Área Restrita Salesforce para habilitar o provisionamento automático de usuários** , forneça as seguintes configurações:
   
   ![Área restrita do SalesForce](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746476.png "Área restrita do Salesforce")   
 1. Na caixa de texto **Nome de Usuário Administrador da área restrita Salesforce**, digite o nome da conta de uma área restrita Salesforce com o perfil **Administrador de Sistema** do Salesforce.com atribuído.
 2. Na caixa de texto **Senha do Administrador da Área Restrita Salesforce** , digite a senha dessa conta.
 3. Na caixa de texto **Token de Segurança do Usuário** , cole o valor do token de segurança.
 4. Clique em **Validar** para verificar sua configuração.
 5. Clique no botão **Avançar** para abrir a página **Confirmação**.
9. Na página **Confirmação**, clique em **Concluir** para salvar sua configuração.
   
## <a name="assigning-users"></a>Atribuindo usuários

Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

**Para atribuir usuários à Área Restrita Salesforce, execute as etapas a seguir:**

1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração do aplicativo **Salesforce Sandbox**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769574.png "Atribuir usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-salesforce-sandbox-tutorial/IC767830.png "Sim")

Agora você deve aguardar 10 minutos e verificar se a conta foi sincronizada com a Área Restrita Salesforce.

Se você quiser testar suas configurações de SSO, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

