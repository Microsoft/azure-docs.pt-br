---
title: 'Tutorial: configurar o conteúdo para o provisionamento automático de usuário com o Azure Active Directory'
description: Saiba como provisionar e desprovisionar automaticamente contas de usuário do Azure Active Directory (Azure AD) para o conteúdo.
services: active-directory
documentationcenter: ''
author: zchia
manager: beatrizd
ms.assetid: 3b761984-a9a0-4519-b23e-563438978de5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2020
ms.author: zhchia
ms.openlocfilehash: c9d19624d90b1228b2a44caeff7d103af3172ed9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97516345"
---
# <a name="tutorial-configure-contentful-for-automatic-user-provisioning"></a>Tutorial: configurar o conteúdo para o provisionamento automático de usuário

Este artigo descreve as etapas que você precisa concluir em conteúdo e em Azure Active Directory (AD do Azure) para configurar o provisionamento automático de usuário. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente usuários e grupos para [conteúdo](https://www.contentful.com/) usando o serviço de provisionamento do Azure AD. Para obter detalhes importantes sobre o que esse serviço faz e como ele funciona, além de respostas a perguntas frequentes, confira [Automatizar o provisionamento e o desprovisionamento de usuário em aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Funcionalidades com suporte

> [!div class="checklist"]
> * Criar usuários com conteúdo
> * Remover usuários em conteúdo quando eles não exigem mais acesso
> * Manter os atributos de usuário sincronizados entre o Azure AD e o conteúdo
> * Provisionar grupos e associações de grupo com conteúdo
> * [Logon único](contentful-tutorial.md) para conteúdo (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](../develop/quickstart-create-new-tenant.md). 
* Uma conta de usuário no Azure AD que tem [permissão](../roles/permissions-reference.md) para configurar o provisionamento (por exemplo, administrador de aplicativos, administrador de aplicativos de nuvem, proprietário do aplicativo ou administrador global). 
* Uma conta de organização com conteúdo que tem uma assinatura que dá suporte ao sistema de provisionamento de SCIM (gerenciamento de identidade entre domínios). Se você tiver dúvidas sobre a assinatura de sua organização, entre em contato com o [suporte ao conteúdo](mailto:support@contentful.com).
 
## <a name="plan-your-provisioning-deployment"></a>Planeje a implantação do provisionamento

1. Saiba mais sobre [como funciona o serviço de provisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem estará no [escopo de provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais dados [mapeados entre o Azure AD e o conteúdo](../app-provisioning/customize-application-attributes.md). 

## <a name="configure-contentful-to-support-provisioning-with-azure-ad"></a>Configurar o conteúdo para oferecer suporte ao provisionamento com o Azure AD

1. Em conteúdo, crie uma conta de **usuário de serviço** . Todas as permissões de provisionamento para o Azure são fornecidas por meio dessa conta. Recomendamos que você escolha **proprietário** como a função da organização para essa conta.

2. Entre no conteúdo como o usuário do **serviço**.

3. No menu à esquerda, selecione **configurações da organização**  >  **ferramentas de acesso**  >  **provisionamento de usuário**.

   ![Captura de tela do menu configurações da organização em conteúdo, com provisionamento de usuário realçado em ferramentas de acesso.](media/contentful-provisioning-tutorial/access.png)

4. Copie e salve a **URL do scim**. Você inserirá esse valor no portal do Azure, na guia **provisionamento** do aplicativo de conteúdo.

5. Selecione **gerar token de acesso pessoal**.

    ![url](media/contentful-provisioning-tutorial/generate.png)

6. Na janela modal, insira um nome para seu token de acesso pessoal e, em seguida, selecione **gerar**.

7. A URL SCIM e o token secreto são gerados. Copie e salve esses valores. Você inserirá esses valores na guia **provisionamento** do aplicativo de conteúdo no portal do Azure.

    ![Captura de tela do painel de token de acesso pessoal, com C F P A T e o nome do espaço reservado do token realçado.](media/contentful-provisioning-tutorial/token.png)


Se você tiver dúvidas enquanto configura o provisionamento no console de administração de conteúdo, entre em contato com o [suporte ao conteúdo](mailto:support@contentful.com).

## <a name="add-contentful-from-the-azure-ad-application-gallery"></a>Adicionar conteúdo da Galeria de aplicativos do Azure AD

Para gerenciar o provisionamento para conteúdo, adicione conteúdo da Galeria de aplicativos do Azure AD. Se você tiver configurado anteriormente o conteúdo para logon único, poderá usar o mesmo aplicativo. No entanto, recomendamos que você crie um aplicativo separado para testar inicialmente a integração. Saiba como [Adicionar um aplicativo na Galeria](../manage-apps/add-application-portal.md). 

## <a name="define-who-will-be-in-scope-for-provisioning"></a>Defina quem estará no escopo de provisionamento 

Você pode usar o serviço de provisionamento do Azure AD para o escopo que será provisionado com base na atribuição para o aplicativo ou com base em atributos do usuário ou grupo. 

Se você optar pelo escopo que será provisionado ao seu aplicativo com base na atribuição, conclua as etapas para [atribuir usuários e grupos ao aplicativo](../manage-apps/assign-user-or-group-access-portal.md).

Se você optar pelo escopo que será provisionado com base apenas em atributos do usuário ou grupo, use um filtro de escopo para [definir regras condicionais para o provisionamento de contas de usuário](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir usuários e grupos ao conteúdo, você deve selecionar uma função diferente de **acesso padrão**. Os usuários que têm a função de acesso padrão são excluídos do provisionamento e são indicados nos logs de provisionamento como não tendo sido efetivamente qualificados. Se a única função disponível no aplicativo for a de Acesso Padrão, você poderá [atualizar o manifesto do aplicativo](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar mais funções. 
* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo de provisionamento é definido para usuários e grupos atribuídos, você pode controlar o escopo atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo com base no filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="configure-automatic-user-provisioning-to-contentful"></a>Configurar o provisionamento automático de usuário para o conteúdo 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e grupos em um aplicativo de teste com base em atribuições de usuário ou de grupo no Azure AD.

### <a name="configure-automatic-user-provisioning-for-contentful-in-azure-ad"></a>Configurar o provisionamento automático de usuário para conteúdo no Azure AD

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.

   ![Captura de tela que mostra o menu aplicativos empresariais na portal do Azure, com todos os aplicativos realçados.](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Contentful**.

   ![Captura de tela que mostra os primeiros 20 resultados retornados na lista de aplicativos.](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

   ![Captura de tela da guia provisionamento realçada na seção Gerenciar do menu à esquerda.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

   ![Captura de tela que mostra as opções do modo de provisionamento, com realce automático.](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira a URL do locatário e o token secreto do conteúdo. Para garantir que o Azure AD possa se conectar ao conteúdo, selecione **testar conexão**. Se a conexão falhar, verifique se sua conta de conteúdo tem permissões de administrador e tente novamente.

   ![Captura de tela que mostra as caixas de texto do locatário U R L e do token secreto, com o botão testar conexão realçado.](common/provisioning-testconnection-tenanturltoken.png)

6. Em **email de notificação**, insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha** .

   ![Captura de tela que mostra a caixa de texto email de notificação.](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para o conteúdo**.

9. Na seção **mapeamento de atributo** , examine os atributos de usuário que são sincronizados do Azure AD com o conteúdo. Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário em conteúdo para operações de atualização. Se você optar por alterar o [atributo de destino correspondente](../app-provisioning/customize-application-attributes.md), deverá garantir que a API de conteúdo dê suporte à filtragem de usuários com base nesse atributo. Selecione o botão **Salvar** para confirmar as alterações.

   |Atributo|Type|Com suporte para filtragem|
   |---|---|---|
   |userName|String|&check;|
   |name.givenName|String|
   |name.familyName|String|

10. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory com o conteúdo**.

11. Na seção **mapeamento de atributo** , examine os atributos de grupo que são sincronizados do Azure AD com o conteúdo. Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos em conteúdo para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    |Atributo|Type|Com suporte para filtragem|
    |---|---|---|
    |displayName|String|&check;|
    |membros|Referência|

12. Para configurar filtros de escopo, conclua as etapas descritas no [tutorial filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD para conteúdo, na seção **configurações** , para status de **provisionamento**, selecione **ativado**.

    ![Captura de tela que mostra a alternância de status de provisionamento e de desativação.](common/provisioning-toggle-on.png)

14. Para definir os usuários ou grupos que você deseja provisionar para o conteúdo, na seção **configurações** , em **escopo**, selecione a opção relevante.

    ![Captura de tela que mostra as opções que você pode selecionar no painel escopo.](common/provisioning-scope.png)

15. Quando estiver pronto para fazer o provisionamento, selecione **Salvar**.

    ![Captura de tela que mostra o botão salvar e o botão Cancelar.](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo de sincronização inicial de todos os usuários e grupos definidos em **escopo** em **configurações**. O ciclo inicial leva mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. 

## <a name="monitor-your-deployment"></a>Monitorar a implantação

Depois de configurar o provisionamento, use os seguintes recursos para monitorar sua implantação:

* Para determinar quais usuários foram provisionados com êxito ou sem êxito, exiba os [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md).
* Para ver o status do ciclo de provisionamento e como fechá-lo para concluir, verifique a barra de [progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).
* Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre os [Estados de quarentena](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
* [Gerenciar provisionamento de conta de usuário para aplicativos empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
