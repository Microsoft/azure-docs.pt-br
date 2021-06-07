---
title: 'Tutorial: provisionamento de usuário para o ThousandEyes – Azure AD'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no ThousandEyes.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: arvinh
ms.openlocfilehash: fcb53dc22cfb4bf7308b92ee76e5aaaf3bb0dead
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735076"
---
# <a name="tutorial-configure-thousandeyes-for-automatic-user-provisioning"></a>Tutorial: Configurar ThousandEyes para provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que precisam ser executadas no ThousandEyes e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o ThousandEyes. 

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Um locatário do Azure Active Directory
* Um locatário do ThousandEyes com o [Plano Standard](https://www.thousandeyes.com/pricing) ou melhor habilitado 
* Uma conta de usuário no ThousandEyes com permissões de administrador 

> [!NOTE]
> A integração de provisionamento do Azure AD depende da [API de SCIM do ThousandEyes](https://success.thousandeyes.com/PublicArticlePage?articleIdParam=kA044000000CnWrCAK), disponível para as equipes do ThousandEyes no plano Standard ou outro melhor.

## <a name="assigning-users-to-thousandeyes"></a>Atribuição de usuários ao ThousandEyes

O Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e os grupos que foram "atribuídos" a um aplicativo no Azure AD serão sincronizados. 

Antes de configurar e habilitar o serviço de provisionamento, é necessário decidir quais usuários e/ou grupos no Azure AD representam os usuários que precisam de acesso ao seu aplicativo ThousandEyes. Depois de decidir, atribua esses usuários ao seu aplicativo ThousandEyes seguindo estas instruções:

[Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-thousandeyes"></a>Dicas importantes para atribuir usuários ao ThousandEyes

* Recomendamos a atribuição de um único usuário do Azure AD ao ThousandEyes para testar a configuração de provisionamento. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao ThousandEyes, é necessário selecionar a função **Usuário** ou outra função válida específica do aplicativo (se disponível) na caixa de diálogo de atribuição. A função **Acesso Padrão** não funciona para o provisionamento e esses usuários são ignorados.

## <a name="configuring-user-provisioning-to-thousandeyes"></a>Configuração do provisionamento de usuários no ThousandEyes 

Esta seção explica como conectar seu Azure AD à API de provisionamento de conta de usuário do ThousandEyes e como configurar o serviço de provisionamento a fim de criar, atualizar e desabilitar contas de usuário atribuídas no ThousandEyes com base na atribuição de usuário e de grupo do Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o ThousandEyes, seguindo as instruções fornecidas no [Portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos sejam complementares.

### <a name="configure-automatic-user-account-provisioning-to-thousandeyes-in-azure-ad"></a>Configurar o provisionamento automático de conta de usuário para o ThousandEyes no Azure AD

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Se você já tiver configurado o ThousandEyes para logon único, pesquise sua instância do ThousandEyes usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise **ThousandEyes** na galeria de aplicativos. Selecione o ThousandEyes nos resultados da pesquisa e adicione-o à lista de aplicativos.

    ![O link do ThousandEyes na lista de Aplicativos](common/all-applications.png)
    
3. Selecione sua instância do ThousandEyes e selecione a guia **Provisionamento**.

    ![Guia Provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

![Uma captura de tela que mostra a guia Provisionamento para o ThousandEyes com o Modo de Provisionamento selecionado como Automático.](./media/thousandeyes-provisioning-tutorial/ThousandEyes1.png)
    

5. Na seção **Credenciais de Administrador**, insira o **Token de Portador do OAuth** gerado pela conta do ThousandEyes (é possível localizar e/ou gerar o token na sua conta do ThousandEyes: seção **Perfil**).

    ![Uma captura de tela que mostra onde encontrar o link de Configurações de Conta para o Grupo de Contas Atual.](./media/thousandeyes-provisioning-tutorial/ThousandEyes2.png)

6. No Portal do Azure, clique em **Testar conectividade** para garantir que o Azure AD possa se conectar ao seu aplicativo ThousandEyes. Se a conexão falhar, verifique se sua conta do ThousandEyes tem permissões de Administrador e repita a etapa 5.

7. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

8. Clique em **Save** (Salvar).

9. Na seção Mapeamentos, selecione **Sincronizar Usuários do Azure Active Directory com o ThousandEyes**.

10. Examine os atributos de usuário que serão sincronizados entre o Azure AD e o ThousandEyes na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** serão usados para obter uma correspondência entre contas de usuário da Parsable para executar operações de atualização. Caso escolha alterar o [atributo de destino correspondente](../app-provisioning/customize-application-attributes.md), será necessário verificar se a API da Parsable é compatível com uma filtragem de usuários baseada nesse atributo. Selecione o botão **Salvar** para confirmar as alterações.

     |Atributo|Type|Com suporte para filtragem|
     |---|---|---|
     |externalId|String|&check;|
     |userName|String|&check;|
     |ativo|Boolean|
     |displayName|String|
     |emails[type eq "work"].value|String|
     |name.formatted|String|


11. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para habilitar um serviço de provisionamento do Azure AD para o ThousandEyes, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

13. Defina usuários e/ou grupos que você deseja provisionar para o ThousandEyes escolhendo os valores desejados na opção **Escopo** da seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

14. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação começa o ciclo de sincronização inicial de todos os usuários e grupos definidos no **Escopo** na seção **Configurações**. O ciclo inicial leva mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar a implantação:

1. Use os [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais usuários foram provisionados com êxito ou não
2. Confira a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o status do ciclo de provisionamento e saber como fechá-la para concluir
3. Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre os estados de quarentena [aqui](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)