---
title: O que é o Tradutor Personalizado?
titleSuffix: Azure Cognitive Services
description: O Tradutor Personalizado oferece recursos semelhantes ao que o Hub do Microsoft Translator faz em relação à SMT (Tradução Automática Estatística), mas exclusivamente para sistemas de NMT (Tradução Automática Neural).
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 12/09/2019
ms.author: lajanuar
ms.topic: overview
ms.openlocfilehash: 89ab25f45cd35a86d2ff4416ceb851ae6cd5e115
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657154"
---
# <a name="what-is-custom-translator"></a>O que é o Tradutor Personalizado?

O [Tradutor Personalizado](https://portal.customtranslator.azure.ai) é um recurso do serviço Microsoft Translator que permite que as empresas de tradução, os desenvolvedores de aplicativos e prestadores de serviços de linguagem criem sistemas de NMT (Tradução Automática Neural). Os sistemas de tradução personalizada se integram aos aplicativos, fluxos de trabalho e sites existentes.

Sistemas de tradução criados com o [Tradutor Personalizado](https://portal.customtranslator.azure.ai) estão disponíveis pela mesmo recurso [seguro](https://cognitive.uservoice.com/knowledgebase/articles/1147537-api-and-customization-confidentiality), baseado em nuvem, de alto desempenho e altamente escalonável que é a [API de Texto V3](../reference/v3-0-translate.md?tabs=curl) do Microsoft Translator, que viabiliza bilhões de traduções todos os dias.

O Tradutor Personalizado dá suporte a mais de três dúzias de idiomas e mapeia diretamente para os idiomas disponíveis para NMT. Para obter uma lista completa, confira os [Idiomas do Microsoft Translator](../language-support.md#customization).

Esta documentação contém os seguintes tipos de artigos:

* Os [**guias de início rápido**](quickstart-build-deploy-custom-model.md) são instruções de introdução que orientam sobre como fazer solicitações ao serviço.  
* Os [**guias de instruções**](how-to-create-project.md) contêm instruções de uso do recurso de maneiras mais específicas ou personalizadas.  
* Os [**conceitos**](workspace-and-project.md) fornecem explicações detalhadas sobre a funcionalidade do recurso.  


## <a name="features"></a>Recursos

O Tradutor Personalizado fornece diferentes recursos para criar um sistema de tradução personalizado e acessá-lo posteriormente.

|Recurso  |Descrição  |
|---------|---------|
|[Aplicar a tecnologia de tradução automática neural](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/)     |  Aprimore sua tradução aplicando a NMT (tradução automática neural) fornecida pelo Tradutor Personalizado.       |
|[Criar sistemas que conhecem sua terminologia comercial](what-are-parallel-documents.md)     |  Personalize e crie sistemas de tradução usando documentos paralelos, que entendem as terminologias usadas em seus negócios e no setor.       |
|[Usar um dicionário para criar seus modelos](what-is-dictionary.md)     |   Se você não tiver um conjunto de dados de treinamento, poderá treinar um modelo com dados de dicionário.       |
|[Colaborar com outras pessoas](how-to-manage-settings.md#share-your-workspace)     |   Colabore com sua equipe compartilhando seu trabalho com pessoas diferentes.     |
|[Acessar seu modelo de tradução personalizada](../reference/v3-0-translate.md?tabs=curl)     |  Seu modelo de tradução personalizada pode ser acessado a qualquer momento em seus aplicativos ou programas existentes por meio da API de Texto V3 do Microsoft Translator.       |

## <a name="get-better-translations"></a>Obter melhores traduções

O Microsoft Translator lançou a [NMT (Tradução Automática Neural)](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/) em 2016. A NMT viabilizou grandes avanços na qualidade das traduções em relação à tecnologia [SMT (Tradução Automática estatística)](https://en.wikipedia.org/wiki/Statistical_machine_translation) padrão do setor. Como a NMT compreende melhor o contexto de frases completas antes de traduzi-las, ela fornece as traduções mais humanas e mais fluentes, com melhor qualidade. O [Tradutor Personalizado](https://portal.customtranslator.azure.ai) fornece NMT para seus modelos personalizados, resultando em uma tradução com melhor qualidade.

Você pode usar documentos traduzidos anteriormente para criar um sistema de tradução. Esses documentos incluem terminologia e estilo específicos do domínio, melhor do que um sistema padrão de tradução. Os usuários podem carregar documentos ALIGN, PDF, LCL, HTML, HTM, XLF, TMX, XLIFF, TXT, DOCX e XLSX.

O Tradutor Personalizado também aceita dados que estão paralelos ao nível do documento para tornar a preparação e a coleta de dados mais eficiente. Se os usuários tiverem acesso às versões do mesmo conteúdo em vários idiomas, mas em documentos separados, o Tradutor Personalizado conseguirá criar automaticamente a correspondência de frases entre os documentos.

Se forem fornecidos o tipo e a quantidade de dados de treinamento adequados, não é incomum ver ganhos de [pontuação BLEU](what-is-bleu-score.md) entre 5 e 10 pontos usando o Tradutor Personalizado.

## <a name="be-productive-and-cost-effective"></a>Ser produtivo e econômico

Com o [Tradutor Personalizado](https://portal.customtranslator.azure.ai), o treinamento e a implantação de um sistema personalizado não exigem habilidades de programação.

Usando o portal seguro do [Tradutor Personalizado](https://portal.customtranslator.azure.ai), os usuários podem carregar dados de treinamento, treinar e testar sistemas e implantá-los em um ambiente de produção por meio de uma interface de usuário intuitiva. O sistema estará disponível para uso em grande escala em algumas horas (o tempo real dependerá do tamanho dos dados de treinamento).

O [Tradutor Personalizado](https://portal.customtranslator.azure.ai) também podem ser acessado programaticamente por meio de uma [API dedicada](https://custom-api.cognitive.microsofttranslator.com/swagger/) (atualmente em versão prévia). A API permite que os usuários gerenciem a criação ou a atualização do treinamento por meio de um aplicativo ou um serviço Web próprio.

O custo do uso de um modelo personalizado para traduzir conteúdo baseia-se no tipo de preço de API de Tradução de Texto do usuário. Confira a [página de preços da API de Tradução de Texto](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) dos Serviços Cognitivos para obter detalhes sobre o tipo de preço.

## <a name="securely-translate-anytime-anywhere-on-all-your-apps-and-services"></a>Traduzir com segurança a qualquer momento, em qualquer lugar em todos os seus aplicativos e serviços

Os sistemas personalizados podem ser acessados diretamente e integrados a qualquer produto ou fluxo de trabalho, e em qualquer dispositivo, por meio da API de Texto do Microsoft Translator usando tecnologia REST padrão.

## <a name="next-steps"></a>Próximas etapas

- Leia sobre [detalhes de preços](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

- Com o [Início Rápido](quickstart-build-deploy-custom-model.md), saiba como criar um modelo de tradução no Tradutor Personalizado.
