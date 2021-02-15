---
title: 'Tutorial: Configurando o Velpic para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Velpic.
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
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94354896"
---
# <a name="tutorial-configuring-velpic-for-automatic-user-provisioning"></a>Tutorial: Configurando o Velpic para o provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que você precisa executar no Velpic e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o Velpic.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Um locatário do Azure Active Directory
* Um locatário do Velpic com o [plano Enterprise](https://www.velpic.com/pricing.html) ou melhor habilitado
* Uma conta de usuário no Velpic com permissões de administrador

## <a name="assigning-users-to-velpic"></a>Atribuindo usuários ao Velpic

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e grupos que foram "atribuídos" a um aplicativo no Azure AD serão sincronizados. 

Antes de configurar e habilitar o serviço de provisionamento, você precisará decidir quais usuários e/ou grupos no Azure AD representam os usuários que precisam de acesso ao seu aplicativo Velpic. Depois de decidir, você pode atribuir esses usuários ao seu aplicativo Velpic seguindo estas instruções:

[Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-velpic"></a>Dicas importantes para atribuir usuários ao Velpic

* É recomendável que um único usuário do Azure AD seja atribuído ao Velpic para testar a configuração de provisionamento. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Velpic, você deve selecionar a função de **usuário** ou outra função válida específica do aplicativo (se disponível) na caixa de diálogo de atribuição. Observe que a função de **acesso padrão** não funciona para provisionamento e esses usuários serão ignorados.

## <a name="configuring-user-provisioning-to-velpic"></a>Configurando o provisionamento de usuário para o Velpic

Esta seção orienta você pela conexão do Azure AD com a API de provisionamento de conta de usuário do Velpic e pela configuração do serviço de provisionamento para criar, atualizar e desabilitar contas de usuário atribuídas no Velpic com base na atribuição de usuário e de grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o Sign-On único baseado em SAML para Velpic, seguindo as instruções fornecidas em [portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-account-provisioning-to-velpic-in-azure-ad"></a>Para configurar o provisionamento automático de conta de usuário para o Velpic no Azure AD:

1. Na [portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory > aplicativos empresariais > todos os aplicativos**  .

2. Se você já tiver configurado o Velpic para logon único, pesquise sua instância do Velpic usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise **Velpic** na Galeria de aplicativos. Selecione Velpic nos resultados da pesquisa e adicione-o à lista de aplicativos.

3. Selecione sua instância do Velpic e, em seguida, selecione a guia **provisionamento** .

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Provisionamento do Velpic](./media/velpic-provisioning-tutorial/Velpic1.png)

5. Na seção **credenciais de administrador** , insira a **URL do locatário&token secreto** de Velpic. (Você pode encontrar esses valores em sua conta do Velpic: **gerenciar**  >  **Integração**  >  do **Plug-in**  >  **Scim** )

    ![Valores de autorização](./media/velpic-provisioning-tutorial/Velpic2.png)

6. No portal do Azure, clique em **testar conexão** para garantir que o Azure ad possa se conectar ao seu aplicativo Velpic. Se a conexão falhar, verifique se sua conta do Velpic tem permissões de administrador e repita a etapa 5.

7. Insira o endereço de email de uma pessoa ou grupo que deve receber notificações de erro de provisionamento no campo **Email de Notificação** e marque a caixa de seleção abaixo.

8. Clique em **Salvar**.

9. Na seção mapeamentos, selecione **sincronizar Azure Active Directory usuários para Velpic**.

10. Na seção **mapeamentos de atributo** , examine os atributos de usuário que serão sincronizados do Azure ad para o Velpic. Observe que os atributos selecionados como propriedades **correspondentes** serão usados para corresponder as contas de usuário no Velpic para operações de atualização. Selecione o botão Salvar para confirmar as alterações.

11. Para habilitar o serviço de provisionamento do Azure AD para o Velpic, altere o **status de provisionamento** para **ativado** na seção **configurações**

12. Clique em **Salvar**.

Isso iniciará a sincronização inicial de todos os usuários e/ou grupos atribuídos ao Velpic na seção usuários e grupos. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em execução. É possível usar a seção **Detalhes de Sincronização** para monitorar o andamento e seguir os links para os relatórios de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)