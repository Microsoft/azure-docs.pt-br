---
title: 'Tutorial: Configurar o SAP Cloud Platform Identity Authentication para provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o SAP Cloud Platform Identity Authentication.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: 419f25ee3df471bc2fc4526254f5677b8bd71856
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96342680"
---
# <a name="tutorial-configure-sap-cloud-platform-identity-authentication-for-automatic-user-provisioning"></a>Tutorial: Configurar o SAP Cloud Platform Identity Authentication para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no SAP Cloud Platform Identity Authentication e no Azure AD (Active Directory) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para o SAP Cloud Platform Identity Authentication.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do SAP Cloud Platform Identity Authentication](https://cloudplatform.sap.com/pricing.html)
* Uma conta de usuário no SAP Cloud Platform Identity Authentication com permissões de Administrador.

## <a name="assigning-users-to-sap-cloud-platform-identity-authentication"></a>Atribuir usuários ao SAP Cloud Platform Identity Authentication

O Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou os grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Para configurar e habilitar o provisionamento automático de usuário, decida quais usuários e/ou grupos no Azure AD precisam de acesso ao SAP Cloud Platform Identity Authentication. Depois de decidir, será possível atribuir esses usuários e/ou grupos ao SAP Cloud Platform Identity Authentication seguindo estas instruções:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-sap-cloud-platform-identity-authentication"></a>Dicas importantes para atribuir usuários ao SAP Cloud Platform Identity Authentication

* Recomendamos que somente um usuário do Azure AD seja atribuído ao SAP Cloud Platform Identity Authentication para testar a configuração de provisionamento automático de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao SAP Cloud Platform Identity Authentication, é necessário selecionar qualquer função específica ao aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="setup-sap-cloud-platform-identity-authentication-for-provisioning"></a>Configurar o SAP Cloud Platform Identity Authentication para provisionamento

1. Entre no [Console de Administração do SAP Cloud Platform Identity Authentication](https://sapmsftintegration.accounts.ondemand.com/admin). Navegue até **Usuários e Autorizações > Administradores**.

    ![Console de Administração do SAP Cloud Platform Identity Authentication](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/adminconsole.png)

2.  Pressione o botão **+Adicionar** no painel esquerdo para adicionar um novo administrador à lista. Escolha **Adicionar Sistema** e digite o nome do sistema.   

> [!NOTE]
> O usuário administrador no SAP Cloud Platform Identity Authentication precisa ser do tipo **Sistema**. A criação de um usuário administrador normal pode levar a erros de *não autorizado* durante o provisionamento.   

3.  Em Configurar Autorizações, alterne o botão de alternância para ativar as opções **Gerenciar Usuários** e **Gerenciar Grupos**.

    ![Adicionar SCIM do SAP Cloud Platform Identity Authentication](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/configurationauth.png)

4. Você receberá um email para ativar sua conta e definir uma senha para o **Serviço do SAP Cloud Platform Identity Authentication**.

4.  Copie a **ID de Usuário** e a **Senha**. Esses valores serão inseridos nos campos Nome de Usuário Administrador e Senha do Administrador respectivamente na guia Provisionamento do aplicativo SAP Cloud Platform Identity Authentication no portal do Azure.

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Adicionar Autenticação de Identidade da SAP Cloud Platform por meio da galeria

Antes de configurar o SAP Cloud Platform Identity Authentication para o provisionamento automático de usuário com o Azure AD, é necessário adicionar o SAP Cloud Platform Identity Authentication da galeria de aplicativos do Azure AD à lista de aplicativos SaaS gerenciados.

**Para adicionar o SAP Cloud Platform Identity Authentication da galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **SAP Cloud Platform Identity Authentication**, selecione **SAP Cloud Platform Identity Authentication** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Adicionar SAP Cloud Platform Identity Authentication na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-sap-cloud-platform-identity-authentication"></a>Configurar o provisionamento automático de usuário para o SAP Cloud Platform Identity Authentication 

Esta seção orienta você pelas etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no SAP Cloud Platform Identity Authentication com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o SAP Cloud Platform Identity Authentication seguindo as instruções fornecidas no [tutorial de logon único do SAP Cloud Platform Identity Authentication](./sap-hana-cloud-platform-identity-authentication-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares

### <a name="to-configure-automatic-user-provisioning-for-sap-cloud-platform-identity-authentication-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o SAP Cloud Platform Identity Authentication no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **SAP Cloud Platform Identity Authentication**.

    ![Link do SAP Cloud Platform Identity Authentication na lista de Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa Modo de Provisionamento com a opção Automático destacada.](common/provisioning-automatic.png)

5. Na seção **Credenciais de Administrador**, insira `https://<tenantID>.accounts.ondemand.com/service/scim ` em **URL do Locatário**. Insira os valores **ID de Usuário** e **Senha** recuperados anteriormente em **Nome de Usuário Administrador** e **Senha de Administrador**. Clique em **Testar Conectividade** para verificar se o Azure AD pode se conectar ao SAP Cloud Platform Identity Authentication. Se a conexão falhar, verifique se a sua conta do SAP Cloud Platform Identity Authentication tem permissões de Administrador e tente novamente.

    ![URL do locatário + token](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/testconnection.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Save** (Salvar).

8. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o SAP Cloud Platform Identity Authentication**.

    ![Mapeamentos de Usuário do SAP Cloud Platform Identity Authentication](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/mapping.png)

9. Examine os atributos de usuário sincronizados do Azure AD com o SAP Cloud Platform Identity Authentication na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário no SAP Cloud Platform Identity Authentication para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de Usuário do SAP Cloud Platform Identity Authentication](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/userattributes.png)

10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento do Azure AD no SAP Cloud Platform Identity Authentication, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou grupos que você gostaria de provisionar para o SAP Cloud Platform Identity Authentication escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Use a seção **Detalhes de Sincronização** para monitorar o progresso e siga os links para o relatório de atividades de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no SAP Cloud Platform Identity Authentication.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* O ponto de extremidade SCIM do SAP Cloud Platform Identity Authentication exige que determinados atributos sejam de um formato específico. Você pode saber mais sobre esses atributos e o formato específico deles [aqui](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/b10fc6a9a37c488a82ce7489b1fab64c.html#).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)