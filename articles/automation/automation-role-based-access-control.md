---
title: "Controle de acesso baseado em função na Automação do Azure | Microsoft Docs"
description: "O RBAC (controle de acesso baseado em função) permite o gerenciamento de acesso aos recursos do Azure. Esse artigo descreve como configurar o RBAC na Automação do Azure."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
keywords: "rbac de automação, controle de acesso baseado em função, rbac azure"
ms.assetid: 04b5625e-0ee8-4b5b-85cd-7734c1b3d4a3
ms.service: automation
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/30/2016
ms.author: magoedte;sngun
ms.openlocfilehash: 946d80d40ac0566db72c787f260f2d4faff01e6d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="role-based-access-control-in-azure-automation"></a>Controle de acesso com base em função na Automação do Azure
## <a name="role-based-access-control"></a>Controle de acesso baseado em função
O RBAC (controle de acesso baseado em função) permite o gerenciamento de acesso aos recursos do Azure. Com o [RBAC](../active-directory/role-based-access-control-configure.md), você pode separar as tarefas dentro de sua equipe e conceder somente a quantidade de acesso que os usuários, os grupos e os aplicativos precisam para realizar seus trabalhos. O acesso baseado em função pode ser concedido aos usuários que usam o Portal do Azure, as ferramentas de Linha de Comando do Azure ou as APIs de Gerenciamento do Azure.

## <a name="rbac-in-automation-accounts"></a>RBAC em Contas de Automação
Na Automação do Azure, o acesso é concedido atribuindo a função apropriada de RBAC aos usuários, grupos e aplicativos no escopo da Conta de Automação. Veja a seguir as funções internas com suporte de uma Conta de Automação:  

| **Função** | **Descrição** |
|:--- |:--- |
| Proprietário |A função Proprietário permite acesso a todos os recursos e ações em uma Conta de Automação, incluindo o fornecimento de acesso a outros usuários, grupos e aplicativos para gerenciar a conta de Automação. |
| Colaborador |A função Colaborador permite gerenciar tudo, exceto a modificação de permissões de acesso de outros usuários para uma conta de Automação. |
| Leitor |A função Leitor permite que você veja todos os recursos em uma conta de Automação, mas não permite realizar alterações. |
| Operador de automação |A função Operador de Automação permite realizar tarefas operacionais como iniciar, parar, suspender, continuar e agendar trabalhos. Essa função é útil se você deseja evitar que seus recursos da Conta de Automação, como runbooks e ativos de credenciais, sejam exibidos ou modificados, mas ainda permitir que os membros de sua organização executem esses runbooks. |
| Administrador de Acesso do Usuário |A função Administrador de Acesso do Usuário permite que você gerencie o acesso dos usuários às Contas de Automação do Azure. |

> [!NOTE]
> Você não pode conceder direitos de acesso a um runbook ou a runbooks específicos, apenas aos recursos e ações em uma conta de Automação.  
> 
> 

Neste artigo, vemos como configurar o RBAC na Automação do Azure. Mas, primeiro, vamos examinar mais de perto as permissões individuais concedidas ao Colaborador, Leitor, Operador de Automação e Administrador de Acesso do Usuário para obtermos um bom entendimento antes de conceder a alguém direitos para a conta de Automação.  Caso contrário, isso poderia ter consequências indesejáveis ou não intencionais.     

## <a name="contributor-role-permissions"></a>Permissões da função Colaborador
A tabela a seguir apresenta as ações específicas que podem ser executadas pela função Colaborador na Automação.

| **Tipo de recurso** | **Ler** | **Gravar** | **Excluir** | **Outras ações** |
|:--- |:--- |:--- |:--- |:--- |
| Conta de Automação do Azure |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Ativo de certificado de automação |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Ativo de conexão de automação |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Ativo de Tipo de Conexão de Automação |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Ativo de credencial de automação |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Ativo de Agendamento de Automação |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Ativo de variável de automação |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Configuração de Estado Desejado de Automação | | | |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |
| Tipo de Recurso de Hybrid Runbook Worker |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Trabalho de Automação do Azure |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |
| Fluxo de Trabalho de Automação |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Agenda de Trabalho de Automação |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Módulo de Automação |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Runbook de Automação do Azure |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |
| Rascunho de Runbook de Automação |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | | |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |
| Trabalho de Teste de Rascunho de Runbook de Automação |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |
| Webhook de Automação |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |

## <a name="reader-role-permissions"></a>Permissões da função Leitor
A tabela a seguir apresenta as ações específicas que podem ser executadas pela função Leitor na Automação.

| **Tipo de recurso** | **Ler** | **Gravar** | **Excluir** | **Outras ações** |
|:--- |:--- |:--- |:--- |:--- |
| Administrador de assinatura clássico |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Bloqueio de gerenciamento |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Permissão |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Operações de provedor |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Atribuição de função |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Definição de função |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |

## <a name="automation-operator-role-permissions"></a>Permissões de função de Operador de Automação
A tabela a seguir apresenta as ações específicas que podem ser executadas pela função Operador de Automação na Automação.

| **Tipo de recurso** | **Ler** | **Gravar** | **Excluir** | **Outras ações** |
|:--- |:--- |:--- |:--- |:--- |
| Conta de Automação do Azure |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Ativo de certificado de automação | | | | |
| Ativo de conexão de automação | | | | |
| Ativo de Tipo de Conexão de Automação | | | | |
| Ativo de credencial de automação | | | | |
| Ativo de Agendamento de Automação |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | | |
| Ativo de variável de automação | | | | |
| Configuração de Estado Desejado de Automação | | | | |
| Tipo de Recurso de Hybrid Runbook Worker | | | | |
| Trabalho de Automação do Azure |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |
| Fluxo de Trabalho de Automação |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Agenda de Trabalho de Automação |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | | |
| Módulo de Automação | | | | |
| Runbook de Automação do Azure |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Rascunho de Runbook de Automação | | | | |
| Trabalho de Teste de Rascunho de Runbook de Automação | | | | |
| Webhook de Automação | | | | |

Para obter mais detalhes, [Ações de um Operador de Automação](../active-directory/role-based-access-built-in-roles.md#automation-operator) lista as ações compatíveis com a função de Operador de Automação na Conta de Automação e seus recursos.

## <a name="user-access-administrator-role-permissions"></a>Permissões da função Administrador de Acesso do Usuário
A tabela a seguir apresenta as ações específicas que podem ser executadas pela função Administrador de Acesso do Usuário na Automação.

| **Tipo de recurso** | **Ler** | **Gravar** | **Excluir** | **Outras ações** |
|:--- |:--- |:--- |:--- |:--- |
| Conta de Automação do Azure |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Ativo de certificado de automação |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Ativo de conexão de automação |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Ativo de Tipo de Conexão de Automação |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Ativo de credencial de automação |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Ativo de Agendamento de Automação |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Ativo de variável de automação |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Configuração de Estado Desejado de Automação | | | | |
| Tipo de Recurso de Hybrid Runbook Worker |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Trabalho de Automação do Azure |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Fluxo de Trabalho de Automação |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Agenda de Trabalho de Automação |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Módulo de Automação |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Runbook de Automação do Azure |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Rascunho de Runbook de Automação |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Trabalho de Teste de Rascunho de Runbook de Automação |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Webhook de Automação |![Status Verde](media/automation-role-based-access-control/green-checkmark.png) | | | |

## <a name="configure-rbac-for-your-automation-account-using-azure-portal"></a>Configurar o RBAC para sua Conta de Automação usando o Portal do Azure
1. Faça logon no [Portal do Azure](https://portal.azure.com/) e abra sua conta de Automação na página Contas de Automação.  
2. Clique no controle **Acesso** no canto superior direito. Isso abre a página **Usuários** onde você pode adicionar novos usuários, grupos e aplicativos para gerenciar sua Conta de Automação e exibir as funções existentes que podem ser configuradas para ela.  
   
   ![Botão de acesso](media/automation-role-based-access-control/automation-01-access-button.png)  

> [!NOTE]
> **Os administradores de assinatura** já existem como usuários padrão. O grupo de administradores de assinatura do Active Directory inclui os administradores de serviço e os coadministradores de sua assinatura do Azure. O administrador de serviços é o proprietário da assinatura do Azure e de seus recursos e também herdará a função de proprietário das Contas de Automação. Isso significa que o acesso é **Herdado** para **administradores e coadministradores de serviço** de uma assinatura e ele é **Atribuído** a todos os outros usuários. Clique em **Administradores de assinatura** para ver mais detalhes sobre suas permissões.  
> 
> 

### <a name="add-a-new-user-and-assign-a-role"></a>Adicionar um novo usuário e atribuir uma função
1. Na página Usuários, clique em **Adicionar** para abrir a página **Adicionar acesso**, na qual você pode adicionar um usuário, grupo ou aplicativo e atribuir uma função a eles.  
   
   ![Adicionar usuário](media/automation-role-based-access-control/automation-02-add-user.png)  
2. Selecione uma função na lista de funções disponíveis. Escolheremos a função **Leitor** , mas você pode escolher qualquer uma das funções internas disponíveis compatíveis com a Conta de Automação ou função personalizada que você tenha definido.  
   
   ![Escolher função](media/automation-role-based-access-control/automation-03-select-role.png)  
3. Clique em **Adicionar usuários** para abrir a página **Adicionar usuários**. Se você estiver adicionado usuários, grupos ou aplicativos para gerenciar sua assinatura, eles estarão listados e você poderá selecioná-los para adicionar o acesso. Se não houver usuários listados, ou se o usuário que você quer adicionar não estiver na lista, clique em **Convidar** para abrir a página **Convidar uma pessoa** e convidar um usuário com um endereço de email válido da conta da Microsoft, como as Ids do Outlook.com, do OneDrive ou do Xbox Live. Depois de inserir o endereço de email do usuário, clique em **Selecionar** para adicionar o usuário e clique em **OK**. 
   
   ![Adicionar usuários](media/automation-role-based-access-control/automation-04-add-users.png)  
   
   Agora você já deve ver o usuário adicionado à página **Usuários** com a função **Leitor** atribuída.  
   
   ![Listar usuários](media/automation-role-based-access-control/automation-05-list-users.png)  
   
   Você também pode atribuir uma função para o usuário na página **Funções** . 
4. Clique em **Funções** na página Usuários para abrir a página **Funções**. Nessa página, você pode exibir o nome da função além do número de usuários e grupos atribuídos a essa função.
   
    ![Atribuir função na página de usuários](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)  
   
   > [!NOTE]
   > O controle de acesso baseado em função só pode ser definido no nível da Conta de Automação e não em qualquer recurso abaixo dela.
   > 
   > 
   
    Você pode atribuir mais de uma função a um usuário, grupo ou aplicativo. Por exemplo, se adicionarmos a função **Operador de Automação** com a **função Leitor** ao usuário, essas funções poderão exibir todos os recursos de automação e executar os trabalhos de runbook. Você pode expandir a lista suspensa para exibir uma lista de funções atribuídas ao usuário.  
   
    ![Exibir várias funções](media/automation-role-based-access-control/automation-07-view-multiple-roles.png)  

### <a name="remove-a-user"></a>Remover um usuário
Você pode remover a permissão de acesso de um usuário que não está gerenciando a Conta de Automação ou que não trabalha mais para a organização. Veja abaixo as etapas para remover um usuário: 

1. Na página **Usuários**, escolha a atribuição de função que você deseja remover.
2. Clique no botão **Remover** na página de detalhes da atribuição.
3. Clique em **Sim** para confirmar a remoção. 
   
   ![Remover usuários](media/automation-role-based-access-control/automation-08-remove-users.png)  

## <a name="role-assigned-user"></a>Usuário com função atribuída
Quando um usuário atribuído a uma função entra em sua Conta de Automação, ele pode ver a conta do proprietário exibida na lista de **Diretórios Padrão**. Para exibir a Conta de Automação à qual ele foi adicionado, é preciso alternar o diretório padrão para o diretório padrão do proprietário.  

![Diretório padrão](media/automation-role-based-access-control/automation-09-default-directory-in-role-assigned-user.png)  

### <a name="user-experience-for-automation-operator-role"></a>Experiência do usuário para a função de Operador de Automação
Quando um usuário, atribuído à função de Operador de Automação, exibe a Conta de Automação atribuída, ele só consegue exibir a lista de runbooks, trabalhos de runbook e agendamentos criados na Conta de Automação, mas não exibe suas definições. Ele pode iniciar, parar, suspender, continuar ou agendar o trabalho de runbook. O usuário não tem acesso a outros recursos da Automação, como configurações, grupos de trabalho híbridos ou nós DSC.  

![Nenhum acesso aos recursos](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)  

Quando o usuário clica no runbook, os comandos para exibir a fonte ou editar o runbook não são fornecidos uma vez que a função de Operador de Automação não permite acesso a esses comandos.  

![Sem acesso para editar o runbook](media/automation-role-based-access-control/automation-11-no-access-to-edit-runbook.png)  

O usuário tem acesso para exibir e criar agendamentos, mas não tem acesso a qualquer outro tipo de ativo.  

![Nenhum acesso a ativos](media/automation-role-based-access-control/automation-12-no-access-to-assets.png)  

Esse usuário não tem acesso para exibir os Webhooks associados a um runbook

![Nenhum acesso ao Webhooks](media/automation-role-based-access-control/automation-13-no-access-to-webhooks.png)  

## <a name="configure-rbac-for-your-automation-account-using-azure-powershell"></a>Configurar o RBAC para sua Conta de Automação usando o Azure PowerShell
O acesso baseado em função também pode ser configurado para uma Conta de Automação usando os seguintes [cmdlets do Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md).

• [Get-AzureRmRoleDefinition](https://msdn.microsoft.com/library/mt603792.aspx) lista todas as funções RBAC que estão disponíveis no Azure Active Directory. Você pode usar esse comando juntamente com a propriedade **Nome** para listar todas as ações que podem ser executadas por uma função específica.  
    **Exemplo:**  
    ![Obter a definição da função](media/automation-role-based-access-control/automation-14-get-azurerm-role-definition.png)  

• [Get-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt619413.aspx) lista as atribuições de função do RBAC do Azure AD no escopo especificado. Sem parâmetros, esse comando retorna todas as atribuições de função realizadas na assinatura. Use o parâmetro **ExpandPrincipalGroups** para listar as atribuições de acesso para o usuário especificado, bem como para os grupos dos quais o usuário é membro.  
    **Exemplo:** use o comando a seguir para listar todos os usuários e suas funções em uma conta de automação.

    Get-AzureRMRoleAssignment -scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>” 

![Obter a atribuição da função](media/automation-role-based-access-control/automation-15-get-azurerm-role-assignment.png)

• [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603580.aspx) para atribuir acesso de usuários, grupos e aplicativos a um determinado escopo.  
    **Exemplo:** use o comando a seguir para atribuir a função "Operador de Automação" para um usuário no escopo da Conta de Automação.

    New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName "Automation operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”  

![Nova atribuição de função](media/automation-role-based-access-control/automation-16-new-azurerm-role-assignment.png)

• Use [Remove-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603781.aspx) para remover o acesso de um usuário, grupo ou aplicativo especificado de um determinado escopo.  
    **Exemplo:** use o comando a seguir para remover o usuário da função "Operador de Automação" do escopo da Conta de Automação.

    Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName "Automation Operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”

Nos exemplos acima, substitua a **Id de entrada**, a **Id da assinatura**, o **nome do grupo de recursos** e o **nome da conta de Automação** pelos detalhes de sua conta. Escolha **sim** quando solicitado a confirmar antes de continuar a remover a atribuição de função de usuário.   

## <a name="next-steps"></a>Próximas etapas
* Para obter informações sobre as diversas maneiras de configurar o RBAC para a Automação do Azure, consulte [gerenciar o RBAC com o Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md).
* Para obter detalhes sobre diferentes maneiras de iniciar um runbook, confira [Iniciando um runbook](automation-starting-a-runbook.md)
* Para obter informações sobre os diferentes tipos de runbook, consulte [Tipos de runbook da Automação do Azure](automation-runbook-types.md)

