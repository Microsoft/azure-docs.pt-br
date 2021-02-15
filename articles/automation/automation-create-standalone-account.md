---
title: Como criar conta autônoma de automação do Azure
description: Este artigo descreve como criar uma conta de Automação autônoma do Azure e uma conta Executar como Clássica.
services: automation
ms.subservice: process-automation
ms.date: 01/07/2021
ms.topic: conceptual
ms.openlocfilehash: e0088fb129e9c6558de7539ba754a45e067dc3d8
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576830"
---
# <a name="create-a-standalone-azure-automation-account"></a>Como criar conta autônoma de automação do Azure

Este artigo mostra como criar uma conta da Automação do Azure no portal do Azure. Use a conta de Automação do portal para avaliar e saber mais sobre a Automação sem usar recursos de gerenciamento adicionais ou integração com os logs do Azure Monitor. Adicione recursos de gerenciamento ou integre os logs do Azure Monitor para o monitoramento avançado de trabalhos de runbook a qualquer momento no futuro.

Com uma conta de Automação, você pode autenticar runbooks gerenciando recursos no Azure Resource Manager ou no modelo de implantação clássico. Uma Conta de Automação pode gerenciar recursos em todas as regiões e assinaturas para determinado locatário.

Quando criar uma conta de Automação no portal do Azure, a conta **Executar como** é criada automaticamente. Essa conta executa as seguintes tarefas:

* Cria uma entidade de serviço no Azure AD (Azure Active Directory).
* Cria um certificado.
* Atribui a função de colaborador, que gerencia Azure Resource Manager recursos usando runbooks.

Com essa conta criada, você pode começar rapidamente a criar e implantar runbooks para dar suporte às suas necessidades de automação.

## <a name="permissions-required-to-create-an-automation-account"></a>Permissões necessárias para criar uma conta de Automação

Para criar ou atualizar uma conta de Automação e concluir as tarefas descritas neste artigo, é necessário ter os seguintes privilégios e permissões:

* Para criar uma conta de Automação, sua conta de usuário do Azure AD precisa ser adicionada a uma função com permissões equivalentes à função de Proprietário para os recursos do `Microsoft.Automation`. Para obter mais informações, confira [Controle de acesso baseado em função na Automação do Azure](automation-role-based-access-control.md).
* No portal do Azure, em **Azure Active Directory** > **GERENCIAR** > **Configurações do usuário**, se **Registros do aplicativo** estiver definido como **Sim**, os usuários não administradores no locatário do Azure AD poderão [registrar aplicativos do Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#check-azure-subscription-permissions). Se **registros de aplicativo** for definido como **não**, o usuário que executar essa ação deverá ter pelo menos uma função de desenvolvedor de aplicativo no Azure AD.

Caso não seja membro da instância do Active Directory da assinatura antes de ser adicionado à função de administrador global/coadministrador da assinatura, você será adicionado ao Active Directory como um convidado. Neste cenário, você recebe esta mensagem no painel Adicionar Conta de Automação: `You do not have permissions to create.`

Se um usuário for adicionado à função de administrador global/coadministrator pela primeira vez, você poderá removê-lo da instância do Active Directory da assinatura. Adicione-o novamente à função de Usuário no Active Directory. Para verificar as funções de usuário:

1. No portal do Azure, acesse o painel Azure Active Directory.
1. Selecione **Usuários e grupos**.
1. Selecione **Todos os usuários**.
1. Depois de selecionar um usuário específico, selecione **Perfil**. O valor do atributo **Tipo de usuário** no perfil do usuário não deve ser igual a **Convidado**.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Criar uma nova conta de Automação no portal do Azure

Para criar uma conta da Automação do Azure no portal do Azure, execute as seguintes etapas:

1. Entre no portal do Azure com uma conta que seja membro da função Administradores da assinatura e um coadministrador da assinatura.
1. Selecione **+ Criar um Recurso**.
1. Pesquise **Automação**. Nos resultados da pesquisa, selecione **Automação**.

   ![Pesquisar e selecionar Automação e Controle no Azure Marketplace](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. Na próxima tela, selecione **Criar nova**.

   ![Adicionar conta de Automação](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

   > [!NOTE]
   > Se você receber a mensagem a seguir no painel Adicionar Conta de Automação, a sua conta não é membro da função Administradores da assinatura nem um coadministrator da assinatura.
   >
   > :::image type="content" source="media/automation-create-standalone-account/create-account-without-perms.png" alt-text="Captura de tela do prompt ' você não tem permissões para criar uma conta Executar como no Azure Active Directory. '":::

1. No painel Adicionar Conta de Automação, insira um nome para a nova conta de Automação no campo **Nome**. Depois de escolher um nome você não pode alterá-lo. 

    > [!NOTE]
    > Os nomes da conta de Automação são exclusivos por região e grupo de recursos. Os nomes das contas de Automação excluídas não estarão disponíveis imediatamente.

1. Se você tem mais de uma assinatura, no campo **Assinatura**, especifique a assinatura para usar na nova conta.
1. Para **Grupo de recursos**, insira ou selecione um grupo de recursos novo ou existente.
1. Para **Local**, selecione um local de datacenter do Azure.
1. Para a opção **Criar conta Executar como do Azure**, verifique se a opção **Sim** está selecionada e, em seguida, clique em **Criar**.

   > [!NOTE]
   > Se optar por não criar a conta Executar como selecionando **Não** em **Criar conta Executar como do Azure**, será exibida uma mensagem no painel Adicionar Conta de Automação. Embora a conta seja criada no portal do Azure, ela não tem uma identidade de autenticação correspondente em sua assinatura do modelo de implantação clássico ou no serviço de diretório da assinatura do Azure Resource Manager. Portanto, a conta de Automação não tem acesso aos recursos em sua assinatura. Isso impede que runbooks que referenciam essa conta possam se autenticar e executar tarefas nos recursos nesses modelos de implantação.
   >
   > :::image type="content" source="media/automation-create-standalone-account/create-account-decline-create-runas-msg.png" alt-text="Captura de tela do prompt com a mensagem ' você optou por não criar uma conta Executar como. '":::
   >
   > Enquanto a entidade de serviço não for criada, a função Colaborador não será atribuída.
   >

1. Para acompanhar o progresso da criação da conta de Automação, selecione **Notificações** no menu.

Quando a criação da conta de Automação tiver sido criada com êxito, vários recursos serão criados automaticamente para você. Após a criação, esses runbooks poderão ser excluídos com segurança se você não quiser mantê-los. As contas Executar como poderão ser usadas para autenticar a conta em um runbook e deverão ser mantidas, exceto se você criar outra ou não forem mais necessárias. A tabela a seguir resume os recursos para a conta Executar como.

| Recurso | Descrição |
| --- | --- |
| Runbook AzureAutomationTutorial |Um runbook gráfico de exemplo que demonstra como fazer a autenticação usando a conta Executar como. O runbook obtém todos os recursos do Resource Manager. |
| Runbook do AzureAutomationTutorialScript |Um runbook de exemplo do PowerShell que demonstra como fazer a autenticação usando a conta Executar como. O runbook obtém todos os recursos do Resource Manager. |
| Runbook AzureAutomationTutorialPython2 |Um runbook de exemplo do Python que demonstra como fazer a autenticação usando a conta Executar como. O runbook lista todos os grupos de recursos presentes na assinatura. |
| AzureRunAsCertificate |Um ativo de certificado criado automaticamente quando a conta de Automação é criada ou usando um script do PowerShell para uma conta existente. O certificado é autenticado no Azure, do modo que você possa gerenciar os recursos do Azure Resource Manager em runbooks. Esse certificado tem um tempo de vida de um ano. |
| AzureRunAsConnection |Um ativo de conexão criado automaticamente quando a conta de Automação é criada ou usando um script do PowerShell para uma conta existente. |

## <a name="create-a-classic-run-as-account"></a>Criação de uma Conta Executar como Clássica

As contas Executar como clássicas não são criadas por padrão quando você cria uma conta de automação do Azure. Se você precisar de uma conta Executar como clássica para gerenciar recursos clássicos do Azure, execute as seguintes etapas:

1. Na sua conta de Automação, selecione **Contas Executar como** em **Configurações da Conta**.
2. Selecione **Conta Executar Como Clássica do Azure**.
3. Clique em **Criar** para prosseguir com a criação da conta Executar como Clássica.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a criação de gráficos, veja [Criar runbooks gráficos na Automação do Azure](automation-graphical-authoring-intro.md).
* Para começar a usar runbooks do PowerShell, veja [Tutorial: Criação de um runbook do PowerShell](learn/automation-tutorial-runbook-textual-powershell.md).
* Para começar a usar runbooks de Fluxo de Trabalho PowerShell, veja [Tutorial: Criação de um runbook de Fluxo de Trabalho do PowerShell](learn/automation-tutorial-runbook-textual.md).
* Para começar a usar runbooks do Python 3, consulte [tutorial: criar um runbook do Python 3](learn/automation-tutorial-runbook-textual-python-3.md).
* Para obter uma referência de cmdlet do PowerShell, confira [Az.Automation](/powershell/module/az.automation).
