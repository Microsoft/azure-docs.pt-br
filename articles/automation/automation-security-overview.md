---
title: Visão geral da autenticação de conta da Automação do Azure
description: Este artigo mostra uma visão geral da autenticação de conta da Automação do Azure.
keywords: segurança de automação, automação segura; autenticação de automação
services: automation
ms.subservice: process-automation
ms.date: 02/01/2021
ms.topic: conceptual
ms.openlocfilehash: e3ed14a0845d90a1b9b951dd5175acf6233f8718
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99254992"
---
# <a name="automation-account-authentication-overview"></a>Visão geral da autenticação da conta de automação

A Automação do Azure permite automatizar tarefas em relação a recursos no Azure, locais e com outros provedores de nuvem, como AWS (Amazon Web Services). Você pode usar runbooks para automatizar suas tarefas ou um Hybrid Runbook Worker se você tiver processos comerciais ou operacionais para gerenciar fora do Azure. O trabalho em qualquer um desses ambientes exige permissões para acessar com segurança os recursos com os direitos mínimos necessários.

Este artigo aborda os vários cenários de autenticação compatíveis com a Automação do Azure e mostra como iniciar com base nos ambientes que você precisa gerenciar.

## <a name="automation-account"></a>Conta de automação

Ao iniciar a Automação do Azure pela primeira vez, você deve criar pelo menos uma conta de Automação. As contas de automação permitem isolar seus recursos de automação, runbooks, ativos e configurações dos recursos de outras contas. Você pode usar contas de automação para separar recursos em ambientes lógicos separados ou responsabilidades delegadas. Por exemplo, você pode usar uma conta para desenvolvimento, outra para produção e outra para seu ambiente local. Ou você pode dedicar uma conta de automação para gerenciar atualizações do sistema operacional em todas as suas máquinas com [Gerenciamento de atualizações](update-management/overview.md). 

Uma conta de Automação do Azure é diferente de sua conta da Microsoft ou de contas criadas em sua assinatura do Azure. Para obter uma introdução à criação de uma conta de Automação, veja [Criar uma conta de automação](automation-quickstart-create-account.md).

## <a name="automation-resources"></a>Recursos de automação

Os recursos de Automação para cada conta de Automação estão associados a uma única região do Azure, mas a conta pode gerenciar todos os recursos em sua assinatura do Azure. O principal motivo para criar contas de Automação em regiões diferentes seria se você tivesse políticas que exijam que dados e recursos sejam isolados em uma região específica.

Todas as tarefas que você cria em relação a recursos usando o Azure Resource Manager e os cmdlets do PowerShell na Automação do Azure devem ser autenticadas no Azure usando a autenticação baseada em credenciais de identidade organizacional do Azure Active Directory.

## <a name="run-as-accounts"></a>Contas Executar como

As contas Executar como na automação do Azure fornecem autenticação para gerenciar Azure Resource Manager recursos ou recursos implantados no modelo de implantação clássico. Há dois tipos de contas Executar como na automação do Azure:

* Conta Executar como do Azure: permite que você gerencie recursos do Azure com base no serviço de implantação e gerenciamento de Azure Resource Manager do Azure.
* Conta Executar como clássica do Azure: permite que você gerencie recursos clássicos do Azure com base no modelo de implantação clássico.

Para saber mais sobre os modelos de implantação Azure Resource Manager e clássico, consulte [Resource Manager e implantação clássica](../azure-resource-manager/management/deployment-models.md).

>[!NOTE]
>As assinaturas do CSP (Provedor de Soluções de Nuvem) do Azure são compatíveis apenas com o modelo do Azure Resource Manager. Serviços que não pertencem ao Azure Resource Manager não estão disponíveis no programa. Quando você estiver usando uma assinatura do CSP, a conta Executar como Clássica do Azure não será criada, mas a conta Executar como do Azure será criada. Para saber mais sobre assinaturas de CSP, consulte [Serviços disponíveis em assinaturas do CSP](/azure/cloud-solution-provider/overview/azure-csp-available-services).

Quando você cria uma conta de automação, a conta Executar como é criada por padrão ao mesmo tempo. Se você optou por não criá-lo junto com a conta de automação, ele poderá ser criado individualmente em um momento posterior. Uma conta Executar como clássica do Azure é opcional e é criada separadamente se você precisar gerenciar recursos clássicos.

### <a name="run-as-account"></a>Conta Executar como

Quando você cria uma conta Executar como, ela executa as seguintes tarefas:

* Cria um aplicativo do Azure AD com um certificado autoassinado, cria uma conta de entidade de serviço para o aplicativo no Azure AD e atribui a função de [colaborador](../role-based-access-control/built-in-roles.md#contributor) para a conta em sua assinatura atual. Você pode alterar a configuração de certificado para [leitor](../role-based-access-control/built-in-roles.md#reader) ou qualquer outra função. Para obter mais informações, confira [Controle de acesso baseado em função na Automação do Azure](automation-role-based-access-control.md).

* Cria um ativo de certificado de Automação chamado `AzureRunAsCertificate` na conta de Automação especificada. O ativo de certificado contém a chave privada do certificado que é usada pelo aplicativo do Azure AD.

* Cria um ativo de conexão de Automação chamado `AzureRunAsConnection` na conta de Automação especificada. O ativo de conexão contém a ID do aplicativo, a ID do locatário, a ID da assinatura e a impressão digital do certificado.

### <a name="azure-classic-run-as-account"></a>Conta Executar como Clássica do Azure

Quando você cria uma conta Executar como clássica do Azure, ela executa as seguintes tarefas:

> [!NOTE]
> Você deve ser um coadministrador na assinatura para criar ou renovar esse tipo de conta Executar como.

* Configurar um certificado de gerenciamento na assinatura.

* Cria um ativo de certificado de Automação chamado `AzureClassicRunAsCertificate` na conta de Automação especificada. O ativo de certificado contém a chave privada do certificado usada pelo certificado de gerenciamento.

* Cria um ativo de conexão de Automação chamado `AzureClassicRunAsConnection` na conta de Automação especificada. O ativo de conexão contém o nome da assinatura, a ID da assinatura e o nome do ativo de certificado.

## <a name="service-principal-for-run-as-account"></a>Entidade de serviço para conta Executar como

A entidade de serviço para uma conta Executar como não tem permissões para ler o Azure Active Directory por padrão. Se quiser adicionar permissões para ler ou gerenciar o Azure Active Directory, você precisará conceder as permissões na entidade de serviço em **Permissões de API**. Para saber mais, consulte [adicionar permissões para acessar sua API Web](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Permissões da conta Executar como

Esta seção define permissões para contas Executar como regulares e contas Executar como Clássicas.

* Para criar ou atualizar uma conta Executar como, um administrador de aplicativo no Azure Active Directory e um proprietário na assinatura podem concluir todas as tarefas.
* Para configurar ou renovar contas Executar como Clássicas, você deve ter a função de coadministrador no nível de assinatura. Para saber mais sobre as permissões de assinatura clássicas, consulte [Administradores de assinatura do Azure Clássico](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

Em uma situação em que você tem separação de tarefas, a tabela a seguir mostra uma listagem das tarefas, o cmdlet equivalente e as permissões necessárias:

|Tarefa|Cmdlet  |Permissões mínimas  |Onde você define as permissões|
|---|---------|---------|---|
|Criar aplicativo do Azure AD|[New-AzADApplication](/powershell/module/az.resources/new-azadapplication)     | Função Desenvolvedor de Aplicativos<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Início > Azure AD > Registros do Aplicativo |
|Adicione uma credencial ao aplicativo.|[New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential)     | Administrador do Aplicativo ou Administrador Global<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Início > Azure AD > Registros do Aplicativo|
|Criar e obter uma entidade de serviço do Azure AD|[New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)     | Administrador do Aplicativo ou Administrador Global<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Início > Azure AD > Registros do Aplicativo|
|Atribuir ou obter a função do Azure para a entidade de segurança especificada|[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Administrador ou Proprietário de acesso do usuário ou ter as seguintes permissões:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Assinatura](../role-based-access-control/role-assignments-portal.md)</br>Home > assinaturas > \<subscription name\> -controle de acesso (iam)|
|Criar ou remover um certificado de Automação|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](/powershell/module/az.automation/remove-azautomationcertificate)     | Colaborador no Grupo de Recursos         |Grupo de Recursos da Conta de Automação|
|Criar ou remover uma conexão de Automação|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](/powershell/module/az.automation/remove-azautomationconnection)|Colaborador no Grupo de Recursos |Grupo de Recursos da Conta de Automação|

<sup>1</sup> usuários não administradores em seu locatário do Azure ad podem [registrar aplicativos do AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app) se a opção os usuários do locatário do Azure ad **podem registrar aplicativos** na página **configurações do usuário** estiver definida como **Sim**. Se a configuração de registro do aplicativo for **Não**, o usuário que executa essa ação deverá ser conforme definido nesta tabela.

Se você não for um membro da instância do Active Directory da assinatura antes de ser adicionado à função de Administrador Global da assinatura, você será adicionado como convidado. Nessa situação, você receberá um `You do not have permissions to create…` aviso na página **adicionar conta de automação** .

Para verificar se a situação que produz a mensagem de erro foi remediada:

1. No painel do Azure Active Directory no portal do Azure, selecione **Usuários e grupos**.
2. Selecione **Todos os usuários**.
3. Escolha seu nome e, em seguida, selecione **Perfil**.
4. Verifique se o valor do atributo **Tipo de usuário** no perfil do usuário não está definido como **Convidado**.

## <a name="role-based-access-control"></a>Controle de acesso baseado em função

O controle de acesso baseado em função está disponível com o Azure Resource Manager para conceder ações permitidas a uma conta de usuário do Azure Active Directory e uma conta Executar como e autenticar a entidade de serviço. Leia o [artigo Controle de acesso baseado em função da Automação do Azure](automation-role-based-access-control.md) para obter mais informações que o ajudarão a desenvolver seu modelo para gerenciar permissões de Automação.

Se você tiver controles de segurança estritos para atribuição de permissão em grupos de recursos, será necessário atribuir a associação da conta Executar como à função **colaborador** no grupo de recursos.

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Autenticação de runbook com Hybrid Runbook Worker

Runbooks em execução em um Hybrid Runbook Worker em seu datacenter ou em relação a serviços de computação em outros ambientes de nuvem, como a AWS, não podem usar o mesmo método que é normalmente usado para autenticar runbooks em recursos do Azure. Isso ocorre porque esses recursos estão em execução fora do Azure e, assim, exigirão suas próprias credenciais de segurança definidas na Automação para se autenticarem em recursos que acessarão localmente. Para obter mais informações sobre a autenticação de runbook com trabalho de runbook, veja [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md).

Para runbooks que usam Hybrid Runbook Workers em VMs do Azure, é possível usar [autenticação de runbook com identidades gerenciadas](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) em vez de contas Executar como para autenticar em recursos do Azure.

## <a name="next-steps"></a>Próximas etapas

* Para criar uma conta de Automação no portal do Azure, veja [Criar uma conta de Automação do Azure autônoma](automation-create-standalone-account.md).
* Se preferir criar sua conta usando um modelo, veja [Criar uma conta de Automação usando um modelo do Azure Resource Manager](quickstart-create-automation-account-template.md).
* Para autenticação usando Amazon Web Services, veja [Autenticar runbooks com Amazon Web Services](automation-config-aws-account.md).