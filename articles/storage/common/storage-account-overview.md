---
title: Visão geral da conta de armazenamento
titleSuffix: Azure Storage
description: Saiba mais sobre os diferentes tipos de contas de armazenamento no armazenamento do Azure. Revise a nomenclatura da conta, as camadas de desempenho, as camadas de acesso, a redundância, a criptografia, os pontos de extremidade e muito mais.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/08/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 5cf43310c68c8446b9465a39d85f84c8273a68d8
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051217"
---
# <a name="storage-account-overview"></a>Visão geral da conta de armazenamento

Uma conta de armazenamento do Azure contém todos os seus objetos de dados do Armazenamento do Azure: blobs, arquivos, filas, tabelas e discos. A conta de armazenamento fornece um namespace exclusivo para os dados do armazenamento do Azure que podem ser acessados de qualquer lugar do mundo por HTTP ou HTTPS. Os dados em sua conta de armazenamento do Azure são duráveis e altamente disponíveis, seguros e amplamente escalonáveis.

Para saber como criar uma conta de armazenamento do Azure, confira [Criar uma conta de armazenamento](storage-account-create.md).

## <a name="types-of-storage-accounts"></a>Tipos de contas de armazenamento

O Armazenamento do Azure oferece diversos tipos de contas de armazenamento. Cada tipo é compatível com recursos diferentes e tem um modelo de preços próprio. Considere essas diferenças antes de criar uma conta de armazenamento para determinar o melhor tipo de conta para seus aplicativos. Os tipos de conta de armazenamento são:

- **Contas para uso geral v2**: tipo de conta de armazenamento básico para blobs, arquivos, filas e tabelas. Recomendado para a maioria dos cenários que usam o Armazenamento do Azure.
- **Contas para uso geral v1**: tipo de conta herdada para blobs, arquivos, filas e tabelas. Use contas de uso geral v2 em vez desta opção, quando possível.
- **Contas de BlockBlobStorage**: contas de armazenamento com características de desempenho premium para blobs de blocos e blobs de acréscimo. Recomendadas para cenários com altas taxas de transações ou cenários que usam objetos menores ou exigem latência de armazenamento consistentemente baixa.
- **Contas de FileStorage**: contas de armazenamento somente para arquivos com características de desempenho premium. Recomendadas para aplicações de escala empresarial ou de alto desempenho.
- **Contas de BlobStorage**: contas de armazenamento somente em blobs herdadas. Use contas de uso geral v2 em vez desta opção, quando possível.

A tabela a seguir descreve os tipos de contas de armazenamento, os serviços aos quais eles oferecem suporte e os modelos de implantação com suporte para cada tipo de conta:

| Tipo de conta de armazenamento | Serviços com suporte | Opções de redundância | Modelo de implantação<sup>1</sup> |
|--|--|--|--|
| Uso geral V2 | BLOB, arquivo, fila, tabela, disco e Data Lake Gen2<sup>2</sup> | LRS, GRS, RA-GRS, ZRS, GZRS, RA-GZRS<sup>3</sup> | Gerenciador de Recursos |
| Uso geral V1 | Blob, arquivo, fila, tabela e disco | LRS, GRS, RA-GRS | Resource Manager, Clássico |
| BlockBlobStorage | Blob (somente blobs de blocos e blobs de acréscimo) | LRS, ZRS<sup>3</sup> | Gerenciador de Recursos |
| FileStorage | Somente arquivo | LRS, ZRS<sup>3</sup> | Gerenciador de Recursos |
| BlobStorage | Blob (somente blobs de blocos e blobs de acréscimo) | LRS, GRS, RA-GRS | Gerenciador de Recursos |

<sup>1</sup>É recomendável usar o modelo de implantação do Azure Resource Manager. Ainda é possível criar contas de armazenamento que usam o modelo de implantação clássico em alguns locais, e as contas clássicas existentes ainda têm suporte. Para saber mais, confira [Azure Resource Manager vs. Implantação clássica: compreenda os modelos de implantação e o estado dos seus recursos](../../azure-resource-manager/management/deployment-models.md).

<sup>2</sup> Azure Data Lake Storage Gen2 é um conjunto de recursos dedicados à análise de Big Data, criado no armazenamento de BLOBs do Azure. O Data Lake Storage Gen2 tem suporte apenas em contas de armazenamento de uso geral v2 com namespace hierárquico habilitado. Para obter mais informações sobre o Data Lake Storage Gen2, confira [Introdução ao Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md).

<sup>3</sup> O ZRS (armazenamento com redundância de zona) e o armazenamento com redundância de zona geográfica (GZRS/RA-GZRS) estão disponíveis somente para contas de armazenamento padrão v2, BlockBlobStorage e de uso geral em determinadas regiões. Para obter mais informações sobre as opções de redundância do Armazenamento do Azure, confira [Redundância no Armazenamento do Azure](storage-redundancy.md).

### <a name="storage-account-redundancy"></a>Redundância da conta de armazenamento

As opções de redundância para uma conta de armazenamento incluem:

- **LRS (armazenamento com redundância local)**: uma estratégia de redundância simples e de baixo custo. Os dados serão copiados três vezes em uma localização física única de maneira síncrona na região primária.
- **ZRS (armazenamento com redundância de zona)**: redundância para cenários que exigem alta disponibilidade. Os dados são copiados de maneira síncrona em três zonas de disponibilidade do Azure na região primária.
- **Armazenamento com redundância geográfica (GRS)**: redundância entre regiões para proteger contra interrupções regionais. Os dados são copiados de maneira síncrona três vezes na região primária e copiados de maneira assíncrona para a região secundária. Para obter acesso de leitura aos dados na região secundária, habilite RA-GRS (armazenamento com redundância geográfica com acesso de leitura).
- **Armazenamento com redundância de zona geográfica (GZRS)**: redundância para cenários que exigem alta disponibilidade e durabilidade máxima. Os dados são copiados de maneira síncrona em três zonas de disponibilidade do Azure na região primária e copiados de maneira assíncrona para a região secundária. Para ter acesso de leitura aos dados na região secundária, habilite o RA-GZRS (armazenamento com redundância de zona geográfica com acesso de leitura).

Para obter mais informações sobre opções de redundância no Armazenamento do Azure, confira [Redundância do Armazenamento do Azure](storage-redundancy.md).

### <a name="general-purpose-v2-accounts"></a>Contas para uso geral v2

As contas de armazenamento para uso geral v2 são compatíveis com os recursos mais recentes do Armazenamento do Azure e incorporam todas as funcionalidades das contas de armazenamento de blobs e para uso geral v1. As contas de armazenamento para uso geral v2 têm os menores preços de capacidade por gigabyte para o Armazenamento do Azure, bem como os preços de transação competitivos no setor. As contas de armazenamento para uso geral v2 são compatíveis com estes serviços do Armazenamento do Azure:

- BLOBs (todos os tipos: bloco, acrescentar, página)
- Data Lake Gen2
- Arquivos
- Discos
- Filas
- Tabelas

> [!NOTE]
> A Microsoft recomenda o uso de contas de armazenamento para uso geral v2 na maioria dos cenários. É possível atualizar facilmente de uma conta de armazenamento de blobs ou para uso geral v1 para uma v2 sem tempo de inatividade e sem a necessidade de copiar dados.
>
> Para saber mais sobre o upgrade para uma conta para uso geral v2, confira [Atualizar para uma conta de armazenamento de para geral v2](storage-account-upgrade.md).

As contas de armazenamento para uso geral v2 oferecem várias camadas de acesso para armazenamento de dados com base nos seus padrões de uso. Para saber mais, confira [Camadas de acesso para dados de blob de blocos](#access-tiers-for-block-blob-data).

### <a name="general-purpose-v1-accounts"></a>Contas para uso geral v1

As contas de armazenamento v1 de uso geral fornecem acesso a todos os serviços de armazenamento do Azure, mas podem não ter os recursos mais recentes ou os preços mais baixos por gigabyte. As contas de armazenamento para uso geral v1 são compatíveis com estes serviços do Armazenamento do Azure:

- Blobs (todos os tipos)
- Arquivos
- Discos
- Filas
- Tabelas

A Microsoft recomenda contas v2 de uso geral para a maioria dos cenários. Você pode usar contas v1 de uso geral para estes cenários:

- O modelo de implantação clássico do Azure é obrigatório para os aplicativos. As contas para uso geral v2 e de Armazenamento de Blobs só são compatíveis com o modelo de implantação do Azure Resource Manager.

- Seus aplicativos têm uso intensivo de transações ou usam uma largura de banda de replicação geográfica significativa, mas não exigem grande capacidade. Nesse caso, o uso geral v1 pode ser a opção mais econômica.

- Você usa uma versão da [API REST dos serviços de armazenamento](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) que é anterior à 2014-02-14 ou uma biblioteca de cliente com uma versão inferior a 4. x. Não é possível atualizar seu aplicativo.

### <a name="blockblobstorage-accounts"></a>Contas do BlockBlobStorage

Uma conta do BlockBlobStorage é uma conta de armazenamento especializada no nível de desempenho premium para armazenar dados de objeto não estruturado como BLOBs de blocos ou BLOBs de acréscimo. Em comparação com contas de uso geral V2 e BlobStorage, as contas BlockBlobStorage fornecem latência baixa, consistente e taxas de transações mais altas.

As contas do BlockBlobStorage atualmente não dão suporte a camadas para camadas de acesso frequente, esporádica ou de arquivamento. Esse tipo de conta de armazenamento não dá suporte a blobs de página, tabelas ou filas.

### <a name="filestorage-accounts"></a>Contas de armazenamento de

Uma conta de armazenamento de arquivo é uma conta de armazenamento especializada usada para armazenar e criar compartilhamentos de arquivos premium. Esse tipo de conta de armazenamento dá suporte a arquivos, mas não a blobs de blocos, blobs de acréscimo, blobs de páginas, tabelas ou filas.

As contas de armazenamento de fileoferecem características dedicadas de desempenho exclusivas, como a intermitência de IOPS. Para obter mais informações sobre essas características, consulte a seção [camadas de armazenamento de compartilhamento](../files/storage-files-planning.md#storage-tiers) de arquivos do guia de planejamento de arquivos.

## <a name="naming-storage-accounts"></a>Nomear contas de armazenamento

Ao nomear sua conta de armazenamento, lembre-se dessas regras:

- Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres e podem conter apenas números e letras minúsculas.
- O nome da sua conta de armazenamento deve ser exclusivo no Azure. Duas contas de armazenamento não podem ter o mesmo nome.

## <a name="performance-tiers"></a>Níveis de desempenho

Dependendo do tipo de conta de armazenamento que você criar, você pode escolher entre as camadas de desempenho Standard e Premium. A tabela a seguir resume quais níveis de desempenho estão disponíveis para o tipo de conta de armazenamento.

| Tipo de conta de armazenamento | Níveis de desempenho compatíveis |
|--|--|
| Uso geral V2 | Standard, Premium<sup>1</sup> |
| Uso geral V1 | Standard, Premium<sup>1</sup> |
| BlockBlobStorage | Premium |
| FileStorage | Premium |
| BlobStorage | Standard |

<sup>1</sup> O desempenho premium para contas de uso geral V2 e V1 de finalidade geral está disponível somente para blob de disco e de página. O desempenho premium para blobs de blocos ou de acréscimo está disponível apenas para contas de BlockBlobStorage. O desempenho premium para arquivos está disponível apenas para contas de FileStorage.

### <a name="general-purpose-storage-accounts"></a>Contas de armazenamento de uso geral

As contas de armazenamento para uso geral podem ser configuradas para qualquer um dos seguintes níveis de desempenho:

- Um nível de desempenho padrão para armazenamento de blobs, arquivos, tabelas, filas e discos da máquina virtual do Azure. Para obter mais informações sobre metas de escalabilidade para contas de armazenamento padrão, consulte [metas de escalabilidade para contas de armazenamento padrão](scalability-targets-standard-account.md).
- Um nível de desempenho premium para armazenar discos de máquina virtual não gerenciados. A Microsoft recomenda o uso de discos gerenciados com máquinas virtuais do Azure em vez de discos não gerenciados. Para obter mais informações sobre metas de escalabilidade para o nível de desempenho premium, consulte [metas de escalabilidade para contas de armazenamento de blobs de páginas Premium](../blobs/scalability-targets-premium-page-blobs.md).

### <a name="blockblobstorage-storage-accounts"></a>Contas de armazenamento BlockBlobStorage

As contas de armazenamento BlockBlobStorage fornecem um nível de desempenho premium para armazenar blobs de bloco e blobs de acréscimo. Para obter mais informações, consulte [metas de escalabilidade para contas de armazenamento de blob de blocos Premium](../blobs/scalability-targets-premium-block-blobs.md).

### <a name="filestorage-storage-accounts"></a>Contas de armazenamento de armazenamento em

As contas de armazenamento de armazenamento de arquivo fornecem um nível de desempenho premium para compartilhamentos de arquivos do Azure. Para obter mais informações, confira [Metas de desempenho e escalabilidade de Arquivos do Azure](../files/storage-files-scale-targets.md).

## <a name="access-tiers-for-block-blob-data"></a>Camadas de acesso para dados de blob de blocos

O Armazenamento do Azure oferece diferentes opções para acessar dados de blob de blocos baseados em padrões de uso. Cada camada de acesso no Armazenamento do Azure é otimizada para um determinado padrão de uso de dados. Ao selecionar a camada de acesso correta para suas necessidades, é possível armazenar seus dados de blob de blocos da maneira mais econômica.

As camadas de acesso disponíveis são:

- A camada de acesso **quente** . Essa camada é otimizada para acesso frequente de objetos na conta de armazenamento. O acesso a dados na camada quente é mais econômico, enquanto os custos de armazenamento são mais altos. Por padrão, as novas contas de armazenamento são criadas na camada de acesso quente.
- A camada de acesso **fria** . Essa camada é otimizada para armazenar grandes quantidades de dados acessados com pouca frequência e armazenados por pelo menos 30 dias. O armazenamento de dados na camada fria é mais econômico, mas acessar esses dados pode ser mais caro do que acessar dados na camada quente.
- A camada **Arquivos**. Essa camada está disponível somente para BLOBs de blocos individuais. A camada de arquivamento é otimizada para dados que podem tolerar várias horas de latência de recuperação e que permanecerão na camada de arquivo por pelo menos 180 dias. A camada de arquivamento é a opção mais econômica para armazenar dados. No entanto, acessar esses dados é mais caro do que acessar dados em camadas quentes ou frias.

Se houver uma alteração no padrão de uso de seus dados, você poderá alternar entre essas camadas de acesso a qualquer momento. Para obter mais informações sobre as camadas de acesso, consulte [armazenamento de BLOBs do Azure: camadas de acesso quentes, frias e de arquivo](../blobs/storage-blob-storage-tiers.md).

A tabela a seguir mostra quais camadas de acesso estão disponíveis para BLOBs em cada tipo de conta de armazenamento.

| Tipo de conta de armazenamento | Camadas de acesso compatíveis |
|--|--|
| Uso geral V2 | Quente, frio, arquivo<sup>1</sup> |
| Uso geral V1 | N/D |
| BlockBlobStorage | N/D |
| FileStorage | N/D |
| BlobStorage | Quente, frio, arquivo<sup>1</sup> |

<sup>1</sup> o armazenamento de arquivo e a camada no nível do blob dão suporte apenas a blobs de blocos. A camada de Arquivos está disponível somente no nível de blob individual, e não no nível da conta de armazenamento. Para obter mais informações, consulte [camadas de acesso para armazenamento de BLOBs do Azure-quente, frio e arquivo](../blobs/storage-blob-storage-tiers.md).

> [!IMPORTANT]
> Alterar a camada de acesso para uma conta de armazenamento existente ou um blob pode resultar em cobranças adicionais. Para obter mais informações, consulte [cobrança da conta de armazenamento](#storage-account-billing).

## <a name="encryption"></a>Criptografia

Todos os dados da sua conta de armazenamento são criptografados no lado do serviço. Para saber mais sobre a criptografia, confira [Criptografia do Serviço de Armazenamento do Azure para dados em repouso](storage-service-encryption.md).

## <a name="storage-account-endpoints"></a>Pontos de extremidade da conta de armazenamento

Uma conta de armazenamento fornece um namespace exclusivo no Azure para seus dados. Todos os objetos armazenados no Armazenamento do Azure têm um endereço que inclui o nome exclusivo da conta. A combinação do nome da conta e do ponto de extremidade de serviço do Armazenamento do Azure forma os pontos de extremidade da sua conta de armazenamento.

A tabela a seguir lista os pontos de extremidade para cada um dos serviços de armazenamento do Azure.

| Serviço de armazenamento | Ponto de extremidade |
|--|--|
| Armazenamento de Blobs | `https://<storage-account>.blob.core.windows.net` |
| Azure Data Lake Storage Gen2 | `https://<storage-account>.dfs.core.windows.net` |
| Arquivos do Azure | `https://<storage-account>.file.core.windows.net` |
| Armazenamento de filas | `https://<storage-account>.queue.core.windows.net` |
| Armazenamento de tabela | `https://<storage-account>.table.core.windows.net` |

> [!NOTE]
> O bloco BLOB e as contas de armazenamento de blob expõem apenas o ponto de extremidade do serviço BLOB.

Construa a URL para acessar um objeto em uma conta de armazenamento acrescentando o local do objeto na conta de armazenamento ao ponto de extremidade. Por exemplo, um endereço de blob pode ter este formato: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

Também é possível configurar um domínio personalizado para blobs para usar com a conta de armazenamento. Para saber mais, confira [Configurar um nome de domínio personalizado para a conta do Armazenamento do Azure](../blobs/storage-custom-domain-name.md).  

## <a name="control-access-to-account-data"></a>Controlar o acesso aos dados da conta

Por padrão, os dados em sua conta estão disponíveis somente para você, o proprietário da conta. Você controla quem pode acessar os dados e quais permissões essas pessoas têm.

Todas as solicitações feitas na conta de armazenamento precisam ser autorizadas. No nível do serviço, a solicitação deve incluir um cabeçalho de *autorização* válido. Especificamente, esse cabeçalho inclui todas as informações necessárias para que o serviço valide a solicitação antes de executá-la.

É possível permitir acesso aos dados da conta de armazenamento usando uma das seguintes abordagens:

- **Azure Active Directory:** Use as credenciais do Azure Active Directory (AD do Azure) para autenticar um usuário, grupo ou outra identidade para acesso a dados de BLOB e de fila. Se a autenticação de uma identidade for bem-sucedida, o Azure AD retornará um token para usar ao autorizar a solicitação de acesso ao armazenamento de blobs ou de filas do Azure. Para saber mais, confira [Autenticar o acesso ao Armazenamento do Azure usando o Azure Active Directory](storage-auth-aad.md).
- **Autorização de chave compartilhada:** use a chave de acesso da conta de armazenamento para criar uma cadeia de conexão para o aplicativo usar em runtime para acessar o Armazenamento do Azure. Os valores na cadeia de conexão são usados para criar o cabeçalho de *autorização* passado para o Armazenamento do Azure. Para saber mais, confira [Configurar cadeias de conexão do Armazenamento do Azure](storage-configure-connection-string.md).
- **Assinatura de acesso compartilhado:** Uma SAS (assinatura de acesso compartilhado) é um token que permite o acesso delegado a recursos em sua conta de armazenamento. O token SAS encapsula todas as informações necessárias para autorizar uma solicitação para o armazenamento do Azure na URL. Ao criar uma SAS, você pode especificar quais permissões a SAS concede a um recurso e o intervalo sobre o qual as permissões são válidas. Um token SAS pode ser assinado com as credenciais do Azure AD ou com uma chave compartilhada. Para obter mais informações, consulte [conceder acesso limitado aos recursos de armazenamento do Azure usando assinaturas de acesso compartilhado (SAS)](storage-sas-overview.md).

> [!NOTE]
> Autenticar usuários ou aplicativos usando as credenciais do Azure AD oferece mais segurança e facilidade ao usar os meios de autorização. Enquanto você pode continuar a usar a autorização de chave compartilhada com seus aplicativos, usando o AD do Azure evita a necessidade de armazenar sua chave de acesso da conta com o seu código. Também é possível continuar a usar assinaturas de acesso compartilhado (SAS) para conceder acesso refinado a recursos em sua conta de armazenamento, mas o Azure AD oferece recursos semelhantes sem a necessidade de gerenciar tokens SAS ou se preocupar sobre revogar uma SAS comprometida.
>
> A Microsoft recomenda usar a autorização do Azure AD para seus aplicativos de BLOB e fila de armazenamento do Azure quando possível.

## <a name="copying-data-into-a-storage-account"></a>Copiar dados para uma conta de armazenamento

A Microsoft oferece utilitários e bibliotecas para importar dados de dispositivos de armazenamento local ou provedores de armazenamento em nuvem de terceiros. A solução que você usa depende da quantidade de dados que você está transferindo.

Ao atualizar uma conta para uso geral v1 para uma v2 ou de Armazenamento de Blobs, os dados são automaticamente migrados. A Microsoft recomenda seguir esse caminho para fazer upgrade da sua conta. No entanto, se você decidir mover dados de uma conta de uso geral v1 para uma conta de armazenamento de BLOBs, você migrará os dados manualmente, usando as ferramentas e bibliotecas descritas abaixo.

### <a name="azcopy"></a>AzCopy

O AzCopy é um utilitário de linha de comando do Windows desenvolvido para cópia de dados de alto desempenho para dentro e para fora do Armazenamento do Azure. É possível usar o AzCopy para copiar dados para uma conta de armazenamento de blobs de uma conta de armazenamento para uso geral existente ou carregar dados de dispositivos de armazenamento local. Para obter mais informações, consulte [transferir dados com o utilitário AzCopy Command-Line](./storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="data-movement-library"></a>Biblioteca de movimentação de dados

A biblioteca de movimentação de dados do Armazenamento do Azure para .NET baseia-se no framework de movimentação de dados de núcleo que habilita o AzCopy. A biblioteca foi projetada para operações de transferência de dados de alto desempenho, confiáveis e fáceis, de forma semelhante ao AzCopy. Você pode usar a biblioteca de movimentação de dados para aproveitar os recursos do AzCopy nativamente. Para obter mais informações, consulte [biblioteca de movimentação de dados do armazenamento do Azure para .net](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>API REST ou biblioteca de cliente

Você pode criar um aplicativo personalizado para migrar seus dados de uma conta de armazenamento v1 de uso geral para uma conta de armazenamento de BLOBs. Use uma das bibliotecas de cliente do Azure ou a API REST dos serviços de armazenamento do Azure. O Armazenamento do Azure fornece bibliotecas de cliente avançadas para várias linguagens e plataformas, como .NET, Java, C++, Node.JS, PHP, Ruby e Python. As bibliotecas de cliente oferecem recursos avançados, como lógica de recuperação, registro em log e carregamentos paralelos. Você também pode desenvolver diretamente na API REST, que pode ser chamada por qualquer linguagem que faça solicitações HTTP/HTTPS.

Para saber mais sobre a API REST do Armazenamento do Azure, confira a [Referência da API REST dos Serviços do Armazenamento do Azure](/rest/api/storageservices/).

> [!IMPORTANT]
> Blobs criptografados usando metadados relacionados à criptografia de armazenamento no lado do cliente com o blob. Se você copiar um blob que é criptografado com criptografia do lado do cliente, verifique se que a operação de cópia preserva os metadados de blob e especialmente os metadados relacionados à criptografia. Se você copiar um blob sem os metadados de criptografia, o conteúdo do blob não poderá ser recuperado novamente. Para obter mais detalhes sobre os metadados relacionados à criptografia, confira [Criptografia no Lado do Cliente do Armazenamento do Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="storage-account-billing"></a>Cobrança de conta de armazenamento

O armazenamento do Azure cobra com base no uso da sua conta de armazenamento. Todos os objetos em uma conta de armazenamento são cobrados juntos como um grupo. Os custos de armazenamento são calculados de acordo com os seguintes fatores:

- **Região** refere-se à região geográfica na qual sua conta está baseada.
- **Tipo de conta** refere-se ao tipo de conta de armazenamento que você está usando.
- **Camada de acesso** refere-se ao padrão de uso de dados que você especificou para sua conta de uso geral v2 ou de armazenamento de blobs.
- A **capacidade** refere-se à quantidade de alocação de sua conta de armazenamento que você está usando para armazenar dados.
- A **replicação** determina quantas cópias dos seus dados serão mantidas de uma só vez e em quais locais.
- As **transações** referem-se a todas as operações de leitura e gravação no Armazenamento do Microsoft Azure.
- A **saída de dados** refere-se a dados transferidos para fora de uma região do Azure. Quando os dados em sua conta de armazenamento são acessados por um aplicativo que não está sendo executado na mesma região, você é cobrado pela saída de dados. Para obter informações sobre como usar grupos de recursos para agrupar seus dados e serviços na mesma região para limitar os encargos de saída, consulte [O que é um grupo de recursos do Azure?](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group).

A página [Preços de Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) fornece informações detalhadas de preços com base no tipo de conta, capacidade de armazenamento, replicação e transações. A página [Detalhes de preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/) fornece informações detalhadas de preços para saída de dados. Você pode usar a [Calculadora de preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) para ajudar a estimar os custos.

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>Próximas etapas

- [Criar uma conta de armazenamento](storage-account-create.md)
- [Criar uma conta de armazenamento de blobs de blocos](../blobs/storage-blob-create-account-block-blob.md)
- [Atualizar para uma conta de armazenamento de uso geral v2](storage-account-upgrade.md)
- [Recuperar uma conta de armazenamento excluída](storage-account-recover.md)