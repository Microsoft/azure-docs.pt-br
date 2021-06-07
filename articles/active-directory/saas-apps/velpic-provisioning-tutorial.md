---
title: 'Tutorial: Configurar o Velpic para provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Velpic.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: zhchia
ms.openlocfilehash: cdd4fb96a42d154ccd8b508950283978ddf58ef4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94354896"
---
# <a name="tutorial-configuring-velpic-for-automatic-user-provisioning"></a>Tutorial: Configurar o Velpic para provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que você precisa seguir no Velpic e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para Velpic.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Um locatário do Azure Active Directory
* Um locatário do Velpic com o [plano Enterprise](https://www.velpic.com/pricing.html) ou superior habilitado
* Uma conta de usuário no Velpic com permissões de Administrador

## <a name="assigning-users-to-velpic"></a>Atribuir usuários ao Velpic

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e grupos que foram "atribuídos" a um aplicativo no Azure AD serão sincronizados. 

Para configurar e habilitar o serviço de provisionamento, você precisará decidir quais usuários e/ou grupos no Azure AD representam os usuários que precisam de acesso ao seu aplicativo Velpic. Depois de decidir, você poderá atribuir esses usuários ao seu aplicativo Velpic seguindo as instruções aqui:

[Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-velpic"></a>Dicas importantes para atribuir usuários ao Velpic

* Recomendamos que um só usuário do Azure AD seja atribuído ao Velpic para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Velpic, é necessário selecionar a função **Usuário** ou outra função válida específica ao aplicativo (se disponível) na caixa de diálogo de atribuição. A função **Acesso Padrão** não funciona para o provisionamento e esses usuários serão ignorados.

## <a name="configuring-user-provisioning-to-velpic"></a>Configurar o provisionamento de usuário para Velpic

Esta seção explica como conectar o Azure AD à API de provisionamento de conta de usuário do Velpic e configurar o serviço de provisionamento, a fim de criar, atualizar e desabilitar contas de usuário atribuídas no Velpic com base na atribuição de usuário e de grupo do Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único com base em SAML para o Velpic, seguindo as instruções fornecidas no [portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-account-provisioning-to-velpic-in-azure-ad"></a>Para configurar o provisionamento automático da conta do usuário para o Velpic no Azure AD:

1. No [Portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory > Aplicativos Empresariais > Todos os aplicativos**.

2. Se você já tiver configurado o Velpic para logon único, procure sua instância do Velpic usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e procure **Velpic** na galeria de aplicativos. Selecione Velpic nos resultados da pesquisa e adicione-o à sua lista de aplicativos.

3. Selecione sua instância do Velpic e a guia **Provisionamento**.

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Provisionamento do Velpic](./media/velpic-provisioning-tutorial/Velpic1.png)

5. Na seção **Credenciais de Administrador**, insira a **URL do Locatário e Token Secreto** do Velpic (você pode encontrar esses valores em sua conta do Velpic: **Gerenciar** > **Integração** > **Plug-in** > **SCIM**)

    ![Valores de Autorização](./media/velpic-provisioning-tutorial/Velpic2.png)

6. No portal do Azure, clique em **Testar Conectividade** para garantir que o Azure AD possa se conectar ao seu aplicativo Velpic. Se a conexão falhar, verifique se a sua conta do Velpic tem permissões de Administrador e repita a etapa 5.

7. Insira o endereço de email de uma pessoa ou grupo que deve receber notificações de erro de provisionamento no campo **Email de Notificação** e marque a caixa de seleção abaixo.

8. Clique em **Salvar**.

9. Na seção Mapeamentos, selecione **Sincronizar Usuários do Azure Active Directory com o Velpic**.

10. Na seção **Mapeamentos de Atributo**, verifique os atributos do usuário que serão sincronizados entre o Azure AD e o Velpic. Observe que os atributos selecionados como propriedades de **Correspondência** serão usados para associação com as contas de usuário no Velpic para operações de atualização. Selecione o botão Salvar para confirmar as alterações.

11. Para habilitar o serviço de provisionamento do Azure AD para o Velpic, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**

12. Clique em **Save** (Salvar).

Isso iniciará a sincronização inicial de todos os usuários e/ou grupos atribuídos ao Velpic na seção Usuários e Grupos. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em execução. É possível usar a seção **Detalhes de Sincronização** para monitorar o andamento e seguir os links para os relatórios de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)