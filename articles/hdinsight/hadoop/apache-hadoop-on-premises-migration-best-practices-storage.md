---
title: Migrar clusters do Apache Hadoop local para o Azure HDInsight – melhores práticas de armazenamento
description: Aprenda as práticas de armazenamento para migrar clusters do Hadoop locais para o Azure HDInsight.
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: c62a5384edf66fd9309bc7afcb50ada48e3fca7d
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095267"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---storage-best-practices"></a>Migrar clusters do Apache Hadoop local para o Azure HDInsight – melhores práticas de armazenamento

Este artigo apresenta recomendações para o armazenamento de dados em sistemas do Azure HDInsight. Ele faz parte de uma série que fornece as melhores práticas para ajudar a migrar sistemas locais do Apache Hadoop para o Azure HDInsight.

## <a name="choose-right-storage-system-for-hdinsight-clusters"></a>Escolha o sistema de armazenamento adequado para clusters do HDInsight

A estrutura do diretório do HDFS (Sistema de Arquivos do Apache Hadoop) local pode ser criada novamente no armazenamento do Azure ou no Azure Data Lake Storage. Você pode excluir com segurança os clusters do HDInsight usados para cálculo sem perder dados do usuário. Ambos os serviços podem ser usados como o sistema de arquivos padrão e um sistema de arquivos adicionais para um cluster do HDInsight. O cluster e a conta de armazenamento do HDInsight devem ser hospedados na mesma região.

### <a name="azure-storage"></a>Armazenamento do Azure

Os clusters do HDInsight podem usar o contêiner de blobs no Armazenamento do Azure como o sistema de arquivos padrão ou um sistema de arquivos adicional. A conta de armazenamento da camada Standard tem suporte para uso com clusters do HDInsight. A camada Premier não é compatível. O contêiner de blob padrão armazena informações específicas do cluster como logs e o histórico do trabalho. Não há suporte para o compartilhamento de um contêiner de blobs como o sistema de arquivos padrão para vários clusters.

As contas de armazenamento definidas no processo de criação e suas respectivas chaves são armazenadas no `%HADOOP_HOME%/conf/core-site.xml` em nós de cluster. Também podem ser acessadas na seção "Site principal personalizado" na configuração do HDFS na interface do usuário do Ambari. A chave de conta de armazenamento é criptografada por padrão e um script personalizado de descriptografia é usado para descriptografar as chaves antes que elas sejam passadas para daemons do Hadoop. Os trabalhos incluindo Hive, MapReduce, streaming de Hadoop e Pig contêm uma descrição de contas de armazenamento e dos metadados com elas.

O armazenamento do Azure pode ser replicado geograficamente. Embora a replicação geográfica forneça redundância de dados e recuperação geográfica, um failover para a localização replicada geograficamente afetará seriamente o desempenho e poderá gerar custos adicionais. A recomendação é escolher a replicação geográfica com sabedoria e somente se o valor dos dados compensar o custo adicional.

Um dos formatos a seguir pode ser usado para acessar dados armazenados no Armazenamento do Azure:

|Formato de Acesso a Dados |DESCRIÇÃO |
|---|---|
|`wasb:///`|Acessar o armazenamento padrão usando comunicação não criptografada.|
|`wasbs:///`|Acessar o armazenamento padrão usando comunicação criptografada.|
|`wasb://<container-name>@<account-name>.blob.core.windows.net/`|Usado ao se comunicar com uma conta de armazenamento não padrão. |


[Metas de Desempenho e Escalabilidade do Armazenamento do Azure](../../storage/common/storage-scalability-targets.md) lista os limites atuais em contas de Armazenamento do Azure. Se as necessidades do aplicativo excederem as metas de escalabilidade de uma única conta de armazenamento, o aplicativo poderá ser criado para usar múltiplas contas de armazenamento e fazer o particionamento dos objetos de dados nessas contas de armazenamento.

[Análise de Armazenamento do Azure](../../storage/storage-analytics.md) fornece métricas para todos os serviços de armazenamento e o portal do Azure pode ser configurado para coletar métricas a serem visualizadas por meio de gráficos. Alertas podem ser criados para notificar quando os limites forem atingidos para métricas de recursos de armazenamento.

O Armazenamento do Azure oferece [exclusão reversível para objetos de blob](../../storage/blobs/storage-blob-soft-delete.md) para ajudar a recuperar dados quando eles forem modificados ou excluídos acidentalmente por um aplicativo ou outro usuário da conta de armazenamento.

Você pode criar [instantâneos de blob](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob). Um instantâneo é uma versão somente leitura de um blob criada em um ponto no tempo e fornece uma maneira de fazer backup de um blob. Quando um instantâneo tiver sido criado, ele pode ser lido, copiado ou excluído, mas não modificado.

> [!Note]
> Para as versões mais antigas das Distribuições do Hadoop locais que não têm o certificado "wasbs", é preciso importá-las para o armazenamento de confiança do Java.

Os métodos a seguir podem ser usados para importar certificados para o repositório de confiança Java:

Baixar o certificado SSL do Blob do Azure para um arquivo

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

- [Usar o Armazenamento do Azure com clusters do Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Metas de desempenho e escalabilidade do Armazenamento do Azure](../../storage/common/storage-scalability-targets.md)
- [Lista de verificação de desempenho e escalabilidade do Armazenamento do Microsoft Azure](../../storage/common/storage-performance-checklist.md)
- [Monitoramento, diagnóstico e solução de problemas de Armazenamento do Microsoft Azure](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)
- [Monitorar uma conta de armazenamento no portal do Azure](../../storage/common/storage-monitor-storage-account.md)

### <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

O Azure Data Lake Storage implementa o modelo de controle de acesso estilo HDFS e POSIX. Ele fornece integração de primeira classe com o AAD para o controle de acesso refinado. Não há limite ao tamanho dos dados que ele pode armazenar nem à sua capacidade de executar análises massivamente paralelas.

Para obter mais informações, consulte os seguintes artigos:

- [Criar clusters HDInsight com o Data Lake Storage usando o portal do Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
- [Usar o Data Lake Storage com clusters Azure HDInsight](../hdinsight-hadoop-use-data-lake-store.md)

### <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

Azure Data Lake armazenamento Gen2 é o armazenamento mais recente oferta. Ele unifica os principais recursos do Azure Data Lake Storage de primeira geração com um ponto de extremidade do sistema de arquivos compatível com Hadoop integrado diretamente ao Armazenamento de Blobs do Azure. Essa melhoria combina os benefícios de escala e custo do armazenamento de objeto com a confiabilidade e o desempenho normalmente associados apenas a sistemas de arquivos locais.

O ADLS Gen 2 baseia-se na parte superior do [Armazenamento de Blobs do Azure](../../storage/blobs/storage-blobs-introduction.md) e permite que você faça interface com os dados usando os paradigmas de armazenamento de objeto e sistema de arquivos. Os recursos do [Azure Data Lake Storage Gen1](../../data-lake-store/index.md), como semântica do sistema de arquivos, segurança e escala em nível de arquivo, são combinados com armazenamento em camadas de baixo custo, alta disponibilidade/recursos de recuperação de desastre e um grande SDK/ecossistema de ferramentas do [Armazenamento de Blobs do Azure](../../storage/blobs/storage-blobs-introduction.md). No Data Lake Storage Gen2, todas as qualidades de armazenamento de objetos permanecem enquanto se adicionam as vantagens de uma interface de sistema de arquivos otimizada para cargas de trabalho de análise.

Um recurso fundamental do Data Lake Storage Gen2 é a adição de um [namespace hierárquico](../../storage/data-lake-storage/namespace.md) ao serviço de Armazenamento de Blobs, que organiza objetos/arquivos em uma hierarquia de diretórios para acesso a dados de alto desempenho. A estrutura hierárquica permite que operações como renomear ou excluir um diretório sejam operações únicas de metadados atômicos no diretório, em vez de enumerar e processar todos os objetos que compartilham o prefixo de nome do diretório.

No passado, a análise baseada na nuvem tinha que se comprometer em áreas de desempenho, gerenciamento e segurança. Os principais recursos do ADLS (Azure Data Lake Storage) Gen2 são os seguintes:

- **Acesso compatível com Hadoop**: O Azure Data Lake Storage Gen2 permite gerenciar e acessar dados como faria com um  [HDFS (Sistema de Arquivos Distribuído) do Hadoop](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). O novo [driver ABFS](../../storage/data-lake-storage/abfs-driver.md) está disponível em todos os ambientes Apache Hadoop incluídos no [Azure HDInsight](../index.yml). Esse driver permite que você acesse dados armazenados no Data Lake Storage Gen2.

- **Um superconjunto de permissões POSIX**: O modelo de segurança do Azure Data Lake Gen2 dá suporte pleno a permissões POSIX e ACL juntamente com alguma granularidade adicional específica para o Azure Data Lake Storage Gen2. As configurações podem ser definidas por meio de ferramentas de administração ou por meio de estruturas, como Hive e Spark.

- **Econômico**: Azure Data Lake Storage Gen2 apresenta capacidade de armazenamento e transações de baixo custo. Conforme os dados fazem a transição em todo o ciclo de vida, as taxas de cobrança mudam para minimizar os custos por meio de recursos internos, como [Ciclo de vida de Armazenamento de Blobs do Azure](../../storage/common/storage-lifecycle-management-concepts.md).

- **Funciona com aplicativos, estruturas e ferramentas de armazenamento de Blob**: O Data Lake Storage Gen2 continua funcionando com uma ampla matriz de ferramentas, estruturas e aplicativos que atualmente existem para armazenamento de Blobs.

- **Driver otimizado**: O driver ABFS (Sistema de Arquivos de Blob do Azure) é [otimizado especificamente](../../storage/data-lake-storage/abfs-driver.md) para análise de Big Data. As APIs REST correspondentes são exibidas por meio do ponto de extremidade DFS, dfs.core.windows.net.

Um dos formatos a seguir pode ser usado para acessar dados armazenados no ADLS Gen2:
- `abfs:///`: Acessar o Data Lake Storage padrão para o cluster.
- `abfs[s]://file_system@account_name.dfs.core.windows.net`: Utilizado ao se comunicar com uma conta do Data Lake Storage não padrão.

Para obter mais informações, consulte os seguintes artigos:

- [Introdução ao Azure Data Lake Storage Gen2](../../storage/data-lake-storage/introduction.md)
- [O driver do Sistema de Arquivos de Blobs do Azure (ABFS.md)](../../storage/data-lake-storage/abfs-driver.md)
- [Usar Gen2 de armazenamento do Azure Data Lake com clusters de HDInsight do Azure](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

## <a name="secure-azure-storage-keys-within-on-premises-hadoop-cluster-configuration"></a>Proteja as Chaves de Armazenamento do Microsoft Azure dentro de configuração de cluster de Hadoop local

As chaves de armazenamento do Azure adicionadas aos arquivos de configuração do Hadoop estabelecem a conectividade entre o Armazenamento de Blobs do Azure e o HDFS local. Essas chaves podem ser protegidas criptografando-as com a estrutura do provedor de credenciais do Hadoop. Depois de criptografadas, podem ser armazenadas e acessadas com segurança.

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
hadoop distcp -D hadoop.security.credential.provider.path=jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks /user/user1/ wasb:<//yourcontainer@youraccount.blob.core.windows.net/>user1
```

## <a name="restrict-azure-storage-data-access-using-sas"></a>Restringir o acesso de dados do Armazenamento do Azure usando SAS

Por padrão, o HDInsight tem acesso completo aos dados nas contas de Armazenamento do Azure associadas ao cluster. SAS (Assinaturas de Acesso Compartilhado) no contêiner de blob pode ser usada para restringir o acesso aos dados, como fornecer aos usuários acesso somente leitura aos dados.

### <a name="using-the-sas-token-created-with-python"></a>Usando o token SAS criado com Python

1. Abra o arquivo [SASToken.py](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature/blob/master/Python/SASToken.py) e altere os valores a seguir:

    |Propriedade Token|DESCRIÇÃO|
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

    **Chave**: `fs.azure.sas.YOURCONTAINER.YOURACCOUNT.blob.core.windows.net` **Valor**: A SAS KEY retornada pela etapa 4 FROM do aplicativo Python acima.

7. Clique no botão **Adicionar** para salvar essa chave e esse valor e clique no botão **Salvar** para salvar as alterações de configuração. Quando solicitado, adicione uma descrição da alteração ("adicionando acesso de armazenamento SAS", por exemplo) e clique em **Salvar**.

8. Na interface do usuário da Web do Ambari, selecione HDFS na lista à esquerda e, em seguida, selecione **Reiniciar Todos os Afetados** na lista suspensa Ações de Serviço à direita. Quando solicitado, selecione **Confirmar Reiniciar Tudo**.

9. Repita esse processo para MapReduce2 e YARN.

Há três pontos importantes a lembrar sobre o uso de Tokens de SAS no Azure:

1. Quando tokens SAS são criados com permissões "READ + LIST", os usuários que acessam o contêiner de Blob com esse token SAS não poderão "gravar e excluir" dados. Os usuários que acessarem o contêiner de Blob com esse token SAS e tentarem realizar uma operação de gravação ou exclusão receberão uma mensagem como `"This request is not authorized to perform this operation"`.

2. Quando os tokens SAS são gerados com permissões `READ + LIST + WRITE` (para restringir `DELETE` apenas), comandos como `hadoop fs -put` primeiro gravam em um arquivo `\_COPYING\_` e, em seguida, tentam renomear o arquivo. A operação HDFS é mapeada para um `copy+delete` para WASB. Uma vez que a permissão `DELETE` não foi fornecida, "put" falhará. A operação `\_COPYING\_` é um recurso do Hadoop que se destina a fornecer algum controle de simultaneidade. No momento, não há nenhuma maneira de restringir apenas a operação "DELETE" sem afetar as operações "WRITE" também.

3. Infelizmente, o provedor de credenciais do hadoop e o provedor da chave de descriptografia (ShellDecryptionKeyProvider) atualmente não funcionam com os tokens SAS, assim, atualmente não podem ser protegidos contra a visibilidade.

Para obter mais informações, confira [Usar Assinaturas de Acesso Compartilhado do Armazenamento do Microsoft Azure para restringir o acesso a dados no HDInsight](../hdinsight-storage-sharedaccesssignature-permissions.md).

## <a name="use-data-encryption-and-replication"></a>Usar criptografia de dados e replicação

Todos os dados gravados no Armazenamento do Azure são criptografados automaticamente usando a [SSE (Criptografia do Serviço de Armazenamento)](../../storage/common/storage-service-encryption.md). Os dados na conta de Armazenamento do Azure sempre são replicados para alta disponibilidade. Ao criar uma conta de armazenamento, deve selecionar uma das seguintes opções de replicação:

- [Armazenamento com redundância local (LRS)](../../storage/common/storage-redundancy-lrs.md)
- [Armazenamento com redundância de zona (ZRS)](../../storage/common/storage-redundancy-zrs.md)
- [Armazenamento com redundância geográfica (GRS)](../../storage/common/storage-redundancy-grs.md)
- [Armazenamento com redundância geográfica com acesso de leitura (RA-GRS)](../../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)

O Azure Data Lake Storage fornece LRS (armazenamento com redundância local), mas você também deve copiar dados críticos para outra conta do Data Lake Storage em outra região com uma frequência alinhada às necessidades do plano de recuperação de desastres. Há uma variedade de métodos para copiar dados, incluindo [ADLCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md), DistCp, [Azure PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md) ou [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md). Também é recomendável impor políticas de acesso para a conta do Data Lake Storage para evitar exclusão acidental.

Para obter mais informações, consulte os seguintes artigos:

- [Replicação do Armazenamento do Azure](../../storage/common/storage-redundancy.md)
- [Orientação sobre desastres para o ADLS (Azure Data Lake Storage)](../../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

## <a name="attach-additional-azure-storage-accounts-to-cluster"></a>Anexar contas de Armazenamento do Azure adicionais ao cluster

Durante o processo de criação do HDInsight, uma conta de Armazenamento do Azure ou a conta do Azure Data Lake Storage é escolhida como o sistema de arquivos padrão. Além dessa conta de armazenamento padrão, é possível adicionar mais contas de armazenamento da mesma assinatura do Azure ou de diferentes assinaturas do Azure durante o processo de criação de cluster ou após a criação de um cluster.

A conta de armazenamento extra pode ser adicionada das seguintes maneiras:
- Site central Personalizado Avançado de Configuração do Ambari HDFS Adicione o Nome da Conta de armazenamento e digite Reiniciando os serviços
- Usando a [ação de Script](../hdinsight-hadoop-add-storage.md) passando o nome da conta de armazenamento e a chave

> [!Note]
> Em casos de uso válidos, os limites de Armazenamento do Azure podem ser aumentados por meio de uma solicitação feita ao [Suporte do Azure](https://azure.microsoft.com/support/faq/).

Para obter mais informações, consulte os seguintes artigos:
- [Adicionar outras contas de armazenamento ao HDInsight](../hdinsight-hadoop-add-storage.md)

## <a name="next-steps"></a>Próximas etapas

Leia o próximo artigo desta série:

- [Melhores práticas de migração de dados para migração do local para o Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-data-migration.md)
