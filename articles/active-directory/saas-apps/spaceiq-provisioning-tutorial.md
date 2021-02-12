---
title: 'Tutorial: Configurar SpaceIQ para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no SpaceIQ.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/07/2019
ms.author: Zhchia
ms.openlocfilehash: a5bd3c39c293880e31ebe63f85b3814725f6027a
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96347286"
---
# <a name="tutorial-configure-spaceiq-for-automatic-user-provisioning"></a>Tutorial: Configurar o SpaceIQ para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no SpaceIQ e no Azure AD (Azure Active Directory) a fim de configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no SpaceIQ.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do SpaceIQ](https://spaceiq.com/)
* Uma conta de usuário no SpaceIQ com permissões de Administrador.

## <a name="assigning-users-to-spaceiq"></a>Como atribuir usuários ao SpaceIQ

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou os grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, decida quais usuários e/ou grupos no Azure AD precisam ter acesso ao SpaceIQ. Depois de decidir isso, você poderá atribuir esses usuários e/ou grupos ao SpaceIQ seguindo estas instruções:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-spaceiq"></a>Dicas importantes para atribuir usuários ao SpaceIQ

* Recomendamos que somente um usuário do Azure AD seja atribuído ao SpaceIQ para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao SpaceIQ, você precisará selecionar qualquer função válida específica do aplicativo (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="set-up-spaceiq-for-provisioning"></a>Configurar o SpaceIQ para o provisionamento

1. Entre no [Console de Administração do SpaceIQ](https://main.spaceiq.com/login/). Procure **Configurações** selecionando-a no menu suspenso, no canto superior direito da tela.

    ![Console de Administração do SpaceIQ](media/spaceiq-provisioning-tutorial/admin.png)

2.  Na página **Configurações**, selecione **Integrações de Terceiros**.

    ![Adicionar o SCIM no SpaceIQ](media/spaceiq-provisioning-tutorial/thirdparty.png)

3.  Acesse a guia **Provisionamento e SSO**. Procure o bloco **Azure**. Clique em **Ativar**.

    ![Provisionamento e SSO no SpaceIQ](media/spaceiq-provisioning-tutorial/provisioning.png)

    ![Ativar o Azure no SpaceIQ ](media/spaceiq-provisioning-tutorial/azure.png)

3.  Copie o **Token de Portador do SCIM**. Esse valor será inserido no campo Token Secreto na guia Provisionamento do aplicativo SpaceIQ no portal do Azure. Clique em **Ativar**

    ![Criar Token no SpaceIQ](media/spaceiq-provisioning-tutorial/token.png)

## <a name="add-spaceiq-from-the-gallery"></a>Adicionar o SpaceIQ por meio da galeria

Antes de configurar o SpaceIQ para o provisionamento automático de usuário com o Azure AD, é necessário adicionar o SpaceIQ por meio da galeria de aplicativos do Azure AD à lista de aplicativos SaaS gerenciados.

**Para adicionar o SpaceIQ por meio da galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **SpaceIQ**, selecione **SpaceIQ** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![SpaceIQ na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-spaceiq"></a>Como configurar o provisionamento automático de usuário no SpaceIQ 

Esta seção descreve as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no SpaceIQ com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único com base em SAML para o SpaceIQ, seguindo as instruções fornecidas no [tutorial de logon único do SpaceIQ](./spaceiq-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares

### <a name="to-configure-automatic-user-provisioning-for-spaceiq-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o SpaceIQ no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **SpaceIQ**.

    ![O link do SpaceIQ na lista Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa Modo de Provisionamento com a opção Automático destacada.](common/provisioning-automatic.png)

5. Na seção **Credenciais de Administrador**, insira `https://api.spaceiq.com/scim` em **URL do Locatário**. Insira o valor do **Token de Autenticação do SCIM** recuperado anteriormente em **Token Secreto**. Clique em **Testar Conectividade** para verificar se o Azure AD pode se conectar ao SpaceIQ. Se a conexão falhar, verifique se a sua conta do SpaceIQ tem permissões de Administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Save** (Salvar).

8. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o SpaceIQ**.

    ![Mapeamentos de Usuário no SpaceIQ](media/spaceiq-provisioning-tutorial/usermapping.png)

9. Examine os atributos de usuário que serão sincronizados do Azure AD para o SpaceIQ na seção **Mapeamento de Atributo**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência entre as contas de usuário no SpaceIQ em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de Usuário no SpaceIQ](media/spaceiq-provisioning-tutorial/userattributes.png)

11. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para habilitar o serviço de provisionamento do Azure AD no SpaceIQ, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

13. Defina os usuários e/ou os grupos que você quer provisionar no SpaceIQ escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

14. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no SpaceIQ.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)