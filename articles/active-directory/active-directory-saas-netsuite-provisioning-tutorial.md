---
title: "Tutorial: Integração do Azure Active Directory com o NetSuite | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Netsuite."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 8a6d3994-ee33-4a6f-b0a2-9d0389467f16
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 277c393536615fc8bfe8af0bc6d487115f04776c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configuring-netsuite-for-automatic-user-provisioning"></a>Tutorial: Configurando o Netsuite para o provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que precisam ser realizadas no Netsuite e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o Netsuite.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

*   Um locatário do Azure Active Directory.
*   Uma assinatura habilitada para logon único do Netsuite.
*   Uma conta de usuário no Netsuite com permissões de Administrador de Equipe.

## <a name="assigning-users-to-netsuite"></a>Atribuindo usuários ao Netsuite

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e os grupos que foram "atribuídos" a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o serviço de provisionamento, você precisa decidir quais usuários e/ou grupos no Azure AD representam os usuários que precisam de acesso ao aplicativo Netsuite. Depois de decidir, atribua esses usuários ao aplicativo Netsuite seguindo estas instruções:

[Atribuir um usuário ou um grupo a um aplicativo empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-netsuite"></a>Dicas importantes para atribuir usuários ao Netsuite

*   Recomendamos a atribuição de um único usuário do Azure AD ao Netsuite para testar a configuração de provisionamento. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um usuário ao Netsuite, você deve selecionar uma função de usuário válida. A função de "Acesso Padrão" não funciona para provisionamento.

## <a name="enable-user-provisioning"></a>Habilitar o provisionamento de usuário

Esta seção explica como conectar o Azure AD à API de provisionamento de conta de usuário do Netsuite e como configurar o serviço de provisionamento para criar, atualizar e desabilitar contas de usuário atribuídas no Netsuite, com base na atribuição de usuário e de grupo do Azure AD.

> [!TIP] 
> Você também pode optar por habilitar o Logon Único baseado em SAML no Netsuite, seguindo as instruções fornecidas no [portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos sejam complementares.

### <a name="to-configure-user-account-provisioning"></a>Para configurar o provisionamento de conta de usuário:

O objetivo desta seção é descrever como habilitar o provisionamento de contas de usuário do Active Directory no Netsuite.

1. No [Portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory > Aplicativos Empresariais > Todos os aplicativos**.

2. Se você já tiver configurado o Netsuite para logon único, pesquise a instância do Netsuite usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise **Netsuite** na galeria de aplicativos. Selecione o Netsuite nos resultados da pesquisa e adicione-o à lista de aplicativos.

3. Selecione a instância do Netsuite e, depois, a guia **Provisionamento**.

4. Defina o **Modo de Provisionamento** como **Automático**. 

    ![provisionamento](./media/active-directory-saas-netsuite-provisioning-tutorial/provisioning.png)

5. Na seção **Credenciais de Administrador**, forneça as seguintes definições de configuração:
   
    a. Na caixa de texto **Nome de Usuário Administrador**, digite um nome de conta do Netsuite que tem o perfil **Administrador do Sistema** atribuído no Netsuite.com.
   
    b. Na caixa de texto **Senha do Administrador**, digite a senha dessa conta.
      
6. No portal do Azure, clique em **Testar conectividade** para garantir que o Azure AD pode se conectar ao aplicativo Netsuite.

7. No campo **Email de Notificação**, insira o endereço de email de uma pessoa ou um grupo que deve receber notificações de erro de provisionamento e marque a caixa de seleção.

8. Clique em **Salvar.**

9. Na seção Mapeamentos, selecione **Sincronizar Usuários do Azure Active Directory com o Netsuite.**

10. Na seção **Mapeamentos de Atributo**, examine os atributos de usuário que são sincronizados do Azure AD para o Netsuite. Observe que os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário do Netsuite em operações de atualização. Selecione o botão Salvar para confirmar as alterações.

11. Para habilitar o serviço de provisionamento do Azure AD no Netsuite, altere o **Status de Provisionamento** para **Ativado** na seção Configurações

12. Clique em **Salvar.**

Isso inicia a sincronização inicial de todos os usuários e/ou grupos atribuídos ao Netsuite na seção Usuários e Grupos. Observe que a sincronização inicial leva mais tempo do que as sincronizações posteriores, que ocorrem aproximadamente a cada 20 minutos, desde que o serviço esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para os relatórios de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento no aplicativo Netsuite.

Agora você pode criar uma conta de teste. Aguarde até 20 minutos para confirmar se a conta foi sincronizada com o Netsuite.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configurar Logon Único](active-directory-saas-netsuite-tutorial.md)