---
title: Mover o armazenamento de mainframe para o armazenamento do Azure
description: Os recursos de armazenamento do Azure amplamente escalonáveis podem ajudar as organizações baseadas em mainframe a migrar e modernizar os aplicativos IBM Z14.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: storage
ms.openlocfilehash: 8f57210ff6b65ee3a893fb344a48629466e90004
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97095348"
---
# <a name="move-mainframe-storage-to-azure"></a>Mover o armazenamento de mainframe para o Azure

Para executar cargas de trabalho de mainframe no Microsoft Azure, você precisa saber como os recursos do mainframe se comparam ao Azure. Os recursos de armazenamento altamente escalonáveis podem ajudar as organizações a se modernizar sem abandonar os aplicativos nos quais eles dependem.

O Azure fornece recursos do tipo mainframe e capacidade de armazenamento comparáveis a sistemas baseados no IBM Z14 (o modelo mais atual a partir deste artigo). Este artigo mostra como obter resultados comparáveis no Azure.

## <a name="mainframe-storage-at-a-glance"></a>Armazenamento em mainframe em um relance

O mainframe da IBM caracteriza o armazenamento de duas maneiras. A primeira é um dispositivo de armazenamento de acesso direto (DASD). A segunda é o armazenamento sequencial. Para gerenciar o armazenamento, o mainframe fornece o sistema de gerenciamento de armazenamento do Data Facility (DFSMS). Ele gerencia o acesso a dados para os vários dispositivos de armazenamento.

O [DASD](https://en.wikipedia.org/wiki/Direct-access_storage_device) refere-se a um dispositivo separado para armazenamento secundário (não na memória) que permite que um endereço exclusivo seja usado para acesso direto aos dados. Originalmente, o termo DASD aplicado à rotação de discos, tambores magnéticos ou células de dados. No entanto, agora o termo também pode se aplicar a SSDs (dispositivos de armazenamento de estado sólido), SANs (redes de área de armazenamento), NAS (armazenamento conectado à rede) e unidades ópticas. Para os fins deste documento, o DASD se refere à rotação de discos, SANs e SSDs.

Ao contrário do armazenamento DASD, o armazenamento sequencial em um mainframe refere-se a dispositivos como unidades de fita em que os dados são acessados de um ponto de partida e lidos ou gravados em uma linha.

Os dispositivos de armazenamento normalmente são anexados usando uma conexão de fibra (FICOn) ou são acessados diretamente no barramento de e/s do mainframe usando o [HiperSockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm), uma tecnologia IBM para comunicações de alta velocidade entre partições em um servidor com um hipervisor.

A maioria dos sistemas de mainframe separa o armazenamento em dois tipos:

- O *armazenamento online* (também conhecido como armazenamento dinâmico) é necessário para operações diárias. O armazenamento DASD geralmente é usado para essa finalidade. No entanto, o armazenamento sequencial, como backups diários em fita (lógico ou físico), também pode ser usado para essa finalidade.

- Não há garantia de que o *armazenamento de arquivo* (também conhecido como armazenamento frio) seja montado em um determinado momento. Em vez disso, ele é montado e acessado conforme necessário. O armazenamento de arquivos geralmente é implementado usando backups em fita sequenciais (lógicos ou físicos) para armazenamento.

## <a name="mainframe-versus-io-latency-and-iops"></a>Mainframe versus latência de e/s e IOPS

Os mainframes são frequentemente usados para aplicativos que exigem e/s de alto desempenho e baixa latência de e/s. Eles podem fazer isso usando as conexões FICOn com dispositivos de e/s e HiperSockets. Quando os HiperSockets são usados para conectar aplicativos e dispositivos diretamente ao canal de e/s de um mainframe, a latência em microssegundos pode ser obtida.

## <a name="azure-storage-at-a-glance"></a>Armazenamento do Azure em um relance

As opções de infraestrutura como serviço ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)) do Azure para armazenamento fornecem capacidade de mainframe comparável.

A Microsoft oferece petabytes de armazenamento para aplicativos hospedados no Azure, e você tem várias opções de armazenamento. Eles variam desde o armazenamento SSD até o alto desempenho até o armazenamento de blobs de baixo custo para armazenamento e arquivamento em massa. Além disso, o Azure fornece uma opção de redundância de dados para armazenamento — algo que leva mais esforço para ser configurado em um ambiente de mainframe.

O armazenamento do Azure está disponível como [discos do Azure](../../../managed-disks-overview.md), [arquivos do Azure](../../../../storage/files/storage-files-introduction.md)e [BLOBs do Azure](../../../../storage/blobs/storage-blobs-overview.md) conforme a tabela a seguir resume. Saiba mais sobre [quando usar cada um deles](../../../../storage/common/storage-introduction.md).

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Tipo</th><th>Descrição</th><th>Use quando desejar:</th></tr>
</thead>
<tbody>
<tr><td>Arquivos do Azure
</td>
<td>
Fornece uma interface SMB, bibliotecas de cliente e uma interface <a href="/rest/api/storageservices/file-service-rest-api">REST</a> que permite o acesso de qualquer lugar a arquivos armazenados.
</td>
<td><ul>
<li>Levante e desloca um aplicativo para a nuvem quando o aplicativo usa as APIs do sistema de arquivos nativo para compartilhar dados entre ele e outros aplicativos em execução no Azure.</li>
<li>Armazene ferramentas de desenvolvimento e depuração que precisam ser acessadas de várias VMs.</li>
</ul>
</td>
</tr>
<tr><td>Blobs do Azure
</td>
<td>Fornece bibliotecas de cliente e uma interface <a href="/rest/api/storageservices/blob-service-rest-api">REST</a> que permite que dados não estruturados sejam armazenados e acessados em grande escala em blobs de blocos. Também dá suporte ao <a href="/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2</a> para soluções de análise de big data do enterprise.
</td>
<td><ul>
<li>Suporte a streaming e cenários de acesso aleatório em um aplicativo.</li>
<li>Ter acesso aos dados do aplicativo de qualquer lugar.</li>
<li>Crie um Enterprise Data Lake no Azure e execute Big Data Analytics.</li>
</ul></td>
</tr>
<tr><td>Discos do Azure
</td>
<td>Fornece bibliotecas de cliente e uma interface <a href="/rest/api/compute/disks">REST</a> que permite que os dados sejam armazenados de forma persistente e acessados de um disco rígido virtual anexado.
</td>
<td><ul>
<li>Migre e SHIFTe os aplicativos que usam APIs do sistema de arquivos nativas para ler e gravar dados em discos persistentes.</li>
<li>Armazene dados que não precisam ser acessados de fora da VM à qual o disco está anexado.</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Armazenamento do Azure quente (online) e frio (arquivamento)

O tipo de armazenamento de um determinado sistema depende dos requisitos do sistema, incluindo o tamanho do armazenamento, a taxa de transferência e o IOPS. Para o armazenamento do tipo DASD em um mainframe, os aplicativos no Azure normalmente usam o armazenamento de unidade de disco do Azure. Para armazenamento de arquivo de mainframe, o armazenamento de BLOBs é usado no Azure.

O SSDs fornece o melhor desempenho de armazenamento no Azure. As seguintes opções estão disponíveis (a partir da escrita deste documento):

| Tipo         | Tamanho           | IOPS                  |
|--------------|----------------|-----------------------|
| SSD Ultra    | 4 GB a 64 TB  | 1.200 a 160.000 IOPS |
| SSD Premium  | 32 GB a 32 TB | 12 a 15.000 IOPS     |
| SSD Standard | 32 GB a 32 TB | 12 a 2.000 IOPS      |

O armazenamento de BLOBs fornece o maior volume de armazenamento no Azure. Além do tamanho do armazenamento, o Azure oferece armazenamento gerenciado e não gerenciado. Com o armazenamento gerenciado, o Azure cuida do gerenciamento das contas de armazenamento subjacentes. Com o armazenamento não gerenciado, o usuário assume a responsabilidade de configurar contas de armazenamento do Azure do tamanho apropriado para atender aos requisitos de armazenamento.

## <a name="next-steps"></a>Próximas etapas

- [Migração de mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Hospedagem de mainframe em máquinas virtuais do Azure](../overview.md)
- [Mover a computação do mainframe para o Azure](mainframe-compute-Azure.md)
- [Decidindo quando usar Blobs do Azure, Arquivos do Azure ou Discos do Azure](../../../../storage/common/storage-introduction.md)
- [SSD Standard Managed Disks para cargas de trabalho de VM do Azure](../../../disks-types.md#standard-ssd)

### <a name="ibm-resources"></a>Recursos da IBM

- [Sysplex paralelo no IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS e o recurso de acoplamento: além dos conceitos básicos](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Criando usuários necessários para uma instalação de recurso do DB2 pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt-comando de criação de instância](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Solução de banco de dados clusterizado DB2 pureScale](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Microsoft Azure Governamental nuvem para aplicativos de mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft e FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Mais recursos de migração

- [Guia de deslocamento e comparação de precisão de Data Center Virtual do Azure](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://glusterdocs.readthedocs.io/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)