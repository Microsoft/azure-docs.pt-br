---
title: 'Tutorial: Configurar o Keeper Password Manager & Digital Vault para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Keeper Password Manager & Digital Vault.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 2670dc0cb56805a2afa966bee1d2aa52b6c8e46a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358963"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>Tutorial: Configurar o Keeper Password Manager & Digital Vault para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Keeper Password Manager & Digital Vault e no Azure AD (Active Directory) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no Keeper Password Manager & Digital Vault.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do Keeper Password Manager & Digital Vault](https://keepersecurity.com/pricing.html?t=e)
* Uma conta de usuário do Keeper Password Manager & Digital Vault com permissões de administrador.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Adicionar o Keeper Password Manager & Digital Vault por meio da galeria

Antes de configurar o Keeper Password Manager & Digital Vault para o provisionamento automático de usuário com o Azure AD, é necessário adicioná-lo da galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Keeper Password Manager & Digital Vault da galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Keeper Password Manager & Digital Vault**, selecione **Keeper Password Manager & Digital Vault** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Keeper Password Manager & Digital Vault na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>Atribuindo usuários ao Keeper Password Manager & Digital Vault

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou os grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Para configurar e habilitar o provisionamento automático de usuário, decida quais usuários e/ou grupos no Azure AD precisam de acesso ao Keeper Password Manager & Digital Vault. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao Keeper Password Manager & Digital Vault seguindo estas instruções:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>Dicas importantes para atribuir usuários ao Keeper Password Manager & Digital Vault

* É recomendável que apenas um usuário do Azure AD seja atribuído ao Keeper Password Manager & Digital Vault para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Keeper Password Manager & Digital Vault, você deve selecionar qualquer função específica de aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>Configurar o provisionamento automático de usuário para o Keeper Password Manager & Digital Vault 

Esta seção orienta você pelas etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Keeper Password Manager & Digital Vault com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Keeper Password Manager & Digital Vault seguindo as instruções fornecidas no [Tutorial de logon único do Keeper Password Manager & Digital Vault](keeperpasswordmanager-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Keeper Password Manager & Digital Vault no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Gerenciador de Senhas Protetor e Cofre Digital**.

    ![O link do Keeper Password Manager & Digital Vault na lista de Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa Modo de Provisionamento com a opção Automático destacada.](common/provisioning-automatic.png)

5. Na seção **Credenciais de Administrador**, insira a **URL do Locatário** e o **Token Secreto** da conta do Keeper Password Manager & Digital Vault conforme descrito na Etapa 6.

6. Entre no [Console de Administração do Keeper](https://keepersecurity.com/console/#login). Clique em **Administrador** e selecione um nó existente ou crie um. Navegue até a guia **Provisionamento** e selecione **Adicionar Método**.

    ![Console de Administração do Keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-admin-console.png)

    Selecione **SCIM (Sistema de Gerenciamento de Usuários entre Domínios)** .

    ![Adicionar SCIM no Keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-add-scim.png)

    Clique em **Criar Token de Provisionamento**.

    ![Criar Ponto de Extremidade do Keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    Copie os valores da **URL** e do **Token** e cole-os em **URL do Locatário** e **Token Secreto** no Azure AD. Clique em **Salvar** para concluir a configuração do provisionamento no Keeper.

    ![Criar Token no Keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-token.png)

7. Depois de preencher os campos mostrados na Etapa 5, clique em **Testar Conectividade** para garantir que o Azure AD possa se conectar ao Keeper Password Manager & Digital Vault. Se a conexão falhar, verifique se a conta do Keeper Password Manager & Digital Vault tem permissões de Administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Save** (Salvar).

10. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Keeper Password Manager & Digital Vault**.

    ![Mapeamentos de Usuário no Keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. Examine os atributos de usuário que serão sincronizados do Azure AD para o Keeper Password Manager & Digital Vault na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário no Keeper Password Manager & Digital Vault em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de Usuário no Keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. Na seção **Mapeamentos**, selecione **Sincronizar Grupos do Azure Active Directory com o Keeper Password Manager & Digital Vault**.

    ![Mapeamentos de Grupo no Keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. Examine os atributos de grupo que serão sincronizados do Azure AD para o Keeper Password Manager & Digital Vault na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência dos grupos no Keeper Password Manager & Digital Vault em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de Grupo no Keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para habilitar o serviço de provisionamento do Azure AD no Keeper Password Manager & Digital Vault, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

16. Defina os usuários e/ou grupos a serem provisionados para o Keeper Password Manager & Digital Vault escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

17. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Keeper Password Manager & Digital Vault.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* O Keeper Password Manager & Digital Vault requer que **emails** e **userName** tenham o mesmo valor de origem, uma vez que qualquer atualização de um dos atributos modificará o outro valor.
* O Keeper Password Manager & Digital Vault não dá suporte para excluir usuários, apenas para desabilitar. Os usuários desabilitados aparecerão como bloqueados na Interface do usuário do Console de Administrador do Keeper.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

