---
title: Visão geral do armazenamento do Azure no HDInsight
description: Visão geral do armazenamento do Azure no HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: f171ab9619f2bcb8ecf15c4bfb3b17146ab5a0ff
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98938952"
---
# <a name="azure-storage-overview-in-hdinsight"></a>Visão geral do armazenamento do Azure no HDInsight

O Armazenamento do Azure é uma solução robusta de armazenamento de uso geral que se integra perfeitamente ao HDInsight. O HDInsight pode usar um contêiner de blobs no Armazenamento do Azure como o sistema de arquivos padrão para o cluster. Ao usar uma interface do HDFS, o conjunto completo de componentes no HDInsight pode operar diretamente sobre os dados estruturados ou não estruturados armazenados como blobs.

É recomendável usar contêineres de armazenamento separados para o armazenamento de cluster padrão e seus dados corporativos. A separação é isolar os logs do HDInsight e os arquivos temporários de seus próprios dados corporativos. Também é recomendável excluir o contêiner de blob padrão, que contém logs do aplicativo e do sistema, após cada uso para reduzir o custo de armazenamento. Certifique-se de recuperar os logs antes de excluir o contêiner.

Se você optar por proteger sua conta de armazenamento com as restrições de **redes virtuais e firewalls** em **redes selecionadas**, certifique-se de habilitar a exceção **permitir serviços confiáveis da Microsoft...**. A exceção é para que o HDInsight possa acessar sua conta de armazenamento.

## <a name="hdinsight-storage-architecture"></a>Arquitetura de armazenamento do HDInsight

O diagrama a seguir fornece uma exibição abstrata da arquitetura do HDInsight do Armazenamento do Azure:

![' Arquitetura de armazenamento do HDInsight '](./media/overview-azure-storage/storage-architecture.png "Arquitetura de armazenamento do HDInsight")

O HDInsight fornece acesso ao sistema de arquivos distribuídos que está anexado localmente aos nós de computação. Esse sistema de arquivos pode ser acessado usando o URI totalmente qualificado, por exemplo:

`hdfs://<namenodehost>/<path>`

Por meio do HDInsight, você também pode acessar dados no armazenamento do Azure. A sintaxe dela é a seguinte:

`wasb://<containername>@<accountname>.blob.core.windows.net/<path>`

Considere os seguintes princípios ao usar uma conta de Armazenamento do Azure com clusters do HDInsight:

* **Contêineres nas contas de armazenamento que estão conectadas a um cluster:** como o nome e a chave da conta são associados ao cluster durante a criação, você tem acesso completo aos blobs nesses contêineres.

* **Contêineres públicos ou BLOBs públicos em contas de armazenamento que não estão conectadas a um cluster:** Você tem permissão somente leitura para os BLOBs nos contêineres.
  
  > [!NOTE]  
  > Os contêineres públicos permitem obter uma lista de todos os blobs disponíveis nesse contêiner e os metadados do contêiner. Um blob público somente permite acessar os blobs se você souber a URL exata. Para obter mais informações, confira [Gerenciar acesso anônimo de leitura aos contêineres e blobs](../storage/blobs/anonymous-read-access-configure.md).

* **Contêineres privados em contas de armazenamento que não estão conectadas a um cluster:** Não é possível acessar os BLOBs nos contêineres, a menos que você defina a conta de armazenamento ao enviar os trabalhos do WebHCat.

As contas de armazenamento definidas no processo de criação e suas chaves são armazenadas em %HADOOP_HOME%/conf/core-site.xml nos nós do cluster. Por padrão, o HDInsight usa as contas de armazenamento definidas no arquivo core-site.xml. Você pode modificar essa configuração usando o [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Vários trabalhos do WebHCat, incluindo Apache Hive. E MapReduce, Apache Hadoop streaming e Apache Pig, contêm uma descrição de contas de armazenamento e metadados. (Esse aspecto é verdadeiro no momento para Pig com contas de armazenamento, mas não para metadados.) Para obter mais informações, consulte [usando um cluster HDInsight com metastores e contas de armazenamento alternativas](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Os blobs podem ser usados para dados estruturados e não estruturados. Os contêineres de blobs armazenam dados como pares de chave/valor e não possuem nenhuma hierarquia de diretório. No entanto, o nome da chave pode incluir o caractere de barra ( / ) para parecer que o arquivo está armazenado em uma estrutura de diretório. Por exemplo, a chave de um blob pode ser `input/log1.txt`. Não existe nenhum diretório `input`, mas por causa do caractere de barra no nome da chave, a chave se parece com um caminho de arquivo.

## <a name="benefits-of-azure-storage"></a>Benefícios do Armazenamento do Azure

Os clusters de cálculo e os recursos de armazenamento que não são colocados acarretam custos de desempenho. Esses custos são reduzidos dependendo da maneira que os clusters de cálculo são criados próximos aos recursos de conta de armazenamento na região do Azure. Nessa região, os nós de computação podem acessar com eficiência os dados na rede de alta velocidade dentro do Armazenamento do Azure.

Quando armazena os dados no armazenamento do Azure em vez de no HDFS, você obtém vários benefícios:

* **Compartilhamento e reutilização de dados:** os dados no HDFS estão localizados dentro do cluster de computação. Apenas os aplicativos que têm acesso ao cluster de computação podem usar os dados usando a API HDFS. Por outro lado, os dados no Armazenamento do Azure podem ser acessados por meio das APIs do HDFS ou por meio das APIs REST do Armazenamento de Blobs. Em razão dessa organização, um conjunto maior de aplicativos (incluindo outros clusters do HDInsight) e ferramentas pode ser usado para produzir e consumir os dados.

* **Arquivamento de dados:** Quando os dados são armazenados no armazenamento do Azure, os clusters HDInsight usados para computação podem ser excluídos com segurança sem perder dados do usuário.

* **Custo de armazenamento de dados:** O armazenamento de dados no DFS por longo prazo é mais dispendioso do que armazenar os dados no armazenamento do Azure. Porque o custo de um cluster de cálculo é maior do que o custo do armazenamento do Azure. Além disso, como os dados não precisam ser recarregados para cada geração de cluster de cálculo, você estará economizando também em custos de carregamento de dados.

* **Escala horizontal elástica:** embora o HDFS forneça um sistema de arquivos escalado horizontalmente, a escala é determinada pelo número de nós que você cria para o seu cluster. A alteração da escala pode ser mais complicada do que os recursos de dimensionamento elástico que você obtém automaticamente no armazenamento do Azure.

* **Replicação geográfica:** O armazenamento do Azure pode ser replicado geograficamente. Embora a replicação geográfica forneça recuperação geográfica e redundância de dados, um failover para a localização com replicação geográfica afetará seriamente o desempenho e poderá gerar custos adicionais. Portanto, escolha a replicação geográfica com cuidado e somente se o valor dos dados justificar o custo adicional.

Determinados trabalhos e pacotes do MapReduce podem criar resultados intermediários que talvez você não queira realmente armazenar no contêiner de Armazenamento do Azure. Nesse caso, é possível ainda optar por armazenar os dados no HDFS local. O HDInsight usa o DFS para vários desses resultados intermediários em trabalhos do Hive e outros processos.

> [!NOTE]  
> A maioria dos comandos HDFS (por exemplo, `ls`, `copyFromLocal` e `mkdir`) ainda funciona conforme o esperado no Armazenamento do Azure. Apenas os comandos específicos à implementação nativa do HDFS (que é conhecida como DFS), como `fschk` e `dfsadmin`, apresentam um comportamento diferente no Armazenamento do Azure.

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introdução ao Armazenamento do Azure](../storage/common/storage-introduction.md)
* [Visão geral do Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen1.md)