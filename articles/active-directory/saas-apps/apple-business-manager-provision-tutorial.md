---
title: 'Tutorial: Configurar o Apple Business Manager para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente contas de usuário do Azure AD para o Apple Business Manager.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 4ad30031-9904-4ac3-a4d2-e8c28d44f319
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/08/2020
ms.author: Zhchia
ms.openlocfilehash: b4f24c9beffcd67fb84940c2e159da615496d9aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96180363"
---
# <a name="tutorial-configure-apple-business-manager-for-automatic-user-provisioning"></a>Tutorial: Configurar o Apple Business Manager para o provisionamento automático de usuário



Este tutorial descreve as etapas que você precisará executar no Apple Business Manager e no Azure AD (Active Directory) para configurar o provisionamento automático de usuário. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente usuários e grupos no [Apple Business Manager](https://business.apple.com/) usando o serviço de Provisionamento do Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Criar usuários no Apple Business Manager
> * Remover usuários do Apple Business Manager quando eles não precisarem mais de acesso
> * Manter os atributos de usuário sincronizados entre o Azure AD e o Apple Business Manager

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](../develop/quickstart-create-new-tenant.md).
* Uma conta de usuário no Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento (por exemplo, Administrador de Aplicativo, Administrador de aplicativos de nuvem, Proprietário de Aplicativo ou Administrador Global).
* Uma conta do Apple Business Manager com a função Administrador ou Gerente de Pessoas.

> [!NOTE]
> A transferência de token para o Azure AD e o estabelecimento de uma conexão bem-sucedida deverão ser concluídos em quatro dias do calendário ou o processo precisará ser reiniciado.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento
1. Saiba mais sobre [como funciona o serviço de provisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem estará no [escopo de provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais dados serão [mapeados entre o Azure AD e o Apple Business Manager](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-apple-business-manager-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configurar o Apple Business Manager para dar suporte ao provisionamento com o Azure AD

1. No Apple Business Manager, entre com uma conta que tenha a função Administrador ou Gerente de Pessoas.
2. Clique em Configurações na parte inferior da barra lateral, clique em Fonte de Dados abaixo de Configurações da Organização e clique em Conectar-se à Fonte de Dados.
3. Clique em Conectar ao lado do SCIM, leia atentamente o aviso, clique em Copiar e em Fechar.
[A janela Conectar ao SCIM, que fornece um token e um botão Copiar abaixo dele.] Mantenha essa janela aberta para copiar a URL do Locatário do Apple Business Manager para o Azure AD, que é 'https://federation.apple.com/feeds/business/scim '

    ![Apple Business Manager](media/applebusinessmanager-provisioning-tutorial/scim-token.png)

> [!NOTE]
> O token secreto não deve ser compartilhado com ninguém que não seja o administrador do Azure AD.

## <a name="step-3-add-apple-business-manager-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar o Apple Business Manager por meio da galeria de aplicativos do Azure AD

Adicione o Apple Business Manager por meio da galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento no Apple Business Manager. Se você já configurou o Apple Business Manager para SSO, use o mesmo aplicativo. No entanto, recomendamos que você crie um aplicativo diferente ao testar a integração no início. Saiba mais sobre como adicionar um aplicativo da galeria [aqui](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo de provisionamento 

No Azure AD, é possível definir quem estará no escopo de provisionamento com base na atribuição ao aplicativo ou nos atributos do usuário/grupo. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você optar por definir quem estará no escopo de provisionamento com base somente em atributos do usuário ou do grupo, poderá usar um filtro de escopo, conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir usuários ao Apple Business Manager, você precisará selecionar uma função diferente de **Acesso Padrão**. Os usuários com a função Acesso Padrão são excluídos do provisionamento e serão marcados como "Não qualificado efetivamente" nos logs de provisionamento. Se a única função disponível no aplicativo for a de acesso padrão, você poderá [atualizar o manifesto do aplicativo](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar outras funções. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo de provisionamento é definido para usuários e grupos atribuídos, é possível controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo com base no filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="step-5-configure-automatic-user-provisioning-to-apple-business-manager"></a>Etapa 5. Configurar o provisionamento automático de usuário no Apple Business Manager

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Apple Business Manager**.

    ![O Apple Business Manager na lista Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia Provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Guia Provisionamento automático](common/provisioning-automatic.png)

5. Na seção **Credenciais de Administrador**, insira os valores de **URL base do SCIM 2.0 e Token de Acesso** recuperados do Apple Business Manager em **URL do Locatário** e **Token Secreto**, respectivamente. Clique em **Testar Conectividade** para verificar se o Azure AD pode se conectar ao Apple Business Manager. Se a conexão falhar, verifique se a sua conta do Apple Business Manager tem permissões de Administrador e tente novamente.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

> [!NOTE]
>Se a conexão for bem-sucedida, o Apple Business Manager mostrará a conexão do SCIM como ativa. Esse processo pode levar até 60 segundos para que o Apple Business Manager reflita o status de conexão mais recente.

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Save** (Salvar).

8. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Apple Business Manager**.

9. Examine os atributos de usuário que serão sincronizados do Azure AD para o Apple Business Manager na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário no Apple Business Manager em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

   |Atributo|Type|
   |---|---|
   |ativo|Boolean|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.givenName|String|
   |externalId|String|
   |localidade|String|
   |timezone|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|

10. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento do Azure AD no Apple Business Manager, altere o **Status de Provisionamento** para **Ativado** na seção Configurações.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou os grupos que deseja provisionar no Apple Business Manager escolhendo os valores desejados em **Escopo** na seção **Configurações**.

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando a configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução.

## <a name="step-6-monitor-your-deployment"></a>Etapa 6. Monitorar a implantação
Depois de configurar o provisionamento, use os seguintes recursos para monitorar a implantação:

1. Use os [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais usuários foram provisionados com êxito ou não
2. Confira a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o status do ciclo de provisionamento e saber como fechá-la para concluir
3. Se a configuração de provisionamento parecer estar em um estado não íntegro, o aplicativo entrará em quarentena. Saiba mais sobre os estados de quarentena [aqui](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Examinar os requisitos do SCIM para o Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apdd88331cd6)
* [Como uma ID da Pessoa é usada no Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apd69e1e48e9)
* [Usar o SCIM para importar usuários para o Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apd3ec7b95ad)
* [Resolver conflitos entre contas de usuário do SCIM no Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apd313013d12)
* [Excluir as contas do Azure AD exibidas no Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apdaa5798fbe)
* [Exibir a atividade do SCIM no Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apd1bfd8dfde)
* [Gerenciar o token do SCIM e as conexões existentes no Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apdc9a823611)
* [Desconectar a conexão do SCIM no Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apd609be3a61)
* [Solução de problemas de conexão do SCIM no Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apd403a0f3bd/web)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)