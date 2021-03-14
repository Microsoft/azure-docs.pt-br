---
title: 'Tutorial: configurar o TravelPerk para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o TravelPerk.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 3e40f87d-8624-4b14-b098-80ff916103c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2021
ms.author: Zhchia
ms.openlocfilehash: 7e3e119c81f6417110d34b7b9b97af9fa1738d35
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054470"
---
# <a name="tutorial-configure-travelperk-for-automatic-user-provisioning"></a>Tutorial: configurar o TravelPerk para o provisionamento automático de usuário

Este tutorial descreve as etapas que você precisa executar tanto no TravelPerk quanto no Azure Active Directory (Azure AD) para configurar o provisionamento automático de usuário. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente usuários e grupos para [TravelPerk](https://www.travelperk.com/) usando o serviço de provisionamento do Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="capabilities-supported"></a>Funcionalidades com suporte

> [!div class="checklist"]
>
> - Criar usuários no TravelPerk
> - Remover usuários no TravelPerk quando eles não exigem mais acesso
> - Manter os atributos de usuário sincronizados entre o Azure AD e o TravelPerk
> - [Logon único](https://docs.microsoft.com/azure/active-directory/saas-apps/travelperk-tutorial) no TravelPerk (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

- [Um locatário do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
- Uma conta de usuário no Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicativo, Administrador de aplicativos de nuvem, Proprietário de Aplicativo ou Administrador Global).
- Uma conta de administrador do [TravelPerk](https://app.travelperk.com/signup) ativa.
- Um [plano](https://www.travelperk.com/pricing/)Premium/pro.


## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento

1. Saiba mais sobre [como funciona o serviço de provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determine quem estará no [escopo de provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais dados [mapeados entre o Azure AD e o TravelPerk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-travelperk-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configurar o TravelPerk para dar suporte ao provisionamento com o Azure AD

1. Faça logon no aplicativo [TravelPerk](https://app.travelperk.com/company/integrations/scim) com sua conta de administrador.

2. Navegue até **configurações da empresa**  >  **integrações**  >  **scim**

3. Clique em **habilitar API do scim**

   ![Habilitar](./media/travelperk-provisioning-tutorial/configuration.png)

4. Você também pode habilitar aprovações por meio do SCIM. As aprovações ajudam a definir governança adicional, garantindo que as viagens sejam aprovadas primeiro pelos aprovadores especificados. Você pode aprender mais sobre isso [aqui](https://support.travelperk.com/hc/en-us/articles/360044168971-How-do-approval-processes-work-).

5. Você pode especificar se deseja que o gerente de cada pessoa se torne automaticamente o usuário responsável pela aprovação de viagens. Portanto, um Aprovador será atribuído no processo de aprovação automática correspondente. TravelPerk mapeará o valor do **gerente** do Azure para o aprovador desejado do usuário. O usuário precisa existir na plataforma antes de se tornar o aprovador do usuário provisionado.
Os aprovadores não serão criados se não estiverem configurados corretamente em TravelPerk.

6. A criação automática do processo de aprovação está disponível nas **configurações do scim** depois de habilitar o scim na página integrações. Para ativá-lo, selecione **por meio de um provedor de identidade** e alterne a alternância para **habilitar a criação do processo de aprovação automática**.

7. Clique em **salvar alterações** quando o processo de aprovação necessário estiver configurado.

   ![Automatizar](./media/travelperk-provisioning-tutorial/approval.png)

## <a name="step-3-add-travelperk-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar o TravelPerk da Galeria de aplicativos do Azure AD

Adicione o TravelPerk da Galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento no TravelPerk. Se você tiver configurado anteriormente o TravelPerk para SSO, poderá usar o mesmo aplicativo. No entanto, recomendamos que você crie um aplicativo diferente ao testar a integração no início. Saiba mais sobre como adicionar um aplicativo da galeria [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo de provisionamento

No Azure AD, é possível definir quem estará no escopo de provisionamento com base na atribuição ao aplicativo ou nos atributos do usuário/grupo. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você optar por definir quem estará no escopo de provisionamento com base somente em atributos do usuário ou do grupo, poderá usar um filtro de escopo, conforme descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

- Ao atribuir usuários ao TravelPerk, você deve selecionar uma função diferente de **acesso padrão**. Os usuários com a função Acesso Padrão são excluídos do provisionamento e serão marcados como "Não qualificado efetivamente" nos logs de provisionamento. Se a única função disponível no aplicativo for a de acesso padrão, você poderá [atualizar o manifesto do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar outras funções.

- Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo de provisionamento é definido para usuários e grupos atribuídos, é possível controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo com base no filtro de escopo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

## <a name="step-5-configure-automatic-user-provisioning-to-travelperk"></a>Etapa 5. Configurar o provisionamento automático de usuário para o TravelPerk

Nesta seção, você verá orientações para seguir as etapas de configuração do serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no TestApp com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-travelperk-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para TravelPerk no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

   ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **TravelPerk**.

   ![O link do TravelPerk na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

   ![Guia Provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

   ![Guia Provisionamento automático](common/provisioning-automatic.png)

5. Na seção **Credenciais de Administrador**, clique em **Autorizar**. Você será redirecionado para a página de logon do **TravelPerk**. Insira seu **nome de usuário** e **senha** e clique no botão **entrar** . Clique em **autorizar aplicativo** na página autorização. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao TravelPerk. Se a conexão falhar, verifique se sua conta do SecureLogin tem permissões de administrador e tente novamente.

   ![Credenciais de administrador](./media/travelperk-provisioning-tutorial/authorize.png)

   ![Bem-Vindo](./media/travelperk-provisioning-tutorial/login.png)

   ![Access](./media/travelperk-provisioning-tutorial/authorization.png)

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

   ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Salvar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para TravelPerk**.

9. Examine os atributos de usuário que são sincronizados do Azure AD para o TravelPerk na seção de **mapeamento de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no TravelPerk para operações de atualização. Se você optar por alterar o [atributo de destino correspondente](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), será necessário garantir que a API TravelPerk dê suporte à filtragem de usuários com base nesse atributo. Selecione o botão **Salvar** para confirmar as alterações.

   | Atributo                                                                         | Type      | Com suporte para filtragem |
   | --------------------------------------------------------------------------------- | --------- | ----------------------- |
   | userName                                                                          | String    | &check;                 |
   | externalId                                                                        | String    |
   | ativo                                                                            | Boolean   |
   | name.honorificPrefix                                                              | String    |
   | name.familyName                                                                   | String    |
   | name.givenName                                                                    | String    |
   | name.middleName                                                                   | String    |
   | preferredLanguage                                                                 | String    |
   | localidade                                                                            | String    |
   | phoneNumbers[type eq "work"].value                                                | String    |
   | externalId                                                                        | String    |
   | título                                                                             | String    |
   | urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter             | String    |
   | urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager                | Referência |
   | urn: IETF: params: SCIM: schemas: Extension: travelperk: 2.0: User: sexo                 | String    |
   | urn: IETF: params: SCIM: schemas: Extension: travelperk: 2.0: User: dateOfBirth            | String    |
   | urn: IETF: params: SCIM: schemas: Extension: travelperk: 2.0: User: invoiceProfiles        | Array     |
   | urn:IETF:params:SCIM:schemas:Extension:travelperk: 2.0:User:emergencyContact. Name  | String    |
   | urn: IETF: params: SCIM: esquemas: extensão: travelperk: 2.0: User: emergencyContact. Phone | String    |
   | urn: IETF: params: SCIM: schemas: Extension: travelperk: 2.0: User: travelPolicy           | String    |

10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento do Azure AD para o TravelPerk, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou grupos que você deseja provisionar para o TravelPerk escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação começa o ciclo de sincronização inicial de todos os usuários e grupos definidos no **Escopo** na seção **Configurações**. O ciclo inicial leva mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução.

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação

Depois de configurar o provisionamento, use os seguintes recursos para monitorar a implantação:

1. Use os [logs de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais usuários foram provisionados com êxito ou não
2. Confira a [barra de progresso](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) para ver o status do ciclo de provisionamento e saber como fechá-la para concluir
3. Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre os estados de quarentena [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Recursos adicionais

- [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas

- [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
