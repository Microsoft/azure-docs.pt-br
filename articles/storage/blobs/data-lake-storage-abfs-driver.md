---
title: Driver do Sistema de Arquivos de Blobs do Azure para o Azure Data Lake Storage Gen2
description: Driver de sistema de arquivos ABFS do Hadoop
services: storage
author: jamesbak
ms.topic: conceptual
ms.author: jamesbak
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 83e2f6f42de5c729667f366a6e068f1c8bd71f02
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60708610"
---
# <a name="the-azure-blob-filesystem-driver-abfs-a-dedicated-azure-storage-driver-for-hadoop"></a>O driver do Sistema de Arquivos de Blobs do Azure (ABFS): Um driver de Armazenamento do Microsoft Azure dedicado para Hadoop

Um dos principais métodos de acesso para dados no Azure Data Lake Storage Gen2 é via [Sistema de Arquivos Hadoop](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/index.html). O Data Lake Storage Gen2 permite que os usuários do Armazenamento de Blobs do Azure acessem um novo driver, o driver do Sistema de Arquivos de Blobs do Azure ou `ABFS`. O ABFS faz parte do Apache Hadoop e está incluído em muitas das distribuições comerciais do Hadoop. Usando esse driver, muitos aplicativos e estruturas podem acessar dados no armazenamento de Blobs do Azure sem nenhum código referenciando explicitamente o serviço Data Lake Store Gen2.

## <a name="prior-capability-the-windows-azure-storage-blob-driver"></a>Funcionalidade anterior: Driver do Azure Storage Blob do Windows

O driver de Blob de Armazenamento do Microsoft Azure ou [driver WASB](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) forneceu suporte original para o Armazenamento de Blobs do Azure. Esse driver executou a tarefa complexa de mapear a semântica do sistema de arquivos (conforme requerido pela interface do Hadoop FileSystem) para aquela da interface de estilo de armazenamento de objeto exposta pelo Armazenamento de Blobs. Esse driver continua dando suporte a esse modelo, fornecendo acesso de alto desempenho aos dados armazenados nos Blobs, mas contém uma quantidade significativa de código que realiza esse mapeamento, dificultando a manutenção. Além disso, algumas operações como [FileSystem.rename()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_renamePath_src_Path_d) e [FileSystem.delete()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_deletePath_p_boolean_recursive) quando aplicadas a diretórios exigem que o driver realize um vasto número de operações (devido à falta de suporte para diretórios), o que geralmente leva a um desempenho degradado. O driver ABFS foi projetado para superar as deficiências inerentes de WASB.

## <a name="the-azure-blob-file-system-driver"></a>Driver de Sistema de Arquivos de Blob do Azure

A [interface REST do Azure Data Lake Store](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) é projetada para dar suporte à semântica do sistema de arquivos no Armazenamento de Blobs. Como o Hadoop FileSystem também é projetado para dar suporte à mesma semântica, não há necessidade de um mapeamento complexo no driver. Portanto, o driver de sistema de Arquivos de Blob do Azure (ou ABFS) é um shim de cliente simples para a API REST.

No entanto, há algumas funções que o driver ainda deve executar:

### <a name="uri-scheme-to-reference-data"></a>Esquema de URI para referência de dados

Consistente com outras implementações do FileSystem no Hadoop, o driver ABFS define o próprio esquema de URI para que os recursos (diretórios e arquivos) possam ser abordados de maneira distinta. O esquema de URI está documentado em [Usar o URI do Azure Data Lake Store Gen2](./data-lake-storage-introduction-abfs-uri.md). A estrutura do URI é: `abfs[s]://file_system@account_name.dfs.core.windows.net/<path>/<path>/<file_name>`

Usando o formato de URI acima, as ferramentas e estruturas padrão do Hadoop podem ser utilizadas para referenciar esses recursos:

```bash
hdfs dfs -mkdir -p abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data 
hdfs dfs -put flight_delays.csv abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data/ 
```

Internamente, o driver ABFS converte os recursos especificados no URI em arquivos e diretórios e faz chamadas à API REST do Azure Data Lake Store com essas referências.

### <a name="authentication"></a>Authentication

O driver ABFS dá suporte a duas formas de autenticação, para que o aplicativo Hadoop possa acessar com segurança os recursos contidos em uma conta compatível com Azure Data Lake Storage Gen2. Detalhes completos dos esquemas de autenticação disponíveis são fornecidos no [guia de segurança do Armazenamento do Microsoft Azure](../common/storage-security-guide.md). Eles são:

- **Chave compartilhada**: Permite que os usuários acessem TODOS os recursos na conta. A chave é criptografada e armazenada na configuração do Hadoop.

- **Token de Portador OAuth do Azure Active Directory:** Os tokens de portador do Azure Active Directory são adquiridos e atualizados pelo driver usando a identidade do usuário final ou uma Entidade de Serviço configurada. Usando esse modelo de autenticação, todo o acesso é autorizado por chamada usando a identidade associada ao token fornecido e avaliado em relação à Lista de Controle de Acesso (ACL) POSIX atribuída.

### <a name="configuration"></a>Configuração

Todas as configurações para o driver ABFS são armazenadas no arquivo de configuração <code>core-site.xml</code>. Nas distribuições do Hadoop que caracterizam [Ambari](https://ambari.apache.org/), a configuração também pode ser gerenciada usando o portal da Web ou a API REST do Ambari.

Os detalhes de todas as entradas de configuração com suporte são especificados na [Documentação oficial do Hadoop](https://hadoop.apache.org/docs/current/hadoop-azure/index.html).

### <a name="hadoop-documentation"></a>Documentação do Hadoop

O driver ABFS está totalmente documentado na [Documentação oficial do Hadoop](https://hadoop.apache.org/docs/current/hadoop-azure/index.html)

## <a name="next-steps"></a>Próximas etapas

- [Criar um cluster do Azure Databricks](./data-lake-storage-quickstart-create-databricks-account.md)
- [Usar o URI do Azure Data Lake Store Gen2](./data-lake-storage-introduction-abfs-uri.md)
