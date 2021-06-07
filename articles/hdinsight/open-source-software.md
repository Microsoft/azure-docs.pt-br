---
title: Suporte para software livre no Azure HDInsight
description: O Microsoft Azure fornece um nível geral de suporte para tecnologias de software livre.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: fec4cff974031982c782c9265a7d3186d6bb0233
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98942553"
---
# <a name="open-source-software-support-in-azure-hdinsight"></a>Suporte para software livre no Azure HDInsight

O serviço Microsoft Azure HDInsight usa um ambiente de tecnologias de software livre construído em torno do Apache Hadoop. O Microsoft Azure fornece um nível geral de suporte para tecnologias de software livre. Para saber mais, confira a seção **Escopo do Suporte** nas [Perguntas frequentes sobre o Suporte do Azure](https://azure.microsoft.com/support/faq/). O serviço HDInsight fornece um nível adicional de suporte a componentes internos.

## <a name="components"></a>Componentes

Dois tipos de componentes de software livre estão disponíveis no serviço do HDInsight:

### <a name="built-in-components"></a>Componentes internos

Esses componentes estão pré-instalados em clusters do HDInsight e fornecem os recursos básicos do cluster. Os seguintes componentes pertencem a essa categoria:

* Resource Manager do [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html).
* A linguagem de consulta do Hive, [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).
* [Apache Mahout](https://mahout.apache.org/).

Uma lista completa de componentes de cluster está disponível em [Quais são os componentes e versões do Apache Hadoop disponíveis com o HDInsight?](hdinsight-component-versioning.md)

### <a name="custom-components"></a>Componentes personalizados

Como usuário do cluster, você pode instalar ou usar em sua carga de trabalho qualquer componente disponível na comunidade ou criado por você.

> [!WARNING]  
> Componentes fornecidos com o cluster HDInsight contam com suporte total. O Suporte da Microsoft ajuda a isolar e resolver problemas relacionados a esses componentes.
>
> Componentes personalizados recebem suporte comercial razoável para ajudar você a solucionar o problema. O Suporte da Microsoft pode conseguir resolver o problema. Ou eles podem solicitar que você busque nos canais disponíveis as tecnologias de código-fonte aberto, onde é possível encontrar conhecimento aprofundado sobre essa tecnologia. Muitos sites de comunidades podem ser usados. Os exemplos são a [Página de Perguntas e respostas da Microsoft para o HDInsight](/answers/topics/azure-hdinsight.html) e [Stack Overflow](https://stackoverflow.com).
>
> Os projetos do Apache têm sites de projeto no [site do Apache](https://apache.org). Um exemplo é o [Hadoop](https://hadoop.apache.org/).

## <a name="component-usage"></a>Uso do componente

O serviço HDInsight possibilita o uso de componentes personalizados de várias formas. O mesmo nível de suporte se aplica, independentemente de como um componente é usado ou instalado no cluster. A tabela a seguir descreve as formas mais comuns de usar os componentes personalizados em clusters do HDInsight:

|Uso |Descrição |
|---|---|
|Envio do trabalho|Trabalhos do Hadoop ou de outros tipos que executam ou usam componentes personalizados podem ser enviados para o cluster.|
|Personalização do cluster|Durante a criação de clusters, você pode especificar configurações adicionais e componentes personalizados para instalação nos nós de cluster.|
|Exemplos|Para componentes personalizados populares, a Microsoft e outras empresas podem fornecer exemplos de uso desses componentes em clusters HDInsight. Esses exemplos são fornecidos sem suporte.|

## <a name="next-steps"></a>Próximas etapas

* [Personalização de clusters do HDInsight através de ações de script](./hdinsight-hadoop-customize-cluster-linux.md)
* [Desenvolver scripts de ação de script para o HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Gerenciamento seguro do ambiente do Python no Azure HDInsight usando ação de script](./spark/apache-spark-python-package-installation.md)