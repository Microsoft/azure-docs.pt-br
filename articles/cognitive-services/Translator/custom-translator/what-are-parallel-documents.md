---
title: O que são documentos paralelos? - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Documentos paralelos são pares de documentos em que um é a tradução do outro. Um documento no par contém sentenças no idioma de origem e o outro documento contém essas frases traduzidas no idioma de destino.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 64192014c9dfe5bb958e730dae62e504f0976fa1
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895587"
---
# <a name="what-are-parallel-documents"></a>O que são documentos paralelos?

Documentos paralelos são pares de documentos em que um é a tradução do outro. Um documento no par contém sentenças no idioma de origem e o outro documento contém essas frases traduzidas no idioma de destino.
Não importa qual idioma está marcado como "source" e qual idioma está marcado como "target" - um documento paralelo pode ser usado para treinar um sistema de tradução em qualquer direção.

## <a name="requirements"></a>Requisitos

Você precisará de um mínimo de 10.000 sentenças paralelas alinhadas exclusivamente para treinar um sistema. Essa limitação é uma rede de segurança para garantir que suas frases paralelas contenham um vocabulário exclusivo suficiente para treinar com êxito um modelo de tradução. Como prática recomendada, adicione continuamente mais conteúdo paralelo e retreine para melhorar a qualidade do seu sistema de tradução. Consulte alinhamento de [frase](./sentence-alignment.md).

A Microsoft exige que os documentos carregados para o conversor personalizado não violem as propriedades de direitos autorais ou intelectual de terceiros. Para mais informações, consulte os [Termos de Uso](https://azure.microsoft.com/support/legal/cognitive-services-terms/).
O upload de um documento usando o portal não altera a propriedade da propriedade intelectual no próprio documento.

## <a name="use-of-parallel-documents"></a>Uso de documentos paralelos

Documentos paralelos são usados pelo sistema:

1.  Aprender como palavras, frases e frases são comumente mapeadas entre os dois idiomas.

2.  Aprender a processar o contexto apropriado, dependendo das frases vizinhas. Uma palavra nem sempre pode traduzir para a mesma palavra na outra língua.

Como prática recomendada, verifique se há uma correspondência de sentença 1:1 entre as versões de idioma de origem e de destino dos documentos.

Se o seu projeto é específico de domínio (categoria), seus documentos devem ser consistentes na terminologia dentro dessa categoria. A qualidade do sistema de tradução resultante depende do número de sentenças no conjunto de documentos e da qualidade das sentenças. Quanto mais exemplos seus documentos contiverem usos diversos para uma palavra específica da sua categoria, o melhor trabalho que o sistema pode fazer durante a tradução.

Os documentos enviados são privados para cada workspace e podem ser usados em quantos projetos ou treinamentos você desejar. Frases extraídas de seus documentos são armazenadas separadamente em seu repositório como arquivos de texto Unicode simples e estão disponíveis para exclusão. Não use o Custom Translator como repositório de documentos, você não poderá fazer o download dos documentos que você enviou no formato que você enviou.



## <a name="next-steps"></a>Próximas etapas

- Saiba como usar um [dicionário](what-is-dictionary.md) no tradutor personalizado.