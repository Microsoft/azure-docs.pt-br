---
title: 'Armazenamento: migrar Apache Hadoop locais para o Azure HDInsight'
description: Aprenda as práticas de armazenamento para migrar clusters do Hadoop locais para o Azure HDInsight.
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/10/2019
ms.openlocfilehash: 8d87d2164a5131b71a2000243c37553610497750
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944847"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight"></a>Migrar clusters de Apache Hadoop locais para o Azure HDInsight

Este artigo apresenta recomendações para o armazenamento de dados em sistemas do Azure HDInsight. Ele faz parte de uma série que fornece as melhores práticas para ajudar a migrar sistemas locais do Apache Hadoop para o Azure HDInsight.

## <a name="choose-right-storage-system-for-hdinsight-clusters"></a>Escolha o sistema de armazenamento adequado para clusters do HDInsight

A estrutura de diretórios do HDFS (Apache Hadoop File System) local pode ser recriada no armazenamento de BLOBs do Azure ou Azure Data Lake Storage. Você pode excluir com segurança os clusters do HDInsight usados para cálculo sem perder dados do usuário. Ambos os serviços podem ser usados como o sistema de arquivos padrão e um sistema de arquivos adicionais para um cluster do HDInsight. O cluster e a conta de armazenamento do HDInsight devem ser hospedados na mesma região.

### <a name="azure-storage"></a>Armazenamento do Azure

Os clusters do HDInsight podem usar o contêiner de blobs no Armazenamento do Azure como o sistema de arquivos padrão ou um sistema de arquivos adicional. A conta de armazenamento da camada Standard tem suporte para uso com clusters do HDInsight. Não há suporte para a camada Premier. O contêiner de blob padrão armazena informações específicas do cluster como logs e o histórico do trabalho. Não há suporte para o compartilhamento de um contêiner de blob como o sistema de arquivos padrão para vários clusters.

As contas de armazenamento definidas no processo de criação e suas respectivas chaves são armazenadas no `%HADOOP_HOME%/conf/core-site.xml` em nós de cluster. Também podem ser acessadas na seção "Site principal personalizado" na configuração do HDFS na interface do usuário do Ambari. A chave de conta de armazenamento é criptografada por padrão e um script personalizado de descriptografia é usado para descriptografar as chaves antes que elas sejam passadas para daemons do Hadoop. Os trabalhos incluindo Hive, MapReduce, streaming de Hadoop e Pig contêm uma descrição de contas de armazenamento e dos metadados com elas.

O armazenamento do Azure pode ser replicado geograficamente. Embora a replicação geográfica forneça redundância de dados e recuperação geográfica, um failover para a localização replicada geograficamente afetará seriamente o desempenho e poderá gerar custos adicionais. A recomendação é escolher a replicação geográfica com sabedoria e somente se o valor dos dados compensar o custo adicional.

Um dos formatos a seguir pode ser usado para acessar dados armazenados no Armazenamento do Azure:

|Formato de Acesso a Dados |Descrição |
|---|---|
|`wasb:///`|Acessar o armazenamento padrão usando comunicação não criptografada.|
|`wasbs:///`|Acessar o armazenamento padrão usando comunicação criptografada.|
|`wasb://<container-name>@<account-name>.blob.core.windows.net/`|Usado ao se comunicar com uma conta de armazenamento não padrão. |

[Metas de escalabilidade para contas de armazenamento Standard](../../storage/common/scalability-targets-standard-account.md) lista os limites atuais nas contas de armazenamento do Azure. Se as necessidades do aplicativo excederem as metas de escalabilidade de uma única conta de armazenamento, o aplicativo poderá ser criado para usar múltiplas contas de armazenamento e fazer o particionamento dos objetos de dados nessas contas de armazenamento.

[Análise de armazenamento do Azure](../../storage/common/storage-analytics.md) fornece métricas para todos os serviços de armazenamento e portal do Azure podem ser configuradas para coletar métricas a serem visualizadas por meio de gráficos. Alertas podem ser criados para notificar quando os limites forem atingidos para métricas de recursos de armazenamento.

O armazenamento do Azure oferece [exclusão reversível para objetos de blob](../../storage/blobs/soft-delete-blob-overview.md) para ajudar a recuperar dados quando ele é modificado acidentalmente ou excluído por um aplicativo ou outro usuário da conta de armazenamento.

Você pode criar [instantâneos de blob](/rest/api/storageservices/creating-a-snapshot-of-a-blob). Um instantâneo é uma versão somente leitura de um blob criada em um ponto no tempo e fornece uma maneira de fazer backup de um blob. Quando um instantâneo tiver sido criado, ele pode ser lido, copiado ou excluído, mas não modificado.

> [!Note]
> Para as versões mais antigas das Distribuições do Hadoop locais que não têm o certificado "wasbs", é preciso importá-las para o armazenamento de confiança do Java.

Os métodos a seguir podem ser usados para importar certificados para o repositório de confiança Java:

Baixar o certificado TLS/SSL do blob do Azure em um arquivo

```bash
echo -n | openssl s_client -connect <storage-account>.blob.core.windows.net:443 | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > Azure_Storage.cer
```

Importe o arquivo acima para o repositório de confiança Java em todos os nós

```bash
keytool -import -trustcacerts -keystore /path/to/jre/lib/security/cacerts -storepass changeit -noprompt -alias blobtrust -file Azure_Storage.cer
```

Verifique se o certificado adicionado está no repositório de confiança

```bash
keytool -list -v -keystore /path/to/jre/lib/security/cacerts
```

Para obter mais informações, consulte os seguintes artigos:

- [Usar o armazenamento do Azure com clusters do Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Metas de escalabilidade para contas de armazenamento Standard](../../storage/common/scalability-targets-standard-account.md)
- [Metas de escalabilidade e desempenho do Armazenamento de Blobs](../../storage/blobs/scalability-targets.md)
- [Lista de verificação de desempenho e escalabilidade do Armazenamento do Microsoft Azure](../../storage/blobs/storage-performance-checklist.md)
- [Monitoramento, diagnóstico e solução de problemas de Armazenamento do Microsoft Azure](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)
- [Monitorar uma conta de armazenamento no portal do Azure](../../storage/common/storage-monitor-storage-account.md)

### <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 implementa o modelo de controle de acesso de estilo de HDFS e POSIX. Ele fornece integração de primeira classe com o Azure AD para controle de acesso refinado. Não há limite ao tamanho dos dados que ele pode armazenar nem à sua capacidade de executar análises massivamente paralelas.

Para obter mais informações, consulte os seguintes artigos:

- [Criar clusters HDInsight com Data Lake Storage Gen1 usando o portal do Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
- [Usar Data Lake Storage Gen1 com clusters HDInsight do Azure](../hdinsight-hadoop-use-data-lake-storage-gen1.md)

### <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 é a oferta de armazenamento mais recente. Ele unifica os principais recursos da primeira geração de Azure Data Lake Storage Gen1 com um ponto de extremidade do sistema de arquivos compatível com Hadoop diretamente integrado ao armazenamento de BLOBs do Azure. Essa melhoria combina os benefícios de escala e custo do armazenamento de objeto com a confiabilidade e o desempenho normalmente associados apenas a sistemas de arquivos locais.

O Azure Data Lake Storage Gen 2 é criado com base no [armazenamento de BLOBs do Azure](../../storage/blobs/storage-blobs-introduction.md) e permite que você utilize a interface com os dados usando os paradigmas de armazenamento de objeto e de sistema de arquivos. Os recursos do [Azure data Lake Storage Gen1](../../data-lake-store/index.yml), como a semântica do sistema de arquivos, a segurança de nível de arquivo e a escala, são combinados com baixo custo, armazenamento em camadas, recursos de alta disponibilidade/recuperação de desastre e um grande ecossistema de SDK/ferramentas do [armazenamento de BLOBs do Azure](../../storage/blobs/storage-blobs-introduction.md). No Data Lake Storage Gen2, todas as qualidades de armazenamento de objetos permanecem enquanto se adicionam as vantagens de uma interface de sistema de arquivos otimizada para cargas de trabalho de análise.

Um recurso fundamental do Data Lake Storage Gen2 é a adição de um [namespace hierárquico](../../storage/blobs/data-lake-storage-namespace.md) ao serviço de armazenamento de BLOBs, que organiza objetos/arquivos em uma hierarquia de diretórios para acesso a dados de alto desempenho. A estrutura hierárquica permite que operações como renomear ou excluir um diretório sejam operações únicas de metadados atômicos no diretório, em vez de enumerar e processar todos os objetos que compartilham o prefixo de nome do diretório.

No passado, a análise baseada na nuvem tinha que se comprometer em áreas de desempenho, gerenciamento e segurança. Os principais recursos do ADLS (Azure Data Lake Storage) Gen2 são os seguintes:

- **Acesso compatível com Hadoop**: o Azure data Lake Storage Gen2 permite que você gerencie e acesse dados da mesma forma que faria com um [sistema de arquivos distribuído do Hadoop (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). O novo [Driver ABFS](../../storage/blobs/data-lake-storage-abfs-driver.md) está disponível em todos os ambientes de Apache Hadoop incluídos no [Azure HDInsight](../index.yml). Esse driver permite que você acesse dados armazenados no Data Lake Storage Gen2.

- **Um superconjunto de permissões POSIX**: o modelo de segurança para o Data Lake Gen2 é totalmente compatível com as permissões ACL e POSIX, juntamente com alguma granularidade extra específica para o Data Lake Storage Gen2. As configurações podem ser definidas por meio de ferramentas de administração ou por meio de estruturas, como Hive e Spark.

- **Econômico**: Data Lake Storage O Gen2 apresenta capacidade de armazenamento e transações de baixo custo. À medida que os dados são transferidos por meio de seu ciclo de vida completo, as taxas de cobrança mudam para minimizar os custos por meio de recursos internos, como o [ciclo de vida do armazenamento de BLOBs do Azure](../../storage/blobs/storage-lifecycle-management-concepts.md).

- **Funciona com ferramentas, estruturas e aplicativos de armazenamento do Blob**: Data Lake Storage O Gen2 continua a trabalhar com uma grande variedade de ferramentas, estruturas e aplicativos que existem hoje para o armazenamento do Blob.

- **Driver otimizado**: o ABFS (driver de sistema de arquivos de blob do Azure) é [otimizado especificamente](../../storage/blobs/data-lake-storage-abfs-driver.md) para análise de Big Data. As APIs REST correspondentes são exibidas por meio do ponto de extremidade DFS, dfs.core.windows.net.

Um dos formatos a seguir pode ser usado para acessar dados armazenados no ADLS Gen2:
- `abfs:///`: Acessar o Data Lake Storage padrão para o cluster.
- `abfs://file_system@account_name.dfs.core.windows.net`: Utilizado ao se comunicar com uma conta do Data Lake Storage não padrão.

Para obter mais informações, consulte os seguintes artigos:

- [Introdução ao Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md)
- [O driver do Sistema de Arquivos de Blobs do Azure (ABFS.md)](../../storage/blobs/data-lake-storage-abfs-driver.md)
- [Usar Gen2 de armazenamento do Azure Data Lake com clusters de HDInsight do Azure](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

## <a name="secure-azure-storage-keys-within-on-premises-hadoop-cluster-configuration"></a>Proteja as Chaves de Armazenamento do Microsoft Azure dentro de configuração de cluster de Hadoop local

As chaves de armazenamento do Azure que são adicionadas aos arquivos de configuração do Hadoop, estabelecem a conectividade entre o HDFS local e o armazenamento de BLOBs do Azure. Essas chaves podem ser protegidas criptografando-as com a estrutura do provedor de credenciais do Hadoop. Depois de criptografadas, podem ser armazenadas e acessadas com segurança.

**Para provisionar as credenciais:**

```bash
hadoop credential create fs.azure.account.key.account.blob.core.windows.net -value <storage key> -provider jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks/file
```

**Para adicionar o caminho do provedor acima ao core-site.xml ou à configuração do Ambari no site central personalizado:**

```xml
<property>
    <name>hadoop.security.credential.provider.path</name>
        <value>
        jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks
        </value>
    <description>Path to interrogate for protected credentials.</description>
</property>
```

> [!Note]
> A propriedade de caminho de provedor também pode ser adicionada à linha de comando distcp, em vez de armazenar a chave no nível do cluster no core-site.xml da seguinte maneira:

```bash
hadoop distcp -D hadoop.security.credential.provider.path=jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks /user/user1/ wasb:<//yourcontainer@youraccount.blob.core.windows.net/>user1
```

## <a name="restrict-azure-storage-data-access-using-sas"></a>Restringir o acesso a dados do armazenamento do Azure usando SAS

Por padrão, o HDInsight tem acesso completo aos dados nas contas de Armazenamento do Azure associadas ao cluster. SAS (Assinaturas de Acesso Compartilhado) no contêiner de blob pode ser usada para restringir o acesso aos dados, como fornecer aos usuários acesso somente leitura aos dados.

### <a name="using-the-sas-token-created-with-python"></a>Usando o token SAS criado com Python

1. Abra o arquivo [SASToken.py](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature/blob/master/Python/SASToken.py) e altere os valores a seguir:

    |Propriedade Token|Descrição|
    |---|---|
    |policy_name|O nome a ser usado para a política armazenada que será criada.|
    |storage_account_name|O nome da sua conta de armazenamento.|
    |storage_account_key|A chave da conta de armazenamento.|
    |storage_container_name|O contêiner na conta de armazenamento para o qual você deseja restringir o acesso.|
    |example_file_path|O caminho para um arquivo que é carregado no contêiner.|

2. O arquivo SASToken.py vem com as permissões `ContainerPermissions.READ + ContainerPermissions.LIST` e pode ser ajustado com base no caso de uso.

3. Execute o script da seguinte maneira: `python SASToken.py`

4. Isso exibirá o token SAS semelhante ao texto a seguir quando o script for concluído: `sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14`

5. Para limitar o acesso a um contêiner com Assinatura de Acesso Compartilhado, adicione uma entrada personalizada à configuração do site central para o cluster na propriedade Adicionar do site central de Configurações Avançadas Personalizadas do Ambari HDFS.

6. Use os valores a seguir para os campos **Chave** e **Valor**:

    **Chave**: `fs.azure.sas.YOURCONTAINER.YOURACCOUNT.blob.core.windows.net` **valor**: a chave SAS retornada pelo aplicativo Python da etapa 4 acima.

7. Clique no botão **Adicionar** para salvar essa chave e esse valor e clique no botão **Salvar** para salvar as alterações de configuração. Quando solicitado, adicione uma descrição da alteração ("adicionando acesso de armazenamento SAS", por exemplo) e, em seguida, clique em **salvar**.

8. Na interface do usuário da Web do Ambari, selecione HDFS na lista à esquerda e, em seguida, selecione **Reiniciar Todos os Afetados** na lista suspensa Ações de Serviço à direita. Quando solicitado, selecione **Confirmar Reiniciar Tudo**.

9. Repita esse processo para MapReduce2 e YARN.

Há três coisas importantes a serem lembradas sobre o uso de tokens SAS no Azure:

1. Quando tokens SAS são criados com permissões "READ + LIST", os usuários que acessam o contêiner de Blob com esse token SAS não poderão "gravar e excluir" dados. Os usuários que acessarem o contêiner de Blob com esse token SAS e tentarem realizar uma operação de gravação ou exclusão receberão uma mensagem como `"This request is not authorized to perform this operation"`.

2. Quando os tokens SAS são gerados com permissões `READ + LIST + WRITE` (para restringir `DELETE` apenas), comandos como `hadoop fs -put` primeiro gravam em um arquivo `\_COPYING\_` e, em seguida, tentam renomear o arquivo. A operação HDFS é mapeada para um `copy+delete` para WASB. Como a `DELETE` permissão não foi fornecida, o "Put" falharia. A operação `\_COPYING\_` é um recurso do Hadoop que se destina a fornecer algum controle de simultaneidade. Atualmente, não há como restringir apenas a operação "excluir" sem afetar as operações de "gravação" também.

3. Infelizmente, o provedor de credenciais do Hadoop e o provedor de chave de descriptografia (ShellDecryptionKeyProvider) atualmente não funcionam com os tokens SAS e, portanto, não podem ser protegidos contra visibilidade no momento.

Para obter mais informações, confira [Usar Assinaturas de Acesso Compartilhado do Armazenamento do Microsoft Azure para restringir o acesso a dados no HDInsight](../hdinsight-storage-sharedaccesssignature-permissions.md).

## <a name="use-data-encryption-and-replication"></a>Usar criptografia de dados e replicação

Todos os dados gravados no Armazenamento do Azure são criptografados automaticamente usando a [Criptografia do Serviço de Armazenamento (SSE)](../../storage/common/storage-service-encryption.md). Os dados na conta de armazenamento do Azure sempre são replicados para alta disponibilidade. Ao criar uma conta de armazenamento, deve selecionar uma das seguintes opções de replicação:

- [Armazenamento com redundância local (LRS)](../../storage/common/storage-redundancy.md#locally-redundant-storage)
- [ZRS (armazenamento com redundância de zona)](../../storage/common/storage-redundancy.md#zone-redundant-storage)
- [Armazenamento com redundância geográfica (GRS)](../../storage/common/storage-redundancy.md#geo-redundant-storage)
- [RA-GRS (armazenamento com redundância geográfica com acesso de leitura)](../../storage/common/storage-redundancy.md)

O armazenamento do Azure fornece LRS (armazenamento com redundância local), mas você também deve copiar dados críticos para outra conta de armazenamento do Azure em outra região com uma frequência alinhada às necessidades do plano de recuperação de desastre. Há métodos diferentes para copiar dados, incluindo [ADLCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md), [DistCp](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html), [Azure PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md)ou [Azure data Factory](../../data-factory/connector-azure-data-lake-store.md). Também é recomendável impor políticas de acesso para a conta de armazenamento do Azure para evitar a exclusão acidental.

Para obter mais informações, consulte os seguintes artigos:

- [Replicação do armazenamento do Azure](../../storage/common/storage-redundancy.md)
- [Diretrizes de desastre para Azure Data Lake Storage Gen1 (ADLS)](../../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

## <a name="attach-additional-azure-storage-accounts-to-cluster"></a>Anexar contas de armazenamento do Azure adicionais ao cluster

Durante o processo de criação do HDInsight, uma conta de armazenamento do Azure, Azure Data Lake Storage Gen1 ou Azure Data Lake Storage Gen2 é escolhida como o sistema de arquivos padrão. Além dessa conta de armazenamento padrão, é possível adicionar mais contas de armazenamento da mesma assinatura do Azure ou de diferentes assinaturas do Azure durante o processo de criação de cluster ou após a criação de um cluster.

A conta de armazenamento extra pode ser adicionada das seguintes maneiras:
- Site central Personalizado Avançado de Configuração do Ambari HDFS Adicione o Nome da Conta de armazenamento e digite Reiniciando os serviços
- Usando a [ação de Script](../hdinsight-hadoop-add-storage.md) passando o nome da conta de armazenamento e a chave

> [!Note]
> Em casos de uso válidos, os limites no armazenamento do Azure podem ser aumentados por meio de uma solicitação feita ao [suporte do Azure](https://azure.microsoft.com/support/faq/).

Para saber mais, confira [Adicionar outras contas de armazenamento ao HDInsight](../hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Próximas etapas

Leia o próximo artigo desta série: [práticas recomendadas de migração de dados para local para Azure HDInsight Hadoop migração](apache-hadoop-on-premises-migration-best-practices-data-migration.md).
