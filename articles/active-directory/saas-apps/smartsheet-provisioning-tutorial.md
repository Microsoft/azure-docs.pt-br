---
title: 'Tutorial: Configurar o Smartsheet para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no Smartsheet.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: e9ee994564e175d3c41cfd5ce415ead8c67df353
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100103525"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Tutorial: Configurar o Smartsheet para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Smartsheet e no Azure AD (Active Directory) a fim de configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no [Smartsheet](https://www.smartsheet.com/pricing). Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Criar usuários no Smartsheet
> * Remover usuários no Smartsheet quando eles não precisarem mais de acesso
> * Manter os atributos de usuário sincronizados entre o Azure AD e o Smartsheet
> * Logon único no Smartsheet (recomendado)

> [!NOTE]
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os Termos de uso gerais do Microsoft Azure para a versão prévia de recursos, confira [Termos de uso adicionais para versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](../develop/quickstart-create-new-tenant.md).
* Uma conta de usuário no Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento (por exemplo, Administrador de Aplicativo, Administrador de aplicativos de nuvem, Proprietário de Aplicativo ou Administrador global).
* [Um locatário do Smartsheet](https://www.smartsheet.com/pricing).
* Uma conta de usuário em um plano Enterprise ou Enterprise Premier do Smartsheet com permissões de administrador do sistema.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento
1. Saiba mais sobre [como funciona o serviço de provisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem estará no [escopo de provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais dados serão [mapeados entre o Azure AD e o Smartsheet](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-smartsheet-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configurar o Smartsheet para dar suporte ao provisionamento com o Azure AD

Antes de configurar o Smartsheet para o provisionamento automático de usuário com o Azure AD, você precisará habilitar o provisionamento do SCIM no Smartsheet.

1. Entre como um **SysAdmin** no **[portal do Smartsheet](https://app.smartsheet.com/b/home)** e procure **Administrador da Conta**.

    ![Administrador da Conta do Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Acesse **Controles de Segurança > Provisionamento Automático de Usuário > Editar**.

    ![Controles de Segurança do Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Adicione e valide os domínios de email para os usuários que você pretende provisionar do Azure AD para o Smartsheet. Escolha **Não Habilitado** para garantir que todas as ações de provisionamento sejam originadas somente do Azure AD e também para garantir que a sua lista de usuários do Smartsheet esteja em sincronia com as atribuições do Azure AD.

    ![Provisionamento de Usuário no Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Depois que a validação for concluída, você precisará ativar o domínio. 

    ![Ativar Domínio no Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Gere o **Token Secreto** necessário para configurar o provisionamento automático de usuário com o Azure AD procurando **Aplicativos e Integrações**.

    ![Captura de tela da página Administrador do Smartsheet com o avatar do usuário e a opção Aplicativos e Integrações em destaque.](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Escolha **Acesso à API**. Clique em **Gerar novo token de acesso**.

    ![Captura de tela da caixa de diálogo Configurações Pessoais com as opções Acesso à API e Gerar novo token de acesso em destaque.](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Defina o nome do Token de Acesso à API. Clique em **OK**.

    ![Captura de tela da Etapa 1 de 2: Gerar Token de Acesso à API com a opção OK em destaque.](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Copie o Token de Acesso à API e salve-o, porque essa será a única vez que poderá vê-lo. Você precisará inseri-lo no campo **Token Secreto** do Azure AD.

    ![Token do Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="step-3-add-smartsheet-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar o Smartsheet por meio da galeria de aplicativos do Azure AD

Adicione o Smartsheet por meio da galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento no Smartsheet. Se você já configurou o Smartsheet para SSO, use o mesmo aplicativo. No entanto, recomendamos que você crie um aplicativo diferente ao testar a integração no início. Saiba mais sobre como adicionar um aplicativo da galeria [aqui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo de provisionamento 

No Azure AD, é possível definir quem estará no escopo de provisionamento com base na atribuição ao aplicativo ou nos atributos do usuário/grupo. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você optar por definir quem estará no escopo de provisionamento com base somente em atributos do usuário ou do grupo, poderá usar um filtro de escopo, conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir usuários e grupos ao Smartsheet, você precisará selecionar uma função diferente de **Acesso Padrão**. Os usuários com a função Acesso Padrão são excluídos do provisionamento e serão marcados como "Não qualificado efetivamente" nos logs de provisionamento. Se a única função disponível no aplicativo for a de acesso padrão, você poderá [atualizar o manifesto do aplicativo](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar outras funções. 

* Para garantir a paridade nas atribuições de função de usuário entre o Smartsheet e o Azure AD, recomendamos utilizar as mesmas atribuições de função preenchidas na lista completa de usuários do Smartsheet. Para recuperar essa lista de usuários do Smartsheet, acesse **Administrador da Conta > Gerenciamento de Usuários > Mais Ações > Baixar Lista de Usuários (CSV)** .

* Para acessar determinados recursos no aplicativo, o Smartsheet exige que um usuário tenha várias funções. Para saber mais sobre os tipos de usuários e as permissões no Smartsheet, acesse [Tipos de usuários e permissões](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Se um usuário tiver várias funções atribuídas no Smartsheet, você **PRECISARÁ** garantir que essas atribuições de função sejam replicadas no Azure AD para evitar um cenário em que os usuários percam o acesso aos objetos do Smartsheet permanentemente. Cada função exclusiva no Smartsheet **PRECISA** ser atribuída a um grupo diferente no Azure AD. Depois, o usuário **PRECISA** ser adicionado a cada um dos grupos correspondentes às funções desejadas. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo de provisionamento é definido para usuários e grupos atribuídos, é possível controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo com base no filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="step-5-configure-automatic-user-provisioning-to-smartsheet"></a>Etapa 5. Configurar o provisionamento automático de usuário no Smartsheet 

Esta seção descreve as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Smartsheet com base em atribuições de usuário e/ou de grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Smartsheet no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Smartsheet**.

    ![O link do Smartsheet na lista Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Captura de tela das opções Gerenciar com a opção Provisionamento destacada.](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Captura de tela da lista suspensa Modo de Provisionamento com a opção Automático destacada.](common/provisioning-automatic.png)

5. Na seção **Credenciais de Administrador**, insira os valores **da URL base do SCIM 2.0 e do Token de Acesso** recuperados anteriormente no Smartsheet em **URL do Locatário** e **Token Secreto**, respectivamente. Clique em **Testar Conectividade** para verificar se o Azure AD pode se conectar ao Smartsheet. Se a conexão falhar, verifique se a sua conta do Smartsheet tem permissões de SysAdmin e tente novamente.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Save** (Salvar).

8. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Smartsheet**.

9. Examine os atributos de usuário sincronizados do Azure AD para o Smartsheet na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário do Smartsheet em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

   |Atributo|Type|Com suporte para filtragem|
   |---|---|---|
   |userName|String|&check;|
   |ativo|Boolean|
   |título|String|
   |name.givenName|String|
   |name.familyName|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "fax"].value|String|
   |emails[type eq "work"].value|String|
   |externalId|String|
   |funções|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|String|


10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento do Azure AD no Smartsheet, altere o **Status de Provisionamento** para **Ativado** na seção **Configurações**.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou os grupos que deseja provisionar no Smartsheet escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar a implantação:

1. Use os [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais usuários foram provisionados com êxito ou não
2. Confira a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o status do ciclo de provisionamento e saber como fechá-la para concluir
3. Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre os estados de quarentena [aqui](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Limitações do conector

* O Smartsheet não dá suporte a exclusões temporárias. Quando o atributo **active** de um usuário é definido como False, o Smartsheet exclui o usuário permanentemente.

## <a name="change-log"></a>Log de alterações

* 16/06/2020 – Adição de suporte aos atributos de extensão empresariais "Centro de Custo", "Divisão", "Gerente" e "Departamento" para os usuários.
* 10/02/2021 – Adição de suporte para atributos básicos "emails[type eq "work"]" para usuários.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)