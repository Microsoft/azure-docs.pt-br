---
title: Como criar conta autônoma de automação do Azure
description: Este artigo orienta você pelas etapas de criação, teste e uso de uma autenticação de entidade de segurança de exemplo na Automação do Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 01/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cdffc339bee1f5456e4eeb619e566b1f9c34b143
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61076741"
---
# <a name="create-a-standalone-azure-automation-account"></a>Como criar conta autônoma de automação do Azure

Este artigo mostra como criar uma conta da Automação do Azure no portal do Azure. Você pode usar a conta de automação do portal para avaliar e saber mais sobre a automação sem usar soluções de gerenciamento adicionais ou integração com os logs do Azure Monitor. Você pode adicionar essas soluções de gerenciamento ou integrar logs do Azure Monitor para o monitoramento avançado de trabalhos de runbook a qualquer momento no futuro.

Com uma conta de Automação, você pode autenticar runbooks gerenciando recursos no Azure Resource Manager ou no modelo de implantação clássico. Uma Conta de Automação pode gerenciar recursos em todas as regiões e assinaturas para determinado locatário.

Ao criar uma conta de Automação no portal do Azure, essas contas são criadas automaticamente:

* **Conta Executar como**. Essa conta executa as seguintes tarefas:
  * Cria uma entidade de serviço no Azure AD (Azure Active Directory).
  * Cria um certificado.
  * Atribui o RBAC (Controle de Acesso Baseado em Função) de Colaborador, que gerencia os recursos do Azure Resource Manager por meio de runbooks.
* **Conta Executar como clássica**. Essa conta carrega um certificado de gerenciamento. O certificado gerencia os recursos clássicos usando runbooks.

Com essas contas criadas para você, você pode começar rapidamente a criar e implantar runbooks em apoio às suas necessidades de automação.

## <a name="permissions-required-to-create-an-automation-account"></a>Permissões necessárias para criar uma conta de Automação

Para criar ou atualizar uma conta de Automação e concluir as tarefas descritas neste artigo, é necessário ter os seguintes privilégios e permissões:

* Para criar uma conta de Automação, sua conta de usuário do Azure AD precisa ser adicionada a uma função com permissões equivalentes à função de Proprietário para os recursos do **Microsoft. Automation**. Para obter mais informações, consulte [Controle de Acesso Baseado em Função na Automação do Azure](automation-role-based-access-control.md).
* No portal do Azure, em **Azure Active Directory** > **GERENCIAR** > **Registros do aplicativo**, se **Registros do aplicativo**  estiver definido como **Sim**, os usuários não administradores no locatário do Azure AD poderão [registrar aplicativos do Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#check-azure-subscription-permissions). Se **Registros do aplicativo** estiver definido como **Não**, o usuário que executar esta ação precisará ser um administrador global no Microsoft Azure AD.

Caso não seja membro da instância do Active Directory da assinatura antes de ser adicionado à função de administrador global/coadministrador da assinatura, você será adicionado ao Active Directory como convidado. Neste cenário, você recebe esta mensagem na página **Adicionar Conta de Automação**: "Você não tem permissões para criar."

Se um usuário for adicionado à função de administrador global/coadministrator pela primeira vez, você poderá removê-lo da instância do Active Directory da assinatura e, em seguida, adicioná-lo novamente à função de Usuário completa no Active Directory.

Para verificar as funções de usuário:

1. No portal do Azure, acesse o painel **Azure Active Directory**.
1. Selecione **Usuários e grupos**.
1. Selecione **Todos os usuários**.
1. Depois de selecionar um usuário específico, selecione **Perfil**. O valor do atributo **Tipo de usuário** no perfil do usuário não deve ser igual a **Convidado**.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Criar uma nova conta de Automação no portal do Azure

Para criar uma conta da Automação do Azure no portal do Azure, execute as seguintes etapas:

1. Entre no portal do Azure com uma conta que seja membro da função Administradores da assinatura e um coadministrador da assinatura.
1. Selecione **+ Criar um Recurso**.
1. Pesquise **Automação**. Nos resultados da pesquisa, selecione **Automação**.

   ![Pesquisar e selecionar Automação e Controle no Azure Marketplace](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. Na próxima tela, selecione **Criar**.

   ![Adicionar conta de Automação](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

   > [!NOTE]
   > Se você receber a mensagem a seguir no painel **Adicionar Conta de Automação**, a sua conta não é membro da função Administradores da assinatura nem um coadministrator da assinatura.
   >
   > ![Aviso Adicionar Conta de Automação](media/automation-create-standalone-account/create-account-without-perms.png)

1. No painel **Adicionar Conta de Automação**, na caixa **Nome**, insira um nome para a nova conta de Automação. Esse nome não pode ser alterado depois de ser escolhido. *Nomes de Conta de Automação são exclusivos por grupo de recursos e região. Nomes de contas da Automação que foram excluídos podem não estar disponíveis imediatamente.*
1. Se você tem mais de uma assinatura, na caixa **Assinatura**, especifique a assinatura que deseja usar para a nova conta.
1. Para **Grupo de recursos**, insira ou selecione um grupo de recursos novo ou existente.
1. Para **Local**, selecione um local de datacenter do Azure.
1. Para a opção **Criar conta Executar como do Azure**, verifique se a opção **Sim** está marcada e, em seguida, selecione **Criar**.

   > [!NOTE]
   > Se você optar por não criar a conta Executar como selecionando **Não** em **Criar conta Executar como do Azure**, será exibida uma mensagem no painel **Adicionar Conta de Automação**. Embora a conta seja criada no portal do Azure, ela não tem uma identidade de autenticação correspondente em sua assinatura do modelo de implantação clássico ou no serviço de diretório da assinatura do Azure Resource Manager. Portanto, a conta de Automação não tem acesso aos recursos em sua assinatura. Isso impede que runbooks que referenciam essa conta possam se autenticar e executar tarefas nos recursos nesses modelos de implantação.
   >
   > ![Aviso Adicionar Conta de Automação](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)
   >
   > Enquanto a entidade de serviço não for criada, a função Colaborador não será atribuída.
   >

1. Para acompanhar o progresso da criação da conta de Automação, no menu, selecione **Notificações**.

### <a name="resources-included"></a>Recursos incluídos

Quando a criação da conta de Automação tiver sido criada com êxito, vários recursos serão criados automaticamente para você. Após a criação, esses runbooks poderão ser excluídos com segurança se você não quiser mantê-los. As contas Executar como poderão ser usadas para autenticar a conta em um runbook e deverão ser mantidas, exceto se você criar outra ou não forem mais necessárias. A tabela a seguir resume os recursos para a conta Executar como.

| Resource | DESCRIÇÃO |
| --- | --- |
| Runbook AzureAutomationTutorial |Um runbook gráfico de exemplo que demonstra como fazer a autenticação usando a conta Executar como. O runbook obtém todos os recursos do Resource Manager. |
| Runbook do AzureAutomationTutorialScript |Um runbook de exemplo do PowerShell que demonstra como fazer a autenticação usando a conta Executar como. O runbook obtém todos os recursos do Resource Manager. |
| Runbook AzureAutomationTutorialPython2 |Um runbook de exemplo do Python que demonstra como fazer a autenticação usando a conta Executar como. O runbook lista todos os grupos de recursos presentes na assinatura. |
| AzureRunAsCertificate |Um ativo de certificado criado automaticamente quando a conta de Automação é criada ou usando um script do PowerShell para uma conta existente. O certificado é autenticado no Azure, do modo que você possa gerenciar os recursos do Azure Resource Manager em runbooks. Esse certificado tem um tempo de vida de um ano. |
| AzureRunAsConnection |Um ativo de conexão criado automaticamente quando a conta de Automação é criada ou usando um script do PowerShell para uma conta existente. |

A tabela a seguir resume os recursos para a conta Executar como Clássica.

| Resource | DESCRIÇÃO |
| --- | --- |
| Runbook do AzureClassicAutomationTutorial |Um runbook gráfico de exemplo. O runbook obtém todas as VMs clássicas de uma assinatura usando a Conta Executar como Clássica (certificado). Em seguida, ele exibe os nomes e status da VM. |
| Runbook do script AzureClassicAutomationTutorial |Um runbook de exemplo do PowerShell. O runbook obtém todas as VMs clássicas de uma assinatura usando a Conta Executar como Clássica (certificado). Em seguida, ele exibe os nomes e status da VM. |
| AzureClassicRunAsCertificate |Um ativo de certificado criado automaticamente. O certificado é autenticado no Azure, do modo que você possa gerenciar os recursos clássicos do Azure em runbooks. Esse certificado tem um tempo de vida de um ano. |
| AzureClassicRunAsConnection |Um ativo de conexão criado automaticamente. O ativo é autenticado no Azure, de modo que você possa gerenciar os recursos clássicos do Azure em runbooks. |

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a criação gráfica, consulte [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md).
* Para começar a usar os runbooks do PowerShell, confira [Meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md).
* Para começar a usar os runbooks do fluxo de trabalho do PowerShell, veja [Meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md).
* Para começar a usar runbooks Python2, veja [Meu primeiro runbook Python2](automation-first-runbook-textual-python2.md).

