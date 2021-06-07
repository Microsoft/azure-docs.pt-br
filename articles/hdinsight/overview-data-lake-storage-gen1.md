---
title: Visão geral do Azure Data Lake Storage Gen1 no HDInsight
description: Visão geral do Data Lake Storage Gen1 no HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 4ac1a48733d104e7674acbc13bfbb1e7a4cf02b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98938873"
---
# <a name="azure-data-lake-storage-gen1-overview-in-hdinsight"></a>Visão geral do Azure Data Lake Storage Gen1 no HDInsight

O Azure Data Lake Storage Gen1 é um repositório de hiperescala em toda a empresa para cargas de trabalho de análise de big data. Usando Azure Data Lake, você pode capturar dados de qualquer tamanho, tipo e velocidade de ingestão. E em um só lugar para análise operacional e exploratório.

Acesse o Data Lake Storage Gen1 a partir do Hadoop (disponível com um cluster do HDInsight) usando as APIs REST compatíveis com WebHDFS. O Data Lake Storage Gen1 foi desenvolvido para permitir a análise dos dados armazenados e está ajustado para trabalhar em cenários de análise de dados. O Gen1 inclui os recursos que são essenciais para casos de uso empresarial do mundo real. Esses recursos incluem segurança, capacidade de gerenciamento, adaptabilidade, confiabilidade e disponibilidade.

Para saber mais sobre o Azure Data Lake Storage Gen1, confira a [Visão geral do Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md).

Os principais recursos do Data Lake Storage Gen1 incluem o seguinte.

## <a name="compatibility-with-hadoop"></a>Compatibilidade com o Hadoop

Data Lake Storage Gen1 é um sistema de arquivos Apache Hadoop compatível com o ambiente HDFS e Hadoop.  Os aplicativos ou serviços do HDInsight que usam a API do WebHDFS podem facilmente integrar com Data Lake Storage Gen1. O Data Lake Storage Gen1 também exibe uma interface REST compatível com WebHDFS para aplicativos.

Os dados armazenados no Data Lake Storage Gen1 podem ser facilmente analisados usando estruturas analíticas do Hadoop. Estruturas como MapReduce ou Hive. Os clusters do Azure HDInsight podem ser provisionados e configurados para acessar diretamente os dados armazenados no Data Lake Storage Gen1.

## <a name="unlimited-storage-petabyte-files"></a>Armazenamento ilimitado, arquivos em petabytes

O Data Lake Storage Gen1 fornece armazenamento ilimitado e é adequado para armazenar diferentes tipos de dados para análise. Ele não impõe limites de tamanhos de conta ou tamanhos de arquivo. Ou a quantidade de dados que pode ser armazenada em um data Lake. Os arquivos individuais variam em tamanho de kilobytes a petabytes, tornando Data Lake Storage Gen1 uma ótima opção para armazenar qualquer tipo de dados. Os dados são armazenados permanentemente pela criação de várias cópias. E não há nenhum limite de quanto tempo os dados podem ser armazenados no data Lake.

## <a name="performance-tuning-for-big-data-analytics"></a>Ajustes de desempenho para a análise de big data

O Data Lake Storage Gen1 foi projetado para sistemas analíticos. Sistemas que exigem produtividade maciça para consultar e analisar grandes quantidades de dados. O Data Lake espalha as partes de um arquivo por vários servidores de armazenamento individual. Quando você estiver analisando os dados, essa configuração melhorará a taxa de transferência de leitura quando o arquivo é lido em paralelo.

## <a name="readiness-for-enterprise-highly-available-and-secure"></a>Preparação para a empresa: altamente disponível e segura

O Data Lake Storage Gen1 fornece disponibilidade e confiabilidade padrão do setor. Os ativos de dados são armazenados permanentemente: cópias redundantes protegem contra falhas inesperadas. As empresas podem usar o Data Lake Storage Gen1 em suas soluções como parte importante de sua plataforma de dados existente.

O Data Lake Storage Gen1 também fornece segurança de nível corporativo para os dados armazenados. Para obter mais informações, consulte [Protegendo dados no Armazenamento de dados do Lake Azure Gen1](#data-security-in-data-lake-storage-gen1).

## <a name="flexible-data-structures"></a>Estruturas de dados flexível

O Data Lake Storage Gen1 pode armazenar qualquer dado em seu formato nativo, no estado em que estiver, sem a necessidade de transformações anteriores. O Data Lake Storage Gen1 não exige a definição de um esquema antes de os dados serem carregados. A estrutura de análise individual interpreta os dados e define um esquema no momento da análise. Data Lake Storage Gen1 pode lidar com dados estruturados. E semiestruturados e dados não estruturados.

Os contêineres Data Lake Storage Gen1 para dados são essencialmente pastas e arquivos. Você opera nos dados armazenados usando SDKs, o portal do Azure e o Azure PowerShell. Os dados colocados no armazenamento com essas interfaces e contêineres podem armazenar qualquer tipo de dados. Data Lake Storage Gen1 não faz qualquer manipulação especial de dados com base no tipo de dados.

## <a name="data-security-in-data-lake-storage-gen1"></a>Segurança de dados no Data Lake Storage Gen1

O Data Lake Storage Gen1 usa o Azure Active Directory para autenticação e as listas de controle de acesso (ACLs) para gerenciar o acesso aos seus dados.

| **Recurso** | **Descrição** |
| --- | --- |
| Autenticação |O Data Lake Storage Gen1 integra-se ao Azure Active Directory (Azure AD) para gerenciamento de identidade e acesso de todos os dados armazenados no Data Lake Storage Gen1. Devido à integração, o Data Lake Storage Gen1 se beneficia de todos os recursos do Azure AD. Esses recursos incluem: autenticação multifator, acesso condicional e controle de acesso baseado em função do Azure. Além disso, monitoramento de uso do aplicativo, monitoramento e alertas de segurança e assim por diante. O Data Lake Storage Gen1 oferece suporte ao protocolo OAuth 2.0 para autenticação na interface REST. Confira o artigo sobre a [autenticação no Azure Data Lake Storage Gen1 usando o Azure Active Directory](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Controle de acesso |O Data Lake Storage Gen1 fornece controle de acesso oferecendo suporte a permissões no estilo POSIX, que são expostas pelo protocolo WebHDFS. As ACLs podem ser habilitadas na pasta raiz, nas subpastas e nos arquivos individuais. Para saber mais sobre como as ACLs funcionam no contexto do Data Lake Storage Gen1, confira o artigo sobre o [controle de acesso no Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md). |
| Criptografia |O Azure Data Lake Storage Gen1 também fornece criptografia para dados armazenados na conta. Você especifica as configurações de criptografia ao criar uma conta do Data Lake Storage Gen1. É possível optar por ter os dados criptografados ou não escolher nenhuma criptografia. Para obter mais informações, consulte [Criptografia no Data Lake Storage Gen1](../data-lake-store/data-lake-store-encryption.md). Para obter instruções sobre como fornecer uma configuração relacionada à criptografia, confira a [Introdução ao Azure Data Lake Storage Gen1 usando o portal do Azure ](../data-lake-store/data-lake-store-get-started-portal.md). |

Para saber mais sobre como proteger os dados no Data Lake Storage Gen1, confira o artigo sobre como [proteger os dados armazenados no Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md).

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Aplicativos compatíveis com o Data Lake Storage Gen1

Data Lake Storage Gen1 é compatível com a maioria dos componentes de software livre no ambiente do Hadoop. Ele também se integra perfeitamente com outros serviços do Azure.  Siga os links abaixo para saber mais sobre como Data Lake Storage Gen1 pode ser usado com componentes de software livre e outros serviços do Azure.

* Consulte [aplicativos de Big data de software livre que funcionam com Azure data Lake Storage Gen1](../data-lake-store/data-lake-store-compatible-oss-other-applications.md).
* Confira o artigo [Integração do Azure Data Lake Storage Gen1 a outros serviços do Azure](../data-lake-store/data-lake-store-integrate-with-other-services.md) para entender como usar os serviços do Data Lake Storage Gen1 com outros serviços do Azure a fim de permitir uma gama mais ampla de cenários.
* Consulte [usando Azure data Lake Storage Gen1 para Big data requisitos](../data-lake-store/data-lake-store-data-scenarios.md).

## <a name="data-lake-storage-gen1-file-system-adl"></a>Sistema de arquivo do Data Lake Storage Gen1 (adl://)

Em ambientes Hadoop, você pode acessar Data Lake Storage Gen1 por meio do novo sistema de arquivos, o AzureDataLakeFilesystem (adl://). O desempenho de aplicativos e serviços que usam o `adl://` pode ser otimizado de maneiras que atualmente não estão disponíveis no WebHDFS. Como resultado, você obtém a flexibilidade para obter o melhor desempenho usando o adl://recomendado. Ou mantenha o código existente continuando a usar a API WebHDFS diretamente. O Azure HDInsight aproveita totalmente o AzureDataLakeFilesystem para fornecer o melhor desempenho no Data Lake Storage Gen1.

Acesse seus dados em Data Lake Storage Gen1 usando o seguinte URI:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Para saber mais sobre como acessar os dados no Data Lake Storage Gen1, confira o artigo [Ações disponíveis nos dados armazenados](../data-lake-store/data-lake-store-get-started-portal.md#properties).

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introdução ao Armazenamento do Azure](../storage/common/storage-introduction.md)
* [Visão geral do Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen2.md)