---
title: "Tutorial: integração do Azure Active Directory com a Área Restrita do Salesforce | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e a Área Restrita Salesforce."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: bab73fda-6754-411d-9288-f73ecdaa486d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: jeedes
ms.openlocfilehash: 7d3c655a754f83284c386d2007c604a731367814
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configuring-salesforce-sandbox-for-automatic-user-provisioning"></a>Tutorial: configurar a Área Restrita Salesforce para provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que precisam ser executadas na Área Restrita Salesforce e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para a Área Restrita do Salesforce.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

*   Um locatário do Azure Active Directory.
*   É preciso ter um locatário válido para a Área Restrita Salesforce for Work ou Área Restrita Salesforce for Education. Você pode usar uma conta de avaliação gratuita para qualquer serviço.
*   Uma conta de usuário na Área Restrita Salesforce com permissões de Administrador de Equipe.

## <a name="assigning-users-to-salesforce-sandbox"></a>Atribuir usuários à Área Restrita Salesforce

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e os grupos que foram "atribuídos" a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o serviço de provisionamento, você precisará decidir quais usuários e/ou grupos no Azure AD representam os usuários que precisam de acesso ao seu aplicativo Área Restrita Salesforce. Depois de decidir, atribua esses usuários ao seu aplicativo Área Restrita Salesforce seguindo estas instruções:

[Atribuir um usuário ou um grupo a um aplicativo empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce-sandbox"></a>Dicas importantes para atribuir usuários à Área Restrita Salesforce

* Recomendamos a atribuição de um único usuário do Azure AD à Área Restrita Salesforce para testar a configuração de provisionamento. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário à Área Restrita Salesforce, você deve selecionar uma função de usuário válida. A função de "Acesso Padrão" não funciona para provisionamento.

> [!NOTE]
> Este aplicativo importa funções personalizadas da Área Restrita Salesforce como parte do processo de provisionamento, que o cliente deve desejar selecionar durante a atribuição de usuários.

## <a name="enable-automated-user-provisioning"></a>Habilitar o provisionamento automatizado de usuários

Esta seção orienta você pela conexão do Azure AD com a API de provisionamento de conta de usuário da Área Restrita Salesforce e pela configuração do serviço de provisionamento, a fim de criar, atualizar e desabilitar contas de usuário atribuídas na Área Restrita Salesforce com base na atribuição de usuário e de grupo do Azure AD.

>[!Tip]
>Você também pode optar por habilitar o Logon Único baseado em SAML para a Área Restrita Salesforce, seguindo as instruções fornecidas no [Portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-account-provisioning"></a>Como configurar o provisionamento de contas de usuário automático:

O objetivo desta seção é descrever como habilitar o provisionamento de contas de usuário do Active Directory na Área Restrita Salesforce.

1. No [Portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory > Aplicativos Empresariais > Todos os aplicativos**.

2. Se você já tiver configurado a Área Restrita Salesforce para logon único, pesquise sua instância da Área Restrita Salesforce usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise **Área Restrita Salesforce** na galeria de aplicativos. Selecione a Área Restrita Salesforce nos resultados da pesquisa e adicione-o à lista de aplicativos.

3. Selecione sua instância da Área Restrita Salesforce e selecione a guia **Provisionamento**.

4. Defina o **Modo de Provisionamento** como **Automático**. 
    ![provisionamento](./media/active-directory-saas-salesforce-sandbox-provisioning-tutorial/provisioning.png)

5. Na seção **Credenciais de Administrador**, forneça as seguintes configurações:
   
    a. Na caixa de texto **Nome de Usuário Administrador**, digite o nome da conta da Área Restrita Salesforce com o perfil **Administrador de Sistema** do Salesforce.com atribuído.
   
    b. Na caixa de texto **Senha do Administrador**, digite a senha desta conta.

6. Para obter o token de segurança da Área Restrita Salesforce, abra uma nova guia e entre na mesma conta do administrador da Área Restrita Salesforce. No canto superior direito da página, clique em seu nome e, em seguida, clique em **Minhas Configurações**.

     ![Habilitar o provisionamento automático de usuário](./media/active-directory-saas-salesforce-sandbox-provisioning-tutorial/sf-my-settings.png "Habilitar o provisionamento de usuário automático")
7. No painel de navegação esquerdo, clique em **Pessoal** para expandir a seção correspondente e, em seguida, clique em **Redefinir Meu Token de Segurança**.
  
    ![Habilitar o provisionamento automático de usuário](./media/active-directory-saas-salesforce-sandbox-provisioning-tutorial/sf-personal-reset.png "Habilitar o provisionamento de usuário automático")
8. Na página **Redefinir Meu Token de Segurança**, clique no botão **Redefinir Token de Segurança**.

    ![Habilitar o provisionamento automático de usuário](./media/active-directory-saas-salesforce-sandbox-provisioning-tutorial/sf-reset-token.png "Habilitar o provisionamento de usuário automático")
9. Marque a caixa de entrada de email associada a essa conta de administrador. Procure um email do Salesforce Sandbox.com que contenha o novo token de segurança.
10. Copie o token, vá até a janela do Azure AD e cole-o no campo **Token do Soquete**.

11. No portal do Azure, clique em **Testar Conexão** para garantir que o Azure AD possa se conectar ao seu aplicativo Área Restrita Salesforce.

12. Insira o endereço de email de uma pessoa ou grupo que deve receber notificações de erro de provisionamento no campo **Email de Notificação** e marque a caixa de seleção.

13. Clique em **Salvar.**  
    
14.  Na seção Mapeamentos, selecione **Sincronizar Usuários do Azure Active Directory com a Área Restrita Salesforce.**

15. Na seção **Mapeamentos de Atributo**, revise os atributos de usuário que serão sincronizados do Azure AD para a Área Restrita Salesforce. Observe que os atributos selecionados como propriedades **Correspondentes** serão usados para corresponder as contas de usuário na Área Restrita Salesforce para operações de atualização. Selecione o botão Salvar para confirmar as alterações.

16. Para habilitar o serviço de provisionamento do Azure AD para a Área Restrita Salesforce, altere o **Status de Provisionamento** para **Ativado** na seção Configurações

17. Clique em **Salvar.**


Isso iniciará a sincronização inicial de todos os usuários e/ou grupos atribuídos à Área Restrita Salesforce na seção Usuários e Grupos. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 20 minutos, desde que o serviço esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para os relatórios de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento em seu aplicativo Área Restrita Salesforce.

Agora você pode criar uma conta de teste. Aguarde 20 minutos para verificar se a conta foi sincronizada com o Salesforce.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configurar Logon Único](active-directory-saas-salesforcesandbox-tutorial.md)