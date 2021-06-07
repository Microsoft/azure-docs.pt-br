---
title: Estratégias de backup do Oracle Database em Máquinas Virtuais do Azure
description: Opções para fazer backup de Oracle Databases em um ambiente de VM do Linux do Azure.
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: 8a1eb1c21663e0294cd384daa0ba644adf78007a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101673215"
---
# <a name="oracle-database-in-azure-linux-vm-backup-strategies"></a>Estratégias de backup do Oracle Database em uma VM do Linux do Azure

Os backups de banco de dados protegem o banco de dados contra a perda de dados devido à falha no componente de armazenamento e no data center. Eles também podem ser um meio de recuperação de erro humano e um modo de clonar um banco de dados para fins de desenvolvimento ou teste. 

No Azure, como todo o armazenamento é altamente redundante e a perda de um ou mais discos não causará uma interrupção do banco de dados, os backups são usados com mais frequência para proteção contra erros humanos, clonagem de operações ou preservação dos dados para fins regulatórios. Eles também são um meio de se proteger contra interrupções regionais quando uma tecnologia de recuperação de desastres como o DataGuard não está em uso. Nesse caso, os backups precisam ser armazenados em diferentes regiões do Azure usando a replicação com redundância geográfica, para que eles fiquem disponíveis fora da região do banco de dados primário.

## <a name="azure-storage"></a>Armazenamento do Azure 

Os [serviços de Armazenamento do Azure](../../../storage/common/storage-introduction.md) são uma solução de armazenamento em nuvem da Microsoft para cenários de armazenamento de dados modernos. O armazenamento do Azure oferece vários serviços que podem ser usados para montar o armazenamento externo da VM do Linux do Azure, que é adequado como mídia de backup para bancos de dados Oracle. Uma ferramenta de backup como o Oracle RMAN é necessária para iniciar uma operação de backup ou de restauração e copiar o backup de/para o serviço de Armazenamento do Azure.
 
Os serviços de Armazenamento do Azure oferecem os seguintes benefícios:

-  Durável e altamente disponível. A redundância garante a segurança dos seus dados no caso de falhas de hardware transitórias. Todo o armazenamento tem, por padrão, espelhamento triplo. Você também pode optar por replicar os dados em data centers ou regiões geográficas para obter mais proteção contra catástrofes locais ou desastres naturais. Os dados replicados dessa maneira permanecem altamente disponíveis no caso de uma interrupção inesperada.

-  Segura. Todos os dados gravados em uma conta de armazenamento do Azure são criptografados pelo serviço. O Armazenamento do Azure oferece um controle refinado sobre quem possui acesso aos seus dados.

-  Escalonável. O Armazenamento do Azure foi concebido para ser altamente escalonável e atender às necessidades de desempenho e armazenamento de dados dos aplicativos atuais.

-  Gerenciado. o Azure cuida da manutenção de hardware, das atualizações e dos problemas críticos para você.

-  Acessível. Os dados no Armazenamento do Azure são acessíveis de qualquer lugar no mundo por HTTP ou HTTPS. A Microsoft fornece bibliotecas de clientes para o Armazenamento do Azure em várias linguagens, incluindo .NET, Java, Node.js, Python, PHP, Ruby, Go, entre outras, bem como uma API REST bem desenvolvida. O Armazenamento do Azure oferece suporte para scripts no Azure PowerShell ou na CLI do Azure. E o Portal do Azure e o Gerenciador de Armazenamento do Azure oferecem soluções visualmente fáceis para o trabalho com os seus dados.

A plataforma de Armazenamento do Azure inclui os seguintes serviços de dados que podem ser usados como mídia de backup para o Oracle Database:

-  Blobs do Azure: um repositório de objetos altamente escalonável para texto e dados binários. Ela também inclui suporte para análise de Big Data por meio do Data Lake Storage Gen2.

-  Arquivos do Azure: compartilhamentos de arquivos gerenciados para implantações locais e em nuvem.

-  Azure Disks: volumes de armazenamento em nível de bloco para VMs do Azure.

### <a name="cross-regional-storage-mounting"></a>Montagem de armazenamento entre regiões

A capacidade de acessar o armazenamento de backup em diversas regiões é um aspecto importante do BCDR e é útil para clonar bancos de dados de backups em diferentes regiões geográficas. O armazenamento em nuvem do Azure fornece cinco níveis de [redundância](../../../storage/common/storage-redundancy.md)
- O [LRS](../../../storage/common/storage-redundancy.md#locally-redundant-storage) (armazenamento com redundância local), no qual os seus dados são replicados três vezes em uma localização física na região primária.  
- O [ZRS](../../../storage/common/storage-redundancy.md#zone-redundant-storage) (armazenamento com redundância de zona), no qual os dados são protegidos por LRS na região primária e replicados de modo síncrono em três AZ (zonas de disponibilidade) na região primária. Cada AZ também é protegida por LRS. 
- O [GRS](../../../storage/common/storage-redundancy.md#geo-redundant-storage) (armazenamento com redundância geográfica), no qual os seus dados são protegidos por LRS na região primária e replicados assincronamente para uma região secundária na qual eles também são protegidos por LRS.
- O [GZRS](../../../storage/common/storage-redundancy.md#geo-redundant-storage) (armazenamento com redundância de zona geográfica) copia os seus dados de modo síncrono em três zonas de disponibilidade do Azure na região primária usando o ZRS. Em seguida, ele copia os dados de forma assíncrona para um único local físico na região secundária. Em todas as localizações, os dados também são protegidos por LRS. 
- O [RA-GRS](../../../storage/common/storage-redundancy.md#geo-redundant-storage) (armazenamento com redundância geográfica com acesso de leitura) e o [RA-GZRS](../../../storage/common/storage-redundancy.md#geo-redundant-storage) (armazenamento com redundância de zona geográfica com acesso de leitura) sempre permitem o acesso somente leitura aos dados replicados para a região secundária. 

#### <a name="blob-storage-and-file-storage"></a>Armazenamento de blobs e armazenamento de arquivos

Ao usar os Arquivos do Azure com os protocolos SMB ou NFS 4.1 (Versão Prévia) para montar o armazenamento de backup, observe que os Arquivos do Azure não dão suporte ao RA-GRS (armazenamento com redundância geográfica com acesso de leitura) e ao RA-GZRS (armazenamento com redundância de zona geográfica com acesso de leitura). 

Além disso, se o requisito de armazenamento de backup for maior que 5 TiB, os Arquivos do Azure exigirão que o [recurso de Compartilhamentos de Arquivos Grandes](../../../storage/files/storage-files-planning.md) esteja habilitado. Isso não dá suporte à redundância GRS nem GZRS, apenas à redundância LRS. 

O Blob do Azure montado usando o protocolo NFS 3.0 (Versão Prévia) atualmente dá suporte apenas à redundância LRS e ZRS.  

O Blob do Azure configurado com qualquer opção de redundância pode ser montado usando o Blobfuse.

#### <a name="recovery-services-vault"></a>Cofre dos Serviços de Recuperação 

Um cofre dos Serviços de Recuperação é uma entidade de gerenciamento que armazena pontos de recuperação criados ao longo do tempo e que fornece uma interface para executar operações relacionadas a backup. Isso inclui fazer backups sob demanda, executar restaurações e criar políticas de backup.
O Backup do Azure administra automaticamente o armazenamento para o cofre. Você precisa especificar como esse armazenamento é replicado no momento da criação. Ele não poderá ser alterado depois que os itens forem protegidos no cofre. Para redundância regional, escolha a configuração com redundância geográfica.

Se você pretende fazer a restauração para uma [região emparelhada do Azure](../../../best-practices-availability-paired-regions.md) secundária habilite o recurso de [Restauração entre Regiões](../../../backup/backup-create-rs-vault.md). Quando a restauração entre regiões estiver habilitada, o armazenamento de backup será movido do GRS para o RA-GRS (armazenamento com redundância geográfica com acesso de leitura). 

### <a name="azure-blob-storage"></a>Armazenamento do Blobs do Azure

O Armazenamento de Blobs do Azure é um serviço de armazenamento baseado em nuvem altamente econômico, durável e seguro usado para armazenar grandes quantidades de dados não estruturados e é uma mídia ideal para usar nos backups do Oracle Database. O Armazenamento de Blobs do Azure pode ser montado na VM do Linux do Azure usando o protocolo NFS v3.0 ou o Blobfuse (Linux FUSE).

#### <a name="azure-blob-blobfuse"></a>Blobfuse do Blob do Azure

O [Blobfuse](../../../storage/blobs/storage-how-to-mount-container-linux.md) é um projeto de software livre desenvolvido para fornecer um sistema de arquivos virtual com suporte no Armazenamento de Blobs do Azure. Ele usa a biblioteca de software livre do libfuse para se comunicar com o módulo de kernel do Linux FUSE e implementa as operações do sistema de arquivos usando as APIs REST do Azure Storage Blob. O Blobfuse está disponível atualmente em distribuições Ubuntu e Centos/RedHat. Ele também está disponível no Kubernetes usando o [driver CSI](https://github.com/kubernetes-sigs/blob-csi-driver). 

Embora esteja onipresente em todas as regiões do Azure e funcione com todos os tipos de conta de armazenamento, incluindo a v1/v2 de uso geral e o Azure Data Lake Storage Gen2, o desempenho oferecido pelo Blobfuse mostrou ser menos eficaz do que protocolos alternativos, como SMB ou NFS. Para adequação à mídia de backup do banco de dados, recomendamos o uso de protocolos SMB ou [NFS](../../../storage/blobs/storage-how-to-mount-container-linux.md) para montar o Armazenamento de Blobs do Azure. 

#### <a name="azure-blob-nfs-v30-preview"></a>NFS v3.0 do Blob do Azure (Versão Prévia)

O Suporte do Azure para o protocolo NFS (Network File System) v3.0 está agora em versão prévia. O suporte ao [NFS](../../../storage/blobs/network-file-system-protocol-support.md) permite que os clientes Windows e Linux montem um contêiner de armazenamento de Blobs em uma VM do Azure. 

A versão prévia pública do NFS 3.0 dá suporte a contas de armazenamento GPV2 com desempenho de camada standard nas seguintes regiões: 
- Leste da Austrália
- Coreia Central
- Centro-Sul dos EUA. 

Para garantir a segurança de rede, a conta de armazenamento usada para a montagem de NFS precisa estar contida em uma VNet. A segurança do Azure AD (Active Directory) e as ACLs (listas de controle de acesso) ainda não têm suporte em contas com o suporte do protocolo NFS 3.0 habilitado.

### <a name="azure-files"></a>Arquivos do Azure

Os [Arquivos do Azure](../../../storage/files/storage-files-introduction.md) são um sistema de arquivos distribuído totalmente gerenciado e baseado em nuvem que pode ser montado em clientes Windows, Linux ou macOS baseados em nuvem ou locais.

Os Arquivos do Azure oferecem compartilhamentos de arquivos totalmente gerenciados multiplataforma na nuvem que são acessíveis por meio do protocolo SMB ou do protocolo NFS (Network File System) (versão prévia). Atualmente, os Arquivos do Azure não dão suporte ao acesso de vários protocolos, portanto, um compartilhamento pode ser apenas um compartilhamento NFS ou um compartilhamento SMB. É recomendável determinar qual protocolo melhor atende às suas necessidades antes de criar compartilhamentos de arquivos do Azure.

Os compartilhamentos de arquivos do Azure também podem ser protegidos por meio do Backup do Azure para o cofre dos Serviços de Recuperação, fornecendo uma camada adicional de proteção para os backups do Oracle RMAN.

#### <a name="azure-files-nfs-v41-preview"></a>NFS v4.1 dos Arquivos do Azure (versão prévia)

Os compartilhamentos de arquivos do Azure podem ser montados em distribuições do Linux usando o protocolo NFS (Network File System) v4.1. Durante a Versão Prévia, há uma série de limitações para os recursos com suporte, que estão documentados [aqui](../../../storage/files/storage-files-how-to-mount-nfs-shares.md). 

Durante a versão prévia, o NFS v4.1 dos Arquivos do Azure também é restrito às seguintes [regiões](../../../storage/files/storage-files-how-to-mount-nfs-shares.md):
- Leste dos EUA (LRS e ZRS)
- Leste dos EUA 2
- Oeste dos EUA 2
- Europa Ocidental
- Sudeste Asiático
- Sul do Reino Unido
- Leste da Austrália

#### <a name="azure-files-smb-30"></a>SMB 3.0 dos Arquivos do Azure

Os compartilhamentos de arquivos do Azure podem ser montados em distribuições do Linux usando o cliente de kernel SMB. O protocolo CIFS (Common Internet File System), disponível em distribuições do Linux, é um dialeto do SMB. Ao montar o SMB dos Arquivos do Azure em VMs Linux, ele é montado como um sistema de arquivos do tipo CIFS e o pacote do CIFS precisa ser instalado. 

O SMB dos Arquivos do Azure está em disponibilidade geral em todas as regiões do Azure e mostra as mesmas características de desempenho que os protocolos NFS v3.0 e v4.1. Portanto, ele é atualmente o método recomendado para fornecer mídia de armazenamento de backup para VMs do Linux do Azure.  

Há duas versões com suporte do SMB disponíveis, SMB 2.1 e SMB 3.0, sendo a última a versão recomendada, pois ela dá suporte à criptografia em trânsito. No entanto, diferentes versões de kernels do Linux têm suportes diferentes para SMB 2.1 e 3.0 e você deve verificar [esta](../../../storage/files/storage-how-to-use-files-linux.md) tabela para garantir que o seu aplicativo dê suporte ao SMB 3.0. 

Como os Arquivos do Azure foram projetados para ser um serviço de compartilhamento de arquivo de vários usuários, há certas características que você deve ajustar para torná-lo mais adequado para uso como uma mídia de armazenamento de backup. É recomendável desativar o cache e configurar as IDs de usuário e de grupo dos arquivos criados.

## <a name="azure-netapp-files"></a>Azure NetApp Files

O serviço [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-solution-architectures.md) é uma solução de armazenamento completa para Oracle Databases em VMs do Azure. Criado em um armazenamento de arquivos medido de alto desempenho e de classe corporativa, ele dá suporte a qualquer tipo de carga de trabalho e é altamente disponível por padrão. Junto com o driver dNFS (Oracle Direct NFS), o Azure NetApp Files fornece uma camada de armazenamento altamente otimizada para o Oracle Database.

O Azure NetApp Files fornece cópias de instantâneos baseadas em armazenamento eficientes no sistema de armazenamento subjacente que usa um mecanismo RoW (Redirecionamento na Gravação). Embora a coleta e restauração das cópias de instantâneos seja muito rápida, elas servem apenas como uma primeira linha de defesa, o que normalmente abrange a grande maioria das operações de restauração necessárias em qualquer organização, que geralmente é a recuperação de erro humano. No entanto, as cópias de instantâneo não são um backup completo. Para abranger todos os requisitos de backup e de restauração, as réplicas de instantâneos externas e/ou outras cópias de backup precisam ser criadas em uma geografia remota para terem proteção contra interrupção regional. Para saber mais sobre como usar o NetApp Files para Oracle Database no Azure, leia este [relatório](https://www.netapp.com/pdf.html?item=/media/17105-tr4780pdf.pdf).

## <a name="azure-backup-service"></a>Serviço de Backup do Azure

O [Backup do Azure](../../../backup/backup-overview.md) é um PaaS totalmente gerenciado que fornece soluções simples, seguras e econômicas para fazer backup dos seus dados e recuperá-los da nuvem do Microsoft Azure. O Backup do Azure pode fazer backup e restaurar clientes locais, VMs do Azure, compartilhamentos de Arquivos do Azure, bem como bancos de dados SQL Server, Oracle, MySQL, PostreSQL e SAP HANA em VMs do Azure. 

O Backup do Azure fornece backups independentes e isolados para proteger contra a destruição acidental de dados originais. Os backups são armazenados em um [cofre dos Serviços de Recuperação](../../../backup/backup-azure-recovery-services-vault-overview.md) com gerenciamento interno de pontos de recuperação. A configuração e a escalabilidade são simples, os backups são otimizados e você pode fazer uma restauração com facilidade, conforme necessário. Ele usa o poder subjacente e a escala ilimitada da nuvem do Azure para proporcionar alta disponibilidade sem sobrecarga de manutenção ou de monitoramento. O Backup do Azure não limita a quantidade de dados de entrada ou de saída transferidos, nem cobra pelos dados transferidos. Além disso, os dados são protegidos em trânsito e em repouso. 

Para garantir a durabilidade, o Backup do Azure oferece vários tipos de replicação para manter os dados de backup altamente disponíveis.

- O [LRS](../../../storage/common/storage-redundancy.md#locally-redundant-storage) (armazenamento com redundância local) replica os seus dados três vezes (ele cria três cópias dos dados) em uma unidade de escala de armazenamento em um data center.
- O [GRS](../../../storage/common/storage-redundancy.md#geo-redundant-storage) (armazenamento com redundância geográfica) é a opção de replicação padrão e recomendada. O GRS replica seus dados para uma região secundária (a centenas de quilômetros da região primária dos dados de origem).

Um cofre criado com redundância GRS inclui a opção de configurar o recurso de [Restauração entre Regiões](../../../backup/backup-create-rs-vault.md#set-storage-redundancy), que permite restaurar dados em uma região emparelhada e secundária do Azure.

O serviço de Backup do Azure fornece uma [estrutura](../../../backup/backup-azure-linux-app-consistent.md) para obter a consistência do aplicativo durante backups de VMs do Windows e do Linux para vários aplicativos, como Oracle, MySQL, Mongo DB, SAP HANA e PostGreSQL, chamados de instantâneos consistentes com o aplicativo. Isso envolve invocar um pré-script (para fechar os aplicativos para novas sessões) antes de realizar um instantâneo dos discos e chamar o pós-script (comandos para descongelar os aplicativos) depois que o instantâneo for concluído, para retornar os aplicativos para o modo normal. Embora os pré-scripts e os pós-scripts de exemplo sejam fornecidos no GitHub, a criação e a manutenção desses scripts são de sua responsabilidade. No caso do Oracle, o banco de dados precisa estar no modo de archivelog para permitir backups online e os comandos de backup de início e término do banco de dados apropriados são executados nos scripts anteriores e posteriores, que você precisa criar e manter. 

O Backup do Azure agora está fornecendo uma [estrutura de pré e pós-script aprimorada](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts), atualmente em versão prévia, em que o serviço de Backup do Azure fornecerá pré-scripts e pós-scripts empacotados para os aplicativos selecionados. Os usuários do Backup do Azure precisam apenas nomear o aplicativo e o backup da VM do Azure invocará automaticamente os pré-scripts e pós-scripts relevantes. Os pré-scripts e pós-scripts empacotados serão mantidos pela equipe de Backup do Azure para que os usuários possam ter a garantia do suporte, da propriedade e da validade desses scripts. Atualmente, os aplicativos com suporte para a estrutura avançada são o Oracle e MySQL, com mais tipos de aplicativos esperados no futuro. O instantâneo será uma cópia completa do armazenamento e não um instantâneo de Gravação ou Cópia incremental, portanto, ele é um meio efetivo no qual restaurar o seu banco de dados.

## <a name="next-steps"></a>Próximas etapas

- [Guia de início rápido de como criar o Oracle Database](oracle-database-quick-create.md)
- [Fazer backup do Oracle Database em Arquivos do Azure](oracle-database-backup-azure-storage.md)
- [Fazer backup do Oracle Database usando o serviço de Backup do Azure](oracle-database-backup-azure-backup.md)


