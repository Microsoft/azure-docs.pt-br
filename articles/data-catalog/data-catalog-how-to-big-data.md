---
title: Como catalogar Big Data no catálogo de dados do Azure
description: Artigo de instruções realçando padrões para uso do Catálogo de Dados do Azure com fontes de dados de "big data", incluindo o Armazenamento de Blobs do Azure, o Azure Data Lake e o HDFS do Hadoop.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 3e898542c25f6ed1a9e6c811ada47220733abb12
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674658"
---
# <a name="how-to-catalog-big-data-in-azure-data-catalog"></a>Como catalogar Big Data no catálogo de dados do Azure

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Introdução

**Catálogo de Dados do Microsoft Azure** é um serviço de nuvem totalmente gerenciado que atua como um sistema de registro e sistema de descoberta em fontes de dados da empresa. O foco é ajudar as pessoas a descobrir, entender e usar fontes de dados e ajudar as empresas a obter mais valor de suas fontes de dados existentes, incluindo Big Data.

O **Catálogo de dados do Azure** dá suporte ao registro de diretórios e blobs de armazenamento do Azure, bem como diretórios e arquivos HDFS do Hadoop. A natureza semiestruturada dessas fontes de dados proporciona excelente flexibilidade. No entanto, para obter o valor máximo de registrá-las com o **Catálogo de Dados do Azure**, os usuários devem considerar como as fontes de dados são organizadas.

## <a name="directories-as-logical-data-sets"></a>Diretórios como conjuntos de dados lógicos

Um padrão comum para a organização de fontes de big data é tratar diretórios como conjuntos de dados lógicos. Diretórios de nível superior são usados para definir um conjunto de dados, enquanto as subpastas definem as partições e os arquivos contidos nessas subpastas armazenam os dados em si.

Um exemplo desse padrão pode ser:

```text
    \vehicle_maintenance_events
        \2013
        \2014
        \2015
            \01
                \2015-01-trailer01.csv
                \2015-01-trailer92.csv
                \2015-01-canister9635.csv
                ...
    \location_tracking_events
        \2013
        ...
```

Neste exemplo, vehicle_maintenance_events e location_tracking_events representam conjuntos de dados lógicos. Cada uma dessas pastas contém arquivos de dados organizados por ano e mês em subpastas. Cada uma dessas pastas pode conter, potencialmente, centenas ou milhares de arquivos.

Nesse padrão, registrar arquivos individuais com o **Catálogo de Dados do Azure** provavelmente não faz sentido. Em vez disso, registre os diretórios que representam os conjuntos de dados que serão significativos para os usuários que estiverem trabalhando com os dados.

## <a name="reference-data-files"></a>Arquivos de dados de referência

Um padrão complementar é armazenar conjuntos de dados de referência como arquivos individuais. É possível pensar nesses conjuntos de dados como o lado “pequeno” de big data; geralmente, são similares às dimensões encontradas em um modelo de dados analíticos. Arquivos de dados de referência contêm registros que são usados para fornecer contexto para a quantidade em massa de arquivos de dados armazenados em outro lugar no armazenamento de big data.

Um exemplo desse padrão pode ser:

```text
    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv
```

Quando um analista ou cientista de dados está trabalhando com os dados contidos nas estruturas de diretório maiores, os dados nesses arquivos de referência podem ser usados para fornecer informações mais detalhadas para entidades que são chamadas apenas por nome ou ID no conjunto de dados maior.

Nesse padrão, faz sentido registrar os arquivos de dados de referência individuais com o **Catálogo de Dados do Azure**. Cada arquivo representa um conjunto de dados e cada um deles pode ser anotado e descoberto individualmente.

## <a name="alternate-patterns"></a>Padrões alternativos

Os padrões descritos na seção anterior são apenas duas maneiras possíveis de organizar um armazenamento de Big Data, mas cada implementação é diferente. Independentemente de como as fontes de dados são estruturadas, ao registrar fontes de Big Data com o **Catálogo de Dados do Azure**, concentre-se em registrar os arquivos e diretórios que representem os conjuntos de dados que terão valor para outras pessoas em sua organização. Registrar todos os arquivos e diretórios pode sobrecarregar o catálogo, tornando mais difícil para os usuários encontrar o que precisam.

## <a name="summary"></a>Resumo

Registrar fontes de dados com o **Catálogo de Dados do Azure** facilita a descoberta e a compreensão. Registrando e anotando os diretórios e arquivos de big data que representam os conjuntos de dados lógicos, você pode ajudar os usuários a localizar e usar as fontes de big data de que precisam.
