---
title: Backup do Azure do SAP HANA no nível do arquivo | Microsoft Docs
description: Há duas possibilidades principais de backup para SAP HANA em máquinas virtuais do Azure, e este artigo aborda o Backup do Azure do SAP HANA no nível do arquivo
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: 70b0f8178a94735a6ef37a225044984508cc2233
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617129"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>Backup do Azure do SAP HANA no nível do arquivo

## <a name="introduction"></a>Introdução

Este artigo é um artigo relacionado ao [Guia de backup para SAP Hana em máquinas virtuais do Azure](./sap-hana-backup-guide.md), que fornece uma visão geral e informações sobre como começar e mais detalhes sobre o serviço de backup do Azure e instantâneos de armazenamento. 

Diferentes tipos de VM no Azure permitem que um número diferente de VHDs sejam anexados. Os detalhes exatos estão documentados em [Tamanhos de máquinas virtuais Linux no Azure](../../sizes.md). Para os testes mencionados nesta documentação, usamos uma VM do Azure GS5, que permite discos de dados anexados a 64. Para sistemas SAP HANA maiores, um número significativo de discos já pode estar ocupado com arquivos de log e de dados, possivelmente em combinação com a distribuição de software para obter a taxa de transferência de E/S de disco ideal. Para obter mais detalhes sobre as configurações de disco sugeridas para implantações de SAP HANA em VMs do Azure, leia o artigo [SAP Hana configurações de armazenamento de máquina virtual do Azure](./hana-vm-operations-storage.md). As sugestões também incluem recomendações de espaço em disco para backups locais.

O modo padrão de gerenciamento de backup/restauração no nível do arquivo é com um backup baseado em arquivo via declarações do SAP HANA Studio ou do SAP HANA SQL. Para obter mais informações, leia o artigo [SAP Hana referência de exibições do SQL e do sistema](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.05/en-US/3859e48180bb4cf8a207e15cf25a7e57.html).

![Esta figura mostra a caixa de diálogo do item de menu de backup no SAP HANA Studio](media/sap-hana-backup-file-level/backup-menue-dialog.png)

Esta figura mostra a caixa de diálogo do item de menu de backup no SAP HANA Studio. Ao escolher o tipo de &quot;arquivo,&quot;, é preciso especificar um caminho no sistema de arquivos no qual o SAP HANA gravará os arquivos de backup. A restauração funciona da mesma maneira.

Embora essa opção pareça simples e direta, há algumas considerações. Uma VM do Azure tem uma limitação do número de discos de dados que podem ser anexados. Talvez não haja capacidade para armazenar arquivos de backup do SAP HANA nos sistemas de arquivos da VM, dependendo dos requisitos de tamanho do banco do dedos e de taxa de transferência do disco, que podem envolver a distribuição de software entre vários discos de dados. Posteriormente, este artigo fornece várias opções para mover esses arquivos de backup e gerenciar as restrições de tamanho de arquivo e desempenho ao lidar com terabytes de dados.

Outra opção, que oferece mais liberdade com relação à capacidade total, é o Armazenamento de Blobs do Azure. Embora um único blob também esteja restrito a 1 TB, a capacidade total de um único contêiner de blob é atualmente de 500 TB. Além disso, ele oferece aos clientes a opção de selecionar o chamado armazenamento de blobs &quot;estático&quot;, que tem um bom custo-benefício. Consulte [armazenamento de BLOBs do Azure: camadas de acesso quentes, frias e de arquivo](../../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal) para obter detalhes sobre o armazenamento de BLOBs frio.

Para obter mais segurança, use uma conta de armazenamento com replicação geográfica para armazenar os backups do SAP HANA. Consulte [redundância de armazenamento do Azure](../../../storage/common/storage-redundancy.md) para obter detalhes sobre a redundância de armazenamento e a replicação de armazenamento.

É possível colocar VHDs dedicados para backups do SAP HANA em uma conta de armazenamento de backup dedicada com replicação geográfica. Ou é possível copiar os VHDs que armazenam os backups do SAP HANA em uma conta de armazenamento com replicação geográfica ou para uma conta de armazenamento em uma região diferente.

## <a name="azure-blobxfer-utility-details"></a>Detalhes do utilitário blobxfer do Azure

Para armazenar diretórios e arquivos no armazenamento do Azure, é possível usar a CLI ou o PowerShell, ou desenvolver uma ferramenta usando um dos [SDKs do Azure](https://azure.microsoft.com/downloads/). Também há um utilitário pronto para uso, AzCopy, para copiar dados para o armazenamento do Azure. (consulte [transferir dados com o utilitário AzCopy Command-Line](../../../storage/common/storage-use-azcopy-v10.md)).

Portanto, o blobxfer foi usado para copiar arquivos de backup do SAP HANA. Ele é um software livre, usado por muitos clientes em ambientes de produção e disponível no [GitHub](https://github.com/Azure/blobxfer). Essa ferramenta permite a cópia dos dados diretamente no Armazenamento de Blobs do Azure ou no compartilhamento de arquivos do Azure. Ele também oferece uma variedade de recursos úteis, como o hash MD5, ou paralelismo automático ao copiar um diretório com vários arquivos.

## <a name="sap-hana-backup-performance"></a>Desempenho de backup do SAP HANA
Neste capítulo, são discutidas considerações sobre o desempenho. Os números obtidos podem não representar o estado mais recente, já que há um desenvolvimento estável para obter uma melhor taxa de transferência para o armazenamento do Azure. Como resultado, você deve executar testes individuais para sua configuração e a região do Azure.

![Essa captura de tela é do console de backup do SAP HANA no SAP HANA Studio](media/sap-hana-backup-file-level/backup-console-hana-studio.png)

Esta captura de tela mostra o console de backup SAP HANA do SAP HANA Studio. Demorou cerca de 42 minutos para executar um backup de 230 GB em um único disco de armazenamento HDD Standard do Azure conectado à VM do HANA usando o sistema de arquivos XFS em um disco.

![Esta captura de tela é do YaST na VM de teste do SAP HANA](media/sap-hana-backup-file-level/one-backup-disk.png)

Esta captura de tela é do YaST na VM de teste do SAP HANA. Você pode ver o disco único de 1 TB para o backup SAP HANA. Demorou aproximadamente 42 minutos para fazer backup de 230 GB. Além disso, cinco discos de 200 GB foram anexados e o RAID de software md0 foi criado, com distribuição nesses cinco discos de dados do Azure.

![Repetição do mesmo backup no RAID de software com distribuição nos cinco discos de dados de armazenamento padrão do Azure anexados](media/sap-hana-backup-file-level/five-backup-disks.png)

A repetição do mesmo backup no RAID de software com distribuição nos cinco discos de dados de armazenamento padrão do Azure anexado reduziu o tempo de backup de 42 minutos para 10 minutos. Os discos foram anexados sem armazenamento em cache para a VM. Este exercício demonstra a importância da taxa de transferência de gravação de disco para atingir um bom tempo de backup. Você pode mudar para o armazenamento de SSD Standard do Azure ou o armazenamento Premium do Azure para acelerar ainda mais o processo de desempenho ideal. Em geral, o armazenamento HDD standard do Azure não é recomendado e foi usado apenas para fins de demonstração. A recomendação é usar um mínimo de armazenamento de SSD Standard do Azure ou armazenamento Premium do Azure para sistemas de produção.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>Copiar arquivos de backup do SAP HANA para o Armazenamento de Blobs do Azure
Os números de desempenho, os números de duração do backup e os números de duração da cópia mencionados podem não representar o estado mais recente da tecnologia do Azure. A Microsoft está aprimorando o armazenamento do Azure de modo contínuo para fornecer mais taxa de transferência e latências menores. Portanto, os números são apenas para fins de demonstração. Você precisa testar sua necessidade individual na região do Azure de sua escolha para poder avaliar com o método é o melhor para você.

Outra opção para armazenar rapidamente arquivos de backup do SAP HANA é o Armazenamento de Blobs do Azure. Um único contêiner de blob tem um limite de cerca de 500 TB, o suficiente para sistemas SAP HANA, usando os tipos de VM M32ts, M32ls, M64ls e GS5 do Azure, para manter backups de SAP HANA suficientes. Os clientes têm a opção &quot; de &quot; &quot; armazenamento de BLOBs quente e frio &quot; (consulte [armazenamento de BLOBs do Azure: camadas de acesso quentes, frias e de arquivo](../../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal)).

Com a ferramenta blobxfer, é fácil copiar os arquivos de backup do SAP HANA diretamente no Armazenamento de Blobs do Azure.

![Aqui é possível ver os arquivos de um backup completo de arquivos do SAP HANA](media/sap-hana-backup-file-level/list-of-backups.png)

Você pode ver os arquivos de um backup de arquivo SAP HANA completo. Dos quatro arquivos, o maior tem um tamanho de aproximadamente 230 GB.

![Demorou aproximadamente 3000 segundos para copiar os 230 GB para um contêiner de blobs da conta de armazenamento standard do Azure](media/sap-hana-backup-file-level/copy-duration-blobxfer.png)

Sem usar um hash md5 no teste inicial, demorou aproximadamente 3000 segundos para copiar os 230 GB para um contêiner de blobs da conta de armazenamento standard do Azure.

O console do backup do HANA Studio permite restringir o tamanho máximo dos arquivos de backup do HANA. No ambiente de exemplo, ele melhorou o desempenho com vários arquivos de backup menores, em vez de um grande arquivo de 230 GB.

Definir o limite de tamanho do arquivo de backup no lado do HANA não&#39;não melhorar o tempo de backup, pois os arquivos são gravados em sequência. O limite de tamanho do arquivo foi definido como 60 GB, portanto o backup criou quatro arquivos de dados grandes, em vez de um único arquivo de 230 GB. Usar vários arquivos de backup pode se tornar uma necessidade de fazer backup de bancos de dados do HANA se seus destinos de backup tiverem limitações em tamanhos de arquivo de tamanhos de BLOB.

![Para testar o paralelismo da ferramenta blobxfer, o tamanho máximo dos backups do HANA foi definido como 15 GB](media/sap-hana-backup-file-level/parallel-copy-multiple-backup-files.png)

Para testar o paralelismo da ferramenta blobxfer, o tamanho máximo dos backups do HANA foi definido como 15 GB, o que resultou em19 arquivos de backup. Essa configuração reduziu o tempo usado pelo blobxfer para copiar os 230 GB para o Armazenamento de Blobs do Azure de 3000 segundos até 875 segundos.

Como você está explorando a cópia de backups executados em discos locais para outros locais, como o armazenamento de BLOBs do Azure, tenha em mente que a largura de banda usada por um processo de cópia paralela eventual é a conta da cota de rede ou de armazenamento do seu tipo de VM individual. Como resultado, você precisa equilibrar a duração da cópia em relação à largura de banda da rede e do armazenamento, a carga de trabalho normal em execução na VM está exigindo. 


## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>Copiar arquivos de backup do SAP HANA para o compartilhamento NFS

Microsoft Azure oferecem compartilhamentos NFS nativos por meio de [Azure NetApp files](https://azure.microsoft.com/services/netapp/). Você pode criar diferentes volumes de dezenas de TBs em capacidade para armazenar e gerenciar backups. Você também pode recapturar esses volumes com base na tecnologia da NetApp. O Azure NetApp Files (seja) é oferecido em três níveis de serviço diferentes que fornecem uma taxa de transferência de armazenamento diferente. Para obter mais detalhes, leia o artigo [níveis de serviço para Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). Você pode criar e montar um volume do NFS do seja conforme descrito no guia de [início rápido do artigo: configurar Azure NetApp Files e criar um volume do NFS](../../../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md?tabs=azure-portal).

Além de usar volumes de NFS nativos do Azure por meio do seja, há várias possibilidades de criar implantações próprias que fornecem compartilhamentos NFS no Azure. Todos têm a desvantagem de que você precisa para implantar e gerenciar essas soluções por conta própria. Algumas dessas possibilidades estão documentadas nestes artigos:

- [Alta disponibilidade do NFS em VMs do Azure no SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md)
- [GlusterFS em VMs do Azure no Red Hat Enterprise Linux para SAP NetWeaver](./high-availability-guide-rhel-glusterfs.md)

Os compartilhamentos NFS criados por meio dos meios descritos acima podem ser usados para executar diretamente os backups do HANA em relação aos backups que foram executados em discos locais para esses compartilhamentos NFS.

> [!NOTE]
> O SAP HANA dá suporte a NFS v3 e NFS v4. x. Qualquer outro formato como SMB com o sistema de arquivos CIFS não tem suporte para gravar backups do HANA no. Consulte também a [Observação de suporte SAP #1820529](https://launchpad.support.sap.com/#/notes/1820529)

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>Copiar arquivos de backup do SAP HANA para os Arquivos do Azure

É possível montar um compartilhamento de Arquivos do Azure dentro de uma VM do Linux do Azure. O artigo [como usar o armazenamento de arquivos do Azure com o Linux](../../../storage/files/storage-how-to-use-files-linux.md) fornece detalhes sobre como executar a configuração. Para limitações do em arquivos do Azure ou arquivos premium do Azure, leia o artigo [metas de desempenho e escalabilidade dos arquivos do Azure](../../../storage/files/storage-files-scale-targets.md).

> [!NOTE]
> Não há suporte para SMB com sistema de arquivos CIFS em SAP HANA para gravar backups do HANA. Consulte também [#1820529 de observação de suporte SAP](https://launchpad.support.sap.com/#/notes/1820529). Como resultado, você só pode usar essa solução como destino final de um backup de banco de dados do HANA que foi conduzido diretamente em discos anexados locais
> 

Em um teste realizado nos arquivos do Azure, não nos arquivos premium do Azure, ele levou cerca de 929 segundos para copiar 19 arquivos de backup com um volume geral de 230 GB. Esperamos que o tempo usando os arquivos premium do Azure seja melhor. No entanto, você precisa ter em mente que precisa equilibrar os interesses de uma cópia rápida com os requisitos que sua carga de trabalho tem na largura de banda da rede. Como cada tipo de VM do Azure impõe a cota de largura de banda de rede, você precisa permanecer dentro do intervalo dessa cota com sua carga de trabalho mais a cópia dos arquivos de backup.

![Esta figura mostra que demorou aproximadamente 929 segundos para copiar os 19 arquivos de backup do SAP HANA](media/sap-hana-backup-file-level/parallel-copy-to-azure-files.png)


Armazenar SAP HANA arquivos de backup em arquivos do Azure pode ser uma opção interessante. Especialmente com latência e taxa de transferência aprimoradas dos arquivos premium do Azure.

## <a name="next-steps"></a>Próximas etapas
* [Guia de backup do SAP HANA nas Máquinas Virtuais do Azure](sap-hana-backup-guide.md) fornece uma visão geral e informações sobre como começar.
* Para saber como estabelecer a alta disponibilidade e o plano de recuperação de desastres do SAP HANA no Azure (instâncias grandes), confira [Alta disponibilidade e recuperação de desastres do SAP HANA (instâncias grandes) no Azure](hana-overview-high-availability-disaster-recovery.md).
