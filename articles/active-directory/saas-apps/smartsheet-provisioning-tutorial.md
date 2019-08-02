---
title: 'Tutorial: Configurar o Smartsheet para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no Smartsheet.
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
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: f0ca2dfa90e1312db664962e7ffbe6b3f4dd96e1
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670944"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Tutorial: Configurar o Smartsheet para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Smartsheet e o Azure Active Directory (Azure AD) para configurar o Azure AD automaticamente provisionar e desprovisionar usuários e/ou grupos ao Smartsheet.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os termos do Microsoft Azure gerais de uso para recursos de visualização, consulte [termos de uso adicionais para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do Smartsheet](https://www.smartsheet.com/pricing)
* Uma conta de usuário em um plano de Smartsheet Enterprise ou Enterprise Premier com permissões de administrador do sistema.

## <a name="assign-users-to-smartsheet"></a>Atribuir usuários ao Smartsheet

Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao Smartsheet. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao Smartsheet, seguindo as instruções aqui:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-smartsheet"></a>Dicas importantes para atribuir usuários ao Smartsheet

* É recomendável que um único usuário do Azure AD seja atribuído ao Smartsheet para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Smartsheet, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

* Para garantir a paridade nas atribuições de função de usuário entre Smartsheet e o Azure AD, é recomendável utilizar as atribuições de função mesmo preenchidas na lista completa de usuário do Smartsheet. Para recuperar a lista de usuários do Smartsheet, navegue até **administrador da conta > Gerenciamento de usuário > mais ações > Baixe a lista de usuários (csv)** .

* Para acessar determinados recursos no aplicativo, Smartsheet requer que um usuário ter várias funções. Para saber mais sobre os tipos de usuário e permissões no Smartsheet, acesse [tipos de usuário e permissões](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Se um usuário tiver várias funções atribuídas no Smartsheet, você **deve** Certifique-se de que essas atribuições de função são replicadas no Azure AD para evitar um cenário em que os usuários poderá perder o acesso a objetos de Smartsheet permanentemente. Cada função exclusiva no Smartsheet **deve** ser atribuído a um grupo diferente no Azure AD. O usuário **deve** ser adicionado a cada um dos grupos correspondentes às funções desejadas. 

## <a name="set-up-smartsheet-for-provisioning"></a>Configurar o Smartsheet para provisionamento

Antes de configurar o Smartsheet para provisionamento com o Azure AD automático de usuário, você precisará habilitar o provisionamento de SCIM no Smartsheet.

1. Entrar como um **SysAdmin** na **[portal Smartsheet](https://app.smartsheet.com/b/home)** e navegue até **administrador da conta**.

    ![Administrador da conta de Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Vá para **controles de segurança > automático de usuário de provisionamento > Editar**.

    ![Controles de segurança do Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Adicione e validar os domínios de email para os usuários que você pretende provisionar do Azure AD ao Smartsheet. Escolher **não habilitada** para garantir que todas as ações de provisionamento ser originado somente do AD do Azure e também garantir que sua lista de usuários do Smartsheet está em sincronia com as atribuições do AD do Azure.

    ![Provisionamento de usuário do Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Depois que a validação estiver concluída, você precisa ativar o domínio. 

    ![Smartsheet ativar domínio](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Gerar a **Token secreto** necessárias para configurar o provisionamento automático de usuário com o Azure AD, navegando até **integrações e aplicativos**.

    ![Instalação do Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Escolher **acesso à API**. Clique em **gerar novo token de acesso**.

    ![Instalação do Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Defina o nome do Token de acesso de API. Clique em **OK**.

    ![Instalação do Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Copie o Token de acesso de API e salvá-lo, pois esta será a única vez em que você pode exibi-lo. Isso é necessário o **segredo do Token** campo no Azure AD.

    ![Token do Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="add-smartsheet-from-the-gallery"></a>Adicionar o Smartsheet da Galeria

Para configurar o Smartsheet para provisionamento com o Azure AD automático de usuário, você precisará adicionar o Smartsheet da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

1. No  **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione a **novo aplicativo** botão na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Smartsheet**, selecione **Smartsheet** no painel de resultados. 

    ![Smartsheet na lista de resultados](common/search-new-app.png)

5. Selecione o **Inscreva-se para Smartsheet** botão que você será redirecionado à página de logon do Smartsheet. 

    ![Adicionar Smartsheet OIDC](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-add.png)

6. Como Smartsheet é um aplicativo OpenIDConnect, optar por fazer logon no Smartsheet usando sua conta corporativa da Microsoft.

    ![Logon de Smartsheet OIDC](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-login.png)

7. Após uma autenticação bem-sucedida, aceite a solicitação de consentimento para a página de consentimento. O aplicativo, em seguida, será adicionado automaticamente ao seu locatário e você será redirecionado para sua conta de Smartsheet.

    ![Smartsheet OIDc Consent](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-consent.png)

## <a name="configure-automatic-user-provisioning-to-smartsheet"></a>Configurar o provisionamento automático de usuário ao Smartsheet 

Esta seção orienta você pelas etapas de configuração do AD do Azure serviço de provisionamento para criar, atualizar e desabilitar usuários e/ou grupos no Smartsheet com base nas atribuições de usuário e/ou grupo no AD do Azure.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para Smartsheet no Azure AD:

1. Entre no [Portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**, em seguida, selecione **todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Smartsheet**.

    ![O link do Smartsheet na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Sob o **credenciais de administrador** seção, de entrada `https://scim.smartsheet.com/v2/` na **URL de locatário**. Adicione o valor recuperado e salvo anteriormente do Smartsheet no **segredo do Token**. Clique em **Conexão de teste** para garantir que o Azure AD pode se conectar ao Smartsheet. Se a conexão falhar, verifique se que sua conta de Smartsheet tem permissões de SysAdmin e tente novamente.

    ![A criptografia do token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Sob o **mapeamentos** seção, selecione **sincronizar Azure usuários do Active Directory ao Smartsheet**.

    ![Mapeamentos de usuário do Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-user-mappings.png)

9. Examine os atributos de usuário que são sincronizados do Azure AD ao Smartsheet na **mapeamento de atributos** seção. Os atributos selecionados como **correspondentes** propriedades são usadas para corresponder as contas de usuário no Smartsheet para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de usuário do Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-user-attributes.png)

10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o provisionamento de serviço para Smartsheet do Azure AD, altere o **Status de provisionamento** para **na** no **configurações** seção.

    ![Status de provisionamento ativado](common/provisioning-toggle-on.png)

12. Definir os usuários e/ou grupos que você deseja provisionar ao Smartsheet escolhendo os valores desejados em **escopo** na **configurações** seção.

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar o **detalhes de sincronização** seção para monitorar o progresso e siga os links para relatório de atividade, que descreve todas as ações executadas pelo serviço no Smartsheet de provisionamento do Azure AD de provisionamento.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* Smartsheet não oferece suporte a exclui temporariamente. Quando um usuário **active** atributo é definido como False, Smartsheet exclui o usuário permanentemente.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
