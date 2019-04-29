---
title: Atualizar um cofre de Backup para um cofre dos Serviços de Recuperação
description: Instruções e informações de suporte para atualizar seu cofre de Backup do Azure em um cofre dos Serviços de Recuperação.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 1/4/2018
ms.author: sogup
ms.openlocfilehash: b4ecebc6bef7f49a23455c7a85f25680df087a95
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60848328"
---
# <a name="upgrade-a-backup-vault-to-a-recovery-services-vault"></a>Atualizar um cofre de Backup para um cofre dos Serviços de Recuperação

Este artigo explica como atualizar um cofre de Backup para um cofre de Serviços de Recuperação. O processo de atualização não afeta nenhum trabalho de backup em execução, e nenhum dado de backup é perdido. Os principais motivos para atualizar um cofre de Backup para um cofre de Serviços de Recuperação são:
- Todos os recursos de um cofre de Backup são mantidos em um cofre de Serviços de Recuperação.
- Os cofres dos Serviços de Recuperação têm mais recursos que os cofres do Backup, incluindo: melhor segurança, monitoramento integrado, restaurações mais rápidas e restaurações no nível do item.
- Gerencie itens de backup em um portal aprimorado e simplificado.
- Os novos recursos se aplicam apenas aos cofres dos Serviços de Recuperação.

## <a name="impact-to-operations-during-upgrade"></a>Impacto às operações durante a atualização

Ao atualizar um cofre de Backup para um cofre de Serviços de Recuperação, não há nenhum impacto sobre suas operações de plano de dados. Todos os trabalhos de backup continuam como normal e quaisquer trabalhos de restauração ativos continuam sem interrupção. Durante a atualização, operações de gerenciamento incorrerem em um curto tempo de inatividade e você não pode proteger novos itens nem criar trabalhos de backups ad hoc. Trabalhos de restauração para VMs de IaaS não são executados durante a atualização. A atualização do cofre leva menos de uma hora para ser concluída. Quando tiver concluído, um cofre de Serviços de Recuperação substitui o cofre de Backup.

## <a name="changes-to-your-automation-and-tool-after-upgrading"></a>Alterações à automação e à ferramenta após a atualização

Ao preparar sua infraestrutura para a atualização do cofre, você deve atualizar sua automação ou ferramentas existentes para garantir que continue funcionando após a atualização.
Consulte as referências de cmdlets do PowerShell para o [Modelo de implantação do Resource Manager](backup-client-automation.md).


## <a name="before-you-upgrade"></a>Antes de atualizar

Verifique os problemas a seguir antes de atualizar seus cofres de Backup para os cofres do serviço de recuperação.

- **Versão mínima do agente**: Para atualizar o cofre, verifique se o agente de MARS (Serviços de Recuperação do Microsoft Azure) é pelo menos a versão 2.0.9083.0. Se o agente do MARS for mais antigo que 2.0.9083.0, atualize o agente antes de iniciar o processo de atualização.
- **Modelo de cobrança baseado na instância**: Os cofres do Serviço de Recuperação somente dão suporte para o modelo de cobrança baseado na instância. Se você tiver um cofre de backup que esteja usando o antigo modelo de cobrança com base em Armazenamento, converta o modelo de cobrança durante a atualização.
- **Nenhuma operação de configuração de backup em andamento**: Durante o upgrade, o acesso ao plano de gerenciamento é restrito. Conclua todas as ações do plano de gerenciamento e inicie a atualização.

## <a name="using-powershell-scripts-to-upgrade-your-vaults"></a>Usando scripts do PowerShell para atualizar seus cofres

Você pode usar scripts do PowerShell para atualizar os cofres de Backup para cofres dos Serviços de Recuperação. Verifique se você tem os componentes necessários do PowerShell para disparar a atualização do cofre. Scripts do PowerShell para os cofres de Backup não funcionam para cofres dos Serviços de Recuperação. Prepare seu ambiente para atualizar os cofres:

1. Instale ou atualize o [WMF (Windows Management Framework) para a versão 5](https://www.microsoft.com/download/details.aspx?id=50395) ou superior.
2. [Instale o MSI do Azure PowerShell](https://github.com/Azure/azure-powershell/releases/download/v3.8.0-April2017/azure-powershell.3.8.0.msi).
3. Baixe o [Script do PowerShell](https://aka.ms/vaultupgradescript2) para atualizar os cofres.

### <a name="run-the-powershell-script"></a>Executar o script do PowerShell

Use o script a seguir para atualizar seus cofres. O exemplo de script a seguir tem explicações dos parâmetros.

RecoveryServicesVaultUpgrade-1.0.2.ps1 **-SubscriptionID** `<subscriptionID>` **-VaultName** `<vaultname>` **-Location** `<location>` **-ResourceType** `BackupVault` **-TargetResourceGroupName** `<rgname>`

**SubscriptionID** – o número de ID da assinatura de cofre que está sendo atualizado.<br/>
**VaultName** – o nome do Cofre de backup que está sendo atualizado.<br/>
**Local** – local do cofre que está sendo atualizado.<br/>
**ResourceType** – use BackupVault.<br/>
**TargetResourceGroupName** – uma vez que você está atualizando o cofre para uma implantação baseada no Resource Manager, especifique um Grupo de recursos. Você pode usar um Grupo de Recursos existente ou criar um fornecendo um novo nome. Se você digitar incorretamente o nome de um Grupo de Recursos, poderá criar um novo Grupo de Recursos. Para saber mais sobre Grupos de Recursos, leia esta [visão geral sobre Grupos de Recursos](../azure-resource-manager/resource-group-overview.md#resource-groups).

>[!NOTE]
> Nomes de Grupo de Recursos têm restrições. Siga as diretrizes; não as seguir pode provocar falha das atualizações do cofre.
>
>Os clientes do **Azure do governo dos EUA** precisam definir o ambiente para "AzureUSGovernment" ao executar o script.
>Clientes do **Azure China** precisam definir o ambiente como "AzureChinaCloud" ao executar o script.

O snippet de código a seguir é um exemplo de como o comando do PowerShell deve ser:

```
RecoveryServicesVaultUpgrade.ps1 -SubscriptionID 53a3c692-5283-4f0a-baf6-49412f5ebefe -VaultName "TestVault" -Location "Australia East" -ResourceType BackupVault -TargetResourceGroupName "ContosoRG"
```

Você também pode executar o script sem parâmetros e será solicitado a fornecer entradas para todos os parâmetros necessários.

O script do PowerShell solicita que você insira suas credenciais. Insira suas credenciais duas vezes: uma vez para a conta do Service Manager e outra para a conta do Resource Manager.

### <a name="pre-requisites-checking"></a>Verificação de pré-requisitos
Depois de inserir suas credenciais do Azure, o Azure verifica se o seu ambiente atende aos seguintes pré-requisitos:

- **Versão mínima do agente** – Atualizar os cofres de Backup para cofres dos Serviços de Recuperação requer que o agente do MARS tenha pelo menos a versão 2.0.9083.0. Se você tiver itens registrados para um cofre de Backup com um agente anterior à versão 2.0.9083.0, a verificação de pré-requisitos falhará. Se a verificação de pré-requisitos falhar, atualize o agente e tente atualizar o cofre novamente. Você pode baixar a última versão do agente a partir de [https://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe](https://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe).
- **Trabalhos de configuração em andamento**: Se alguém estiver configurando um trabalho para um upgrade do Cofre de Backup, ou registrando um item, a verificação de pré-requisito falhará. Conclua a configuração ou conclua o registro do item e, em seguida, inicie o processo de atualização do cofre.
- **Modelo de cobrança com base em armazenamento**: Os cofres dos Serviços de Recuperação dão suporte para o modelo de faturamento baseado na instância. Se você executar a atualização do cofre em um cofre de Backup que use o modelo de cobrança baseado em armazenamento, será solicitado a atualizar o modelo de cobrança junto com o cofre. Caso contrário, poderá atualizar o modelo de cobrança primeiro e então executar a atualização do cofre.
- Identificar um Grupo de Recursos para o cofre de Serviços de Recuperação. Para aproveitar os recursos de implantação do Resource Manager, coloque um cofre de Serviços de Recuperação em um Grupo de Recursos. Se você não souber qual Grupo de Recursos usar, forneça um nome e o processo de atualização criará o Grupo de Recursos para você. O processo de atualização também associa o cofre ao novo Grupo de Recursos.

Depois que o processo de atualização termina de verificar os pré-requisitos, o processo solicita que você inicie a atualização do cofre. Depois de confirmar, o processo de atualização normalmente leva cerca de 15 a 20 minutos para ser concluído, dependendo do tamanho de seu cofre. Se você tiver um grande cofre, a atualização poderá levar até 90 minutos.

## <a name="managing-your-recovery-services-vaults"></a>Gerenciando seus cofres dos Serviços de Recuperação

As telas a seguir mostram um novo cofre de Serviços de Recuperação, atualizado do cofre de Backup, no portal do Azure. A primeira tela mostra o painel do cofre que exibe as entidades principais para o cofre.

![exemplo de cofre dos Serviços de Recuperação atualizado de um cofre de Backup](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

A segunda tela mostra os links de ajuda disponíveis para ajudá-lo a começar a usar o cofre de Serviços de Recuperação.

![links de ajuda na folha Início Rápido](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="post-upgrade-steps"></a>Etapas de pós-atualização
O cofre do Serviços de Recuperação dá suporte à especificação de informações de fuso horário na política de backup. Depois que o cofre for atualizado com êxito, acesse as políticas do Backup no menu de configurações do cofre e atualize as informações de fuso horário de cada uma das políticas configuradas no cofre. Esta tela já mostra a hora do agendamento de backup especificado por fuso horário local usado quando você criou a política.

## <a name="enhanced-security"></a>Segurança aprimorada

Quando um cofre de Backup é atualizado para um cofre dos Serviços de Recuperação, as configurações de segurança desse cofre são automaticamente ativadas. Quando as configurações de segurança estão ativadas, determinadas operações, como excluir backups ou alterar uma frase secreta, exigem um PIN da [Autenticação Multifator do Azure](../active-directory/authentication/multi-factor-authentication.md). Para obter mais informações sobre a segurança aprimorada, consulte o artigo [Recursos de segurança para proteger os backups híbridos](backup-azure-security-feature.md).

Quando a segurança aprimorada é ativada, os dados são retidos por até 14 dias após as informações de ponto de recuperação serem excluídas do cofre. Os clientes são cobrados pelo armazenamento de dados de segurança. A retenção de dados de segurança aplica-se aos pontos de recuperação tomados pelo agente do Backup do Azure, o Servidor de Backup do Azure e o System Center Data Protection Manager.

## <a name="gather-data-on-your-vault"></a>Reunir dados no cofre

Depois de atualizar para um cofre dos Serviços de Recuperação, configure relatórios do Backup do Azure (para as VMs de IaaS e MARS (Serviços de Recuperação do Microsoft Azure)) e use o Power BI para acessar os relatórios. Para obter informações adicionais sobre a coleta de dados, consulte o artigo [Configurar relatórios de Backup do Azure](backup-azure-configure-reports.md).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="does-the-upgrade-plan-affect-my-ongoing-backups"></a>O plano de atualização afeta meus backups em andamento?
Não. Os backups em andamento continuam sem interrupções durante e após a atualização.

### <a name="if-i-dont-plan-on-upgrading-soon-what-happens-to-my-vaults"></a>O que acontecerá com meus cofres se eu não planejar atualizar em breve?
Como todos os novos recursos se aplicam apenas aos cofres dos Serviços de Recuperação, insistimos que você atualize seus cofres. A partir de 1º de setembro de 2017, a Microsoft começará a atualização automática dos cofres de backup para cofres dos Serviços de Recuperação. Após 30 de novembro de 2017, você não poderá mais criar cofres de backup usando o PowerShell. Seu cofre pode ser atualizado automaticamente a qualquer momento até esse prazo. A Microsoft recomenda que você atualize seu cofre assim que possível.

### <a name="what-does-this-upgrade-mean-for-my-existing-tooling"></a>O que esta atualização significa para as minhas ferramentas existentes?
Atualize suas ferramentas para o modelo de implantação do Resource Manager. Os cofres dos Serviços de Recuperação foram criados para serem usados no modelo de implantação do Resource Manager. É importante o planejamento para o modelo de implantação do Resource Manager, bem como é importante levar em conta as diferenças em seus cofres.

### <a name="during-the-upgrade-is-there-much-downtime"></a>Durante a atualização, haverá muito tempo de inatividade?
Isso depende do número de recursos que estão sendo atualizados. Para implantações menores (algumas dezenas de instâncias protegidas), a atualização inteira deve levar menos de 20 minutos. Para implantações maiores, deve levar um máximo de uma hora.

### <a name="can-i-roll-back-after-upgrading"></a>Posso reverter após a atualização?
Não. Não há suporte para reversão após os recursos terem sido atualizados com êxito.

### <a name="can-i-validate-my-subscription-or-resources-to-see-if-theyre-capable-of-upgrade"></a>Posso validar minha assinatura ou recursos para ver se eles podem ser atualizados?
Sim. A primeira etapa na atualização valida se os recursos podem ser atualizados. No caso de falha na validação dos pré-requisitos, você recebe mensagens para todos os motivos pelos quais a atualização não pode ser concluída.

### <a name="can-i-upgrade-my-csp-based-backup-vault"></a>Posso atualizar meu cofre de Backup baseado em CSP?
Não. No momento não é possível atualizar os cofres de backup baseados em CSP. Adicionaremos suporte para atualizar os cofres de backup baseados em CSP nas próximas versões.

### <a name="can-i-view-my-classic-vault-post-upgrade"></a>Posso exibir meu cofre clássico após a atualização?
Não. Você não pode exibir nem gerenciar o cofre clássico pós-atualização. Você só poderá usar o novo portal do Azure para todas as ações de gerenciamento no cofre.

### <a name="my-upgrade-failed-but-the-machine-that-held-the-agent-requiring-updating-doesnt-exist-anymore-what-do-i-do-in-such-a-case"></a>Ocorreu falha na minha atualização, mas o computador que mantinha o agente que exigia a atualização não existe mais. O que devo fazer nesse caso?
Se você precisar usar o armazenamento, os backups desse computador para retenção de longo prazo, você não poderá atualizar o cofre. Nas versões futuras, adicionaremos suporte para atualizar esse cofre.
Se você não precisar mais armazenar os backups desse computador, cancele o registro desse computador do cofre e tente realizar a atualização novamente.

### <a name="why-cant-i-see-the-jobs-information-for-my-resources-after-upgrade"></a>Por que não consigo ver as informações de trabalhos de meus recursos após a atualização?
O Monitoramento de backups (agente MARS e IaaS) é um novo recurso que você obtém ao atualizar seu cofre de Backup para o cofre de Serviços de Recuperação. As informações de monitoramento levam até 12 horas para serem sincronizadas com o serviço.

### <a name="how-do-i-report-an-issue"></a>Como faço para relatar um problema?
Se qualquer parte da atualização do cofre falhar, observe a OperationId listada no erro. O Suporte da Microsoft trabalhará de forma proativa para resolver o problema. Você pode contatar o Suporte ou enviar um email para nós no endereço rsvaultupgrade@service.microsoft.com com sua ID de Assinatura, nome do cofre e OperationId. Tentaremos resolver o problema o mais rápido possível. Não repita a operação, a menos que seja explicitamente instruído a fazer isso pela Microsoft.


## <a name="next-steps"></a>Próximas etapas
Use o seguinte artigo para:</br>
[Fazer backup de uma VM IaaS](backup-azure-arm-vms-prepare.md)</br>
[Fazer backup de um Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md)</br>
[Fazer backup de um Windows Server](backup-configure-vault.md).
