---
title: Oracle Database em estratégias de backup de máquinas virtuais do Azure
description: Opções para fazer backup de bancos de dados Oracle em um ambiente de VM Linux do Azure.
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: 8a1eb1c21663e0294cd384daa0ba644adf78007a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101673215"
---
# <a name="oracle-database-in-azure-linux-vm-backup-strategies"></a>Oracle Database em estratégias de backup de VM Linux do Azure

Os backups de banco de dados protegem o banco de dados contra a perda de dado devido à falha do componente de armazenamento e data center Eles também podem ser um meio de recuperação de erro humano e uma maneira de clonar um banco de dados para fins de desenvolvimento ou teste. 

No Azure, como todo o armazenamento é altamente redundante e a perda de um ou mais discos não levará a uma interrupção do banco de dados, os backups são usados com mais frequência para se proteger contra erros humanos, para clonar operações ou preservação do dado para fins regulatórios. Eles também são meios de se proteger contra interrupções regionais em que uma tecnologia de recuperação de desastres como o DataGuard não está em uso. Nesse caso, os backups devem ser armazenados em diferentes regiões do Azure usando a replicação com redundância geográfica, para que ele fique disponível fora da região do banco de dados primário.

## <a name="azure-storage"></a>Armazenamento do Azure 

Os [serviços de armazenamento do Azure](../../../storage/common/storage-introduction.md) são a solução de armazenamento em nuvem da Microsoft para cenários de armazenamento de dados modernos. O armazenamento do Azure oferece vários serviços que podem ser usados para montar o armazenamento externo para a VM Linux do Azure, adequado como mídia de backup para bancos de dados Oracle. Uma ferramenta de backup como o Oracle RMAN é necessária para iniciar uma operação de backup ou restauração e copiar o backup de/para o serviço de armazenamento do Azure.
 
Os serviços de armazenamento do Azure oferecem os seguintes benefícios:

-  Durável e altamente disponível. A redundância garante a segurança dos seus dados no caso de falhas de hardware transitórias. Todo o armazenamento é, por padrão, espelhado em triplo. Você também pode optar por replicar os dados em data centers ou regiões geográficas para obter mais proteção contra catástrofes locais ou desastres naturais. Os dados replicados dessa maneira permanecem altamente disponíveis no caso de uma interrupção inesperada.

-  Segura. Todos os dados gravados em uma conta de armazenamento do Azure são criptografados pelo serviço. O Armazenamento do Azure oferece um controle refinado sobre quem possui acesso aos seus dados.

-  Escalonável. O Armazenamento do Azure foi concebido para ser altamente escalonável e atender às necessidades de desempenho e armazenamento de dados dos aplicativos atuais.

-  Gerenciado. O Azure lida com a manutenção de hardware, atualizações e problemas críticos para você.

-  Acessível. Os dados no Armazenamento do Azure são acessíveis de qualquer lugar no mundo por HTTP ou HTTPS. A Microsoft fornece bibliotecas de cliente para o armazenamento do Azure em uma variedade de linguagens, incluindo .NET, Java, Node.js, Python, PHP, Ruby, Go e outros, bem como uma API REST madura. O Armazenamento do Azure oferece suporte para scripts no Azure PowerShell ou na CLI do Azure. E o Portal do Azure e o Gerenciador de Armazenamento do Azure oferecem soluções visualmente fáceis para o trabalho com os seus dados.

A plataforma de armazenamento do Azure inclui os seguintes serviços de dados que são adequados para usar como mídia de backup para o Oracle Database:

-  Blobs do Azure: um repositório de objetos altamente escalonável para texto e dados binários. Também inclui suporte para análise de Big Data por meio de Data Lake Storage Gen2.

-  Arquivos do Azure: compartilhamentos de arquivos gerenciados para implantações locais ou na nuvem.

-  Discos do Azure: volumes de armazenamento de nível de bloco para VMs do Azure.

### <a name="cross-regional-storage-mounting"></a>Montagem de armazenamento entre regiões

A capacidade de acessar o armazenamento de backup entre regiões é um aspecto importante do BCDR e é útil para clonar bancos de dados de backups em diferentes regiões geográficas. O armazenamento em nuvem do Azure fornece cinco níveis de [redundância](../../../storage/common/storage-redundancy.md)
- [LRS (](../../../storage/common/storage-redundancy.md#locally-redundant-storage) armazenamento com redundância local) em que os dados são replicados três vezes em um único local físico na região primária.  
- [ZRS (](../../../storage/common/storage-redundancy.md#zone-redundant-storage) armazenamento com redundância de zona) em que os dados são protegidos por LRS na região primária e replicados de forma síncrona em três zonas de disponibilidade (AZ) na região primária. Cada AZ também é LRS protegido. 
- Armazenamento de Geo-Redundant [(GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) em que os dados são protegidos por LRS na região primária e replicados assincronamente para uma região secundária onde também é protegido pelo lRS.
- O [GZRS (](../../../storage/common/storage-redundancy.md#geo-redundant-storage) armazenamento com redundância de zona geográfica) copia seus dados de forma síncrona em três zonas de disponibilidade do Azure na região primária usando ZRS. Em seguida, ele copia os dados de forma assíncrona para um único local físico na região secundária. Em todos os locais, os dados são protegidos por LRS também. 
- O acesso de leitura Geo-Redundant armazenamento [(ra-grs)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) e o armazenamento com redundância de zona geográfica com acesso de leitura [(ra-GZRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) permite o acesso somente leitura aos dados replicados para a região secundária em todos os momentos. 

#### <a name="blob-storage-and-file-storage"></a>Armazenamento de BLOBs e armazenamento de arquivos

Ao usar os arquivos do Azure com os protocolos SMB ou NFS 4,1 (versão prévia) para montar como armazenamento de backup, observe que os arquivos do Azure não oferecem suporte ao armazenamento com redundância geográfica com acesso de leitura (RA-GRS) e ao armazenamento com redundância de acesso de leitura (RA-GZRS). 

Além disso, se o requisito de armazenamento de backup for maior que 5, os arquivos do Azure TiB exigirão um [recurso de compartilhamentos de arquivos grandes](../../../storage/files/storage-files-planning.md) habilitado, que não oferece suporte à redundância grs ou GZRS somente com suporte para lRS. 

O blob do Azure montado usando o protocolo NFS 3,0 (versão prévia) atualmente dá suporte apenas à redundância LRS e ZRS.  

O blob do Azure configurado com qualquer opção de redundância pode ser montado usando Blobfuse.

#### <a name="recovery-services-vault"></a>Cofre dos Serviços de Recuperação 

Um cofre dos Serviços de Recuperação é uma entidade de gerenciamento que armazena pontos de recuperação criados ao longo do tempo e que fornece uma interface para executar operações relacionadas a backup. Isso inclui fazer backups sob demanda, executar restaurações e criar políticas de backup.
O backup do Azure manipula automaticamente o armazenamento para o cofre. Você precisa especificar como esse armazenamento é replicado no momento da criação. Ele não pode ser alterado depois que os itens são protegidos no cofre. Para redundância regional, escolha a configuração com redundância geográfica.

Se você pretende restaurar para um secundário, a [região emparelhada do Azure](../../../best-practices-availability-paired-regions.md) habilita o recurso de [restauração entre regiões](../../../backup/backup-create-rs-vault.md) . Quando a restauração entre regiões estiver habilitada, o armazenamento de backup será movido de GRS para o armazenamento com redundância geográfica com acesso de leitura (RA-GRS). 

### <a name="azure-blob-storage"></a>Armazenamento do Blobs do Azure

O armazenamento de BLOBs do Azure é um serviço de armazenamento baseado em nuvem altamente econômico, durável e seguro usado para armazenar grandes quantidades de dados não estruturados e é uma mídia ideal a ser usada para backups de Oracle Database. O armazenamento de BLOBs do Azure pode ser montado em VMs Linux do Azure usando o protocolo NFS v 3.0 ou Blobfuse (fusível do Linux).

#### <a name="azure-blob-blobfuse"></a>Blobfuse de blob do Azure

[Blobfuse](../../../storage/blobs/storage-how-to-mount-container-linux.md) é um projeto de código-fonte aberto desenvolvido para fornecer um sistema de arquivos virtual apoiado pelo armazenamento de BLOBs do Azure. Ele usa a biblioteca de código aberto do libfuse para se comunicar com o módulo de kernel de fusível do Linux e implementa as operações do sistema de arquivos usando as APIs REST do Azure Storage Blob. O Blobfuse está disponível atualmente para distribuições Ubuntu e CentOS/RedHat. Ele também está disponível para kubernetes usando o [Driver CSI](https://github.com/kubernetes-sigs/blob-csi-driver). 

Embora onipresente em todas as regiões do Azure e funcione com todos os tipos de conta de armazenamento, incluindo uso geral V1/V2 e Azure Data Lake Store Gen2, o desempenho oferecido pelo Blobfuse mostrou ser menos do que protocolos alternativos, como SMB ou NFS. Para adequação à mídia de backup do banco de dados, recomendamos o uso de protocolos SMB ou [NFS](../../../storage/blobs/storage-how-to-mount-container-linux.md) para montar o armazenamento de BLOBs do Azure. 

#### <a name="azure-blob-nfs-v30-preview"></a>NFS do blob do Azure v 3.0 (visualização)

O suporte do Azure para o protocolo NFS (Network File System) v 3.0 está agora em versão prévia. O suporte a [NFS](../../../storage/blobs/network-file-system-protocol-support.md) permite que os clientes Windows e Linux montem um contêiner de armazenamento de BLOBs em uma VM do Azure. 

A visualização pública do NFS 3,0 dá suporte a contas de armazenamento GPV2 com desempenho de camada Standard nas seguintes regiões: 
- Leste da Austrália
- Coreia Central
- EUA Central do Sul. 

Para garantir a segurança de rede, a conta de armazenamento usada para a montagem de NFS deve estar contida em uma VNet. A segurança do Azure Active Directory (AD) e as listas de controle de acesso (ACLs) ainda não têm suporte em contas que têm o suporte do protocolo NFS 3,0 habilitado neles.

### <a name="azure-files"></a>Arquivos do Azure

O [arquivos do Azure](../../../storage/files/storage-files-introduction.md) é um sistema de arquivos distribuído totalmente gerenciado e baseado em nuvem que pode ser montado em clientes locais ou Windows, Linux ou MacOS baseados em nuvem.

Os arquivos do Azure oferecem compartilhamentos de arquivos totalmente gerenciados entre plataformas na nuvem que são acessíveis por meio do protocolo SMB e do protocolo NFS (sistema de arquivos de rede) (visualização). Atualmente, os arquivos do Azure não dão suporte ao acesso de vários protocolos, portanto, um compartilhamento pode ser apenas um compartilhamento NFS ou um compartilhamento SMB. É recomendável determinar qual protocolo melhor atende às suas necessidades antes de criar compartilhamentos de arquivos do Azure.

Os compartilhamentos de arquivos do Azure também podem ser protegidos por meio do backup do Azure para o cofre dos serviços de recuperação, fornecendo uma camada adicional de proteção para os backups do Oracle RMAN.

#### <a name="azure-files-nfs-v41-preview"></a>Arquivos do Azure NFS v 4.1 (visualização)

Os compartilhamentos de arquivos do Azure podem ser montados em distribuições do Linux usando o protocolo NFS 4.1 (sistema de arquivos de rede). Durante a visualização, há uma série de limitações para os recursos com suporte, que estão documentados [aqui](../../../storage/files/storage-files-how-to-mount-nfs-shares.md). 

Embora na versão prévia, o NFS v 4.1 dos arquivos do Azure também é restrito às seguintes [regiões](../../../storage/files/storage-files-how-to-mount-nfs-shares.md):
- Leste dos EUA (LRS e ZRS)
- Leste dos EUA 2
- Oeste dos EUA 2
- Europa Ocidental
- Sudeste Asiático
- Sul do Reino Unido
- Leste da Austrália

#### <a name="azure-files-smb-30"></a>Arquivos do Azure SMB 3,0

Os compartilhamentos de arquivos do Azure podem ser montados em distribuições do Linux usando o cliente de kernel SMB. O protocolo CIFS (Common Internet File System), disponível em distribuições do Linux, é um dialeto de SMB. Ao montar o SMB de arquivos do Azure em VMs Linux, ele é montado como um sistema de arquivos CIFS e o pacote CIFS deve ser instalado. 

O SMB de arquivos do Azure está disponível em todas as regiões do Azure e mostra as mesmas características de desempenho que os protocolos NFS v 3.0 e v 4.1 e, portanto, é o método recomendado para fornecer mídia de armazenamento de backup para VMs Linux do Azure.  

Há duas versões com suporte do SMB disponíveis, SMB 2,1 e SMB 3,0, com o último recomendado, pois ele dá suporte à criptografia em trânsito. No entanto, diferentes versões de kernels do Linux têm suporte diferente para SMB 2,1 e 3,0 e você deve verificar a tabela [aqui](../../../storage/files/storage-how-to-use-files-linux.md) para garantir que seu aplicativo dê suporte a SMB 3,0. 

Como o arquivos do Azure foi projetado para ser um serviço de compartilhamento de arquivos de vários usuários, há certas características que você deve ajustar para torná-lo mais adequado como uma mídia de armazenamento de backup. É recomendável desativar o cache e definir as IDs de usuário e grupo para arquivos criados.

## <a name="azure-netapp-files"></a>Azure NetApp Files

O serviço de [Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-solution-architectures.md) é uma solução de armazenamento completa para bancos de dados Oracle em VMs do Azure. Criado em um armazenamento de arquivos medido de alto desempenho e de classe empresarial, ele dá suporte a qualquer tipo de carga de trabalho e está altamente disponível por padrão. Junto com o driver dNFS (Oracle Direct NFS), o Azure NetApp Files fornece uma camada de armazenamento altamente otimizada para o Oracle Database.

O Azure NetApp Files fornece cópias de instantâneos baseadas em armazenamento eficientes no sistema de armazenamento subjacente que usa um redirecionamento no mecanismo de gravação (linha). Embora as cópias de instantâneo sejam extremamente rápidas de serem executadas e restauradas, elas servem apenas como uma primeira linha de defesa, que pode considerar a grande maioria das operações de restauração necessárias de qualquer determinada organização, que geralmente é a recuperação de erro humano. No entanto, as cópias de instantâneo não são um backup completo. Para abranger todos os requisitos de backup e restauração, as réplicas de instantâneos externas e/ou outras cópias de backup devem ser criadas em uma geografia remota para proteger contra interrupção regional. Para saber mais sobre como usar os arquivos do NetApp para bancos de dados Oracle no Azure, leia este [relatório](https://www.netapp.com/pdf.html?item=/media/17105-tr4780pdf.pdf).

## <a name="azure-backup-service"></a>Serviço de Backup do Azure

[O backup do Azure](../../../backup/backup-overview.md) é um PaaS totalmente gerenciado que fornece soluções simples, seguras e econômicas para fazer backup de seus dados e recuperá-los da nuvem Microsoft Azure. O backup do Azure pode fazer backup e restaurar clientes locais, VMS do Azure, compartilhamentos de arquivos do Azure, bem como SQL Server, Oracle, MySQL, PostreSQL e SAP HANA bancos de dados em VMs do Azure. 

O Backup do Azure fornece backups independentes e isolados para proteger contra a destruição acidental de dados originais. Os backups são armazenados em um [cofre dos serviços de recuperação](../../../backup/backup-azure-recovery-services-vault-overview.md) com gerenciamento interno de pontos de recuperação. A configuração e a escalabilidade são simples, os backups são otimizados e você pode fazer uma restauração com facilidade, conforme necessário. Ele usa a potência subjacente e a escala ilimitada da nuvem do Azure para fornecer alta disponibilidade sem sobrecarga de manutenção ou monitoramento. O backup do Azure não limita a quantidade de dados de entrada ou saída que você transfere, ou cobra pelos dados transferidos, e os dados são protegidos em trânsito e em repouso. 

Para garantir a durabilidade, o backup do Azure oferece vários tipos de replicação para manter os dados de backup altamente disponíveis.

- O [LRS (](../../../storage/common/storage-redundancy.md#locally-redundant-storage) armazenamento com redundância local) Replica seus dados três vezes (ele cria três cópias de seus dados) em uma unidade de escala de armazenamento em um Data Center.
- O armazenamento com redundância geográfica [(GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) é a opção de replicação padrão e recomendada. O GRS replica seus dados para uma região secundária (a centenas de quilômetros da região primária dos dados de origem).

Um cofre criado com redundância GRS inclui a opção de configurar o recurso de [restauração entre regiões](../../../backup/backup-create-rs-vault.md#set-storage-redundancy) , que permite restaurar dados em uma região emparelhada do Azure secundária.

O serviço de backup do Azure fornece uma [estrutura](../../../backup/backup-azure-linux-app-consistent.md) para obter a consistência do aplicativo durante backups de VMs do Windows e do Linux para vários aplicativos, como Oracle, MySQL, Mongo DB, SAP Hana e PostgreSQL, chamados de instantâneos consistentes de aplicativos. Isso envolve invocar um pré-script (para desativar os aplicativos) antes de tirar um instantâneo dos discos e chamar o post-script (comandos para descongelar os aplicativos) depois que o instantâneo for concluído, para retornar os aplicativos para o modo normal. Embora os pré-scripts de exemplo e os pós-scripts sejam fornecidos no GitHub, a criação e a manutenção desses scripts são de sua responsabilidade. No caso do Oracle, o banco de dados deve estar no modo de ARCHIVELOG para permitir backups online e os comandos de backup de início e término do banco de dados apropriados são executados nos scripts anteriores e posteriores, que você deve criar e manter. 

O backup do Azure agora está fornecendo uma [estrutura de pré e pós-script aprimorada](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts), atualmente em visualização, em que o serviço de backup do Azure fornecerá pré-scripts e pós-scripts empacotados para os aplicativos selecionados. Os usuários do backup do Azure precisam apenas nomear o aplicativo e, em seguida, o backup da VM do Azure invocará automaticamente os pré-scripts e pós-scripts relevantes. Os pré-scripts e pós-scripts empacotados serão mantidos pela equipe de backup do Azure para que os usuários possam estar garantidos do suporte, da propriedade e da validade desses scripts. Atualmente, os aplicativos com suporte para a estrutura avançada são Oracle e MySQL, com mais tipos de aplicativos esperados no futuro. O instantâneo será uma cópia completa do armazenamento e não um instantâneo de gravação incremental ou cópia, portanto, é um meio efetivo para restaurar seu banco de dados do.

## <a name="next-steps"></a>Próximas etapas

- [Criar Oracle Database início rápido](oracle-database-quick-create.md)
- [Fazer backup de Oracle Database em arquivos do Azure](oracle-database-backup-azure-storage.md)
- [Fazer backup Oracle Database usando o serviço de backup do Azure](oracle-database-backup-azure-backup.md)


