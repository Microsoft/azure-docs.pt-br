---
title: Backup do Azure do SAP HANA no nível do arquivo | Microsoft Docs
description: Há duas possibilidades principais de backup para SAP HANA em máquinas virtuais do Azure, e este artigo aborda o Backup do Azure do SAP HANA no nível do arquivo
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: 02ee65020f72fb9c3262db82e035e628f780e2cf
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709989"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>Backup do Azure do SAP HANA no nível do arquivo

## <a name="introduction"></a>Introdução

Este artigo faz parte de uma série dividida em três partes com artigos relacionados ao backup do SAP HANA. [Guia de backup do SAP HANA em máquinas virtuais do Azure](./sap-hana-backup-guide.md) fornece uma visão geral e informações sobre como começar, e [Backup do SAP HANA com base em instantâneos de armazenamento](./sap-hana-backup-storage-snapshots.md) aborda a opção de backup baseado em instantâneo de armazenamento.

Diferentes tipos de VM no Azure permitem que um número diferente de VHDs sejam anexados. Os detalhes exatos estão documentados em [Tamanhos de máquinas virtuais Linux no Azure](../../linux/sizes.md). Para os testes mencionados nesta documentação, usamos uma VM GS5 do Azure que permite 64 discos de dados anexados. Para sistemas SAP HANA maiores, um número significativo de discos já pode estar ocupado com arquivos de log e de dados, possivelmente em combinação com a distribuição de software para obter a taxa de transferência de E/S de disco ideal. Para obter mais detalhes sobre as configurações de disco sugeridas para implantações do SAP HANA em VMs do Azure, leia o artigo [SAP HANA no guia de operações do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations). As sugestões também incluem recomendações de espaço em disco para backups locais.

No momento, não há integração de backup do SAP HANA disponível com o serviço de Backup do Azure. O modo padrão de gerenciamento de backup/restauração no nível do arquivo é com um backup baseado em arquivo via declarações do SAP HANA Studio ou do SAP HANA SQL. Confira [SAP HANA SQL e referência de exibições do sistema](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf) para saber mais.

![Esta figura mostra a caixa de diálogo do item de menu de backup no SAP HANA Studio](media/sap-hana-backup-file-level/image022.png)

Esta figura mostra a caixa de diálogo do item de menu de backup no SAP HANA Studio. Ao escolher o tipo de &quot;arquivo,&quot;, é preciso especificar um caminho no sistema de arquivos no qual o SAP HANA gravará os arquivos de backup. A restauração funciona da mesma maneira.

Embora essa opção pareça simples e direta, há algumas considerações. Conforme mencionado anteriormente, uma VM do Azure tem uma limitação no número de discos de dados que podem ser anexados. Talvez não haja capacidade para armazenar arquivos de backup do SAP HANA nos sistemas de arquivos da VM, dependendo dos requisitos de tamanho do banco do dedos e de taxa de transferência do disco, que podem envolver a distribuição de software entre vários discos de dados. Posteriormente, este artigo fornece várias opções para mover esses arquivos de backup e gerenciar as restrições de tamanho de arquivo e desempenho ao lidar com terabytes de dados.

Outra opção, que oferece mais liberdade com relação à capacidade total, é o Armazenamento de Blobs do Azure. Embora um único blob também esteja restrito a 1 TB, a capacidade total de um único contêiner de blob é atualmente de 500 TB. Além disso, ele oferece aos clientes a opção de selecionar o chamado armazenamento de blobs &quot;estático&quot;, que tem um bom custo-benefício. Consulte [o armazenamento de BLOBs do Azure: Camadas de armazenamento dinâmica e estática](../../../storage/blobs/storage-blob-storage-tiers.md) para obter detalhes sobre o armazenamento de BLOBs esporádico.

Para obter mais segurança, use uma conta de armazenamento com replicação geográfica para armazenar os backups do SAP HANA. Confira [Replicação de Armazenamento do Azure](../../../storage/common/storage-redundancy.md) para obter mais detalhes sobre a replicação da conta de armazenamento.

É possível colocar VHDs dedicados para backups do SAP HANA em uma conta de armazenamento de backup dedicada com replicação geográfica. Ou é possível copiar os VHDs que armazenam os backups do SAP HANA em uma conta de armazenamento com replicação geográfica ou para uma conta de armazenamento em uma região diferente.

## <a name="azure-backup-agent"></a>Agente de backup do Azure

Além de oferecer a opção de fazer backup de VMs completas, o backup do Azure também oferece o backup de arquivos e diretórios por meio do agente de backup, que deve ser instalado no sistema operacional convidado. Mas esse agente só tem suporte no Windows (confira [Fazer backup de um Windows Server ou de um cliente no Azure usando o modelo de implantação do Resource Manager](../../../backup/backup-configure-vault.md)).

Uma solução alternativa é copiar primeiro os arquivos de backup do SAP HANA para uma VM do Windows no Azure (por exemplo, por meio do compartilhamento do SAMBA) e, depois, usar o agente de backup do Azure a partir daí. Embora seja tecnicamente possível, isso acrescentaria complexidade e tornaria o processo de backup ou restauração mais lento devido a uma cópia entre a VM do Linux e do Windows. Não recomendamos seguir essa abordagem.

## <a name="azure-blobxfer-utility-details"></a>Detalhes do utilitário blobxfer do Azure

Para armazenar diretórios e arquivos no armazenamento do Azure, é possível usar a CLI ou o PowerShell, ou desenvolver uma ferramenta usando um dos [SDKs do Azure](https://azure.microsoft.com/downloads/). Também há um utilitário pronto para uso, o AzCopy, para copiar dados no armazenamento do Azure, mas ele é compatível apenas com o Windows (confira [Transferir dados com o utilitário de linha de comando AzCopy](../../../storage/common/storage-use-azcopy.md)).

Portanto, o blobxfer foi usado para copiar arquivos de backup do SAP HANA. Ele é um software livre, usado por muitos clientes em ambientes de produção e disponível no [GitHub](https://github.com/Azure/blobxfer). Essa ferramenta permite a cópia dos dados diretamente no Armazenamento de Blobs do Azure ou no compartilhamento de arquivos do Azure. Ele também oferece vários recursos úteis, como o hash md5 ou o paralelismo automático ao copiar um diretório com vários arquivos.

## <a name="sap-hana-backup-performance"></a>Desempenho de backup do SAP HANA

![Essa captura de tela é do console de backup do SAP HANA no SAP HANA Studio](media/sap-hana-backup-file-level/image023.png)

Essa captura de tela é do console de backup do SAP HANA no SAP HANA Studio. Demorou aproximadamente 42 minutos para fazer o backup de 230 GB em um único disco de armazenamento padrão do Azure anexado à VM HANA usando o sistema de arquivos XFS.

![Esta captura de tela é do YaST na VM de teste do SAP HANA](media/sap-hana-backup-file-level/image024.png)

Esta captura de tela é do YaST na VM de teste do SAP HANA. Conforme mencionado anteriormente, é possível ver o único disco de 1 TB para backup do SAP HANA. Demorou aproximadamente 42 minutos para fazer backup de 230 GB. Além disso, cinco discos de 200 GB foram anexados e o RAID de software md0 foi criado, com distribuição nesses cinco discos de dados do Azure.

![Repetição do mesmo backup no RAID de software com distribuição nos cinco discos de dados de armazenamento padrão do Azure anexados](media/sap-hana-backup-file-level/image025.png)

A repetição do mesmo backup no RAID de software com distribuição nos cinco discos de dados de armazenamento padrão do Azure anexado reduziu o tempo de backup de 42 minutos para 10 minutos. Os discos foram anexados sem armazenamento em cache para a VM. Portanto, fica óbvia a importância da taxa de transferência de gravação de disco para o tempo de backup. Assim, é possível mudar para o Armazenamento Premium do Azure para acelerar mais o processo de obter o desempenho ideal. Em geral, o Armazenamento Premium do Azure deve ser usado para sistemas de produção.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>Copiar arquivos de backup do SAP HANA para o Armazenamento de Blobs do Azure

Outra opção para armazenar rapidamente arquivos de backup do SAP HANA é o Armazenamento de Blobs do Azure. Um único contêiner de blob tem um limite de 500 TB, que é suficiente para alguns sistemas SAP HANA menores, usando os tipos de VM M32ts, M32ls, M64ls e GS5 do Azure, para manter backups suficientes do SAP HANA. Os clientes têm a opção entre &quot;hot&quot; e &quot;frio&quot; armazenamento de BLOBs (consulte [o armazenamento de BLOBs do Azure: Camadas de armazenamento dinâmica e estática](../../../storage/blobs/storage-blob-storage-tiers.md)).

Com a ferramenta blobxfer, é fácil copiar os arquivos de backup do SAP HANA diretamente no Armazenamento de Blobs do Azure.

![Aqui é possível ver os arquivos de um backup completo de arquivos do SAP HANA](media/sap-hana-backup-file-level/image026.png)

Aqui é possível ver os arquivos de um backup completo de arquivos do SAP HANA. Há quatro arquivos, e o maior tem aproximadamente 230 GB.

![Demorou aproximadamente 3000 segundos para copiar os 230 GB para um contêiner de blobs da conta de armazenamento standard do Azure](media/sap-hana-backup-file-level/image027.png)

Sem usar um hash md5 no teste inicial, demorou aproximadamente 3000 segundos para copiar os 230 GB para um contêiner de blobs da conta de armazenamento standard do Azure.

![Nesta captura de tela, é possível ver como isso aparece no Portal do Azure](media/sap-hana-backup-file-level/image028.png)

Nesta captura de tela, é possível ver como isso aparece no Portal do Azure. Um contêiner de blob denominado &quot;sap-hana-backups&quot; foi criado e inclui os quatro blobs, que representam os arquivos de backup do SAP HANA. Um deles tem um tamanho de aproximadamente 230 GB.

O console do backup do HANA Studio permite restringir o tamanho máximo dos arquivos de backup do HANA. No ambiente de exemplo, ele melhorou o desempenho tornando possível ter vários arquivos menores de backup, em vez de um arquivo grande de 230 GB.

![Definir o limite de tamanho do arquivo de backup no lado do HANA não melhora o tempo do backup](media/sap-hana-backup-file-level/image029.png)

Definir o limite de tamanho do arquivo de backup no lado do HANA não melhora o tempo de backup, pois os arquivos são gravados em sequência, conforme mostra esta figura. O limite de tamanho do arquivo foi definido como 60 GB, portanto o backup criou quatro arquivos de dados grandes, em vez de um único arquivo de 230 GB. O uso de vários arquivos de backup é uma necessidade para fazer backup de bancos de dados do HANA que aproveitam a memória de VMs do Azure maiores, como as M64s, M64ms, M128s e M128ms.

![Para testar o paralelismo da ferramenta blobxfer, o tamanho máximo dos backups do HANA foi definido como 15 GB](media/sap-hana-backup-file-level/image030.png)

Para testar o paralelismo da ferramenta blobxfer, o tamanho máximo dos backups do HANA foi definido como 15 GB, o que resultou em19 arquivos de backup. Essa configuração reduziu o tempo usado pelo blobxfer para copiar os 230 GB para o Armazenamento de Blobs do Azure de 3000 segundos até 875 segundos.

Esse resultado é possível devido ao limite de 60 MB/s para gravar em um blob do Azure. O paralelismo por meio de vários blobs resolve o gargalo, mas há uma desvantagem: aumentar o desempenho da ferramenta blobxfer para copiar todos esses arquivos de backup do HANA para o Armazenamento de Blobs do Azure coloca carga na VM do HANA e na rede. Operação do sistema HANA fica afetada.

## <a name="blob-copy-of-dedicated-azure-data-disks-in-backup-software-raid"></a>Cópia de blob de discos de dados do Azure dedicados no RAID do software de backup

Ao contrário do backup de disco de dados de VM manual, nessa abordagem a pessoa não faz o backup de todos os discos de dados em uma VM para salvar toda a instalação SAP, incluindo dados de HANA, os arquivos de log de HANA e os arquivos de configuração. Em vez disso, a ideia é ter um RAID de software dedicado com distribuição entre vários VHDs de dados do Azure a fim de armazenar um backup completo de arquivos do SAP HANA. Apenas esses discos são copiados, os quais têm o backup do SAP HANA. Eles poderiam ser mantidos facilmente em uma conta de armazenamento de backup dedicada do HANA, ou anexados a uma &quot;VM de gerenciamento de backup dedicada&quot; para realização de outros processamentos.

![Todos os VHDs envolvidos foram copiados usando o comado do PowerShell **start-azurestorageblobcopy**](media/sap-hana-backup-file-level/image031.png)

Após a conclusão do backup no RAID de software local, todos os VHDs envolvidos foram copiados usando o comando do PowerShell **start-azurestorageblobcopy** (confira [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy)). Como afeta somente o sistema de arquivos dedicado para manter os arquivos de backup, não há preocupações sobre a consistência do arquivo de dados ou de log do SAP HANA no disco. Um benefício desse comando é que ele trabalha enquanto a VM está online. Para ter certeza de que nenhum processo grava o conjunto de backup distribuído, desmonte-o antes da cópia de blob e monte-o novamente depois. Ou é possível usar uma forma apropriada de &quot;congelar&quot; o sistema de arquivos. Por exemplo, via xfs\_freeze para o sistema de arquivos XFS.

![Esta captura de tela mostra a lista de blobs no contêiner de vhds no Portal do Azure](media/sap-hana-backup-file-level/image032.png)

Esta captura de tela mostra a lista de blobs no contêiner de &quot;vhds&quot; no Portal do Azure. A captura de tela mostra os cinco VHDs que foram anexados à VM do servidor do SAP HANA VM para servir como o RAID de software para armazenar os arquivos de backup do HANA SAP. Ele também mostra as cinco cópias realizadas por meio do comando de cópia de blob.

![Para fins de teste, as cópias dos discos de RAID de software de backup do SAP HANA foram anexadas à VM do servidor de aplicativo](media/sap-hana-backup-file-level/image033.png)

Para fins de teste, as cópias dos discos de RAID de software de backup do SAP HANA foram anexadas à VM do servidor de aplicativo.

![A VM do servidor de aplicativo foi desligada para anexar as cópias de disco](media/sap-hana-backup-file-level/image034.png)

A VM do servidor de aplicativo foi desligada para anexar as cópias de disco. Depois de iniciar a VM, os discos e o RAID foram detectados corretamente (montados via UUID). Somente o ponto de montagem estava faltando, que foi criado por meio do particionador YaST. Depois disso, as cópias do arquivo de backup do SAP HANA tornaram-se visíveis no nível do sistema operacional.

## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>Copiar arquivos de backup do SAP HANA para o compartilhamento NFS

Para reduzir o possível impacto no sistema SAP HANA de uma perspectiva de espaço em disco ou do desempenho, é possível considerar o armazenamento dos arquivos de backup do SAP HANA em um compartilhamento NFS. Tecnicamente funciona, mas isso significa usar uma segunda VM do Azure como o host do compartilhamento NFS. Não deve ser uma VM pequena, devido à largura de banda de rede da VM. Faria sentido desligar essa &quot;VM de backup&quot; e ativá-la somente para executar o backup do SAP HANA. Gravar em um compartilhamento NFS sobrecarrega a rede e afeta o sistema do SAP HANA, mas simplesmente gerenciar os arquivos de backup posteriormente na &quot;VM de backup&quot; não influenciaria em nada o sistema do SAP HANA.

![Um compartilhamento NFS da outra VM do Azure foi montado para a VM do servidor SAP HANA](media/sap-hana-backup-file-level/image035.png)

Para verificar o caso de uso de NFS, um compartilhamento NFS da outra VM do Azure foi montado para a VM do servidor SAP HANA. Não havia um ajuste de NFS especial aplicado.

![Demorou 1 hora e 46 minutos para realizar o backup diretamente](media/sap-hana-backup-file-level/image036.png)

O compartilhamento NFS era um conjunto de distribuição rápida, como aquele no servidor do SAP HANA. No entanto, demorou 1 hora e 46 minutos para realizar o backup diretamente no compartilhamento NFS, em vez de 10 minutos, ao gravar em um conjunto de distribuição local.

![A alternativa não foi muito mais rápida com 1 hora e 43 minutos](media/sap-hana-backup-file-level/image037.png)

A alternativa de fazer um backup em um conjunto de distribuição local e copiar no compartilhamento NFS no nível do sistema operacional (um comando **cp - avr** simples) não foi muito mais rápida. Demorou 1 hora e 43 minutos.

Funciona, mas o desempenho não foi bom para o teste de backup de 230 GB. Ficaria ainda pior para vários terabytes.

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>Copiar arquivos de backup do SAP HANA para os Arquivos do Azure

É possível montar um compartilhamento de Arquivos do Azure dentro de uma VM do Linux do Azure. O artigo [Como usar o Armazenamento de arquivos do Azure com o Linux](../../../storage/files/storage-how-to-use-files-linux.md) fornece detalhes sobre como fazer isso. Tenha em mente de que há um limite de cota de 5 TB de um compartilhamento de arquivos do Azure e um limite de tamanho de arquivo de 1 TB por arquivo. Confira [Metas de desempenho e de escalabilidade do Armazenamento do Azure](../../../storage/common/storage-scalability-targets.md) para saber mais sobre os limites de armazenamento.

Os testes mostraram, no entanto, que o backup do SAP HANA não funciona diretamente no momento com esse tipo de montagem de CIFS. Também está declarado na [Nota SAP 1820529](https://launchpad.support.sap.com/#/notes/1820529) que o CIFS não é recomendado.

![Esta figura mostra um erro na caixa de diálogo de backup no SAP HANA Studio](media/sap-hana-backup-file-level/image038.png)

Esta figura mostra um erro na caixa de diálogo de backup no SAP HANA Studio, ao tentar fazer backup diretamente em um compartilhamento de arquivos do Azure montado em CIFS. Portanto, é necessário realizar primeiro um backup padrão do SAP HANA em um sistema de arquivos da VM e, depois, copiar os arquivos de backup a partir daí para o serviço de arquivos do Azure.

![Esta figura mostra que demorou aproximadamente 929 segundos para copiar os 19 arquivos de backup do SAP HANA](media/sap-hana-backup-file-level/image039.png)

Esta figura mostra que demorou aproximadamente 929 segundos para copiar 19 arquivos de backup do SAP HANA com um tamanho total de aproximadamente 230 GB para o compartilhamento de arquivos do Azure.

![A estrutura do diretório de origem na VM do SAP HANA foi copiada para o compartilhamento de arquivos do Azure](media/sap-hana-backup-file-level/image040.png)

Nesta captura de tela, é possível ver que a estrutura de diretórios de origem na VM do SAP HANA foi copiada para o compartilhamento de arquivos do Azure: um diretório (hana\_backup\_fsl\_15gb) e 19 arquivos de backup individuais.

Armazenar os arquivos de backup do SAP HANA em arquivos do Azure pode ser uma opção interessante no futuro, quando os backups de arquivo do SAP HANA oferecerem suporte diretamente. Ou quando for possível montar os arquivos do Azure por meio de NFS, e o limite de cota máximo for consideravelmente maior do que 5 TB.

## <a name="next-steps"></a>Próximas etapas
* [Guia de backup do SAP HANA nas Máquinas Virtuais do Azure](sap-hana-backup-guide.md) fornece uma visão geral e informações sobre como começar.
* [O backup do SAP HANA com base em instantâneos de armazenamento](sap-hana-backup-storage-snapshots.md) descreve a opção de backup baseados em instantâneo de armazenamento.
* Para saber como estabelecer a alta disponibilidade e o plano de recuperação de desastres do SAP HANA no Azure (instâncias grandes), confira [Alta disponibilidade e recuperação de desastres do SAP HANA (instâncias grandes) no Azure](hana-overview-high-availability-disaster-recovery.md).
