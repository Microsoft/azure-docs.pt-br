---
title: "Planejando uma implantação de Sincronização de Arquivo do Azure (versão prévia) | Microsoft Docs"
description: "Saiba o que considerar ao planejar uma implantação de Arquivos do Azure."
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: d8ac076334a7ed9476b4830596d6ea54c29c0e3c
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="planning-for-an-azure-file-sync-preview-deployment"></a>Planejando uma implantação de Sincronização de Arquivo do Azure (versão prévia)
A Sincronização de Arquivos do Azure (versão prévia) permite que você centralize os compartilhamentos de arquivos da sua organização em Arquivos do Azure sem abrir mão da flexibilidade, do desempenho e da compatibilidade de um servidor de arquivos local. Ele faz isso transformando Windows Servers em um cache rápido do seu compartilhamento de Arquivos do Azure. Você pode usar qualquer protocolo disponível no Windows Server para acessar seus dados localmente (incluindo SMB, NFS e FTPS) e pode ter todos os caches de que precisar ao redor do mundo.

Este guia descreve o que considerar ao implantar a Sincronização de Arquivo do Azure. É recomendável que você leia o teste de guia [Planning for an Azure Files deployment](storage-files-planning.md) (Planejando uma implantação de Arquivos do Azure). 

## <a name="understanding-azure-file-sync-terminology"></a>Entendendo a terminologia da Sincronização de Arquivo do Azure
Antes de continuar com os detalhes da Sincronização de Arquivo do Azure, é importante compreender a terminologia.

### <a name="storage-sync-service"></a>Serviço de Sincronização de Armazenamento
O Serviço de Sincronização de Armazenamento é o recurso do Azure de nível superior que representa a Sincronização de Arquivo do Azure. O recurso do Serviço de Sincronização de Armazenamento está emparelhado com o recurso da conta de armazenamento e pode, de maneira semelhante, ser implantado nos Grupos de Recursos do Azure. Um recurso de nível superior distinto do recurso de conta de armazenamento é necessário porque o Serviço de Sincronização de Armazenamento pode criar relações de sincronização com várias contas de armazenamento por meio de vários Grupos de Sincronização. Uma assinatura pode ter vários recursos de Serviço de Sincronização de Armazenamento implantados.

### <a name="sync-group"></a>Grupo de Sincronização
Um Grupo de Sincronização define a topologia de sincronização para um conjunto de arquivos. Os pontos de extremidade em um Grupo de Sincronização serão mantidos em sincronia entre si. Se, por exemplo, você tiver dois conjuntos distintos de arquivos que você deseja gerenciar com AFS, crie dois Grupos de Sincronização e adicione pontos de extremidade diferentes a cada um. Um Serviço de Sincronização de Armazenamento pode hospedar quantos Grupos de Sincronização forem necessários.  

### <a name="registered-server"></a>Servidor registrado
O objeto de Servidor Registrado representa uma relação de confiança entre seu servidor (ou cluster) e o Serviço de Sincronização de Armazenamento. Você pode registrar quantos servidores desejar para uma instância do Serviço de Sincronização de Armazenamento. No entanto, um servidor (ou cluster) só pode ser registrado com um Serviço de Sincronização de Armazenamento a qualquer momento.

### <a name="azure-file-sync-agent"></a>Agente de Sincronização de Arquivo do Azure
O agente de Sincronização de Arquivo do Azure é um pacote baixável que permite que um Windows Server seja sincronizado com um compartilhamento de Arquivo do Azure. O agente de Sincronização de Arquivo do Azure consiste em três componentes principais: 
- **FileSyncSvc.exe**: o serviço Windows em segundo plano responsável por monitorar alterações nos pontos de extremidade do servidor e iniciar as sessões de sincronização para o Azure.
- **StorageSync.sys**: o filtro do sistema de arquivos de Sincronização de Arquivo do Azure, responsável por dispor os arquivos em camadas para os Arquivos do Azure (quando as camadas de nuvem estão habilitadas).
- **Cmdlets de gerenciamento do PowerShell**: cmdlets do PowerShell para interagir com o provedor de recursos do Azure Microsoft.StorageSync. Eles podem ser encontrados nas seguintes localizações (por padrão):
    - C:\Arquivos de Programas\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Arquivos de Programas\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="cloud-endpoint"></a>Ponto de extremidade de nuvem
Um ponto de extremidade de nuvem é um compartilhamento de Arquivo do Azure que faz parte de um Grupo de Sincronização. Todas as sincronizações de compartilhamento de Arquivo do Azure e um Arquivo do Azure podem ser um membro apenas de um ponto de extremidade de nuvem e, portanto, um Grupo de Sincronização. Se você adicionar um Compartilhamento de Arquivo do Azure com um conjunto existente de arquivos como um ponto de extremidade de nuvem a um Grupo de Sincronização, esses arquivos serão mesclados com quaisquer outros arquivos que já estiverem em outros pontos de extremidade no Grupo de Sincronização.

### <a name="server-endpoint"></a>Ponto de extremidade do servidor
Um ponto de extremidade do servidor representa uma localização específica em um servidor registrado, como uma pasta em um volume do servidor ou a raiz do volume. Pode haver vários pontos de extremidade do servidor no mesmo volume se seus namespaces não forem sobrepostos (por exemplo, F:\sync1 e F:\sync2). Você pode configurar políticas de disposição em camadas de nuvem individualmente para cada ponto de extremidade do servidor. Se você adicionar uma localização de servidor com um conjunto existente de arquivos como um ponto de extremidade de servidor a um Grupo de Sincronização, esses arquivos serão mesclados com quaisquer outros arquivos que já estiverem em outros pontos de extremidade no Grupo de Sincronização.

### <a name="cloud-tiering"></a>Disposição em camadas de nuvem 
A disposição em camadas de nuvem é um recurso opcional da Sincronização de Arquivo do Azure, que permite que os arquivos pouco usados ou acessados sejam dispostos em camadas nos Arquivos do Azure. Quando um arquivo está disposto em camadas, o filtro do sistema de arquivos da Sincronização de Arquivo do Azure (StorageSync.sys) substitui o arquivo localmente por um ponteiro ou ponto de nova análise, representando uma URL para o arquivo nos Arquivos do Azure. Um arquivo em camadas tem o atributo "offline" definido em NTFS, de forma que os aplicativos de terceiros podem identificar arquivos em camadas. Quando um usuário abre um arquivo em camadas, a Sincronização de Arquivo do Azure diretamente recupera os dados de arquivo dos Arquivos do Azure sem o usuário precisar saber que o arquivo não está armazenado localmente no sistema. Essa funcionalidade também é conhecida como HSM (Gerenciamento de Armazenamento Hierárquico).

## <a name="azure-file-sync-interoperability"></a>Interoperabilidade de Sincronização de Arquivo do Azure 
Esta seção aborda a interoperabilidade de Sincronização de Arquivo do Azure com soluções de terceiros, funções e recursos do Windows Server.

### <a name="supported-versions-of-windows-server"></a>Versões com suporte do Windows Server
No momento, as versões com suporte do Windows Server para a Sincronização de Arquivo do Azure são:

| Versão | SKUs com suporte | Opções de implantação com suporte |
|---------|----------------|------------------------------|
| Windows Server 2016 | Datacenter e Standard | Completo (servidor com uma interface do usuário) |
| Windows Server 2012 R2 | Datacenter e Standard | Completo (servidor com uma interface do usuário) |

Versões futuras do Windows Server serão adicionadas à medida que elas forem lançadas e as versões mais antigas do Windows podem ser adicionadas com base nos comentários do usuário.

> [!Important]  
> É recomendável manter todos os Windows Server usados com a Sincronização de Arquivo do Azure atualizados com as atualizações mais recentes do Windows Update. 

### <a name="file-system-features"></a>Recursos do sistema de arquivos
| Recurso | Status de suporte | Observações |
|---------|----------------|-------|
| ACLs (Listas de Controle de Acesso) | Com suporte total | As ACLs do Windows são preservadas pela Sincronização de Arquivo do Azure e são impostas pelo Windows Server nos pontos de extremidade do servidor. As ACLs do Windows (ainda) não têm suporte pelos Arquivos do Azure se os Arquivos forem acessados diretamente na nuvem. |
| Links físicos | Ignorado | |
| Links simbólicos | Ignorado | |
| Pontos de montagem | Com suporte parcial | Os pontos de montagem podem ser a raiz de um ponto de extremidade do servidor, mas serão ignorados se contidos no namespace do ponto de extremidade do servidor. |
| Junções | Ignorado | |
| Pontos de nova análise | Ignorado | |
| Compactação NTFS | Com suporte total | |
| Arquivos esparsos | Com suporte total | Os arquivos esparsos serão sincronizados (não bloqueados), mas serão sincronizados com a nuvem como um arquivo completo. Se o conteúdo do arquivo for alterado na nuvem (ou em outro servidor), o arquivo não será mais esparso quando a alteração for baixada |
| ADS (Fluxos de Dados Alternativos) | Preservados, mas não sincronizados | |

> [!Note]  
> Há suporte para apenas os volumes NTFS.

### <a name="failover-clustering"></a>Clustering de failover
O clustering de failover do Windows Server tem suporte pela Sincronização de Arquivo do Azure para a opção de implantação “Servidor de Arquivos de uso geral”. Não há suporte para o clustering de failover em “SOFS (Servidor de Arquivos de Escalabilidade Horizontal) para dados de aplicativos” ou CSV (Volumes Compartilhados Clusterizados).

> [!Note]  
> O agente de Sincronização de Arquivo do Azure deve ser instalado em cada nó em um cluster de failover para a sincronização funcionar corretamente.

### <a name="windows-server-data-deduplication"></a>Eliminação de duplicação de dados do Windows Server
Para volumes sem a disposição em camadas habilitada, a Sincronização de Arquivo do Azure dá suporte à eliminação de duplicação de dados habilitada no volume. Não damos suporte à interoperabilidade entre a Sincronização de Arquivo do Azure com a disposição em camadas de nuvem habilitada e a eliminação de duplicação de dados no momento.

### <a name="anti-virus-solutions"></a>Soluções de antivírus
Como os antivírus funcionam examinando os arquivos em relação ao código mal-intencionado conhecido, eles podem causar o recall de arquivos em camadas. Como os arquivos em camadas têm o atributo “offline” definido, é recomendável consultar seu fornecedor de software em relação a como configurar sua solução para ignorar a leitura de arquivos offline. 

Sabe-se que as seguintes soluções dão suporte à possibilidade de ignorar arquivos offline:

- [Symantec Endpoint Protection](https://support.symantec.com/en_US/article.tech173752.html)
- [McAfee EndPoint Security](https://kc.mcafee.com/resources/sites/MCAFEE/content/live/PRODUCT_DOCUMENTATION/26000/PD26799/en_US/ens_1050_help_0-00_en-us.pdf) (consulte a seção "Scan only what you need to" (Examinar apenas o necessário) na página 90)
- [Kaspersky Anti-Virus](https://support.kaspersky.com/4684)
- [Sophos Endpoint Protection](https://community.sophos.com/kb/en-us/40102)
- [TrendMicro OfficeScan](https://success.trendmicro.com/solution/1114377-preventing-performance-or-backup-and-restore-issues-when-using-commvault-software-with-osce-11-0#collapseTwo) 

### <a name="backup-solutions"></a>Soluções de backup
Como as soluções de antivírus, as soluções de backup podem causar o recall de arquivos em camadas. É recomendável usar uma solução de backup de nuvem para o backup do compartilhamento do Arquivo do Azure em vez de usar o produto de backup local.

### <a name="encryption-solutions"></a>Soluções de criptografia
O suporte para soluções de criptografia depende de como elas são implementadas. A Sincronização de Arquivo do Azure é conhecida por funcionar com:

- Criptografia de BitLocker
- Azure RMS (e RMS do Active Directory herdado)

A Sincronização de Arquivo do Azure é conhecida por não funcionar com:

- EFS (Sistema de Arquivos com Criptografia) NTFS

Em geral, a Sincronização de Arquivo do Azure deve ser capaz de interoperar com soluções de criptografia que ficam abaixo do sistema de arquivos, como o BitLocker, e soluções implementadas no formato de arquivo, como o BitLocker, mas não foi feita nenhuma interoperabilidade especial para a interoperabilidade com soluções que ficam acima do sistema de arquivos (como o Sistema de Arquivos com Criptografia NTFS).

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Outras soluções de HSM (Gerenciamento de Armazenamento Hierárquico)
Nenhuma outra solução de Gerenciamento de Armazenamento Hierárquico deve ser usada com a Sincronização de Arquivos do Azure.

## <a name="region-availability"></a>Disponibilidade de região
A Sincronização de Arquivo do Azure está disponível apenas nas seguintes regiões na versão prévia:

| Região | Localização do Datacenter |
|--------|---------------------|
| Oeste dos EUA | Califórnia, EUA |
| Europa Ocidental | Países Baixos |
| Sudeste da Ásia | Cingapura |
| Leste da Austrália | Nova Gales do Sul, Austrália |

Na versão prévia, há suporte apenas para a sincronização com um compartilhamento de Arquivo do Azure na mesma região que o Serviço de Sincronização de Armazenamento.

## <a name="azure-file-sync-agent-update-policy"></a>Política de atualização do agente de Sincronização de Arquivo do Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Próximas etapas
* [Planejando uma implantação de Arquivos do Azure](storage-files-planning.md)
* [Implantando Arquivos do Azure](storage-files-deployment-guide.md)
* [Implantando a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md)
