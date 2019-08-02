---
title: 'Tutorial: Configurar o Software de recrutamento Comeet para provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Software de recrutamento Comeet.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: d54750e731f92a574ac6571612f8dbd9054a9530
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672973"
---
# <a name="tutorial-configure-comeet-recruiting-software-for-automatic-user-provisioning"></a>Tutorial: Configurar o Software de recrutamento Comeet para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Software de recrutamento Comeet e o Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos ao Software de recrutamento Comeet.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os termos do Microsoft Azure gerais de uso para recursos de visualização, consulte [termos de uso adicionais para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do Software de recrutamento Comeet](https://www.comeet.co/)
* Uma conta de usuário no Software de recrutamento Comeet com permissões de administrador.

## <a name="add-comeet-recruiting-software-from-the-gallery"></a>Adicionar o Software de recrutamento Comeet da Galeria

Antes de configurar o Software de recrutamento Comeet para provisionamento automático de usuário com o Azure AD, você precisará adicionar o Software de recrutamento Comeet da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Software de recrutamento Comeet da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No  **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione a **novo aplicativo** botão na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Comeet Software de recrutamento**, selecione **Software de recrutamento Comeet** no painel de resultados e em seguida, clique o **adicionar** botão para adicionar o aplicativo.

    ![Comeet Recruiting Software na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-comeet-recruiting-software"></a>Atribuir usuários ao Software de recrutamento Comeet

Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao Software de recrutamento Comeet. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao Software de recrutamento Comeet seguindo as instruções aqui:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-comeet-recruiting-software"></a>Dicas importantes para atribuir usuários ao Software de recrutamento Comeet

* É recomendável que um único usuário do Azure AD seja atribuído ao Software de recrutamento Comeet para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Software de recrutamento Comeet, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-comeet-recruiting-software"></a>Configurando o provisionamento automático de usuário para o Software de recrutamento Comeet 

Esta seção explica as etapas de configuração do AD do Azure serviço de provisionamento para criar, atualizar e desabilitar usuários e/ou grupos no Software de recrutamento Comeet com base no usuário e/ou as atribuições de grupo no AD do Azure.

> [!TIP]
> Você também pode optar por habilitar baseado em SAML SSO para o Software de recrutamento Comeet, seguindo as instruções fornecidas na [tutorial do logon único Software de recrutamento Comeet](comeetrecruitingsoftware-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-comeet-recruiting-software-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Software de recrutamento Comeet no Azure AD:

1. Entre no [Portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**, em seguida, selecione **todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Comeet recrutamento Software**.

    ![O link do Software de recrutamento Comeet na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Sob o **credenciais de administrador** seção, de entrada a **URL do locatário** e **Token secreto** da conta de Comeet recrutamento do Software, conforme descrito na etapa 6.

6. No [console de administração do Software de recrutamento Comeet](https://app.comeet.co/), navegue até **Comeet > Configurações > autenticação > Microsoft Azure**e copie o **segredo do Token para a sua empresa**de valor para o **Token secreto** campo no Azure AD.

    ![Comeet recrutamento provisionamento de Software](./media/comeet-recruiting-software-provisioning-tutorial/secret-token-1.png)

7. Ao popular os campos mostrados na etapa 5, clique em **Conexão de teste** para garantir que o Azure AD pode se conectar ao Software de recrutamento Comeet. Se a conexão falhar, verifique se que sua conta do Software de recrutamento Comeet tem permissões de administrador e tente novamente.

    ![A criptografia do token](common/provisioning-testconnection-token.png)

8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Salvar**.

10. Sob o **mapeamentos** seção, selecione **sincronizar o Azure Active Directory Users para Comeet**.

    ![Comeet recrutamento mapeamentos de usuário do Software](media/comeet-recruiting-software-provisioning-tutorial/user-mappings.png)

11. Examine os atributos de usuário que são sincronizados do Azure AD com o Software de recrutamento Comeet na **mapeamento de atributos** seção. Os atributos selecionados como **correspondentes** propriedades são usadas para corresponder as contas de usuário do Software de recrutamento Comeet para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Comeet recrutamento atributos de grupo de Software](media/comeet-recruiting-software-provisioning-tutorial/user-mapping-attributes.png)

12. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o provisionamento de serviço para o Software de recrutamento Comeet do Azure AD, altere o **Status de provisionamento** ao **na** no **configurações** seção.

    ![Status de provisionamento ativado](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você gostaria serem provisionados para Software de recrutamento Comeet escolhendo os valores desejados em **escopo** na **configurações** seção.

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar o **detalhes de sincronização** seção para monitorar o progresso e siga os links para relatório de atividade, que descreve todas as ações executadas pelo serviço no Software de recrutamento Comeet de provisionamento do Azure AD de provisionamento.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* Software de recrutamento comeet atualmente não dá suporte a grupos.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)

