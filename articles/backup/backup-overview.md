---
title: O que é o Backup do Azure?
description: Fornece uma visão geral do serviço de Backup do Azure e como implantá-lo como parte de sua estratégia de BCDR (continuidade dos negócios e recuperação de desastres).
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: overview
ms.date: 04/24/2019
ms.author: dacurwin
ms.custom: mvc
ms.openlocfilehash: e3577a1a0da7809298697c55c84662bc15b0f1e7
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639641"
---
# <a name="what-is-azure-backup"></a>O que é o Backup do Azure?

O serviço de Backup do Azure faz backup de dados na nuvem do Microsoft Azure. Você pode fazer backup de computadores e cargas de trabalho locais e de VMs (máquinas virtuais) do Azure.


## <a name="why-use-azure-backup"></a>Por que usar o Backup do Azure?

O Backup do Azure oferece estes principais benefícios:

- **Descarregue o backup local**: O Backup do Azure oferece uma solução simples para fazer backup de seus recursos locais na nuvem. Obtenha um backup de curto e longo prazo sem a necessidade de implantar soluções complexas de backup local.
- **Faça backup de VMs IaaS do Azure**: O Backup do Azure fornece backups independentes e isolados para proteger contra a destruição acidental de dados originais. Os backups são armazenados em um cofre dos Serviços de Recuperação com gerenciamento interno de pontos de recuperação. A configuração e a escalabilidade são simples, os backups são otimizados e você pode fazer uma restauração com facilidade, conforme necessário.
- **Dimensionar com facilidade** – o Backup do Azure usa o poder subjacente e a escala ilimitada da nuvem do Azure para proporcionar alta disponibilidade sem sobrecarga de manutenção ou de monitoramento.
- **Obter transferência de dados ilimitados**: o Backup do Azure não limita a quantidade de dados de entrada ou saída transferidos nem cobra pelos dados transferidos.
    - Os dados de saída são aqueles transferidos de um cofre dos Serviços de Recuperação durante uma operação de restauração.
    - Se você fizer um backup inicial offline usando o serviço de Importação/Exportação do Azure para importar grandes quantidades de dados, haverá um custo associado aos dados de entrada.  [Saiba mais](backup-azure-backup-import-export.md).
- **Mantenha os dados seguros**: O Backup do Azure fornece soluções para proteger dados em trânsito e em repouso.
- **Obtenha backups consistentes com aplicativo**: Um backup consistente com aplicativo significa que um ponto de recuperação tem todos os dados necessários para restaurar a cópia de backup. O Backup do Azure fornece backups consistentes com aplicativos, garantindo que correções adicionais não sejam necessárias para restaurar os dados. Restaurar dados consistentes com aplicativos reduz o tempo de restauração, permitindo que você rapidamente retorne ao estado de execução.
- **Manter os dados de curto e longo prazo**: Use os cofres dos Serviços de Recuperação para retenção de dados de curto e longo prazo. O Azure não limita o período de tempo que os dados podem ser mantidos em um cofre de Serviços de Recuperação. Você poderá mantê-los por quanto tempo desejar. O Backup do Azure tem um limite de pontos de recuperação 9999 por instância protegidos. 
- **Gerenciamento automático de armazenamento** - ambientes híbridos geralmente exigem armazenamento heterogêneo, alguns locais e alguns na nuvem. Com o Backup do Azure, não há nenhum custo para o uso de dispositivos de armazenamento local. O Backup do Azure aloca e gerencia o armazenamento de backup automaticamente e usa um modelo de pagamento conforme o uso, de modo que você pague apenas pelo armazenamento que consumir. [ Saiba mais ](https://azure.microsoft.com/pricing/details/backup) sobre preços.
- **Várias opções de armazenamento** – o Backup do Azure oferece dois tipos de replicação para manter seu armazenamento/seus dados altamente disponíveis.
    - O [LRS (armazenamento com redundância local)](../storage/common/storage-redundancy-lrs.md) replica seus dados três vezes (ele cria três cópias dos dados) em uma unidade de escala de armazenamento em um datacenter. Todas as cópias dos dados existem na mesma região. O LRS é uma opção de baixo custo para proteger seus dados contra falhas de hardware local.
    - O [GRS (armazenamento com redundância geográfica)](../storage/common/storage-redundancy-grs.md) é a opção de replicação padrão e recomendada. O GRS replica seus dados para uma região secundária (a centenas de quilômetros da região primária dos dados de origem). O GRS é mais caro do que o LRS, mas fornece um nível mais alto de durabilidade para seus dados, mesmo se houver uma interrupção regional.


## <a name="whats-the-difference-between-azure-backup-and-azure-site-recovery"></a>Qual é a diferença entre o Backup do Azure e o Azure Site Recovery?

Os serviços Backup do Azure e Azure Site Recovery contribuem para uma estratégia de BCDR (continuidade dos negócios e recuperação de desastres) em sua empresa. A BCDR consiste em dois objetivos amplos:

- Manter seus dados corporativos seguros e recuperáveis quando ocorrerem interrupções.
- Manter seus aplicativos e suas cargas de trabalho em funcionamento durante tempos de inatividade planejados e não planejados.

Ambos os serviços fornecem funcionalidades complementares, mas diferentes.

- **Azure Site Recovery**: O Site Recovery fornece uma solução de recuperação de desastre para computadores locais e VMs do Azure. Você replica os computadores de um local primário para um secundário. Quando ocorre um desastre, você faz failover dos computadores para o local secundário e os acessa nele. Quando tudo voltar a funcionar normalmente, você fará failback dos computadores para recuperá-los no site primário.
- **Backup do Azure**: O serviço de Backup do Azure faz backup dos dados de computadores locais e VMs do Azure. Os dados podem ser copiados em backup e recuperados em um nível granular, incluindo backup de arquivos, pastas, estado do sistema de computador e backup de dados com reconhecimento de aplicativo. O Backup do Azure lida com os dados em um nível mais granular do que o Site Recovery. Por exemplo, se uma apresentação em um laptop for corrompida, você usará o Backup do Azure para restaurar a apresentação. Caso deseje manter uma configuração de VM e os dados seguros e acessíveis, você poderá usar o Site Recovery.  

Use os pontos da tabela para ajudar a descobrir suas necessidades de BCDR.

**Objetivo** | **Detalhes** | **Comparação**
--- | --- | ---
**Backup de dados/retenção** | Os dados de backup podem ser retidos e armazenados por dias, meses ou até anos, se necessário, de uma perspectiva de conformidade. | Soluções de backup, como o Backup do Azure, permitem que você escolha minuciosamente os dados que deseja copiar em backup e ajuste as políticas de backup e retenção.<br/><br/> O Site Recovery não permite o mesmo ajuste fino.
**RPO (objetivo de ponto de recuperação)** | A quantidade de perda de dados aceitável se uma recuperação precisar ser feita. | Os backups têm um RPO mais variável.<br/><br/> Os backups de VM geralmente têm um RPO de um dia, enquanto os backups de banco de dados têm RPOs de até 15 minutos.<br/><br/> O Site Recovery fornece um RPO baixo, pois a replicação é contínua ou frequente, de modo que o delta entre a cópia de origem e de réplica seja pequeno.
**RTO (objetivo de tempo de recuperação)** |O tempo necessário para concluir uma recuperação ou restauração. | Devido ao RPO maior, a quantidade de dados que uma solução de backup precisa processar normalmente é muito maior, o que leva a RTOs mais longos. Por exemplo, a restauração de dados de fitas pode demorar dias, dependendo do tempo necessário para transportar a fita de um local externo.

## <a name="what-backup-scenarios-are-supported"></a>Há suporte para quais cenários de backup?

O Backup do Azure pode fazer backup de computadores locais e VMs do Azure.

**Computador** | **Cenário de backup**
--- | ---
**Backup local** |  1) Execute o agente do MARS (Serviços de Recuperação do Microsoft Azure) do Backup do Azure em computadores Windows locais para fazer backup do estado do sistema e de arquivos individuais. <br/><br/>2) Faça backup de computadores locais em um servidor de backup (System Center DPM [Data Protection Manager] ou MABS [Servidor de Backup do Microsoft Azure]) e, em seguida, configure o servidor de backup para fazer backup em um cofre dos Serviços de Recuperação do Backup do Azure no Azure.
**VMs do Azure** | 1) Habilite o backup para VMs individuais do Azure. Quando você habilita o backup, o Backup do Azure instala uma extensão no agente de VM do Azure em execução na VM. O agente faz backup de toda a VM.<br/><br/> 2) Execute o agente do MARS em uma VM do Azure. Isso é útil se você deseja fazer backup de arquivos e pastas individuais na VM.<br/><br/> 3) Faça backup de uma VM do Azure em um servidor DPM ou no MABS em execução no Azure. Em seguida, faça backup do servidor DPM/MABS em um cofre usando o Backup do Azure.


## <a name="why-use-a-backup-server"></a>Por que usar um servidor de backup?
As vantagens de fazer backup de computadores e aplicativos no armazenamento DPM/MABS e, em seguida, fazer backup do armazenamento DPM/MABS em um cofre são as seguintes:

- Fazer backup no MABS/DPM fornece backups com reconhecimento de aplicativo otimizados para aplicativos comuns, como SQL Server, Exchange e SharePoint, além de backups de arquivo/pasta/volume e backups de estado do computador (bare-metal, estado do sistema).
- Para computadores locais, você não precisa instalar o agente do MARS em cada computador que deseja fazer backup. Cada computador executa o agente de proteção do DPM/MABS e o agente do MARS é executado somente no MABS/DPM.
- Você tem mais flexibilidade e opções de agendamento granulares para a execução de backups.
- Você pode gerenciar backups para vários computadores reunidos em grupos de proteção em um único console. Isso é particularmente útil quando os aplicativos são organizados em camadas em vários computadores e você deseja fazer backup deles juntos.

Saiba mais sobre [como funciona o backup](backup-architecture.md#architecture-back-up-to-dpmmabs) ao usar um servidor de backup e os [requisitos de suporte](backup-support-matrix-mabs-dpm.md) para servidores de backup.

## <a name="what-can-i-back-up"></a>Do que é possível fazer backup?

**Computador** | **Método de backup** | **Fazer backup**
--- | --- | ---
**VMs locais do Windows** | Executar o agente do MARS | Faça backup de arquivos, pastas e do estado do sistema.<br/><br/> Computadores Linux não compatíveis.
**Computadores locais** | Fazer backup para o DPM/MABS | Faça backup de tudo o que é protegido pelo [DPM](backup-support-matrix-mabs-dpm.md#supported-backups-to-dpm) ou pelo [MABS](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs), incluindo arquivos/pastas/compartilhamentos/volumes e dados específicos do aplicativo.
**VMs do Azure** | Executar a extensão do backup de agente de VM do Azure | Fazer backup de toda a VM
**VMs do Azure** | Executar o agente do MARS | Faça backup de arquivos, pastas e do estado do sistema.<br/><br/> Computadores Linux não compatíveis.
**VMs do Azure** | Fazer backup no MABS/DPM em execução no Azure | Faça backup de tudo o que é protegido pelo [MABS](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs) ou pelo [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807), incluindo arquivos/pastas/compartilhamentos/volumes e dados específicos do aplicativo.

## <a name="what-backup-agents-do-i-need"></a>Quais agentes de backup são necessários?

**Cenário** | **Agente**
--- | ---
**Fazer backup de VMs do Azure** | Nenhum agente necessário. A extensão de VM do Azure para backup é instalada na VM do Azure quando você executa o primeiro backup de VM do Azure.<br/><br/> Suporte para Windows e Linux.
**Fazer backup de computadores Windows locais** | Baixe, instale e execute o agente do MARS diretamente no computador.
**Fazer backup das VMs do Azure com o agente do MARS** | Baixe, instale e execute o agente do MARS diretamente no computador. O agente do MARS pode ser executado junto com a extensão de backup.
**Fazer backup de computadores locais e VMs do Azure no DPM/MABS** | O agente de proteção do DPM ou do MABS é executado nos computadores que você deseja proteger. O agente do MARS é executado no servidor DPM/MABS para fazer backup no Azure.

## <a name="which-backup-agent-should-i-use"></a>Qual agente de backup devo usar?

**Backup** | **Solução** | **Limitação**
--- | --- | ---
**Desejo fazer backup de toda uma VM do Azure** | Habilite o backup para a VM. A extensão de backup será automaticamente configurada na VM do Azure no Windows ou no Linux. | Toda a VM é copiada em backup <br/><br/> Para VMs Windows, o backup é consistente com aplicativo. para o Linux, o backup é consistente com arquivo. Caso precise de reconhecimento de aplicativo para VMs do Linux, você precisará configurar isso com scripts personalizados.
**Desejo fazer backup de pastas/arquivos específicos em uma VM do Azure** | Implante o agente do MARS na VM.
**Desejo fazer backup diretamente de computadores Windows locais** | Instale o agente do MARS no computador. | Você pode fazer backup de arquivos, de pastas e do estado do sistema no Azure. Os backups não têm reconhecimento de aplicativo.
**Desejo fazer backup diretamente de computadores Linux locais** | Você precisará implantar o DPM ou o MABS para fazer backup no Azure. | Não há suporte para o host de Backup do Linux, você pode apenas fazer backup do computador convidado do Linux hospedado em Hyper-V ou VMWare.
**Desejo fazer backup de aplicativos executados localmente** | Para backups com reconhecimento de aplicativo, os computadores precisam estar protegidos pelo DPM ou pelo MABS.
**Desejo obter configurações granulares e flexíveis de backup e recuperação para VMs do Azure** | Proteja as VMs do Azure com o MABS/DPM em execução no Azure para flexibilidade adicional no agendamento de backup e flexibilidade total para proteger e restaurar arquivos, pastas, volumes, aplicativos e o estado do sistema.

## <a name="backup-and-retention"></a>Backup e retenção

O Backup do Azure tem um limite de 9999 pontos de recuperação, também conhecido como cópias de backup ou instantâneos, por *instância protegida*.

- Uma instância protegida é um computador, servidor (físico ou virtual) ou carga de trabalho configurado para fazer backup de dados no Azure. Uma instância está protegida depois que uma cópia de backup de dados foi salva.
- A cópia de backup de dados é a proteção. Se a fonte de dados foi perdida ou corrompida, a cópia de backup pode restaurar os dados de origem.

A tabela a seguir mostra a frequência máxima de backup de cada componente. A configuração da política de backup determina a rapidez com a qual você consome os pontos de recuperação. Por exemplo, se você criar um ponto de recuperação por dia, poderá depois retê-los por 27 anos antes de executá-los. Se você usar um ponto de recuperação por mês, poderá depois retê-los por 833 anos antes de executá-los. O serviço de Backup não define um limite de tempo de validade em um ponto de recuperação.

|  | Agente de Backup do Azure | System Center DPM | Servidor de Backup do Azure | Backup de VM IaaS do Azure |
| --- | --- | --- | --- | --- |
| Frequência de backup<br/> (para o Cofre dos Serviços de Recuperação) |Três backups por dia |Dois backups por dia |Dois backups por dia |Um backup por dia |
| Frequência de backup<br/> (para o disco) |Não aplicável |A cada 15 minutos para o SQL Server<br/><br/> A cada hora para outras cargas de trabalho |A cada 15 minutos para o SQL Server<br/><br/> A cada hora para outras cargas de trabalho |Não aplicável |
| Opções de retenção |Diária, semanal, mensal, anual |Diária, semanal, mensal, anual |Diária, semanal, mensal, anual |Diária, semanal, mensal, anual |
| Pontos de recuperação máximos por instância protegida |9999|9999|9999|9999|
| Período de retenção máximo |Depende da frequência de backup |Depende da frequência de backup |Depende da frequência de backup |Depende da frequência de backup |
| Pontos de recuperação no disco local |Não aplicável | 64 para Servidores de Arquivos<br/><br/> 448 para Servidores de Aplicativos | 64 para Servidores de Arquivos<br/><br/> 448 para Servidores de Aplicativos |Não aplicável |
| Pontos de recuperação em fita |Não aplicável |Ilimitado |Não aplicável |Não aplicável |

## <a name="how-does-azure-backup-work-with-encryption"></a>Como o Backup do Azure funciona com criptografia?

**Criptografia** | **Backup no local** | **Fazer backup de VMs do Azure** | **Fazer backup do SQL em VMs do Azure**
--- | --- | --- | ---
Criptografia em repouso<br/> (Criptografia de dados que são persistidos/armazenados) | A frase secreta especificada pelo cliente é usada para criptografar os dados | A [Criptografia do Serviço de Armazenamento (SSE)](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) do Azure é usada para criptografar os dados armazenados no cofre.<br/><br/> O backup criptografa automaticamente os dados antes de armazená-los. O Armazenamento do Azure descriptografa os dados antes de recuperá-los. Atualmente, não há suporte para uso de chaves gerenciadas pelo cliente para SSE.<br/><br/> Você pode fazer backup de VMs que usam o [Azure Disk Encryption (ADE)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) para criptografar o sistema operacional e discos de dados. O Backup do Azure dá suporte a VMs criptografadas somente com BEK e com BEK e [KEK](https://blogs.msdn.microsoft.com/cclayton/2017/01/03/creating-a-key-encrypting-key-kek/). Examine as [limitações](backup-azure-vms-encryption.md#encryption-support). | O Backup do Azure dá suporte a backup de bancos de dados do SQL Server ou de servidor com [TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) habilitado. O Backup dá suporte a TDE com chaves gerenciadas pelo Azure ou com chaves gerenciadas pelo cliente (BYOK).<br/><br/> O Backup não realiza qualquer criptografia do SQL como parte do processo de backup.
Criptografia em trânsito<br/> (Criptografia de dados em movimento de um local para outro) | Dados são criptografados usando AES256 e enviados para o cofre no Azure por HTTPS | Dentro do Azure, os dados entre o armazenamento do Azure e o cofre são protegidos por HTTPS. Esses dados permanecem na rede de backbone do Azure.<br/><br/> Para a recuperação de arquivos, o iSCSI protege os dados transmitidos entre o cofre e a VM do Azure. O túnel seguro protege o canal iSCSI. | Dentro do Azure, os dados entre o armazenamento do Azure e o cofre são protegidos por HTTPS.<br/><br/> A recuperação de arquivos não é relevante para SQL.

## <a name="next-steps"></a>Próximas etapas

- [Examine](backup-architecture.md) a arquitetura e os componentes para diferentes cenários de backup.
- [Verifique](backup-support-matrix.md) os requisitos e limitações de suporte para backup e para [backup de VMs do Azure](backup-support-matrix-iaas.md).

[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png
