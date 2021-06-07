---
title: Visão geral de recursos de segurança
description: Saiba mais sobre as funcionalidades de segurança no Backup do Azure que ajudam a proteger seus dados de backup e a atender às necessidades de segurança de sua empresa.
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 9aa1909f1590b477d9a7f7a09ad0c2b1936e3e29
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96325648"
---
# <a name="overview-of-security-features-in-azure-backup"></a>Visão geral dos recursos de segurança no Backup do Azure

Um dos passos mais importantes que você pode dar para proteger seus dados é ter uma infraestrutura de backup confiável. Mas é igualmente importante garantir que os dados sejam submetidos a backup de maneira segura e que os backups sejam protegidos o tempo todo. O Backup do Azure fornece segurança para seu ambiente de backup quando seus dados estão em trânsito e também quando estão em repouso. Este artigo lista as funcionalidades de segurança no Backup do Azure que ajudam a proteger seus dados de backup e a atender às necessidades de segurança de sua empresa.

## <a name="management-and-control-of-identity-and-user-access"></a>Gerenciamento e controle de identidade e acesso de usuário

As contas de armazenamento usadas pelos cofres dos serviços de recuperação são isoladas e não podem ser acessadas por usuários para fins mal-intencionados. O acesso é permitido somente por meio de operações de gerenciamento do Backup do Azure, como a restauração. O backup do Azure permite que você controle as operações gerenciadas por meio de acesso refinado usando o [controle de acesso baseado em função do Azure (RBAC do Azure)](./backup-rbac-rs-vault.md). O RBAC do Azure permite separar as tarefas dentro de sua equipe e conceder apenas a quantidade de acesso para os usuários necessários para realizar seus trabalhos.

O Backup do Azure oferece três [funções internas](../role-based-access-control/built-in-roles.md) para controlar as operações de gerenciamento de backups:

* Colaborador de Backup: para criar e gerenciar backups, exceto criar o cofre dos Serviços de Recuperação e fornecer acesso a outras pessoas
* Operador de Backup: fazer tudo que um colaborador faz, exceto remover backups e gerenciar políticas de backup
* Leitor de Backup: permissões para exibir todas as operações de gerenciamento de backups

Saiba mais sobre o [controle de acesso baseado em função do Azure para gerenciar o backup do Azure](./backup-rbac-rs-vault.md).

O Backup do Azure tem vários controles de segurança incorporados ao serviço para impedir, detectar e responder a vulnerabilidades de segurança. Saiba mais sobre [controles de segurança para o Backup do Azure](./security-baseline.md).

## <a name="separation-between-guest-and-azure-storage"></a>Separação entre o convidado e o Armazenamento do Azure

Com o Backup do Azure, que inclui backup de máquina virtual, bem como SQL e SAP HANA em backup de VM, os dados de backup são armazenados no Armazenamento do Azure e o convidado não tem acesso direto ao armazenamento de backup ou ao conteúdo dele.  Com o backup de máquina virtual, a criação e o armazenamento de instantâneos de backup são feitos pela malha do Azure, em que o único envolvimento do convidado é fazer o fechamento da carga de trabalho para backups consistentes do aplicativo.  Com o SQL e o SAP HANA, a extensão de backup obtém acesso temporário para gravar em blobs específicos.  Dessa forma, mesmo em um ambiente comprometido, os backups existentes não podem ser adulterados nem excluídos pelo convidado.

## <a name="internet-connectivity-not-required-for-azure-vm-backup"></a>A conectividade com a Internet não necessária para o backup de VM do Azure

O backup de VMs do Azure requer a movimentação de dados do disco da máquina virtual para o cofre dos Serviços de Recuperação. No entanto, toda a comunicação necessária e a transferência de dados acontecem apenas na rede de backbone do Azure, sem a necessidade de acessar sua rede virtual. Portanto, o backup de VMs do Azure colocado dentro de redes seguras não exige que você permita o acesso a nenhum IP nem FQDN.

## <a name="private-endpoints-for-azure-backup"></a>Pontos de extremidade privados para o backup do Azure

Agora, você pode usar [Pontos de Extremidade Privados](../private-link/private-endpoint-overview.md) para fazer backup de dados de maneira segura de servidores dentro de uma rede virtual para seu cofre dos Serviços de Recuperação. O ponto de extremidade privado usa um IP do espaço de endereço de VNET para seu cofre, de modo que você não precisa expor suas redes virtuais para qualquer IPs público. Os Pontos de Extremidade Privados podem ser usados para fazer backup e restaurar os bancos de dados SQL e SAP HANA executados dentro de suas VMs do Azure. Eles também podem ser usados para servidores locais que usam o agente MARS.

Leia mais sobre pontos de extremidade privados para o Backup do Azure [aqui](./private-endpoints.md).

## <a name="encryption-of-data"></a>Criptografia de dados

A criptografia protege seus dados e ajuda a atender aos compromissos de conformidade e segurança de sua organização. A criptografia de dados ocorre em muitos estágios no backup do Azure:

* No Azure, os dados em trânsito entre o armazenamento do Azure e o cofre são [protegidos por HTTPS](backup-support-matrix.md#network-traffic-to-azure). Esses dados permanecem na rede de backbone do Azure.

* Os dados de backup são criptografados automaticamente usando [chaves gerenciadas por plataforma](backup-encryption.md)e você não precisa tomar nenhuma ação explícita para habilitá-lo. Você também pode criptografar seus dados de backup usando [chaves gerenciadas pelo cliente](encryption-at-rest-with-cmk.md) armazenadas no Azure Key Vault. Ele se aplica a todas as cargas de trabalho cujo backup está sendo feito em seu cofre dos Serviços de Recuperação.

* O backup do Azure dá suporte ao backup e à restauração de VMs do Azure que têm seus discos de sistema operacional/dados criptografados com [Azure Disk Encryption (Ade)](backup-azure-vms-encryption.md#encryption-support-using-ade) e [VMs com discos criptografados CMK](backup-azure-vms-encryption.md#encryption-using-customer-managed-keys). Para obter mais informações, [saiba mais sobre as VMs do Azure e o backup do Azure criptografados](./backup-azure-vms-encryption.md).

* Quando é feito backup dos dados de servidores locais com o agente MARS, os dados são criptografados com uma senha antes do upload para o backup do Azure e descriptografados somente após serem baixados do backup do Azure. Leia mais sobre [os recursos de segurança para ajudar a proteger os backups híbridos](#security-features-to-help-protect-hybrid-backups).

## <a name="protection-of-backup-data-from-unintentional-deletes"></a>Proteção de dados de backup de exclusões não intencionais

O Backup do Azure fornece recursos de segurança para ajudar a proteger os dados de backup mesmo após a exclusão. Com a exclusão reversível, se o usuário excluir o backup de uma VM, os dados de backup serão mantidos por 14 dias adicionais, permitindo a recuperação desse item de backup sem perda de dados. A retenção adicional de 14 dias dos dados de backup no estado de "exclusão reversível" não incorre em nenhum custo para você. [Saiba mais sobre a exclusão temporária](backup-azure-security-feature-cloud.md).

## <a name="monitoring-and-alerts-of-suspicious-activity"></a>Monitoramento e alertas de atividade suspeita

O Backup do Azure fornece [funcionalidades internas de monitoramento e alerta](./backup-azure-monitoring-built-in-monitor.md) para exibir e configurar ações para eventos relacionados ao Backup do Azure. Os [Relatórios de Backup](./configure-reports.md) funcionam como um destino único para controlar o uso, auditar backups e restaurações e identificar as principais tendências em níveis diferentes de granularidade. Usar as ferramentas de monitoramento e relatório do Backup do Azure pode alertá-lo sobre qualquer atividade não autorizada, suspeita ou mal-intencionada assim que ela ocorrer.

## <a name="security-features-to-help-protect-hybrid-backups"></a>Recursos de segurança para ajudar a proteger backups híbridos

O serviço de Backup do Azure usa o agente de MARS (Serviços de Recuperação do Microsoft Azure) para fazer backup e restaurar arquivos, pastas e o estado do volume ou do sistema de um computador local para o Azure. O MARS agora fornece recursos de segurança para ajudar a proteger backups híbridos. Esses recursos incluem:

* Uma camada adicional de autenticação será adicionada sempre que uma operação crítica, como a alteração de senha, for executada. Essa validação é garantir que essas operações possam ser realizadas apenas por usuários com credenciais válidas do Azure. [Saiba mais sobre os recursos que impedem ataques](./backup-azure-security-feature.md#prevent-attacks).

* Os dados de backup excluídos são retidos por mais 14 dias desde a data da exclusão. Isso garante a capacidade de recuperação de dados em um determinado período de tempo de maneira que não haja perda de dados mesmo se houver um ataque. Além disso, mais pontos de recuperação mínimos são mantidos para proteção contra dados corrompidos. [Saiba mais sobre a recuperação de dados de backup excluídos](./backup-azure-security-feature.md#recover-deleted-backup-data).

* Para backup de dados usando o agente de MARS (Serviços de Recuperação do Microsoft Azure), uma frase secreta é usada para garantir que os dados sejam criptografados antes do carregamento para o Backup do Azure e descriptografados somente após o download do Backup do Azure. Os detalhes da frase secreta estão disponíveis somente para o usuário que criou a senha e o agente que está configurado com ele. Nada é transmitido nem compartilhado com o serviço. Isso garante a segurança total de seus dados, pois todos os dados expostos inadvertidamente (como um ataque man-in-the-Middle na rede) são inutilizáveis sem a frase secreta e a senha não é enviada pela rede.

## <a name="compliance-with-standardized-security-requirements"></a>Conformidade com requisitos de segurança padronizados

Para ajudar as organizações a atenderem aos requisitos nacionais, regionais e específicos do setor que controlam a coleta e o uso de dados de indivíduos, o Microsoft Azure e o Backup do Azure oferecem um conjunto mais abrangente de certificações e atestados. [Confira a lista de certificações de conformidade](compliance-offerings.md)

## <a name="next-steps"></a>Próximas etapas

* [Recursos de segurança para ajudar a proteger cargas de trabalho de nuvem que usam o Backup do Azure](backup-azure-security-feature-cloud.md)
* [Recursos de segurança para ajudar a proteger backups híbridos usando o Backup do Azure](backup-azure-security-feature.md)