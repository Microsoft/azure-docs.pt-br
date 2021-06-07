---
title: 'Tutorial: Configurar GoToMeeting para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o GoToMeeting.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: ff377b0f93968eb6743187e4e659f4e888e5010e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358892"
---
# <a name="tutorial-configure-gotomeeting-for-automatic-user-provisioning"></a>Tutorial: Configurar GoToMeeting para provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que precisam ser executadas no GoToMeeting e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o GoToMeeting.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

*   Um locatário do Azure Active Directory.
*   Uma assinatura do GoToMeeting habilitada para logon único.
*   Uma conta de usuário no GoToMeeting com permissões de Administrador de Equipe.

## <a name="assigning-users-to-gotomeeting"></a>Atribuir usuários ao GoToMeeting

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e os grupos que foram "atribuídos" a um aplicativo no Azure AD serão sincronizados.

Antes de configurar e habilitar o serviço de provisionamento, você precisa decidir quais usuários e/ou grupos no Azure AD representam os usuários que precisam de acesso ao seu aplicativo GoToMeeting. Depois de decidir, você pode atribuir esses usuários ao seu aplicativo GoToMeeting seguindo estas instruções:

[Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-gotomeeting"></a>Dicas importantes para atribuir usuários ao GoToMeeting

*   Recomendamos atribuir um único usuário do Azure AD ao GoToMeeting para testar a configuração de provisionamento. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um usuário ao GoToMeeting, você precisa selecionar uma função de usuário válida. A função de "Acesso Padrão" não funciona para provisionamento.

## <a name="enable-automated-user-provisioning"></a>Habilitar o Provisionamento Automatizado de Usuários

Esta seção orienta você quanto à conexão do Azure AD com a API de provisionamento de conta de usuário do GoToMeeting e à configuração do serviço de provisionamento, a fim de criar, atualizar e desabilitar contas de usuário atribuídas no GoToMeeting com base na atribuição de usuário e de grupo do Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o GoToMeeting seguindo as instruções fornecidas no [Portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-account-provisioning"></a>Para configurar o provisionamento automático de conta de usuário:

1. No [Portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory > Aplicativos Empresariais > Todos os aplicativos**.

1. Se já tiver configurado o GoToMeeting para logon único, pesquise por sua instância do GoToMeeting usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise por **GoToMeeting** na galeria de aplicativos. Selecione GoToMeeting nos resultados da pesquisa e adicione-o à lista de aplicativos.

1. Selecione sua instância do GoToMeeting e selecione a guia **Provisionamento**.

1. Defina o **Modo de Provisionamento** como **Automático**. 

    ![Captura de tela da guia Provisionamento para o GoToMeeting no portal do Azure. O Modo de Provisionamento está definido como Automático e Nome do Usuário Administrador, Senha e Testar Conectividade estão realçados.](./media/citrixgotomeeting-provisioning-tutorial/provisioning.png)

1. Na seção “Credenciais de Administrador”, realize as seguintes etapas:
   
    a. Na caixa de texto **Nome de Usuário Administrador do GoToMeeting**, digite o nome de usuário de um administrador.

    b. Na caixa de texto **Senha do Administrador do GoToMeeting**, digite a senha do administrador.

1. No portal do Azure, clique em **Testar conexão** para garantir que o Azure AD possa se conectar ao seu aplicativo GoToMeeting. Se a conexão falhar, verifique se a sua conta do GoToMeeting tem permissões de Administrador de Equipe e repita a etapa **"Credenciais de Administrador"**.

1. Insira o endereço de email de uma pessoa ou um grupo que deve receber notificações de erro de provisionamento no campo **Email de Notificação** e marque a caixa de seleção.

1. Clique em **Salvar.**

1. Na seção Mapeamentos, selecione **Sincronizar Usuários do Azure Active Directory com o GoToMeeting.**

1. Na seção **Mapeamentos de Atributo**, examine os atributos de usuário que são sincronizados do Azure AD para o GoToMeeting. Os atributos selecionados como propriedades **Correspondentes** serão usados para fazer a correspondência das contas de usuário no GoToMeeting para operações de atualização. Selecione o botão Salvar para confirmar as alterações.

1. Para habilitar o serviço de provisionamento do Azure AD para o GoToMeeting, altere o **Status de Provisionamento** para **Ativado** na seção Configurações

1. Clique em **Salvar.**

Isso iniciará a sincronização inicial dos usuários e/ou grupos atribuídos ao GoToMeeting na seção Usuários e Grupos. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em execução. Use a seção **Detalhes da Sincronização** para monitorar o progresso e siga os links para os logs de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento em seu aplicativo GoToMeeting.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Configurar Logon Único](./citrix-gotomeeting-tutorial.md)