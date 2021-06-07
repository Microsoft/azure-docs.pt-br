---
title: 'Tutorial: Provisionamento de usuário para o Asana – Azure AD'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Asana.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: arvinh
ms.reviewer: celested
ms.openlocfilehash: 4abc117ae0e983cf684f0e70a363758f9be196aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94359419"
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>Tutorial: Configurar o Asana para provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que você precisa realizar no Asana e no Azure AD (Azure Active Directory) para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o Asana.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Um locatário do Azure AD
* Um locatário do Asana com um plano [Enterprise](https://www.asana.com/pricing) ou melhor habilitado
* Uma conta de usuário no Asana com permissões de administrador

> [!NOTE]
> A integração do provisionamento do Azure AD depende da [API do Asana](https://asana.com/developers/api-reference/users) que está disponível para o Asana.

## <a name="assign-users-to-asana"></a>Atribuir usuários ao Asana

O Azure AD usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o serviço de provisionamento, você deve decidir quais usuários, no Azure AD, precisam de acesso ao aplicativo Asana. Então, você pode atribuir esses usuários ao seu aplicativo Asana seguindo as instruções aqui:

[Atribuir um usuário a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>Dicas importantes para atribuir usuários ao Asana

Recomendamos que você atribua um único usuário do Azure AD ao Asana para testar a configuração de provisionamento. Você pode atribuir outros usuários mais tarde.

## <a name="configure-user-provisioning-to-asana"></a>Configurar o provisionamento de usuário ao Asana

Esta seção orienta você sobre como conectar o Azure AD à API de provisionamento de conta de usuário do Asana. Você também configura o serviço de provisionamento para criar, atualizar e desabilitar contas de usuário atribuídas no Asana com base nas atribuições de usuário do Azure AD.

> [!TIP]
> Para habilitar o logon único baseado em SAML para o Asana, siga as instruções fornecidas no [Portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>Para configurar o provisionamento automático da conta do usuário para o Asana no Azure AD

1. No [Portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory** > **Aplicativos Empresariais** > **Todos os aplicativos**.

1. Se você já configurou o Asana para logon único, pesquise sua instância do Asana usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e procure **Asana** na galeria de aplicativos. Selecione **Asana** nos resultados da pesquisa e adicione-o à sua lista de aplicativos.

1. Selecione sua instância do Asana e, em seguida, selecione a guia **Provisionamento**.

1. Defina o **Modo de Provisionamento** como **Automático**.

    ![Provisionamento do Asana](./media/asana-provisioning-tutorial/asanaazureprovisioning.png)

1. Na seção de **Credenciais de Administrador**, siga essas instruções para gerar o token e inseri-la em **Token Secreto**:

    a. Entre no [Asana](https://app.asana.com) usando sua conta do administrador.

    b. Selecione a foto do perfil da barra superior e selecione as configurações atuais de nome da organização.

    c. Acesse a guia **Contas de Serviço**.

    d. Selecione **Adicionar Conta de Serviço**.

    e. Atualize **Nome** e **Sobre**, bem como a foto do perfil, conforme necessário. Copie o token em **Token** e selecione-o em **Salvar Alterações**.

1. No Portal do Azure, selecione **Testar Conectividade** para verificar se o Azure AD pode se conectar ao aplicativo Asana. Se a conexão falhar, verifique se sua conta do Asana tem permissões de administrador e tente a etapa **Testar Conectividade** novamente.

1. Insira o endereço de email de uma pessoa ou um grupo que você deseja que receba notificações de erro de provisionamento no campo **Email de Notificação**. Marque a caixa de seleção logo abaixo.

1. Selecione **Salvar**.

1. Na seção **Mapeamentos**, selecione **Sincronizar usuários do Azure Active Directory com o Asana**.

1. Na seção **Mapeamentos de Atributo**, verifique os atributos do usuário a serem sincronizados entre o Azure AD e o Asana. Os atributos selecionados como propriedades **Correspondentes** serão usados para fazer a correspondência entre as contas de usuário no Asana para operações de atualização. Para confirmar eventuais alterações, selecione **Salvar**. Para obter mais informações, consulte [Personalizar mapeamentos de atributo de provisionamento de usuário](../app-provisioning/customize-application-attributes.md).

1. Para habilitar o serviço de provisionamento do Azure AD para o Asana, na seção **Configurações**, altere o **Status de Provisionamento** para **Ativado**.

1. Selecione **Salvar**.

Agora, a sincronização inicial de todos os usuários atribuídos ao Asana na seção **Usuários** é iniciada. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o andamento e seguir os links para os logs da atividade de provisionamento. Os logs de auditoria descrevem todas as ações executadas pelo serviço de provisionamento no aplicativo Asana.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar provisionamento de conta de usuário para aplicativos empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Configurar Logon Único](asana-tutorial.md)
