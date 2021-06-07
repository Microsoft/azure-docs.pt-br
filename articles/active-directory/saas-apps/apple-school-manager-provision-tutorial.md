---
title: 'Tutorial: Configurar o Apple School Manager para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente contas de usuário do Azure AD para o Apple School Manager.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: f006c177-7b35-4af1-84f2-db4a4e2bf96a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/08/2020
ms.author: Zhchia
ms.openlocfilehash: be21151be719098dd6ec65f369ea6fc81ff45a98
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97591813"
---
# <a name="tutorial-configure-apple-school-manager-for-automatic-user-provisioning"></a>Tutorial: Configurar o Apple School Manager para o provisionamento automático de usuário



Este tutorial descreve as etapas que você precisará executar no Apple School Manager e no Azure AD (Active Directory) para configurar o provisionamento automático de usuário. Quando configurado, o Azure AD provisiona e desprovisiona automaticamente usuários e grupos no [Apple School Manager](https://school.apple.com/) usando o serviço de Provisionamento do Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Funcionalidades com suporte
> [!div class="checklist"]
> * Criar usuários no Apple School Manager
> * Remover usuários no Apple School Manager quando eles não precisarem mais de acesso
> * Manter atributos de usuário específicos sincronizados entre o Azure AD e o Apple School Manager

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* [Um locatário do Azure AD](../develop/quickstart-create-new-tenant.md) 
* Uma conta de usuário no Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento (por exemplo, Administrador de aplicativos, Administrador de aplicativos de nuvem, Proprietário de aplicativo ou Administrador global). 
* Uma conta do Apple School Manager com a função Administrador, Gerente de Unidades ou Gerente de Pessoas.

> [!NOTE]
> A transferência de token para o Azure AD e o estabelecimento de uma conexão bem-sucedida deverão ser concluídos em quatro dias do calendário ou o processo precisará ser reiniciado.

## <a name="step-1-plan-your-provisioning-deployment"></a>Etapa 1. Planeje a implantação do provisionamento
1. Saiba mais sobre [como funciona o serviço de provisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem estará no [escopo de provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais dados serão [mapeados entre o Azure AD e o Apple School Manager](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-apple-school-manager-to-support-provisioning-with-azure-ad"></a>Etapa 2. Configurar o Apple School Manager para dar suporte ao provisionamento com o Azure AD

1. No Apple School Manager, entre com uma conta que tenha a função Administrador, Gerente de Unidades ou Gerente de Pessoas.
2. Clique em Configurações na parte inferior da barra lateral, clique em Fonte de Dados abaixo de Configurações da Organização e clique em Conectar-se à Fonte de Dados.
3. Clique em Conectar ao lado do SCIM, leia atentamente o aviso, clique em Copiar e em Fechar.
[A janela Conectar ao SCIM, que fornece um token e um botão Copiar abaixo dele.] Mantenha essa janela aberta para copiar a URL do Locatário do Apple Business Manager para o Azure AD, que é 'https://federation.apple.com/feeds/school/scim '

    ![Apple School Manager](media/appleschoolmanager-provisioning-tutorial/scim-token.png)

> [!NOTE]
> O token secreto não deve ser compartilhado com ninguém que não seja o administrador do Azure AD.

## <a name="step-3-add-apple-school-manager-from-the-azure-ad-application-gallery"></a>Etapa 3. Adicionar o Apple School Manager por meio da galeria de aplicativos do Azure AD

Adicione o Apple School Manager por meio da galeria de aplicativos do Azure AD para começar a gerenciar o provisionamento no Apple School Manager. Se você já configurou o Apple School Manager para SSO, use o mesmo aplicativo. No entanto, recomendamos que você crie um aplicativo diferente ao testar a integração no início. Saiba mais sobre como adicionar um aplicativo da galeria [aqui](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Etapa 4. Defina quem estará no escopo de provisionamento 

No Azure AD, é possível definir quem estará no escopo de provisionamento com base na atribuição ao aplicativo ou nos atributos do usuário/grupo. Se você optar por definir quem estará no escopo de provisionamento com base na atribuição, poderá usar as [etapas](../manage-apps/assign-user-or-group-access-portal.md) a seguir para atribuir usuários e grupos ao aplicativo. Se você optar por definir quem estará no escopo de provisionamento com base somente em atributos do usuário ou do grupo, poderá usar um filtro de escopo, conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir usuários ao Apple School Manager, você precisará selecionar uma função diferente de **Acesso Padrão**. Os usuários com a função Acesso Padrão são excluídos do provisionamento e serão marcados como "Não qualificado efetivamente" nos logs de provisionamento. Se a única função disponível no aplicativo for a de acesso padrão, você poderá [atualizar o manifesto do aplicativo](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar outras funções. 

* Comece pequeno. Teste com um pequeno conjunto de usuários e grupos antes de implementar para todos. Quando o escopo de provisionamento é definido para usuários e grupos atribuídos, é possível controlar isso atribuindo um ou dois usuários ou grupos ao aplicativo. Quando o escopo é definido para todos os usuários e grupos, é possível especificar um [atributo com base no filtro de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="step-5-configure-automatic-user-provisioning-to-apple-school-manager"></a>Etapa 5. Configurar o provisionamento automático de usuário no Apple School Manager

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **Aplicativos Empresariais** e **Todos os Aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Apple School Manager**.

    ![O Apple School Manager na lista Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia Provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Guia Provisionamento automático](common/provisioning-automatic.png)

5. Na seção **Credenciais de Administrador**, insira os valores de **URL base do SCIM 2.0 e Token de Acesso** recuperados do Apple School Manager em **URL do Locatário** e **Token Secreto**, respectivamente. Clique em **Testar Conectividade** para verificar se o Azure AD pode se conectar ao Apple School Manager. Se a conexão falhar, verifique se a sua conta do Apple School Manager tem permissões de Administrador e tente novamente.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

> [!NOTE]
>Se a conexão for bem-sucedida, o Apple School Manager mostrará a conexão do SCIM como ativa. Esse processo pode levar até 60 segundos para que o Apple School Manager reflita o status de conexão mais recente.

6. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Save** (Salvar).

8. Na seção **Mapeamentos**, selecione **Sincronizar Usuários do Azure Active Directory com o Apple School Manager**.

9. Examine os atributos de usuário que serão sincronizados do Azure AD para o Apple School Manager na seção **Mapeamento de Atributos**. Os atributos selecionados como propriedades **Correspondentes** são usados para fazer a correspondência das contas de usuário no Apple School Manager em operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

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

11. Para habilitar o serviço de provisionamento do Azure AD no Apple School Manager, altere o **Status de Provisionamento** para **Ativado** na seção Configurações.

    ![Status do provisionamento ativado](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou os grupos que deseja provisionar no Apple School Manager escolhendo os valores desejados em **Escopo** na seção **Configurações**.

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
* [Examinar os requisitos do SCIM para o Apple School Manager](https://support.apple.com/guide/apple-school-manager/apdd88331cd6)
* [Como uma ID da Pessoa é usada no Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd69e1e48e9)
* [Usar o SCIM para importar usuários para o Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd3ec7b95ad)
* [Resolver conflitos entre contas de usuário do SCIM no Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd313013d12)
* [Excluir as contas do Azure AD exibidas no Apple School Manager](https://support.apple.com/guide/apple-school-manager/apdaa5798fbe)
* [Exibir a atividade do SCIM no Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd1bfd8dfde)
* [Gerenciar o token do SCIM e as conexões existentes no Apple School Manager](https://support.apple.com/guide/apple-school-manager/apdc9a823611)
* [Desconectar a conexão do SCIM no Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd609be3a61)
* [Solução de problemas de conexão do SCIM no Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd403a0f3bd)

## <a name="next-steps"></a>Próximas etapas

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)