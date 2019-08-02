---
title: 'Tutorial: Configurar o Dropbox for Business para provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para Dropbox for Business.
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
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: d7a7a76c86100041b544916c7d10e43bf3aaa44d
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672904"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Tutorial: Configurar o Dropbox for Business para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Dropbox for Business e o Azure Active Directory (Azure AD) configurar o Azure AD automaticamente provisionar e desprovisionar usuários e/ou grupos ao Dropbox for Business.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do Dropbox for Business](https://www.dropbox.com/business/pricing)
* Uma conta de usuário no Dropbox for Business com permissões de administrador.

## <a name="add-dropbox-for-business-from-the-gallery"></a>Adicionar o Dropbox for Business da Galeria

Antes de configurar o Dropbox for Business para provisionamento com o Azure AD automático de usuário, você precisará adicionar o Dropbox for Business da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Dropbox for Business da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No  **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione a **novo aplicativo** botão na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Dropbox for Business**, selecione **Dropbox for Business** no painel de resultados e em seguida, clique o **Add** botão para adicionar o aplicativo.

    ![Dropbox for Business na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-dropbox-for-business"></a>Atribuindo usuários ao Dropbox for Business

Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao Dropbox for Business. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao Dropbox for Business, seguindo as instruções aqui:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Dicas importantes para atribuir usuários ao Dropbox for Business

* É recomendável que um único usuário do Azure AD é atribuído ao Dropbox for Business testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário para Dropbox for Business, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-dropbox-for-business"></a>Configurando o provisionamento automático de usuário para Dropbox for Business 

Esta seção orienta você pelas etapas de configuração do AD do Azure serviço de provisionamento para criar, atualizar e desabilitar usuários e/ou grupos no Dropbox for Business com base nas atribuições de usuário e/ou grupo no AD do Azure.

> [!TIP]
> Você também pode optar por habilitar baseado em SAML single sign-on para Dropbox for Business, seguindo as instruções fornecidas na [Dropbox para o tutorial do logon único Business](dropboxforbusiness-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-dropbox-for-business-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para Dropbox for Business no Azure AD:

1. Entre no [Portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**, em seguida, selecione **todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Dropbox for Business**.

    ![O link do Dropbox for Business na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Na seção **Credenciais de Administrador**, clique em **Autorizar**. Ela abre uma caixa de diálogo de logon do Dropbox for Business em uma nova janela do navegador.

    ![Provisionamento ](common/provisioning-oauth.png)

6. Sobre o **entrar no Dropbox for Business vincular com o Azure AD** caixa de diálogo, entre no seu locatário Dropbox for Business e verificar sua identidade.

    ![Dropbox para entrar no Business](media/dropboxforbusiness-provisioning-tutorial/dropbox01.png)

7. Depois de concluir as etapas 5 e 6, clique em **Conexão de teste** para garantir que o Azure AD pode se conectar ao Dropbox for Business. Se a conexão falhar, certifique-se que do Dropbox para a conta de negócios tem permissões de administrador e tente novamente.

    ![A criptografia do token](common/provisioning-testconnection-oauth.png)

8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Salvar**.

10. Sob o **mapeamentos** seção, selecione **sincronizar Azure usuários do Active Directory para o Dropbox**.

    ![Mapeamentos de usuário do dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-mapping.png)

11. Examine os atributos de usuário que são sincronizados do Azure AD ao Dropbox na **mapeamento de atributos** seção. Os atributos selecionados como **correspondentes** propriedades são usadas para corresponder as contas de usuário no Dropbox para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de usuário do dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-attributes.png)

12. Sob o **mapeamentos** seção, selecione **sincronizar grupos do Azure Active Directory dropbox**.

    ![Mapeamentos de grupo do dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-mapping.png)

13. Examine os atributos de grupo que são sincronizados do Azure AD ao Dropbox na **mapeamento de atributos** seção. Os atributos selecionados como **correspondentes** propriedades são usadas para corresponder os grupos no Dropbox para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de grupo do dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-attributes.png)

14. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para habilitar o provisionamento de serviço para o Dropbox do Azure AD, altere o **Status de provisionamento** ao **na** no **configurações** seção.

    ![Status de provisionamento ativado](common/provisioning-toggle-on.png)

16. Definir os usuários e/ou grupos que você gostaria para provisionar o dropbox, escolhendo os valores desejados em **escopo** na **configurações** seção.

    ![Escopo de provisionamento](common/provisioning-scope.png)

17. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar o **detalhes de sincronização** seção para monitorar o progresso e siga os links para relatório de atividade, que descreve todas as ações executadas pelo serviço do dropbox de provisionamento do Azure AD de provisionamento.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector
 
* Dropbox não oferece suporte a suspender os usuários convidados. Se um usuário convidado é suspenso, que o usuário será excluído.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)

