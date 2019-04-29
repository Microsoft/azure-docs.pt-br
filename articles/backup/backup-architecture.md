---
title: Arquitetura de Backup do Azure
description: Fornece uma visão geral da arquitetura, componentes e processos usados pelo serviço de Backup do Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: raynew
ms.openlocfilehash: 98ffe145103b4be04014627ed04d04dcf7542015
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60647335"
---
# <a name="azure-backup-architecture"></a>Arquitetura de Backup do Azure

Você pode usar o [serviço de Backup do Azure](backup-overview.md) para fazer backup de dados para a plataforma de nuvem do Microsoft Azure. Este artigo resume a arquitetura, componentes e processos de Backup do Azure. 

## <a name="what-does-azure-backup-do"></a>O que o Backup do Azure faz?

Backup do Azure faz backup de dados, o estado da máquina e a cargas de trabalho em execução em máquinas locais e instâncias de máquina virtual do Azure (VM). Há vários cenários de Backup do Azure.

## <a name="how-does-azure-backup-work"></a>Como funciona o Backup do Azure?

Você pode fazer backup de máquinas e dados usando vários métodos:

- **Fazer backup dos computadores locais**:
    - Você pode fazer backup de máquinas do Windows local diretamente no Azure usando o agente de serviços de recuperação do Azure Backup Microsoft Azure (MARS). Computadores Linux não tem suporte.
    - Você pode fazer backup de máquinas locais para um servidor de backup (System Center Data Protection Manager (DPM) ou Microsoft Azure Backup MABS (servidor)). Em seguida, você pode fazer backup do servidor de backup em um cofre de serviços de recuperação no Azure.

- **Fazer backup de VMs do Azure**:
    - Você pode fazer backup de VMs do Azure diretamente. O Backup do Azure instala uma extensão de backup para o agente de VM do Azure que está em execução na VM. Essa extensão faz backup de toda a VM.
    - Você pode fazer backup de arquivos e pastas específicos na VM do Azure executando o agente MARS.
    - Você pode fazer backup de VMs do Azure para o MABS que está em execução no Azure e, em seguida, você pode fazer backup do MABS para um cofre dos serviços de recuperação.

Saiba mais sobre [o que você pode fazer backup](backup-overview.md) sobre [suporte para cenários de backup](backup-support-matrix.md).

## <a name="where-is-data-backed-up"></a>Onde os dados são armazenados em backup?

O Backup do Azure armazena dados de backup em um cofre dos serviços de recuperação. Um cofre é uma entidade de armazenamento online no Azure que é usado para armazenar dados, como cópias de backup, pontos de recuperação e as políticas de backup.

Cofres dos serviços de recuperação têm os seguintes recursos:

- Os cofres facilitam a organização dos dados de backup, minimizando a sobrecarga de gerenciamento.
- Em cada assinatura do Azure, você pode criar até 500 cofres.
- Você pode monitorar itens de backup em um cofre, incluindo as máquinas locais e VMs do Azure.
- É possível gerenciar o acesso ao cofre com o [controle de acesso baseado em função (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) do Azure.
- Você especifica como os dados no cofre são replicados para redundância:
    - **LRS (armazenamento com redundância local)**: Para proteger contra falhas em um data center, você pode usar o LRS. O LRS replica os dados para uma unidade de escala de armazenamento. [Saiba mais](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs).
    - **GRS (Armazenamento com redundância geográfica)**: Para proteger contra interrupções de toda a região, você pode usar GRS. GRS replica seus dados para uma região secundária. [Saiba mais](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs). 
    - Por padrão, os cofres dos serviços de recuperação usam GRS. 

## <a name="backup-agents"></a>Agentes de backup

O Backup do Azure fornece agentes de backup diferentes, dependendo do tipo de máquina está sendo feito backup:

**Agente** | **Detalhes** 
--- | --- 
**Agente de MARS** | <ul><li>É executado em computadores com Windows Server individuais no local para fazer backup de arquivos, pastas e o estado do sistema.</li> <li>É executado em VMs do Azure para fazer backup de arquivos, pastas e o estado do sistema.</li> <li>É executado em servidores MABS/DPM para fazer backup do disco de armazenamento local do MABS/DPM no Azure.</li></ul> 
**Extensão da VM do Azure** | É executado em VMs do Azure para fazer backup delas em um cofre.

## <a name="backup-types"></a>Tipos de backup

A tabela a seguir explica os diferentes tipos de backups e quando eles são usados:

**Tipo de backup** | **Detalhes** | **Uso**
--- | --- | ---
**Full** | Um backup completo contém a fonte de dados inteiro. Usa mais largura de banda de rede que os backups incrementais ou diferenciais. | Usado no backup inicial.
**Differential** |  Um backup diferencial armazena os blocos que foram alterados desde o backup completo inicial. Usa uma quantidade menor de armazenamento e rede e não manter cópias redundantes dos dados inalterados.<br/><br/> Ineficiente como blocos de dados que são as mesmas entre os backups posteriores são transferidos e armazenados. | Não é usada pelo Backup do Azure.
**Incremental** | Um backup incremental armazena apenas os blocos de dados que foram alterados desde o backup anterior. Alto armazenamento e eficiência de rede. <br/><br/> Com o backup incremental, não é necessário complementar com backups completos. | Usado pelo MABS/DPM para backups em disco e usado em todos os backups no Azure.

## <a name="sql-server-backup-types"></a>Tipos de backup do SQL Server

A tabela a seguir explica os diferentes tipos de backups usados para bancos de dados do SQL Server e a frequência com que eles são usados:

**Tipo de backup** | **Detalhes** | **Uso**
--- | --- | ---
**Backup completo** | Um backup completo do banco de dados faz backup do banco de dados inteiro. Ele contém todos os dados em um banco de dados específico ou em um conjunto de grupos de arquivos ou arquivos. Um backup completo também contém logs de forma para recuperar esses dados. | No máximo, você pode acionar um backup completo por dia.<br/><br/> Você pode optar por fazer um backup completo em um intervalo diário ou semanal.
**Backup diferencial** | Um backup diferencial é baseado no mais recente completa dos dados backup anterior.<br/><br/> Ele captura apenas os dados que foram alterados desde o backup completo. |  No máximo, você pode acionar um backup diferencial por dia.<br/><br/> Você não pode configurar um backup completo e um backup diferencial no mesmo dia.
**Backup de log de transações** | Um backup de log permite a restauração pontual até um determinado segundo. | No máximo, você pode configurar backups de log de transações a cada 15 minutos.

### <a name="comparison-of-backup-types"></a>Comparação dos tipos de backup

O consumo do armazenamento, o RTO (objetivo do tempo de recuperação) e o consumo de rede variam para cada tipo de backup. A imagem a seguir mostra uma comparação dos tipos de backup:

- Fonte de dados A é composta de 10 blocos de armazenamento, A1 A10, que são obtidos em backup mensal.
- Os blocos A2, A3, A4 e A9 alteram-se no primeiro mês e o bloco A5 altera-se no próximo mês.
- Para backups diferenciais, no segundo mês é feito o backup dos blocos que foram alterados, A2, A3, A4 e A9. No terceiro mês, é feito novamente o backup desses mesmos blocos, junto com o bloco A5 que foi alterado. O backup dos blocos alterados continua a ser feito até que o próximo backup completo aconteça.
- Para backups incrementais, no segundo mês, os blocos A2, A3, A4 e A9 são marcados como alteram e transferidos. No terceiro mês, somente o bloco alterado A5 é marcado e transferido. 

![Imagem mostrando comparações de métodos de backup](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Recursos de backup

A tabela a seguir resume os recursos com suporte para os diferentes tipos de backup:

**Recurso** | **Computadores com Windows Server local (direto)** | **VMs do Azure** | **Computadores ou aplicativos com o MABS/DPM**
--- | --- | --- | ---
Fazer backup para o cofre | ![Sim][green] | ![sim][green] | ![Sim][green] 
Fazer backup em disco do MABS/DPM, em seguida, para o Azure | | | ![Sim][green] 
Compactar os dados enviados para backup | ![Sim][green] | Nenhuma compactação é usada durante a transferência de dados. O armazenamento está um pouco inflado, mas a restauração é mais rápida.  | ![Sim][green] 
Executa o backup incremental |![Sim][green] |![sim][green] |![Sim][green] 
Fazer backup de discos com eliminação de duplicação | | | ![Parcialmente][yellow]<br/><br/> Para servidores do MABS/DPM implantados apenas localmente. 

![Chave de tabela](./media/backup-architecture/table-key.png)

## <a name="architecture-direct-backup-of-azure-vms"></a>Arquitetura: Backup direto de VMs do Azure

1. Quando você habilita o backup para uma VM do Azure, um backup executado de acordo com a agenda que você especificar.
1. Durante o primeiro backup, uma extensão de backup está instalada na VM, se a VM estiver em execução.
    - Para VMs do Windows, a extensão VMSnapshot está instalada.
    - Para VMs do Linux, a extensão VMSnapshot Linux é instalada.
1. A extensão tira um instantâneo de nível de armazenamento. 
    - Para VMs do Windows que estão em execução, o Backup coordena com o Windows Volume Shadow cópia Service (VSS) para criar um instantâneo consistente com o aplicativo da VM. Por padrão, o Backup usa backups completos de VSS. Se o Backup não conseguir obter um instantâneo consistente do aplicativo, ele gerará um instantâneo consistente com o arquivo.
    - Para VMs Linux, o Backup tira um instantâneo consistente com o arquivo. Para instantâneos consistentes com o aplicativo, você precisa personalizar manualmente os scripts pré/pós.
    - O backup é otimizado pela execução do backup em cada disco de VM em paralelo. Para cada disco cujo backup está sendo feito, o Backup do Azure lê os blocos no disco e armazena somente os dados alterados. 
1. Após o instantâneo, os dados são transferidos para o cofre. 
    - Apenas os blocos de dados alterados desde o último backup são copiados.
    - Os dados não são criptografados. O Backup do Azure pode fazer backup de VMs do Azure que foram criptografados com o Azure Disk Encryption.
    - Dados de instantâneo não podem ser imediatamente copiados no cofre. Em horários de pico, o backup pode levar algumas horas. Tempo total de backup para uma máquina virtual será menor que 24 horas para políticas de backup diárias.
1. Depois que os dados são enviados para o cofre, o instantâneo é removido e um ponto de recuperação é criado.

As VMs do Azure precisam de acesso à internet para comandos de controle. Se você estiver fazendo backup de cargas de trabalho dentro da VM (por exemplo, backups de banco de dados do SQL Server), os dados de back-end também precisam de acesso à internet. 

![Backup de VMs do Azure](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>Arquitetura: Backup direto de computadores com Windows Server locais ou arquivos de VM do Azure ou pastas

1. Para configurar o cenário, você pode baixar e instala o agente do MARS na máquina. Você, em seguida, selecione os itens para backup, quando backups serão executados e quanto tempo eles serão mantidos no Azure.
1. O backup inicial executa de acordo com as configurações de backup.
1. O agente do MARS usa o VSS para tirar um instantâneo point-in-time dos volumes selecionados para backup.
    - O agente do MARS usa somente a operação de gravação de sistema Windows para capturar o instantâneo.
    - Como o agente não usa todos os gravadores VSS de aplicativo, ele não captura instantâneos consistentes de aplicativos.
1. Depois de tirar o instantâneo com o VSS, o agente do MARS cria um disco rígido virtual (VHD) na pasta de cache que você especificou quando configurou o backup. O agente também armazena somas de verificação para cada bloco de dados.
1. Backups incrementais execute acordo com o agendamento que você especificar, a menos que você executar um backup ad hoc.
1. Nos backups incrementais, os arquivos alterados são identificados e um novo VHD é criado. O VHD é compactado e criptografado e, em seguida, ele é enviado para o cofre.
1. Após o backup incremental, o novo VHD será mesclado com o VHD criado após a replicação inicial. Esse VHD mesclada fornece o estado mais recente a ser usado para comparação de backup em andamento.

![Backup de máquinas do Windows Server local com o agente MARS](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Arquitetura: Fazer backup para o DPM/MABS

1. Você pode instalar o agente de proteção do DPM ou MABS em máquinas que você deseja proteger. Você, em seguida, adicione as máquinas para um grupo de proteção do DPM.
    - Para proteger as máquinas locais, o servidor do DPM ou MABS deverá ser local.
    - Para proteger as VMs do Azure, o servidor do MABS deverá estar no Azure, em execução como uma VM do Azure.
    - Com o MABS/DPM, você pode proteger compartilhamentos, pastas, arquivos e volumes de backup. Você também pode proteger o estado do sistema do computador (sem sistema operacional), e você pode proteger aplicativos específicos com reconhecimento de aplicativo de configurações de backup.
1. Quando você configurar a proteção para uma máquina ou o aplicativo no MABS/DPM, você optar por fazer backup no disco local do MABS/DPM para armazenamento de curto prazo e para o Azure para proteção online. Você também especificar quando executar o backup no armazenamento local do MABS/DPM e quando o backup online para o Azure deve ser executado.
1. O disco da carga de trabalho protegida é feito backup dos discos do MABS/DPM local, acordo com a agenda especificada.
4. Os discos do MABS/DPM passam por backup para o cofre pelo agente de MARS está em execução no servidor MABS/DPM.

![Backup de máquinas e cargas de trabalho protegidas pelo DPM ou MABS](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Armazenamento da VM do Azure

As VMs do Azure usam discos para armazenar seu sistema operacional, aplicativos e dados. Cada VM do Azure tem pelo menos dois discos: um disco para o sistema operacional e um disco temporário. As VMs do Azure também podem ter discos de dados para dados de aplicativo. Os discos são armazenados como VHDs.

- VHDs são armazenados como blobs de página nas contas de armazenamento standard ou premium no Azure:
    - **Armazenamento Standard:** suporte de disco confiável e de baixo custo para VMs que executam cargas de trabalho que não são sensíveis à latência. Armazenamento Standard pode usar discos padrão unidade de estado sólido (SSD) ou unidade de disco rígido padrão (HDD).
    - **Armazenamento Premium:** suporte de disco de alto desempenho. Usa discos SSD premium.
- Há diferentes níveis de desempenho de discos:
    - **Disco HDD padrão:** Com suporte de HDDs e usado para armazenamento econômico.
    - **Disco SSD de padrão:** Combina elementos de discos SSD premium e discos padrão do HDD. Oferece mais consistente de desempenho e confiabilidade de disco rígido, mas ainda econômica.
    - **Premium SSD disk:** Apoiada por SSDs e oferece alto desempenho e baixa latência para VMs que estão executando cargas de trabalho de e/S intensas.
- Os discos podem ser gerenciados ou não:
    - **Discos não gerenciados:** Tipo tradicional de discos usados pelas VMs. Com esses discos, você cria sua própria conta de armazenamento e a especifica ao criar o disco. Em seguida, você precisa descobrir como maximizar os recursos de armazenamento para suas VMs.
    - **Discos gerenciados:** Azure cria e gerencia as contas de armazenamento para você. Você especifica a camada de tamanho e o desempenho do disco e Azure cria discos gerenciados para você. Como adicionar discos e dimensionar as VMs, o Azure lida com as contas de armazenamento.

Para obter mais informações sobre o armazenamento em disco e os tipos de disco disponível para VMs, consulte estes artigos:

- [Managed disks do Azure para VMs do Windows](../virtual-machines/windows/managed-disks-overview.md)
- [Managed disks do Azure para VMs do Linux](../virtual-machines/linux/managed-disks-overview.md)
- [Tipos de disco disponível para VMs](../virtual-machines/windows/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Fazer backup e restaurar VMs do Azure com o armazenamento premium 

Você pode fazer backup de máquinas virtuais do Azure usando o armazenamento premium com o Backup do Azure:

- Durante o processo de backup de máquinas virtuais com o armazenamento premium, o serviço de Backup cria um local de preparo temporário, denominado *AzureBackup -*, na conta de armazenamento. O tamanho do local de preparo é igual ao tamanho do instantâneo de ponto de recuperação.
- Certifique-se de que a conta de armazenamento premium tenha espaço livre suficiente para acomodar o local de preparo temporário. [Saiba mais](../storage/common/storage-scalability-targets.md#premium-performance-storage-account-scale-limits). Não modifique o local de preparo.
- Após a conclusão do trabalho de backup, o local de preparo será excluído.
- O preço do armazenamento usado para o local de preparo é consistente com os [preços do armazenamento premium](../virtual-machines/windows/disks-types.md#billing).

Quando você restaurar VMs do Azure usando o armazenamento premium, você pode restaurá-los para o armazenamento standard ou premium. Normalmente, você teria restaurá-los para o armazenamento premium. Mas se você precisar apenas um subconjunto dos arquivos da VM, pode ser econômico para restaurá-los para o armazenamento standard.

### <a name="back-up-and-restore-managed-disks"></a>Fazer backup e restaurar os discos gerenciados

Você pode fazer backup de VMs do Azure com discos gerenciados:

- Faça o backup de VMs com discos gerenciados da mesma maneira que faria com qualquer outra VM do Azure. Você pode fazer backup da VM diretamente nas configurações da máquina virtual ou pode habilitar o backup para VMs no cofre dos Serviços de Recuperação.
- Você pode fazer backup de VMs em discos gerenciados por meio de coleções de RestorePoint criadas com base em discos gerenciados.
- O Backup do Azure também dá suporte a backup de VMs com discos gerenciados que foram criptografados usando o Azure Disk Encryption.

Quando você restaurar VMs com discos gerenciados, você pode restaurar para uma VM completa com discos gerenciados ou para uma conta de armazenamento:

- Durante o processo de restauração, o Azure lida com os discos gerenciados. Se você estiver usando a opção de conta de armazenamento, você pode gerenciar a conta de armazenamento que é criada durante o processo de restauração.
- Se você restaurar uma VM gerenciada que é criptografada, certifique-se de chaves da VM e segredos existirem no cofre de chaves antes de iniciar o processo de restauração.

## <a name="next-steps"></a>Próximas etapas

- Examine a matriz de suporte para [Saiba mais sobre os recursos com suporte e limitações para cenários de backup](backup-support-matrix.md).
- Configure backup para uma das seguintes situações:
    - [Fazer backup de VMs do Azure](backup-azure-arm-vms-prepare.md).
    - [Fazer backup direto de computadores Windows](tutorial-backup-windows-server-to-azure.md), sem um servidor de backup.
    - [Configurar o MABS](backup-azure-microsoft-azure-backup.md) para backup no Azure e, em seguida, fazer backup de cargas de trabalho no MABS.
    - [Configurar o DPM](backup-azure-dpm-introduction.md) para backup no Azure e, em seguida, fazer backup de cargas de trabalho no DPM.


[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png

