---
title: Migrar Azure Data Lake Storage de Gen1 para Gen2
description: Migre Azure Data Lake Storage de Gen1 para Gen2, que se baseia no armazenamento de BLOBs do Azure e fornece um conjunto de recursos dedicados à análise de Big Data.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 03/11/2020
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 875c046406b7989a39f09dde82640ed5567b6b3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101714860"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2"></a>Migrar Azure Data Lake Storage de Gen1 para Gen2

Você pode migrar seus dados, cargas de trabalho e aplicativos de Data Lake Storage Gen1 para Data Lake Storage Gen2.

O Azure Data Lake Storage Gen2 é criado no [armazenamento de BLOBs do Azure](storage-blobs-introduction.md) e fornece um conjunto de recursos dedicados à análise de Big Data. [Data Lake Storage Gen2](https://azure.microsoft.com/services/storage/data-lake-storage/) combina recursos de [Azure data Lake Storage Gen1](../../data-lake-store/index.yml), como a semântica do sistema de arquivos, o diretório e a segurança de nível de arquivo e a escala com armazenamento em camadas de baixo custo, de alta disponibilidade/recuperação de desastres do [armazenamento de BLOBs do Azure](storage-blobs-introduction.md).

> [!NOTE]
> Para facilitar a leitura, este artigo usa o termo *Gen1* para se referir a Azure data Lake Storage Gen1 e o termo *Gen2* para se referir a Azure data Lake Storage Gen2.

## <a name="recommended-approach"></a>Abordagem recomendada

Para migrar para o Gen2, recomendamos a seguinte abordagem.

: heavy_check_mark: etapa 1: avaliar a preparação

: heavy_check_mark: etapa 2: preparar para migrar

: heavy_check_mark: etapa 3: migrar cargas de trabalho de dados e aplicativos

: heavy_check_mark: etapa 4: transferência de Gen1 para Gen2

> [!NOTE]
> Gen1 e Gen2 são serviços diferentes, não há experiência de atualização in-loco, esforço de migração intencional necessário. 

### <a name="step-1-assess-readiness"></a>Etapa 1: avaliar a preparação

1. Saiba mais sobre a [oferta de data Lake Storage Gen2](https://azure.microsoft.com/services/storage/data-lake-storage/); são os benefícios, os custos e a arquitetura geral. 

2. [Compare os recursos](#gen1-gen2-feature-comparison) do Gen1 com os de Gen2. 

3. Examine uma lista de [problemas conhecidos](data-lake-storage-known-issues.md) para avaliar qualquer lacuna na funcionalidade.

4. O Gen2 dá suporte a recursos de armazenamento de BLOBs como [log de diagnóstico](../common/storage-analytics-logging.md), [camadas de acesso](storage-blob-storage-tiers.md)e [políticas de gerenciamento do ciclo de vida do armazenamento de BLOBs](storage-lifecycle-management-concepts.md) Se você for interessante ao usar qualquer um desses recursos, examine o [nível de suporte atual](./data-lake-storage-supported-blob-storage-features.md).

5. Examine o estado atual do [suporte de ecossistema do Azure](./data-lake-storage-multi-protocol-access.md) para garantir que o Gen2 dê suporte a todos os serviços dos quais suas soluções dependem.

### <a name="step-2-prepare-to-migrate"></a>Etapa 2: preparar-se para migrar

1. Identifique os conjuntos de dados que você migrará.

   Aproveite essa oportunidade para limpar os conjuntos de dados que você não usa mais. A menos que você planeje migrar todos os seus dados ao mesmo tempo, reserve esse tempo para identificar grupos lógicos de dados que podem ser migrados em fases.
   
2. Determine o impacto que uma migração terá em sua empresa.

   Por exemplo, considere se você pode arcar com qualquer tempo de inatividade enquanto a migração ocorre. Essas considerações podem ajudá-lo a identificar um padrão de migração adequado e a escolher as ferramentas mais apropriadas.

3. Crie um plano de migração. 

   Recomendamos esses [padrões de migração](#migration-patterns). Você pode escolher um desses padrões, combiná-los juntos ou criar um padrão personalizado por conta própria.

### <a name="step-3-migrate-data-workloads-and-applications"></a>Etapa 3: migrar dados, cargas de trabalho e aplicativos

Migre dados, cargas de trabalho e aplicativos usando o padrão que você preferir. Recomendamos que você valide cenários de forma incremental.

1. [Crie uma conta de armazenamento](../common/storage-account-create.md) e habilite o recurso de namespace hierárquico. 

2. Migre seus dados. 

3. Configure [serviços em suas cargas de trabalho](./data-lake-storage-supported-azure-services.md) para apontar para o ponto de extremidade do Gen2. 
   
4. Atualizar aplicativos para usar APIs do Gen2. Consulte guias para [.net](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [python](data-lake-storage-directory-file-acl-python.md), [JavaScript](data-lake-storage-directory-file-acl-javascript.md) e [REST](/rest/api/storageservices/data-lake-storage-gen2). 
   
5. Atualize os scripts para usar Data Lake Storage Gen2 [cmdlets do PowerShell](data-lake-storage-directory-file-acl-powershell.md)e [CLI do Azure comandos](data-lake-storage-directory-file-acl-cli.md).
   
6. Pesquise referências de URI que contenham a cadeia `adl://` de caracteres em arquivos de código ou em blocos de anotações do databricks, Apache Hive arquivos HQL ou qualquer outro arquivo usado como parte de suas cargas de trabalho. Substitua essas referências pelo [URI formatado Gen2](data-lake-storage-introduction-abfs-uri.md) da sua nova conta de armazenamento. Por exemplo: o URI Gen1: `adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile` pode se tornar `abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile` . 

7. Configure a segurança em sua conta para incluir [funções do Azure](../common/storage-auth-aad-rbac-portal.md), [segurança no nível de arquivo e pasta](data-lake-storage-access-control.md)e [firewalls de armazenamento do Azure e redes virtuais](../common/storage-network-security.md).

### <a name="step-4-cutover-from-gen1-to-gen2"></a>Etapa 4: transferência de Gen1 para Gen2

Depois de ter certeza de que seus aplicativos e cargas de trabalho estão estáveis no Gen2, você pode começar a usar o Gen2 para satisfazer seus cenários de negócios. Desative todos os pipelines restantes em execução no Gen1 e encerre sua conta do Gen1. 

<a id="gen1-gen2-feature-comparison"></a>

## <a name="gen1-vs-gen2-capabilities"></a>Recursos do Gen1 vs Gen2

Esta tabela compara os recursos de Gen1 com o de Gen2.

|Área |Gen1   |Gen2 |
|---|---|---|
|Organização de dados|[Namespace hierárquico](data-lake-storage-namespace.md)<br>Suporte a arquivos e pastas|[Namespace hierárquico](data-lake-storage-namespace.md)<br>Suporte a contêineres, arquivos e pastas |
|Redundância geográfica| [LRS](../common/storage-redundancy.md#locally-redundant-storage)| [LRS](../common/storage-redundancy.md#locally-redundant-storage), [ZRS](../common/storage-redundancy.md#zone-redundant-storage), [grs](../common/storage-redundancy.md#geo-redundant-storage), [ra-grs](../common/storage-redundancy.md#read-access-to-data-in-the-secondary-region) |
|Autenticação|[Identidade gerenciada do AAD](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Entidades de serviço](../../active-directory/develop/app-objects-and-service-principals.md)|[Identidade gerenciada do AAD](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Entidades de serviço](../../active-directory/develop/app-objects-and-service-principals.md)<br>[Chave de acesso compartilhado](/rest/api/storageservices/authorize-with-shared-key)|
|Autorização|Gerenciamento- [RBAC do Azure](../../role-based-access-control/overview.md)<br>Dados – [ACLs](data-lake-storage-access-control.md)|Gerenciamento – [RBAC do Azure](../../role-based-access-control/overview.md)<br>[ACLs](data-lake-storage-access-control.md)de dados, [RBAC do Azure](../../role-based-access-control/overview.md) |
|Criptografia – dados em repouso|Lado do servidor – com chaves [gerenciadas pela Microsoft](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ou [gerenciadas pelo cliente](../common/customer-managed-keys-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|Lado do servidor – com chaves [gerenciadas pela Microsoft](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ou [gerenciadas pelo cliente](../common/customer-managed-keys-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Suporte VNET|[Integração VNET](../../data-lake-store/data-lake-store-network-security.md)|[Pontos de extremidade de serviço](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [pontos de extremidade privados](../common/storage-private-endpoints.md)|
|Experiência do desenvolvedor|[REST](../../data-lake-store/data-lake-store-data-operations-rest-api.md), [.net](../../data-lake-store/data-lake-store-data-operations-net-sdk.md), [Java](../../data-lake-store/data-lake-store-get-started-java-sdk.md), [Python](../../data-lake-store/data-lake-store-data-operations-python.md), [PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md), [CLI do Azure](../../data-lake-store/data-lake-store-get-started-cli-2.0.md)|Disponibilidade geral – [REST](/rest/api/storageservices/data-lake-storage-gen2), [.net](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md)<br>Visualização pública- [JavaScript](data-lake-storage-directory-file-acl-javascript.md), [PowerShell](data-lake-storage-directory-file-acl-powershell.md), [CLI do Azure](data-lake-storage-directory-file-acl-cli.md)|
|Logs de recursos|Logs clássicos<br>[Azure Monitor integrado](../../data-lake-store/data-lake-store-diagnostic-logs.md)|[Logs clássicos](../common/storage-analytics-logging.md) – geralmente disponíveis<br>[Integrado Azure monitor](monitor-blob-storage.md) – versão prévia|
|Ecossistema|[HDInsight (3,6)](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md), [Azure Databricks (3,1 e posterior)](https://docs.databricks.com/data/data-sources/azure/azure-datalake.html), [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md), [ADF](../../data-factory/load-azure-data-lake-store.md)|[HDInsight (3,6, 4,0)](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md), [Azure Databricks (5,1 e superior)](/azure/databricks/data/data-sources/azure/azure-datalake-gen2), [Azure Synapse Analytics](../../azure-sql/database/vnet-service-endpoint-rule-overview.md), [ADF](../../data-factory/load-azure-data-lake-storage-gen2.md)|

<a id="migration-patterns"></a>

## <a name="gen1-to-gen2-patterns"></a>Padrões de Gen1 para Gen2

Escolha um padrão de migração e, em seguida, modifique esse padrão conforme necessário.

|||
|---|---|
|**Comparação de precisão e deslocamento**|O padrão mais simples. Ideal se seus pipelines de dados puderem ser tempo de inatividade.|
|**Cópia incremental**|Semelhante a *move e Shift*, mas com menos tempo de inatividade. Ideal para grandes quantidades de dados que levam mais tempo para serem copiados.|
|**Pipeline duplo**|Ideal para pipelines que não podem arcar com nenhum tempo de inatividade.|
|**Sincronização bidirecional**|Semelhante ao *pipeline duplo*, mas com uma abordagem mais em fases adequada para pipelines mais complicados.|

Vamos examinar mais de perto cada padrão.
 
### <a name="lift-and-shift-pattern"></a>Padrão de comparação de precisão e deslocamento

Esse é o padrão mais simples. 

1. Interrompa todas as gravações no Gen1.

2. Mover dados de Gen1 para Gen2. Recomendamos [Azure data Factory](../../data-factory/connector-azure-data-lake-storage.md). As ACLs são copiadas com os dados.

3. Operações de ingestão de ponto e cargas de trabalho para Gen2.

4. Descomissionamento de Gen1.

Confira nosso código de exemplo para o padrão de comparação de precisão e deslocamento em nosso exemplo de migração de comparação de [precisão e deslocamento](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Lift%20and%20Shift/README.md).

> [!div class="mx-imgBorder"]
> ![padrão de comparação de precisão e deslocamento](./media/data-lake-storage-migrate-gen1-to-gen2/lift-and-shift.png)

#### <a name="considerations-for-using-the-lift-and-shift-pattern"></a>Considerações sobre o uso do padrão de comparação de precisão e deslocamento

: heavy_check_mark: transferência de Gen1 para Gen2 para todas as cargas de trabalho ao mesmo tempo.

: heavy_check_mark: tempo de inatividade esperado durante a migração e o período de transferência.

: heavy_check_mark: ideal para pipelines que podem arcar com tempo de inatividade e todos os aplicativos podem ser atualizados ao mesmo tempo.

### <a name="incremental-copy-pattern"></a>Padrão de cópia incremental

1. Comece a mover dados de Gen1 para Gen2. Recomendamos [Azure data Factory](../../data-factory/connector-azure-data-lake-storage.md). As ACLs são copiadas com os dados.

2. Copie de forma incremental os novos dados do Gen1.

3. Depois que todos os dados forem copiados, interrompa todas as gravações para Gen1 e aponte as cargas de trabalho para Gen2.

4. Descomissionamento de Gen1.

Confira nosso código de exemplo para o padrão de cópia incremental em nosso [exemplo de migração de cópia incremental](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Incremental/README.md).


> [!div class="mx-imgBorder"]
> ![Padrão de cópia incremental](./media/data-lake-storage-migrate-gen1-to-gen2/incremental-copy.png)

#### <a name="considerations-for-using-the-incremental-copy-pattern"></a>Considerações sobre o uso do padrão de cópia incremental:

: heavy_check_mark: transferência de Gen1 para Gen2 para todas as cargas de trabalho ao mesmo tempo.

: heavy_check_mark: tempo de inatividade esperado somente durante o período de transferência.

: heavy_check_mark: ideal para pipelines em que todos os aplicativos são atualizados ao mesmo tempo, mas a cópia de dados requer mais tempo.

### <a name="dual-pipeline-pattern"></a>Padrão de pipeline duplo

1. Mover dados de Gen1 para Gen2. Recomendamos [Azure data Factory](../../data-factory/connector-azure-data-lake-storage.md). As ACLs são copiadas com os dados.

2. Ingerir novos dados para Gen1 e Gen2.

3. Apontar cargas de trabalho para Gen2.

4. Interrompa todas as gravações para Gen1 e, em seguida, encerre o Gen1.

Confira nosso código de exemplo para o padrão de pipeline duplo em nosso [exemplo de migração de pipeline duplo](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Dual%20pipeline/README.md).

> [!div class="mx-imgBorder"]
> ![Padrão de pipeline duplo](./media/data-lake-storage-migrate-gen1-to-gen2/dual-pipeline.png)

#### <a name="considerations-for-using-the-dual-pipeline-pattern"></a>Considerações sobre o uso do padrão de pipeline duplo:

: heavy_check_mark: os pipelines Gen1 e Gen2 são executados lado a lado.

: heavy_check_mark: dá suporte a zero tempo de inatividade.

: heavy_check_mark: ideal em situações em que suas cargas de trabalho e aplicativos não podem arcar com nenhum tempo de inatividade e você pode ingerir em ambas as contas de armazenamento.

### <a name="bi-directional-sync-pattern"></a>Padrão de sincronização bidirecional

1. Configure a replicação bidirecional entre Gen1 e Gen2. Recomendamos [WanDisco](https://docs.wandisco.com/bigdata/wdfusion/adls/). Ele oferece um recurso de reparo para os dados existentes.

3. Quando todas as movimentações forem concluídas, interrompa todas as gravações para Gen1 e desative a replicação bidirecional.

4. Descomissionamento de Gen1.

Confira nosso código de exemplo para o padrão de sincronização bidirecional em nosso [exemplo de migração bidirecional de sincronização](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Bi-directional/README.md).

> [!div class="mx-imgBorder"]
> ![Padrão bidirecional](./media/data-lake-storage-migrate-gen1-to-gen2/bidirectional-sync.png)

#### <a name="considerations-for-using-the-bi-directional-sync-pattern"></a>Considerações sobre o uso do padrão de sincronização bidirecional:

: heavy_check_mark: ideal para cenários complexos que envolvem um grande número de pipelines e dependências em que uma abordagem em fases pode fazer mais sentido.  

: heavy_check_mark: o esforço de migração é alto, mas fornece suporte lado a lado para Gen1 e Gen2.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as várias partes da configuração de segurança para uma conta de armazenamento. Consulte o [Guia de segurança do armazenamento do Azure](./security-recommendations.md).
- Otimize o desempenho para seu Data Lake Store. Consulte [otimizar Azure data Lake Storage Gen2 para desempenho](data-lake-storage-performance-tuning-guidance.md)
- Examine as práticas recomendadas para gerenciar seu Data Lake Store. Consulte [as práticas recomendadas para usar Azure data Lake Storage Gen2](data-lake-storage-best-practices.md)