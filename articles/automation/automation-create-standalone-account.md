---
title: "Como criar conta autônoma de automação do Azure | Microsoft Docs"
description: "Tutorial que explica a criação, os testes e o uso de exemplo da autenticação de entidade de segurança na Automação do Azure."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 2f783441-15c7-4ea0-ba27-d7daa39b1dd3
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/18/2017
ms.author: magoedte
ms.openlocfilehash: e3c18c7886c8338efc6168464b63a9557909a769
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-standalone-azure-automation-account"></a>Como criar conta autônoma de automação do Azure
Este tópico mostra como criar uma conta de automação no portal do Azure, se desejar avaliar e aprender a automação do Azure sem incluir a integração com o Log Analytics do OMS para fornecer monitoramento avançado de trabalhos de runbook ou soluções de gerenciamento adicionais.  Você pode adicionar essas soluções de gerenciamento ou integrar o Log Analytics a qualquer momento no futuro.  Com a conta de automação, você será capaz de autenticar runbooks gerenciamento de recursos no Azure Resource Manager ou implantação clássica do Azure.

Quando você cria uma conta de Automação no Portal do Azure, ela cria automaticamente:

* A conta Executar como, que cria uma nova entidade de serviço no Azure Active Directory, um certificado, e atribui o controle de acesso baseado em função de Colaborador (RBAC), que será usado para gerenciar os recursos do Resource Manager usando runbooks.   
* Conta Executar como clássica carregando um certificado de gerenciamento, que é usado para gerenciar os recursos clássicos usando runbooks.  

Isso simplifica o processo para você e o ajuda a começar a criar e a implantar rapidamente os runbooks para dar suporte às suas necessidades de automação.  

## <a name="permissions-required-to-create-automation-account"></a>Permissões necessárias para criar a conta da Automação
Para criar ou atualizar uma conta da Automação, você deve ter os seguintes privilégios específicos e as permissões necessárias para concluir este tópico.   
 
* Para criar uma conta da Automação, sua conta de usuário do AD precisa ser adicionada a uma função com permissões equivalentes à função de Proprietário para recursos Microsoft.Automation conforme descrito no artigo [Controle de acesso baseado em função na Automação do Azure](automation-role-based-access-control.md).  
* Se a Configuração dos registros do aplicativo estiver definida como **Sim**, os usuários não administrativos em seu locatário do Azure AD poderão [Registrar aplicativos do AD](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions).  Se a configuração de registros do aplicativo estiver definido como **Não**, o usuário que executa esta ação deverá ser um administrador global no Azure AD. 

Caso não seja membro da instância do Active Directory da assinatura antes de ser adicionados à função de administrador global/coadministrador da assinatura, você será adicionado ao Active Directory como convidado. Nesse caso, você receberá um aviso "Você não tem permissões para criar..." na folha **Adicionar Conta de Automação**. Os usuários adicionados à função de administrador global/coadministrador primeiro podem ser removidos das assinaturas da instância do Active Directory e adicionados novamente para torná-los Usuários completos no Active Directory. Para verificar essa situação, no painel **Azure Active Directory** no portal do Azure, selecione **Usuários e grupos**, selecione **Todos os usuários** e, depois de selecionar o usuário específico, selecione **Perfil**. O valor do atributo **Tipo de usuário** sob o perfil de usuários não deve ser igual a **Convidado**.

## <a name="create-a-new-automation-account-from-the-azure-portal"></a>Criar uma nova Conta de automação no Portal do Azure
Nesta seção, execute as seguintes etapas para criar uma conta da Automação do Azure no Portal do Azure.    

1. Conecte-se no Portal do Azure com uma conta que seja membro da função Administradores da Assinatura e coadministradora da assinatura.
2. Clique em **Novo**.<br><br> ![Selecione a opção Novo no portal do Azure](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  
3. Procure **Automação** e, em seguida, nos resultados da pesquisa selecione **Automação e controle***.<br><br> ![Pesquise e selecione Automação no Marketplace](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)<br> 
3. Na folha Contas de Automação, clique em **Adicionar**.<br><br>![Adicionar Conta de Automação](media/automation-create-standalone-account/automation-create-automationacct-properties.png)


   > [!NOTE]
   > Caso você veja o seguinte aviso na folha **Adicionar Conta de Automação**, é porque sua conta não é um membro da função Administradores da Assinatura e coadministrador da assinatura.<br><br>![Aviso Adicionar Conta de Automação](media/automation-create-standalone-account/create-account-without-perms.png)
   > 
   > 
4. Na folha **Adicionar Conta de Automação**, na caixa **Nome**, digite um nome para a nova conta de Automação.
5. Se você tiver mais de uma assinatura, especifique uma para a nova conta, um **Grupo de recursos** novo ou existente, e uma **Localização** no datacenter do Azure.
6. Verifique se o valor **Sim** está selecionado para a opção **Criar conta Executar Como do Azure** e clique no botão **Criar**.  
   
   > [!NOTE]
   > Se optar por não criar a conta Executar como selecionando a opção **Não**, você receberá uma mensagem de aviso na folha **Adicionar Conta de Automação**.  Embora a conta seja criada no Portal do Azure, ela não tem uma identidade de autenticação correspondente em seu serviço de diretório de assinaturas clássico ou do Resource Manager e, portanto, não haverá acesso aos recursos em sua assinatura.  Isso impedirá que qualquer runbook que faça referência a essa conta seja capaz de se autenticar e de executar tarefas nos recursos nesses modelos de implantação.
   > 
   > ![Aviso Adicionar Conta de Automação](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)<br>
   > Enquanto a entidade de serviço não for criada, a função Colaborador não será atribuída.
   > 

7. Enquanto o Azure cria a conta de Automação, você poderá acompanhar o andamento em **Notificações** no menu.

### <a name="resources-included"></a>Recursos incluídos
Quando a criação da conta de Automação tiver sido criada com êxito, vários recursos serão criados automaticamente para você.  A tabela a seguir resume os recursos para a conta Executar como.<br>

| Recurso | Descrição |
| --- | --- |
| Runbook AzureAutomationTutorial |Um exemplo de runbook Gráfico que demonstra como autenticar usando a conta Executar como e que obtém todos os recursos do Resource Manager. |
| Runbook do AzureAutomationTutorialScript |Um exemplo de runbook do PowerShell que demonstra como autenticar usando a conta Executar como e que obtém todos os recursos do Resource Manager. |
| Runbook AzureAutomationTutorialPython2 |Um runbook de python de exemplo que demonstra como autenticar usando a conta executar como e, em seguida, lista os grupos de recursos presentes na assinatura especificada. |
| AzureRunAsCertificate |O ativo de certificado criado se você tiver optado pela criação da conta Executar como durante a criação da conta de Automação ou usando o script do PowerShell abaixo para uma conta existente.  Ele permite autenticar com o Azure para que você possa gerenciar recursos do Azure Resource Manager de runbooks.  Esse certificado tem um tempo de vida de um ano. |
| AzureRunAsConnection |O ativo de conexão automaticamente criado durante a criação da conta de Automação ou usando o script do PowerShell abaixo para uma conta existente. |

A tabela a seguir resume os recursos para a conta Executar como Clássica.<br>

| Recurso | Descrição |
| --- | --- |
| Runbook do AzureClassicAutomationTutorial |Um runbook gráfico de exemplo que obtém todas as VMs clássicas em uma assinatura usando a conta Executar como clássica (certificado) e, em seguida, exibe o nome e o status da VM. |
| Runbook do script AzureClassicAutomationTutorial |Um runbook do PowerShell de exemplo que obtém todas as VMs clássicas em uma assinatura usando a conta Executar como clássica (certificado) e, em seguida, exibe o nome e o status da VM. |
| AzureClassicRunAsCertificate |Ativo de certificado criado automaticamente que é usado para autenticar no Azure para que você possa gerenciar recursos clássicos do Azure de runbooks.  Esse certificado tem um tempo de vida de um ano. |
| AzureClassicRunAsConnection |Ativo de conexão criado automaticamente que é usado para autenticar no Azure para que você possa gerenciar recursos clássicos do Azure de runbooks. |


## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre a autorização gráfica, confira [Criação gráfica na automação do Azure](automation-graphical-authoring-intro.md).
* Para começar a usar os runbooks do PowerShell, confira [Meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md).
* Para começar a usar os runbooks do fluxo de trabalho do PowerShell, veja [Meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md).
* Para começar a usar runbooks Python2, veja [Meu primeiro runbook Python2](automation-first-runbook-textual-python2.md).
