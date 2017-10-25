---
title: "Atualizar um cofre de Backup para um cofre dos Serviços de Recuperação do Backup do Azure | Microsoft Docs"
description: "Atualize o cofre de Backup para o cofre dos Serviços de Recuperação para obter novos recursos como backup de VMs do Resource Manager, segurança aprimorada, backup de VMs do VMware e backup do estado do sistema para servidores Windows"
services: backup
documentationcenter: 
author: trinadhk
manager: vijayts
editor: 
keyword: backup vault; upgrade vault; recovery services vault
ms.assetid: d037a8bf-49f2-4578-974a-3471d87ca278
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: trinadhk, sogup
ms.openlocfilehash: cfc2fde552b029412042474e31a1b28dd80b3021
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="backup-vault-upgraded-to-recovery-services-vault"></a>Atualização de cofre de Backup para cofre dos Serviços de Recuperação
Este artigo fornece uma visão geral do que é oferecido pelo sofre dos Serviços de Recuperação, perguntas frequentes sobre a atualização do cofre de Backup existente para o cofre dos Serviços de Recuperação e as etapas pós-atualização. Um cofre dos Serviços de Recuperação é o equivalente do Azure Resource Manager a um cofre de Backup que contém seus dados de backup. Os dados normalmente são cópias de dados ou informações de configuração de VMs (máquinas virtuais), cargas de trabalho, servidores ou estações de trabalho, sejam esses dados locais ou no Azure.

## <a name="what-is-a-recovery-services-vault"></a>O que é um cofre dos Serviços de Recuperação?
Um cofre dos Serviços de Recuperação é uma entidade de armazenamento online no Azure usada para armazenar dados como cópias de backup, pontos de recuperação e políticas de backup. Você pode usar cofres dos Serviços de Recuperação para armazenar dados de backup para vários serviços do Azure, como VMs de IaaS (Windows ou Linux) e bancos de dados SQL do Azure. Os cofres de Serviços de Recuperação dão suporte a System Center DPM, Windows Server, Servidor de Backup do Azure e outros. Os cofres dos Serviços de Recuperação facilitam a organização dos dados de backup, minimizando a sobrecarga de gerenciamento.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Comparação de cofres de Serviços de Recuperação e cofres de Backup
Cofres de Serviços de Recuperação são baseados no modelo do Azure Resource Manager, enquanto os cofres de Backup são baseados no modelo do Azure Service Manager. Quando você atualiza um cofre de Backup para um cofre de Serviços de Recuperação, os dados de backup permanecem intactos durante e após o processo de atualização. Cofres de Serviços de Recuperação fornecem recursos não disponíveis a cofres de Backup, como:

- **Recursos aprimorados para ajudar a proteger dados de backup**: com os cofres de Serviços de Recuperação, o Backup do Azure fornece recursos de segurança para proteger backups em nuvem. Esses recursos de segurança asseguram que você possa proteger seus backups e recuperar com segurança dados de backups em nuvem, mesmo que os servidores de produção e de backup sejam comprometidos. [Saiba mais](backup-azure-security-feature.md)

- **Monitoramento central para seu ambiente de TI híbrida**: com os cofres de Serviços de Recuperação, você pode monitorar não apenas suas [VMs da IaaS do Azure](backup-azure-manage-vms.md), como também seus [ativos locais](backup-azure-manage-windows-server.md#manage-backup-items) de um portal central. [Saiba mais](http://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **RBAC (Controle de Acesso Baseado em Função)**: o RBAC oferece controle de gerenciamento de acesso detalhado no Azure. [O Azure fornece várias funções internas](../active-directory/role-based-access-built-in-roles.md), e o Backup do Azure tem três [funções internas para gerenciar pontos de recuperação](backup-rbac-rs-vault.md). Cofres de Serviços de Recuperação são compatíveis com RBAC, que restringe o acesso de backup e restauração ao conjunto definido de funções de usuário. [Saiba mais](backup-rbac-rs-vault.md)

- **Proteger todas as configurações de Máquinas Virtuais do Azure**: cofres de Serviços de Recuperação protegem VMs com base no Resource Manager, incluindo Premium Disks, Managed Disks e VMs Criptografadas. Atualizar um cofre de Backup para um cofre de Serviços de Recuperação possibilita atualizar as VMs baseadas no Service Manager para VMs baseadas no Resource Manager. Ao atualizar o cofre, você pode manter os seus pontos de recuperação de VM baseada no Service Manager e configurar a proteção para VMs atualizadas (habilitadas para Resource Manager). [Saiba mais](http://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Restauração instantânea para VMs da IaaS**: usando os cofres de Serviços de Recuperação, você pode restaurar arquivos e pastas em uma VM IaaS sem restaurar toda a VM, o que permite tempos de restauração mais rápidos. Restauração instantânea para VMs da IaaS está disponível para VMs Linux e Windows. [Saiba mais](http://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

> [!NOTE]
> Se tiver itens registrados em um cofre de Backup com o agente de MARS anteriores ao 2.0.9083.0, [baixe a versão mais recente do agente MARS]( http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe) para tirar proveito dos benefícios de todos os recursos do cofre dos Serviços de Recuperação. 
> 

## <a name="managing-your-recovery-services-vaults"></a>Gerenciando seus cofres dos Serviços de Recuperação
As telas a seguir mostram um novo cofre de Serviços de Recuperação, atualizado do cofre de Backup, no portal do Azure. O cofre atualizado estará presente em um grupo de recursos padrão chamado "Default-RecoveryServices-ResourceGroup-geo". Exemplo: se seu cofre de Backup ficava localizado no Oeste dos EUA, ele será colocado em um grupo de recursos padrão denominado Default-RecoveryServices-ResourceGroup-westus.
> [!NOTE]
> Para clientes do CPS padrão, o grupo de recursos não será alterado após a atualização do cofre e permanecerá o mesmo que era antes da atualização.

A primeira tela mostra o painel do cofre que exibe as entidades principais para o cofre.
![exemplo de cofre dos Serviços de Recuperação atualizado de um cofre de Backup](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

A segunda tela mostra os links de ajuda disponíveis para ajudá-lo a começar a usar o cofre de Serviços de Recuperação.

![links de ajuda na folha Início Rápido](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="post-upgrade-steps"></a>Etapas de pós-atualização
O cofre do Serviços de Recuperação dá suporte à especificação de informações de fuso horário na política de backup. Depois que o cofre for atualizado com êxito, acesse as políticas do Backup no menu de configurações do cofre e atualize as informações de fuso horário de cada uma das políticas configuradas no cofre. Esta tela já mostra a hora do agendamento de backup especificado por fuso horário local usado quando você criou a política. 

## <a name="enhanced-security"></a>Segurança aprimorada
Quando um cofre de Backup é atualizado para um cofre dos Serviços de Recuperação, as configurações de segurança desse cofre são automaticamente ativadas. Quando as configurações de segurança estão ativadas, determinadas operações, como excluir backups ou alterar uma frase secreta, exigem um PIN da [Autenticação Multifator do Azure](../multi-factor-authentication/multi-factor-authentication.md). Para obter mais informações sobre a segurança aprimorada, consulte o artigo [Recursos de segurança para proteger os backups híbridos](backup-azure-security-feature.md). Quando a segurança aprimorada é ativada, os dados são retidos por até 14 dias após as informações de ponto de recuperação serem excluídas do cofre. Os clientes são cobrados pelo armazenamento de dados de segurança. A retenção de dados de segurança aplica-se aos pontos de recuperação tomados pelo agente do Backup do Azure, o Servidor de Backup do Azure e o System Center Data Protection Manager. 

## <a name="gather-data-on-your-vault"></a>Reunir dados no cofre
Depois de atualizar para um cofre dos Serviços de Recuperação, configure relatórios do Backup do Azure (para as VMs de IaaS e o agente dos Serviços de Recuperação do Microsoft Azure) e use o Power BI para acessar os relatórios. Para obter informações adicionais sobre a coleta de dados, consulte o artigo [Configurar relatórios de Backup do Azure](backup-azure-configure-reports.md).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**O plano de atualização afeta meus backups em andamento?**</br>
Não. Os backups em andamento continuam sem interrupções durante e após a atualização.

**O que esta atualização significa para as minhas ferramentas existentes?**</br>
Você precisa atualizar suas ferramentas ou automação existente para o modelo de implantação do Resource Manager para garantir que continuem funcionando após a atualização. Consulte as referências de cmdlets do PowerShell para o [Modelo de implantação do Service Manager](backup-client-automation-classic.md) e o [Modelo de implantação do Resource Manager](backup-client-automation.md).

**Posso reverter após a atualização?**</br>
Não. Não há suporte para reversão após os recursos terem sido atualizados com êxito.

**Posso exibir meu cofre clássico após a atualização?**</br>
Não. Você não pode exibir nem gerenciar o cofre clássico pós-atualização. Você só poderá usar o novo portal do Azure para todas as ações de gerenciamento no cofre.

**Por que não consigo ver servidores protegidos pelo agente de MARS em meu cofre atualizado?**</br>
Você precisa instalar o agente de MARS mais recente para ver todos os servidores protegidos pelo agente de MARS em seu cofre. Você pode baixar a versão mais recente do agente [aqui]( http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe).

**Não consigo ver a política de Backup para os servidores protegidos pelo agente de MARS após a atualização**</br>
A política de backup do cofre pode estar desatualizada e, portanto, não pôde ser sincronizada para o cofre atualizado. Atualize a política para garantir que você continue vendo suas políticas no cofre atualizado.
Para atualizar a política, vá para o agente de MARS e atualize a política de backup configurada.

**Por que não é possível atualizar minha política de Backup após a atualização?**</br>
Isso acontece quando você está em um agente de backup antigo e seleciona um período de retenção mínimo menor do que o valor mínimo permitido. Quando um cofre de Backup é atualizado para um cofre dos Serviços de Recuperação, as configurações de segurança desse cofre são automaticamente ativadas. Para garantir que sempre haja um número válido de pontos de recuperação disponíveis, há um período de retenção mínimo que precisa ser mantido de acordo com o recurso de segurança. Para obter mais detalhes, consulte [aqui](backup-azure-security-feature.md).
Além disso, você precisa atualizar seus agentes de Backup do Azure para a versão mais recente para aproveitar as vantagens dos recursos mais recentes do Backup do Azure.

**Atualizei meu agente, mas ainda não é possível ver nenhum objeto sendo sincronizado, mesmo dias após a atualização**</br>
Verifique se você registrou o mesmo computador em vários cofres. Certifique-se de que você está vendo o mesmo cofre no qual o agente de MARS está registrado. Para descobrir em qual cofre seu agente de MARS está registrado, abra o Registro do Windows e verifique o valor de chave ServiceResourceName em HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config. O cofre registrado para o agente de MARS aparecerá lá. Se a chave ServiceResourceName não estiver visível em seu sistema, entre em contato conosco com o valor das chaves de ResourceId e MachineId em HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config e ajudaremos você a resolver o problema.

**Por que não consigo ver as informações de trabalhos de meus recursos após a atualização?**</br>
O Monitoramento de backups (agente MARS e IaaS) é um novo recurso que você obtém ao atualizar seu cofre de Backup para o cofre de Serviços de Recuperação. As informações de monitoramento levam até 12 horas para serem sincronizadas com o serviço.

**Como faço para relatar um problema?**</br>
Se qualquer parte da atualização do cofre falhar, observe a OperationId listada no erro. O Suporte da Microsoft trabalhará de forma proativa para resolver o problema. Você pode contatar o Suporte ou enviar um email para nós no endereço rsvaultupgrade@service.microsoft.com com sua ID de Assinatura, nome do cofre e OperationId. Tentaremos resolver o problema o mais rápido possível. Não repita a operação, a menos que seja explicitamente instruído a fazer isso pela Microsoft.

## <a name="next-steps"></a>Próximas etapas
Use os seguintes artigos para:</br>
[Fazer backup de uma VM IaaS](backup-azure-arm-vms-prepare.md)</br>
[Fazer backup de um Servidor de Backup do Azure](backup-azure-microsoft-azure-backup.md)</br>
[Fazer backup de um Windows Server](backup-configure-vault.md)
