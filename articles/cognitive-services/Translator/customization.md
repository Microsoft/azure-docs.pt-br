---
title: Personalização de tradução-Tradutor
titleSuffix: Azure Cognitive Services
description: Use o Hub do Microsoft Translator para compilar seu próprio sistema de tradução automática usando seu estilo e sua terminologia preferenciais.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 0930024a791fe8b76c90e8ab4249a070b22a1c16
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898029"
---
# <a name="customize-your-text-translations"></a>Personalizar suas traduções de texto

O tradutor personalizado é um recurso do serviço tradutor, que permite aos usuários personalizar a tradução automática do computador neural do Microsoft Translator ao traduzir texto usando o Tradutor (somente versão 3).

O recurso também pode ser usado para personalizar a tradução de fala quando usado a [Fala de Serviços Cognitivos](../speech-service/index.yml).

## <a name="custom-translator"></a>Tradutor personalizado

Com o Tradutor Personalizado, você pode compilar sistemas de tradução neural que entendam a terminologia usada na sua própria empresa e setor. O sistema de tradução personalizada então se integrará aos aplicativos, fluxos de trabalho e sites existentes.

### <a name="how-does-it-work"></a>Como ele funciona?

Use seus documentos traduzidos anteriormente (leaflets, páginas da Web, documentação, etc.) para criar um sistema de tradução que reflita sua terminologia e estilo específicos de domínio, melhor do que um sistema de tradução padrão. Os usuários podem fazer upload de documentos TMX XLIFF, TXT, DOCX e XLSX.  

O sistema também aceita dados que são paralelos no nível do documento, mas ainda não estão alinhados no nível de frase. Se os usuários tiverem acesso às versões do mesmo conteúdo em vários idiomas, mas em documentos separados, o Tradutor Personalizado conseguirá criar automaticamente a correspondência de frases entre os documentos.  O sistema também pode usar dados monolíngues em um ou ambos os idiomas para complementar os dados de treinamento paralelos para melhorar as traduções.

Em seguida, o sistema personalizado está disponível por meio de uma chamada regular para o tradutor usando o parâmetro Category.

Dado o tipo e a quantidade apropriados de dados de treinamento, não é incomum esperar ganhos entre 5 e 10, ou até mais pontos BLEU na qualidade de tradução usando o Tradutor Personalizado.

Para obter mais detalhes sobre os vários níveis de personalização com base nos dados disponíveis, veja o [Guia do usuário do Tradutor Personalizado](./custom-translator/overview.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configurar um sistema de idioma personalizado usando o Tradutor Personalizado](./custom-translator/overview.md)
