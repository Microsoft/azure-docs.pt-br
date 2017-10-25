---
title: "Tutorial: integração do Azure Active Directory do ao Dropbox for Business | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Dropbox for Business."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 0f3a42e4-6897-4234-af84-b47c148ec3e1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 6f7616e47322242f01a13d763f71c93d4ac06a92
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configuring-dropbox-for-business-for-automatic-user-provisioning"></a>Tutorial: Configurando o Dropbox for Business para o provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que precisam ser realizadas no Dropbox for Business e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o Dropbox for Business.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

*   Um locatário do Azure Active Directory.
*   Uma assinatura habilitada para logon único do Dropbox for Business.
*   Uma conta de usuário no Dropbox for Business com permissões de Administrador de Equipe.

## <a name="assigning-users-to-dropbox-for-business"></a>Atribuindo usuários ao Dropbox for Business

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e os grupos que foram "atribuídos" a um aplicativo no Azure AD serão sincronizados.

Antes de configurar e habilitar o serviço de provisionamento, você precisa decidir quais usuários e/ou grupos no Azure AD representam os usuários que precisam de acesso ao aplicativo Dropbox for Business. Depois de decidir, atribua esses usuários ao aplicativo Dropbox for Business seguindo estas instruções:

[Atribuir um usuário ou um grupo a um aplicativo empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Dicas importantes para atribuir usuários ao Dropbox for Business

*   Recomendamos a atribuição de um único usuário do Azure AD ao Dropbox for Business para testar a configuração de provisionamento. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um usuário ao Dropbox for Business, você deve selecionar uma função de usuário válida. A função “Acesso Padrão” não funciona para o provisionamento.

## <a name="enable-automated-user-provisioning"></a>Habilitar o Provisionamento Automatizado de Usuários

Esta seção explica como conectar o Azure AD à API de provisionamento de conta de usuário do Dropbox for Business e como configurar o serviço de provisionamento para criar, atualizar e desabilitar contas de usuário atribuídas no Dropbox for Business, com base na atribuição de usuário e de grupo do Azure AD.

>[!Tip]
>Você também pode optar por habilitar o Logon Único baseado em SAML no Dropbox for Business, seguindo as instruções fornecidas no [portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-account-provisioning"></a>Para configurar o provisionamento automático de conta de usuário:

1. No [Portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory > Aplicativos Empresariais > Todos os aplicativos**.

2. Se você já tiver configurado o Dropbox for Business para logon único, pesquise a instância do Dropbox for Business usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise **Dropbox for Business** na galeria de aplicativos. Selecione o Dropbox for Business nos resultados da pesquisa e adicione-o à lista de aplicativos.

3. Selecione a instância do Dropbox for Business e, depois, a guia **Provisionamento**.

4. Defina o **Modo de Provisionamento** como **Automático**. 

    ![provisionamento](./media/active-directory-saas-dropboxforbusiness-provisioning-tutorial/provisioning.png)

5. Na seção **Credenciais de Administrador**, clique em **Autorizar**. Ela abre uma caixa de diálogo de logon do Dropbox for Business em uma nova janela do navegador.

6. Na caixa de diálogo **Entrar no Dropbox para vincular com o Azure AD**, entre no locatário do Dropbox for Business.

     ![Provisionamento do usuário](./media/active-directory-saas-dropboxforbusiness-provisioning-tutorial/ic769518.png "Provisionamento do usuário")

7. Confirme se deseja conceder permissão ao Azure Active Directory para fazer alterações em seu locatário do Dropbox for Business. Clique em **Permitir**.
    
      ![Provisionamento do usuário](./media/active-directory-saas-dropboxforbusiness-provisioning-tutorial/ic769519.png "Provisionamento do usuário")

8. No portal do Azure, clique em **Testar conectividade** para garantir que o Azure AD pode se conectar ao aplicativo Dropbox for Business. Se a conexão falhar, verifique se sua conta do Dropbox for Business tem permissões de Administrador de Equipe e repita a etapa **“Autorizar”**.

9. Insira o endereço de email de uma pessoa ou um grupo que deve receber notificações de erro de provisionamento no campo **Email de Notificação** e marque a caixa de seleção.

10. Clique em **Salvar.**

11. Na seção Mapeamentos, selecione **Sincronizar Usuários do Azure Active Directory com o Dropbox for Business.**

12. Na seção **Mapeamentos de Atributo**, examine os atributos de usuário que são sincronizados do Azure AD para o Dropbox for Business. Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário do Dropbox for Business em operações de atualização. Selecione o botão Salvar para confirmar as alterações.

13. Para habilitar o serviço de provisionamento do Azure AD no Dropbox for Business, altere o **Status de Provisionamento** para **Ativado** na seção Configurações

14. Clique em **Salvar.**

Isso inicia a sincronização inicial de todos os usuários e/ou grupos atribuídos ao Dropbox for Business na seção Usuários e Grupos. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 20 minutos, desde que o serviço esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para os relatórios de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento no aplicativo Dropbox for Business.

Agora você pode criar uma conta de teste. Aguarde até 20 minutos para confirmar se a conta foi sincronizada com o Dropbox for Business.

Um ciclo de provisionamento de usuário concluído com êxito é indicado por um status relacionado.

![Atribuir usuários](./media/active-directory-saas-dropboxforbusiness-provisioning-tutorial/IC769523.png "Atribuir usuários")


## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configurar Logon Único](active-directory-saas-dropboxforbusiness-tutorial.md)