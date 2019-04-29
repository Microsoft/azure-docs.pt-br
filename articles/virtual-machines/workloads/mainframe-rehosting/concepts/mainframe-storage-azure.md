---
title: Mover o armazenamento de mainframe para armazenamento do Azure
description: Recursos de armazenamento do Azure altamente escalonável podem ajudar organizações baseados em mainframe migrar e modernizar aplicativos z14 da IBM.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: storage
ms.openlocfilehash: 4fea787e7fe20d60de91761811e1b69bebf3a010
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485469"
---
# <a name="move-mainframe-storage-to-azure"></a>Mover o armazenamento de mainframe no Azure

Para executar cargas de trabalho de mainframe no Microsoft Azure, você precisa saber como os recursos do seu mainframe se comparam com o Azure. Os recursos de armazenamento altamente escalonável podem ajudar as organizações começam a modernizar sem eliminar os aplicativos que eles dependem.

O Azure fornece recursos semelhantes de mainframe e a capacidade de armazenamento que é comparável para sistemas baseados em z14 da IBM (o modelo mais recente a partir da redação deste artigo). Este artigo informa como obter resultados comparáveis no Azure.

## <a name="mainframe-storage-at-a-glance"></a>Armazenamento de mainframe em um relance

O mainframe IBM caracteriza o armazenamento de duas maneiras. A primeira é um dispositivo de armazenamento de acesso direto (DASD). O segundo é o armazenamento sequencial. Para gerenciar o armazenamento, o mainframe fornece o subsistema de gerenciamento de armazenamento (DFSMS) dados Facility. Ele gerencia o acesso a dados para vários dispositivos de armazenamento.

[DASD](https://en.wikipedia.org/wiki/Direct-access_storage_device) refere-se a um dispositivo separado para o armazenamento secundário (não na memória) que permite que um endereço exclusivo a ser usado para acesso direto de dados. Originalmente, o termo DASD aplicado a rotação de discos, tambores magnéticas ou células de dados. No entanto, agora o termo também pode aplicar a dispositivos de armazenamento de estado sólido (SSDs), redes de área de armazenamento (SANs), conectado à rede (NAS) de armazenamento e unidades ópticas. Para os fins deste documento, DASD refere-se a discos, SANs e SSDs de rotação.

Em contraste com o armazenamento de DASD sequencial armazenamento em um mainframe refere-se aos dispositivos, como unidades de fita em que dados são acessados de um ponto de partida, e em seguida, lidos ou gravados em uma linha.

Dispositivos de armazenamento geralmente são conectados usando uma conexão de fibra (FICON) ou são acessados diretamente sobre o uso de barramento de e/s o mainframe [HiperSockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm), uma tecnologia da IBM para comunicações de alta velocidade entre partições em um servidor com um hipervisor.

A maioria dos sistemas de mainframe separar o armazenamento em dois tipos:

- *Armazenamento on-line* (também conhecido como armazenamento quente) é necessária para as operações diárias. Armazenamento DASD geralmente é usado para essa finalidade. No entanto, o armazenamento sequencial, como backups diários em fita (lógicos ou físicos), também pode ser usado para essa finalidade.

- *Armazenamento de arquivo morto* (também conhecido como armazenamento frio) não é garantido para ser montado em um determinado momento. Em vez disso, ele é montado e acessado conforme a necessidade. O armazenamento de arquivos geralmente é implementado usando os backups em fita sequencial (lógicos ou físicos) para armazenamento.

## <a name="mainframe-versus-io-latency-and-iops"></a>Mainframe versus IOPS e latência de e/s

Mainframes geralmente são usadas para aplicativos que exigem alto desempenho de e/s e baixa latência de e/s. Eles podem fazer isso usando as conexões de FC para dispositivos de e/s e HiperSockets. Quando HiperSockets são usadas para se conectar a aplicativos e dispositivos diretamente para o canal de e/s do mainframe, a latência nos microssegundos pode ser obtida.

## <a name="azure-storage-at-a-glance"></a>Armazenamento do Azure em um relance

Azure infraestrutura-como-um-serviço ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)) opções de armazenamento fornecem capacidade de mainframe comparável.

A Microsoft oferece a partir de petabytes de armazenamento para aplicativos hospedados no Azure, e você tem várias opções de armazenamento. Esses variam de armazenamento de SSD de alto desempenho para o armazenamento de blob de baixo custo para armazenamento em massa e arquivos mortos. Além disso, o Azure fornece uma opção de redundância de dados para armazenamento — algo que exige mais esforço para configurar em um ambiente de mainframe.

O armazenamento do Azure está disponível como [discos do Azure](/azure/virtual-machines/windows/managed-disks-overview), [arquivos do Azure](/azure/storage/files/storage-files-introduction), e [Blobs do Azure](/azure/storage/blobs/storage-blobs-overview) conforme a tabela a seguir resume. Saiba mais sobre [quando usar cada](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Type</th><th>DESCRIÇÃO</th><th>Use quando você quiser:</th></tr>
</thead>
<tbody>
<tr><td>Arquivos do Azure
</td>
<td>
Fornece uma interface SMB, bibliotecas de cliente e um <a href="https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api">REST</a> interface que permite o acesso em qualquer lugar para arquivos armazenados.
</td>
<td><ul>
<li>Comparar e Deslocar um aplicativo para a nuvem quando o aplicativo usa as APIs do sistema de arquivos nativo para compartilhar dados entre ele e outros aplicativos em execução no Azure.</li>
<li>Store desenvolvimento e ferramentas de depuração que precisam ser acessados de várias VMs.</li>
</ul>
</td>
</tr>
<tr><td>Blobs do Azure
</td>
<td>Fornece bibliotecas de cliente e um <a href="https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api">REST</a> interface que permite que os dados não estruturados sejam armazenados e acessados em grande escala em blobs de blocos. Também dá suporte ao <a href="/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2</a> para soluções de análise de big data do enterprise.
</td>
<td><ul>
<li>Dar suporte a cenários de acesso aleatório e streaming em um aplicativo.</li>
<li>Ter acesso aos dados do aplicativo em qualquer lugar.</li>
<li>Crie um lago de dados empresariais no Azure e executar análise de big data.</li>
</ul></td>
</tr>
<tr><td>Discos do Azure
</td>
<td>Fornece bibliotecas de cliente e um <a href="https://docs.microsoft.com/rest/api/compute/disks">REST</a> interface que permite que os dados sejam armazenados e acessados em um disco rígido virtual anexado persistentemente.
</td>
<td><ul>
<li>Lift- and -shift aplicativos que usam APIs do sistema de arquivos nativo para ler e gravar dados em discos persistentes.</li>
<li>Store dados que não não necessários para ser acessado de fora da VM à qual o disco está anexado.</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Azure ativa (online) e armazenamento frio (arquivo)

O tipo de armazenamento para um determinado sistema depende dos requisitos do sistema, incluindo o tamanho de armazenamento, taxa de transferência e IOPS. Para o armazenamento do tipo DASD em um mainframe, aplicativos no Azure geralmente usam o armazenamento de unidade de discos do Azure em vez disso. Para o armazenamento de arquivos de mainframe, armazenamento de BLOBs é usado no Azure.

Os SSDs fornecem o melhor desempenho de armazenamento no Azure. As seguintes opções estão disponíveis (no momento da elaboração deste documento):

| Type         | Tamanho           | IOPS                  |
|--------------|----------------|-----------------------|
| SSD Ultra    | 4 GB a 64 TB  | IOPS de 1.200 para 160,000 |
| SSD Premium  | 32 GB para 32 TB | IOPS de 12 a 15.000     |
| SSD Standard | 32 GB para 32 TB | IOPS de 12 a 2.000      |

O armazenamento de BLOBs oferece o maior volume de armazenamento no Azure. Além do tamanho de armazenamento, o Azure oferece armazenamento gerenciado e não gerenciados. Com armazenamento gerenciado, o Azure cuida do gerenciamento de contas de armazenamento subjacente. Com o armazenamento não gerenciado, o usuário assume a responsabilidade pela configuração de contas de armazenamento do Azure de tamanho apropriado cumprir os requisitos de armazenamento.

## <a name="next-steps"></a>Próximas etapas

- [Migração de mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Mainframe rehosting em máquinas virtuais do Azure](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [Mover computação de mainframe para o Azure](mainframe-compute-Azure.md)
- [Decidindo quando usar Blobs do Azure, arquivos do Azure ou discos do Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)
- [SSD Managed Disks Standard para cargas de trabalho de VM do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)

### <a name="ibm-resources"></a>Recursos da IBM

- [Sysplex paralelo no IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS e a facilidade de acoplamento: Além do básico](http://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Criando usuários necessários para uma instalação do Recurso Db2 pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - criar instância de comando](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2 pureScale solução de banco de dados clusterizado](http://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Nuvem de governo do Microsoft Azure para aplicativos de mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [A Microsoft e FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Mais recursos de migração

- [Aliança de Modernização de Plataforma: IBM Db2 no Azure](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)
- [Guia de deslocamento e comparação de precisão de Data Center Virtual do Azure](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
