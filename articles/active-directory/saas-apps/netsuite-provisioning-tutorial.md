---
title: 'Tutorial: Configurar o NetSuite OneWorld para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o NetSuite OneWorld.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: b1c03bafd6d97dd6a60defee00d4efe854315631
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648077"
---
# <a name="tutorial-configuring-netsuite-for-automatic-user-provisioning"></a>Tutorial: Configuração do NetSuite para o provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que você precisa executar no NetSuite OneWorld e no Azure AD para provisionar e descontinuar automaticamente as contas de usuário do Azure AD para o NetSuite.

> [!WARNING]
> Essa integração de provisionamento deixará de funcionar com o lançamento da atualização do NetSuite na primavera de 2021 devido a uma alteração nas APIs do NetSuite que são usadas pela Microsoft para provisionar usuários no NetSuite.  Essa atualização atingirá os clientes do NetSuite entre fevereiro e abril de 2021. Como resultado disso, a funcionalidade de provisionamento do aplicativo NetSuite na galeria de aplicativos empresariais do Azure Active Directory será removida em breve. A funcionalidade de SSO do aplicativo permanecerá intacta. A Microsoft está trabalhando com o NetSuite para criar uma nova integração de provisionamento modernizada, mas atualmente não há previsão de quando isso será concluído.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

*   Um locatário do Azure Active Directory.
*   Uma assinatura do NetSuite OneWorld. Observe que o provisionamento automático de usuários atualmente é suportado apenas com o NetSuite OneWorld.
*   Uma conta de usuário no Netsuite com permissões de administrador.
*   A integração com o Azure AD requer uma isenção de 2FA. Entre em contato com a equipe de suporte do NetSuite para solicitar essa isenção.

## <a name="assigning-users-to-netsuite-oneworld"></a>Atribuindo usuários ao NetSuite OneWorld

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e os grupos que foram "atribuídos" a um aplicativo no Azure AD são sincronizados.

Para configurar e habilitar o provisionamento automático de usuário, decida quais usuários e/ou grupos no Azure AD precisam de acesso ao aplicativo NetSuite. Depois de decidir, atribua esses usuários ao aplicativo NetSuite seguindo estas instruções:

[Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-netsuite-oneworld"></a>Dicas importantes para atribuir usuários ao NetSuite OneWorld

*   Recomendamos que somente um usuário do Azure AD seja atribuído ao NetSuite para testar a configuração de provisionamento. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um usuário ao NetSuite, você deve selecionar uma função de usuário válida. A função de "Acesso Padrão" não funciona para provisionamento.

## <a name="enable-user-provisioning"></a>Habilitar o provisionamento de usuário

Esta seção explica como conectar o Azure AD à API de provisionamento de conta de usuário do NetSuite e como configurar o serviço de provisionamento para criar, atualizar e desabilitar contas de usuário atribuídas no Netsuite, com base na atribuição de usuário e de grupo do Azure AD.

> [!TIP] 
> Você também pode optar por habilitar o Logon Único baseado em SAML no NetSuite, seguindo as instruções fornecidas no [portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos sejam complementares.

### <a name="to-configure-user-account-provisioning"></a>Para configurar o provisionamento de conta de usuário:

O objetivo desta seção é descrever como habilitar o provisionamento de contas de usuário do Active Directory no NetSuite.

1. No [Portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory > Aplicativos Empresariais > Todos os aplicativos**.

1. Se você já tiver configurado o NetSuite para logon único, pesquise a instância do NetSuite usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise **NetSuite** na galeria de aplicativos. Selecione o NetSuite nos resultados da pesquisa e adicione-o à lista de aplicativos.

1. Selecione a instância do NetSuite e, depois, a guia **Provisionamento**.

1. Defina o **Modo de Provisionamento** como **Automático**. 

    ![A captura de tela mostra a página Provisionamento do NetSuite, com o Modo de Provisionamento definido como Automático e outros valores que você pode definir.](./media/netsuite-provisioning-tutorial/provisioning.png)

1. Na seção **Credenciais de Administrador**, forneça as seguintes definições de configuração:
   
    a. Na caixa de texto **Nome de Usuário Administrador**, digite um nome de conta do NetSuite que tem o perfil **Administrador do Sistema** atribuído no NetSuite.com.
   
    b. Na caixa de texto **Senha do Administrador**, digite a senha dessa conta.
      
1. No portal do Azure, clique em **Testar Conectividade** para garantir que o Azure AD pode se conectar ao aplicativo NetSuite.

1. No campo **Email de Notificação**, insira o endereço de email de uma pessoa ou um grupo que deve receber notificações de erro de provisionamento e marque a caixa de seleção.

1. Clique em **Salvar.**

1. Na seção Mapeamentos, selecione **Sincronizar Usuários do Azure Active Directory com o NetSuite.**

1. Na seção **Mapeamentos de Atributos**, examine os atributos de usuário que são sincronizados do Azure AD para o NetSuite. Observe que os atributos selecionados como propriedades **Correspondentes** são usados para corresponder as contas de usuário do NetSuite em operações de atualização. Selecione o botão Salvar para confirmar as alterações.

1. Para habilitar o serviço de provisionamento do Azure AD no NetSuite, altere o **Status de Provisionamento** para **Ativado** na seção Configurações

1. Clique em **Salvar.**

Isso inicia a sincronização inicial de todos os usuários e/ou grupos atribuídos ao NetSuite na seção Usuários e Grupos. Observe que a sincronização inicial leva mais tempo do que as sincronizações posteriores, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para os logs de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento em seu aplicativo NetSuite.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Configurar Logon Único](netsuite-tutorial.md)
