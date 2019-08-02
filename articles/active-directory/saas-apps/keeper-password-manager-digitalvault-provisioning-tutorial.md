---
title: 'Tutorial: Configurar o Gerenciador de senhas protetor e cofre Digital para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário ao Gerenciador de senhas protetor e cofre Digital.
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
ms.openlocfilehash: 74bfe37323a17bde19e4a9bf4ec28c9c3910b37f
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67666241"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>Tutorial: Configurar o Gerenciador de senhas protetor e cofre Digital para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Gerenciador de senhas protetor e & cofre Digital e Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos ao protetor de senha Gerenciador de & cofre Digital.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os termos do Microsoft Azure gerais de uso para recursos de visualização, consulte [termos de uso adicionais para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do Gerenciador de senhas protetor e cofre Digital](https://keepersecurity.com/pricing.html?t=e)
* Uma conta de usuário no Gerenciador de senhas protetor e cofre Digital com permissões de administrador.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Adicionar Gerenciador de senhas protetor e cofre Digital da Galeria

Antes de configurar o Gerenciador de senhas protetor e cofre Digital para provisionamento automático de usuário com o Azure AD, você precisará adicionar o Gerenciador de senhas protetor e cofre Digital da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Gerenciador de senhas protetor e cofre Digital da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No  **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione a **novo aplicativo** botão na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Gerenciador de senhas protetor e cofre Digital**, selecione **Gerenciador de senhas protetor e cofre Digital** no painel de resultados e em seguida, clique o **Add**botão para adicionar o aplicativo.

    ![Keeper Password Manager & Digital Vault na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>Atribuindo usuários ao Gerenciador de senhas protetor e cofre Digital

Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no AD do Azure precisa ter acesso ao Gerenciador de senhas protetor e cofre Digital. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao Gerenciador de senhas protetor e cofre Digital, seguindo as instruções aqui:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>Dicas importantes para atribuir usuários ao Gerenciador de senhas protetor e cofre Digital

* É recomendável que um único usuário do Azure AD seja atribuído ao Gerenciador de senhas protetor e cofre Digital para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao Gerenciador de senhas protetor e cofre Digital, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>Configurando o provisionamento automático de usuário ao Gerenciador de senhas protetor e cofre Digital 

Esta seção orienta você pelas etapas de configuração do AD do Azure serviço de provisionamento para criar, atualizar e desabilitar usuários e/ou grupos no Gerenciador de senhas protetor e cofre Digital com base no usuário e/ou as atribuições de grupo no AD do Azure.

> [!TIP]
> Você também pode optar por habilitar baseado em SAML SSO para o Gerenciador de senhas protetor e cofre Digital, seguindo as instruções fornecidas na [Gerenciador de senhas protetor e cofre Digital única tutorial do logon](keeperpasswordmanager-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Gerenciador de senhas protetor e cofre Digital no Azure AD:

1. Entre no [Portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**, em seguida, selecione **todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Gerenciador de Senhas Protetor e Cofre Digital**.

    ![O link do Keeper Password Manager & Digital Vault na lista de Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia de provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Sob o **credenciais de administrador** seção, insira o **URL do locatário** e **Token secreto** do seu Gerenciador de senhas protetor e & conta do cofre Digital conforme descrito na etapa 6.

6. Entrar no seu [Console de administração do protetor e](https://keepersecurity.com/console/#login). Clique em **Admin** e selecione um nó existente ou crie um novo. Navegue até a **provisionamento** e selecione **Add Method**.

    ![Console de administração do protetor](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-admin-console.png)

    Selecione **SCIM (sistema de gerenciamento de identidade entre domínios de**.

    ![Protetor e adicionar SCIM](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-add-scim.png)

    Clique em **criar Token de provisionamento**.

    ![Protetor e criar o ponto de extremidade](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    Copie os valores para **URL** e **Token** e cole-as no **URL do locatário** e **Token secreto** no Azure AD. Clique em **salvar** para concluir a configuração de provisionamento em acertou na mosca.

    ![Protetor e criar Token](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-token.png)

7. Ao popular os campos mostrados na etapa 5, clique em **Conexão de teste** para garantir que o Azure AD pode se conectar ao Gerenciador de senhas protetor e cofre Digital. Se a conexão falhar, verifique se que sua conta do Gerenciador de senhas protetor e cofre Digital tem permissões de administrador e tente novamente.

    ![URL do locatário + Token](common/provisioning-testconnection-tenanturltoken.png)

8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Salvar**.

10. Sob o **mapeamentos** seção, selecione **sincronizar Azure usuários do Active Directory ao Gerenciador de senhas protetor e cofre Digital**.

    ![Mapeamentos de usuário de protetor](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. Examine os atributos de usuário que são sincronizados do Azure AD ao Gerenciador de senhas protetor e cofre Digital na **mapeamento de atributos** seção. Os atributos selecionados como **correspondentes** propriedades são usadas para corresponder as contas de usuário no Gerenciador de senhas protetor e cofre Digital para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de usuário do protetor](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. Sob o **mapeamentos** seção, selecione **sincronizar grupos do Azure Active Directory ao Gerenciador de senhas protetor e cofre Digital**.

    ![Mapeamentos de grupo de protetor](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. Examine os atributos de grupo que são sincronizados do Azure AD ao Gerenciador de senhas protetor e cofre Digital na **mapeamento de atributos** seção. Os atributos selecionados como **correspondentes** propriedades são usadas para corresponder os grupos no Gerenciador de senhas protetor e cofre Digital para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de grupo de protetor](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para habilitar o provisionamento de serviço para Gerenciador de senhas protetor e cofre Digital do Azure AD, altere o **Status de provisionamento** ao **na** no **configurações** seção.

    ![Status de provisionamento ativado](common/provisioning-toggle-on.png)

16. Definir os usuários e/ou grupos que você deseja provisionar ao Gerenciador de senhas protetor e cofre Digital, escolhendo os valores desejados em **escopo** na **configurações** seção.

    ![Escopo de provisionamento](common/provisioning-scope.png)

17. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar o **detalhes de sincronização** seção para monitorar o progresso e siga os links para relatório de atividade, que descreve todas as ações executadas pelo AD do Azure no Gerenciador de senhas protetor e do serviço de provisionamento de provisionamento & Cofre digital.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* Requer o Gerenciador de senhas protetor e cofre Digital **emails** e **nome de usuário** ter o mesmo valor de origem, como todas as atualizações para qualquer um dos atributos modificará o outro valor.
* Gerenciador de senhas protetor e cofre Digital dá suporte a exclusões de usuário, apenas desabilitar. Usuários desabilitados serão exibidos como bloqueado na interface do usuário do Console de administração de protetor e.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)

