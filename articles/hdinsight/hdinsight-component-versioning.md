---
title: Componentes e versões do Apache Hadoop - Azure HDInsight
description: Saiba mais sobre componentes e versões do Apache Hadoop no Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: 166c52660c9f47b8ecfce10f7e9c3b81ff067f35
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699361"
---
# <a name="azure-hdinsight-versions"></a>Versões do Azure HDInsight

Os pacotes do HDInsight Apache Hadoop componentes de ambiente e a plataforma HDInsight em um pacote que é implantado em um cluster. Para obter mais detalhes, consulte [como funciona o controle de versão do HDInsight](hdinsight-overview-versioning.md).

## <a name="supported-hdinsight-versions"></a>Versões do HDInsight com suporte

Esta tabela lista as versões do HDInsight que estão disponíveis no portal do Azure e outros métodos de implantação, como PowerShell, CLI e SDK do .NET.

| Versão do HDInsight | SO da VM | Data de liberação | Data de expiração do suporte | Data de baixa | Alta disponibilidade |
| --- | --- | --- | --- | --- | --- |
| [HDInsight 4.0](hdinsight-40-component-versioning.md) |Ubuntu 16.0.4 LTS |24 de setembro de 2018 | | |Sim |
| [HDInsight 3.6](hdinsight-36-component-versioning.md) |Ubuntu 16.0.4 LTS |4 de abril de 2017      | * 30 de junho de 2021 |30 de junho de 2021 |Sim |

* Estamos estendendo o cronograma de suporte para determinados tipos de cluster HDInsight 3,6. Consulte [versões de componente do HDInsight 3,6](hdinsight-36-component-versioning.md).

## <a name="release-notes"></a>Notas de versão

Para ver notas adicionais sobre as versões mais recentes do HDInsight, confira [Notas sobre a versão do HDInsight](hdinsight-release-notes.md).

## <a name="support-options-for-hdinsight-versions"></a>Opções de suporte para versões do HDInsight

O HDInsight oferece suporte padrão que é definido como um período de tempo que uma versão do HDInsight tem suporte pelo atendimento ao cliente e suporte da Microsoft.

**Expiração do suporte** significa que a Microsoft não oferece mais suporte para a versão específica do HDInsight. Além disso, ele não está mais disponível por meio do portal do Azure para criação de clusters.

**Desativação** significa que os clusters existentes de uma versão do HDInsight continuam a ser executados como estão. Novos clusters desta versão não podem ser criados por nenhum meio, o que inclui a CLI e os SDKs. Outros recursos de plano de controle, como dimensionamento manual e dimensionamento automático, não têm garantia de trabalho após a data de desativação. O suporte não está disponível para versões desativadas.

## <a name="versioning-considerations"></a>Considerações sobre controle de versão
- Depois que um cluster é implantado com uma imagem, esse cluster não é atualizado automaticamente para uma versão de imagem mais nova. Ao criar novos clusters, a versão da imagem mais recente será implantada.
- Os clientes devem testar e validar que os aplicativos sejam executados corretamente ao usar a nova versão do HDInsight.
- O HDInsight reserva o direito de alterar a versão padrão sem aviso prévio. Se você tiver uma dependência de versão, especifique a versão do HDInsight ao criar seus clusters.
- O HDInsight pode desativar uma versão de componente do OSS antes de desativar a versão do HDInsight.

## <a name="next-steps"></a>Próximas etapas

- [Configuração de cluster para o Apache Hadoop, Spark e muito mais no HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Enterprise Security Package](./enterprise-security-package.md)
- [Trabalhe no Apache Hadoop no HDInsight a partir de um PC Windows](hdinsight-hadoop-windows-tools.md)