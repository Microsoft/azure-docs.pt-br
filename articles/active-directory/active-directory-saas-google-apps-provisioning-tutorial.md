---
title: "Tutorial: Configurando o Google Apps para o provisionamento automático de usuário no Azure | Microsoft Docs"
description: "Saiba como provisionar e desprovisionar automaticamente contas de usuário do Azure AD para o Google Apps."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: e8ca7fdacf8361570d88260b3c359ee6e2fd3e17
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configure-google-apps-for-automatic-user-provisioning"></a>Tutorial: configurar o Google Apps para o provisionamento automático de usuário

O objetivo desse tutorial é mostrar a você como provisionar e desprovisionar automaticamente as contas de usuário do Azure AD (Azure Active Directory) para o Google Apps.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito nesse tutorial pressupõe que você já tem os seguintes itens:

*   Um locatário do Azure Active Directory.
*   Um locatário válido do Google Apps for Work ou do Google Apps for Education. Você pode usar uma conta de avaliação gratuita para qualquer serviço.
*   Uma conta de usuário no Google Apps com permissões de Administrador de Equipe.

## <a name="assign-users-to-google-apps"></a>Atribuir usuários ao Google Apps

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e os grupos que foram "atribuídos" a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o serviço de provisionamento, você precisa decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao aplicativo Google Apps. Depois de decidir, atribua esses usuários ao aplicativo Google Apps seguindo as instruções em [Atribuir um usuário ou grupo a um aplicativo empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

> [!IMPORTANT]
> Recomendamos a atribuição de um único usuário do Azure AD ao Google Apps para testar a configuração de provisionamento. Você pode atribuir usuários e grupos adicionais mais tarde.

> Quando você atribui um usuário ao Google Apps, é necessário selecionar a função **Usuário** ou **Grupo** na caixa de diálogo de atribuição. A função de **Acesso Padrão** não funciona para provisionamento.

## <a name="enable-automated-user-provisioning"></a>Habilitar o provisionamento automatizado de usuários

Esta seção orienta você sobre o processo de conectar o Azure AD à API de provisionamento de conta de usuário do Google Apps. Ele também ajuda você a configurar o serviço de provisionamento para criar, atualizar e desabilitar contas de usuário atribuído no Google Apps com base na atribuição de usuário e de grupo no Azure AD.

>[!TIP]
>Você também pode optar por habilitar o Logon Único baseado em SAML no Google Apps, seguindo as instruções no [Portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos sejam complementares.

### <a name="configure-automatic-user-account-provisioning"></a>Configurar o provisionamento automático de conta de usuário

> [!NOTE]
> Outra opção viável para automatizar o provisionamento do usuário para o Google Apps é usar o [GADS (Google Apps Directory Sync)](https://support.google.com/a/answer/106368?hl=en). O GADS provisiona suas identidades do Active Directory local no Google Apps. Por outro lado, a solução neste tutorial provisiona seus usuários do Azure Active Directory (nuvem) e grupos habilitados para email para o Google Apps. 

1. Entre no [Console de Admin do Google Apps](http://admin.google.com/) usando sua conta de administrador e selecione **Segurança**. Se você não vê o link, ele pode estar oculto sob o menu **Mais Controles**, na parte inferior da tela.
   
    ![Selecione segurança.][10]

2. Na página **Segurança**, selecione **Referência de API**.
   
    ![Selecione Referência de API.][15]

3. Selecione **Habilitar o acesso à API**.
   
    ![Selecione Referência de API.][16]

    > [!IMPORTANT]
    > Para cada usuário que você pretende provisionar para o Google Apps, seu nome de usuário no Azure Active Directory *deve* estar vinculado a um domínio personalizado. Por exemplo, nomes de usuários parecidos com bob@contoso.onmicrosoft.com não são aceitos pelo Google Apps. Por outro lado, bob@contoso.com é aceito. É possível alterar o domínio de um usuário existente editando as propriedades dele no AD do Azure. Incluímos instruções sobre como definir um domínio personalizado para o Azure Active Directory e o Google Apps nas etapas a seguir.
      
4. Se você ainda não adicionou um nome de domínio personalizado ao Azure Active Directory, siga as seguintes etapas:
  
    a. No [Portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, selecione **Active Directory**. Na lista de diretórios, selecione o diretório. 

    b. Selecione **Nome de domínio** no painel de navegação à esquerda e, depois, clique em **Adicionar**.
     
     ![Domínio](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_1.png)

     ![Adição de domínio](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_2.png)

    c. Digite seu nome de domínio no campo **Nome de domínio** . Esse nome de domínio deve ser o mesmo nome de domínio que você pretende usar para o Google Apps. Selecione o botão **Adicionar Domínio**.
     
     ![Nome de domínio](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_3.png)

    d. Selecione **Avançar** para ir até a página de verificação. Para verificar se você é proprietário desse domínio, é preciso editar os registros DNS do domínio de acordo com os valores fornecidos nesta página. É possível optar por verificar usando **Registros MX** ou **Registros TXT**, dependendo do que você selecionar na opção **Tipo de Registro**. 
    
    Para obter instruções mais abrangentes sobre como verificar nomes de domínio com o Azure AD, consulte [Adicionar seu próprio nome de domínio ao Azure AD](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409).
     
     ![Domínio](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_4.png)

    e. Repita as etapas anteriores para todos os domínios que você pretende adicionar ao diretório.

5. Agora que confirmou todos os domínios com o Azure AD, você deve confirmá-los novamente com o Google Apps. Para cada domínio que já não estiver registrado com o Google Apps, realize as etapas a seguir:
   
    a. No [Console de Administrador do Google Apps](http://admin.google.com/), selecione **Domínios**.
     
     ![Selecione Domínios][20]

    b. Selecione **Adicionar um domínio ou um alias de domínio**.
     
     ![Adicione um novo domínio][21]

    c. Selecione **Adicionar outro domínio** e digite o nome do domínio que você deseja adicionar.
     
     ![Digite o nome de domínio][22]

    d. Selecione **Continuar e confirmar a propriedade do domínio**. Em seguida, siga as etapas para verificar se você possui o nome de domínio. Para obter instruções abrangentes sobre como verificar seu domínio com o Google Apps, consulte [Verificar sua propriedade do site com o Google Apps](https://support.google.com/webmasters/answer/35179).

    e. Repita as etapas anteriores para os domínios adicionais que você pretende adicionar ao Google Apps.
     
     > [!WARNING]
     > Se você alterar o domínio primário do locatário do Google Apps e já tiver configurado o logon único com o Azure AD, precisará repetir a etapa 3 em [Etapa 2: habilitar logon único](#step-two-enable-single-sign-on).
       
6. No [Console de Administrador do Google Apps](http://admin.google.com/), selecione **Funções de Administrador**.
   
     ![Selecione Google Apps][26]

7. Determine a conta do administrador que você gostaria de usar para gerenciar o provisionamento de usuários. Para a **função de administrador** dessa conta, edite os **Privilégios** para essa função. Verifique se você habilitou todos os **Privilégios de API de Administrador**, de modo que essa conta possa ser usada para o provisionamento.
   
     ![Selecione Google Apps][27]
   
    > [!NOTE]
    > Se estiver configurando um ambiente de produção, a melhor prática será criar uma conta do administrador no Google Apps especificamente para esta etapa. Essas contas devem ter uma função de administrador associada a elas que tem os privilégios de API necessários.
     
8. No [Portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory** > **Aplicativos Empresariais** > **Todos os aplicativos**.

9. Se você já tiver configurado o Google Apps para logon único, pesquise a instância do Google Apps usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise por **Google Apps** na galeria de aplicativos. Selecione o **Google Apps** nos resultados da pesquisa e adicione-o à lista de aplicativos.

10. Selecione a instância do Google Apps e, depois, a guia **Provisionamento**.

11. Defina o **Modo de Provisionamento** como **Automático**. 

     ![Provisionamento](./media/active-directory-saas-google-apps-provisioning-tutorial/provisioning.png)

12. Na seção **Credenciais de Administrador**, selecione **Autorizar**. Isso abre uma caixa de diálogo de autorização do Google Apps em uma nova janela do navegador.

13. Confirme que você deseja dar permissão ao Azure Active Directory para fazer alterações em seu locatário do Google Apps. Selecione **Aceitar**.
    
     ![Confirme permissões.][28]

14. No Portal do Azure, selecione **Testar Conectividade** para assegurar-se de que o Azure AD pode se conectar ao aplicativo Google Apps. Se a conexão falhar, verifique se sua conta do Google Apps tem permissões de Administrador de Equipe. Em seguida, tente a etapa **Autorizar** novamente.

15. Insira o endereço de email de uma pessoa ou um grupo que deve receber notificações de erro de provisionamento no campo **Email de Notificação**. Em seguida, selecione a caixa de seleção.

16. Selecione **Salvar**.

17. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Google Apps**.

18. Na seção **Mapeamentos de Atributo**, examine os atributos de usuário que são sincronizados do Azure AD para o Google Apps. Os atributos que são propriedades **Correspondentes** são usados para corresponder as contas de usuário do Google Apps em operações de atualização. Para confirmar eventuais alterações, selecione **Salvar**.

19. Para habilitar o serviço de provisionamento do Azure AD no Google Apps, altere o **Status de Provisionamento** para **Ativado** em **Configurações**.

20. Selecione **Salvar**.

Esse processo inicia a sincronização inicial de todos os usuários ou grupos atribuídos ao Google Apps na seção Usuários e Grupos. A sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 20 minutos enquanto o serviço está em execução. 

É possível usar a seção **Detalhes de Sincronização** para monitorar o andamento e seguir os links para os relatórios de atividade de provisionamento. Esses relatórios descrevem todas as ações executadas pelo serviço de provisionamento no aplicativo Google Apps.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configurar Logon Único](active-directory-saas-google-apps-tutorial.md)



<!--Image references-->

[10]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-auth.png
