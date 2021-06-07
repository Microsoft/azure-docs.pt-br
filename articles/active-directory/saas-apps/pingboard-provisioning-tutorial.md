---
title: 'Tutorial: Provisionamento de usuário para o Pingboard – Azure AD'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar contas de usuário automaticamente para o Pingboard.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: arvinh
ms.openlocfilehash: ac36f5d6d1f57fd8453c54bcc8cf19dd964f47f6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94357888"
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>Tutorial: Configurar Pingboard para provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que você precisa seguir para habilitar o provisionamento automático e o desprovisionamento de contas de usuário do Azure AD (Active Directory do Azure) para o Pingboard.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Um locatário do Azure AD
* Uma [Conta Pro](https://pingboard.com/pricing) do locatário do Pingboard
* Uma conta de usuário no Pingboard com permissões de administrador

> [!NOTE]
> A integração do provisionamento do Azure AD depende da [API Pingboard](https://pingboard.docs.apiary.io/#) que está disponível para a sua conta.

## <a name="assign-users-to-pingboard"></a>Atribuir usuários ao Pingboard

O Azure AD usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários atribuídos a um aplicativo no Azure AD são sincronizados. 

Antes de configurar e habilitar o serviço de provisionamento, você deve decidir quais usuários, no Azure AD, precisam de acesso ao aplicativo Pingboard. Então, você pode atribuir esses usuários ao seu aplicativo Pingboard seguindo as instruções aqui:

[Atribuir um usuário a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>Dicas importantes para atribuir usuários ao Pingboard

Recomendamos que você atribua um único usuário do Azure AD ao Pingboard para testar a configuração de provisionamento. Você pode atribuir outros usuários mais tarde.

## <a name="configure-user-provisioning-to-pingboard"></a>Configurar o provisionamento de usuário para o Pingboard 

Esta seção orienta você sobre como conectar o Azure AD à API de provisionamento de conta de usuário do Pingboard. Você também configura o serviço de provisionamento para criar, atualizar e desabilitar contas de usuários atribuídas no Pingboard baseadas em atribuições de usuários no AD do Azure.

> [!TIP]
> Para habilitar o logon único baseado em SAML para o Pingboard, siga as instruções fornecidas no [Portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-ad"></a>Para configurar o provisionamento automático de conta de usuário para Pingboard no Azure AD

1. No [Portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory** > **Aplicativos Empresariais** > **Todos os aplicativos**.

1. Se você já configurou o Pingboard para logon único, pesquise sua instância do Pingboard usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e procure **Pingboard** na galeria de aplicativos. Selecione **Pingboard** nos resultados da pesquisa e adicione-o à sua lista de aplicativos.

1. Selecione sua instância do Pingboard e, em seguida, selecione a guia **Provisionamento**.

1. Defina o **Modo de Provisionamento** como **Automático**.

    ![Provisionamento de Pingboard](./media/pingboard-provisioning-tutorial/pingboardazureprovisioning.png)

1. Sob o **credenciais de administrador** seção, use as seguintes etapas:

    a. Em **URL do Locatário**, insira `https://your_domain.pingboard.com/scim/v2` e substitua "your_domain" pelo seu domínio real.

    b. Entre no [Pingboard](https://pingboard.com/) usando sua conta do administrador.

    c. Selecione **Complementos** > **Integrações** > **Azure Active Directory**.

    d. Acesse a guia **Configurar** e selecione **Habilitar provisionamento de usuário do Azure**.

    e. Copie o token em **Token de Portador OAuth** e insira-o em **Token Secreto**.

1. No portal do Azure, selecione **Testar Conexão** para testar se o Azure AD pode se conectar ao seu aplicativo Pingboard. Se a conexão falhar, teste a sua conta do Pingboard tem permissões de administrador e tente a **Testar Conexão** etapa novamente.

1. Insira o endereço de email de uma pessoa ou um grupo que você deseja que receba notificações de erro de provisionamento no campo **Email de Notificação**. Marque a caixa de seleção logo abaixo.

1. Selecione **Salvar**.

1. Na seção **Mapeamentos**, selecione **Sincronizar usuários do Azure Active Directory com o Pingboard**.

1. Na seção **Mapeamentos de Atributo**, verifique os atributos do usuário a serem sincronizados entre o Azure AD e o Pingboard. Os atributos selecionados como propriedades de **Correspondência** são usados para associar as contas de usuário no Pingboard para operações de atualização. Para confirmar eventuais alterações, selecione **Salvar**. Para obter mais informações, consulte [Personalizar mapeamentos de atributo de provisionamento do usuário](../app-provisioning/customize-application-attributes.md).

1. Para habilitar o serviço de provisionamento do Azure AD para o Pingboard, na seção **Configurações**, altere o **Status de Provisionamento** para **Ativado**.

1. Selecione **Salvar** para iniciar a sincronização inicial de usuários atribuídos ao Pingboard.

A sincronização inicial leva mais tempo para ser executada do que as sincronizações, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o andamento e seguir os links para os logs da atividade de provisionamento. Os logs descrevem todas as ações realizadas pelo serviço de provisionamento no seu aplicativo Pingboard.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar provisionamento de conta de usuário para aplicativos empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Configurar Logon Único](pingboard-tutorial.md)
