---
title: "Tutorial: Integração do Azure Active Directory ao Central Desktop | Microsoft Docs"
description: "Saiba como usar o Central Desktop com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
ms.openlocfilehash: fe32c1d68040ceb9d9de2ad6c4a6dc9ea93f5aef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Tutorial: Integração do Active Directory do Azure ao Central Desktop
O objetivo deste tutorial é mostrar a integração do Azure ao Central Desktop. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura habilitada para logon único do Central Desktop/locatário do Central Desktop

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

* Habilitando a integração de aplicativos para o Central Desktop
* Configuração do SSO (logon único)
* Configurando o provisionamento de usuários
* Atribuindo usuários

![Cenário](./media/active-directory-saas-central-desktop-tutorial/IC769558.png "Cenário")

## <a name="enable-the-application-integration-for-central-desktop"></a>Habilitar a integração de aplicativos para o Central Desktop
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Central Desktop.

**Para habilitar a integração de aplicativos para o Central Desktop, execute as seguintes etapas:**

1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-central-desktop-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-central-desktop-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-central-desktop-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-central-desktop-tutorial/IC749322.png "Adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **Central Desktop**.
   
   ![Galeria de aplicativos](./media/active-directory-saas-central-desktop-tutorial/IC769559.png "Galeria de aplicativos")
7. No painel de resultados, selecione **Central Desktop** e clique em **Concluir** para adicionar o aplicativo.
   
   ![Área de Trabalho Central](./media/active-directory-saas-central-desktop-tutorial/IC769560.png "Área de Trabalho Central")
   
## <a name="configure-single-sign-on"></a>Configurar o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Central Desktop com a respectiva conta do AD do Azure usando federação baseada no protocolo SAML.

Como parte deste procedimento, é necessário carregar um certificado codificado em base 64 no locatário do Desktop Central.  
Se você não estiver familiarizado com este procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

**Para configurar o logon único, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração do aplicativo **Central Desktop**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
   ![Configurar logon único](./media/active-directory-saas-central-desktop-tutorial/IC749323.png "Configurar logon único")
2. Na página **Como você deseja que os usuários façam logon no Central Desktop**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar logon único](./media/active-directory-saas-central-desktop-tutorial/IC777628.png "Configurar logon único")
3. Na página **Configurar URL do Aplicativo**, realize as etapas a seguir e clique em **Avançar**: 
   
   1. Na caixa de texto **URL de Logon do Central Desktop**, digite a URL do seu locatário do Central Desktop (por exemplo: *http://contoso.centraldesktop.com*).
   2. Na caixa de texto URL de Resposta do Central Desktop, digite a URL AssertionConsumerService do Central Desktop (por exemplo:  https://contoso.centraldesktop.com/saml2-assertion.php).
   
   >[!NOTE]
   >Você pode obter o valor nos metadados do Central Desktop (por exemplo: *http://contoso.centraldesktop.com*).
   >  
   
   ![Configurar URL do Aplicativo](./media/active-directory-saas-central-desktop-tutorial/IC769561.png "Configurar URL do Aplicativo")
4. Na página **Configurar logon único no Central Desktop**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado no computador.
   
  ![Configurar logon único](./media/active-directory-saas-central-desktop-tutorial/IC769562.png "Configurar logon único")
5. Faça logon em seu locatário do **Central Desktop** .
6. Vá para **Configurações**, clique em **Avançadas** e em **Logon Único**.
   
  ![Configuração – Avançada](./media/active-directory-saas-central-desktop-tutorial/IC769563.png "Configuração – Avançada")
7. Na página **Configurações de Logon Único** , realize as seguintes etapas:
   
  ![Configurações de Logon Único](./media/active-directory-saas-central-desktop-tutorial/IC769564.png "Configurações de Logon Único")
   
  1. Selecione **Habilitar Logon Único do SAML v2**.
  2. No portal clássico do Azure, na página **Configurar logon único no Central Desktop**, copie o valor da **URL do Emissor** e cole-o na caixa de texto **URL de SSO**.
  3. No portal clássico do Azure, na página **Configurar logon único no Central Desktop**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de SSO**.
  4. No portal clássico do Azure, na página **Configurar logon único no Central Desktop**, copie o valor da **URL do Serviço de Saída Única** e cole-o na caixa de texto **URL de Logout de SSO**.
8. Na seção **Método de Verificação de Assinatura de Mensagem** , realize as seguintes etapas:
   
   ![Método de Verificação de Assinatura da Mensagem](./media/active-directory-saas-central-desktop-tutorial/IC769565.png "Método de Verificação de Assinatura da Mensagem")
   
  1. Selecione **Certificado**.
  2. Na lista **Certificado de SSO**, selecione **RSH SHA256**.
  3. Crie um arquivo de texto por meio do certificado baixado, copie o conteúdo do arquivo de texto e cole-o no campo **Certificado de SSO** .  
     >[!TIP]
     >Para obter mais detalhes, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)
      >  
   4. Selecione **Exibir um link para a sua página de logon do SAMLv2**.
9. Clique em **Atualizar**.
10. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
    
    ![Configurar logon único](./media/active-directory-saas-central-desktop-tutorial/IC769566.png "Configurar logon único")
    
## <a name="configure-user-provisioning"></a>Configurar provisionamento do usuário

Para que os usuários do AAD possam fazer logon, eles devem ser provisionados no aplicativo Central Desktop. Esta seção descreve como criar contas de usuário do AAD no Central Desktop.

**Para provisionar contas de usuário no Central Desktop:**
1. Faça logon no seu locatário do Central Desktop.
2. Vá para **Pessoas \> Membros Internos**.
3. Clique em **Adicionar Membros Internos**.
   
  ![Pessoas](./media/active-directory-saas-central-desktop-tutorial/IC781051.png "Pessoas")
4. Na caixa de texto **Endereço de Email dos Novos Membros**, digite uma conta do AAD que você deseja provisionar e clique em **Avançar**.
   
  ![Endereços de Email de Novos Membros](./media/active-directory-saas-central-desktop-tutorial/IC781052.png "Endereços de Email de Novos Membros")
5. Clique em **Adicionar Membro(s) interno(s)**.
   
  ![Adicionar Membro Interno](./media/active-directory-saas-central-desktop-tutorial/IC781053.png "Adicionar Membro Interno")
   
   >[!NOTE]
   >Os usuários que você adicionou receberão um email com um link de confirmação em que eles precisam clicar para ativar a conta.
   > 

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do Central Desktop ou as APIs fornecidas pelo Central Desktop para provisionar as contas de usuário do AAD.
>  

## <a name="assign-users"></a>Atribuir usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

**Para atribuir usuários ao Central Desktop, execute as seguintes etapas:**

1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração de aplicativos do **Central Desktop**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-central-desktop-tutorial/IC769567.png "Atribuir usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-central-desktop-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

