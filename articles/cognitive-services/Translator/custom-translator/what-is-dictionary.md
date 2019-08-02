---
title: O que é um dicionário? - Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Um dicionário é um documento alinhado que especifica uma lista de frases ou sentenças (e suas traduções) que você sempre quer que o Microsoft Translator traduza da mesma maneira. Os dicionários às vezes também são chamados de glossários ou bases de termos.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 5103526956b5041771a1d8e4abb5e8800b971059
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595376"
---
# <a name="what-is-a-dictionary"></a>O que é um dicionário?

Um dicionário é um par de documentos alinhados que especifica uma lista de frases ou sentenças e suas respectivas traduções. Use um dicionário no seu treinamento, quando quiser que o Microsoft Translator sempre traduza quaisquer instâncias da frase ou sentença de origem, usando a tradução que você forneceu no dicionário. Dicionários são às vezes chamados de glossários ou bases de termos. Você pode pensar no dicionário como uma força bruta “copiar e substituir” para todos os termos listados.

Os dicionários funcionam apenas para projetos em pares de idiomas que possuem um sistema NMT (Microsoft neural machine translation) totalmente suportado por trás deles. [Exiba a lista completa de idiomas](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization).

## <a name="phrase-dictionary"></a>Dicionário de frase
Quando você inclui um dicionário de frases no treinamento do seu modelo, qualquer palavra ou frase listada é traduzida da maneira que você especificou. O resto da frase é traduzido como de costume. Você pode usar um dicionário de frases para especificar frases que não devem ser traduzidas, fornecendo a mesma frase não traduzida no arquivo de origem e de destino no dicionário.

## <a name="sentence-dictionary"></a>Dicionário de frases
O dicionário de sentenças permite que você especifique uma tradução de destino exata para uma frase de origem. Para que uma correspondência de dicionário de sentença ocorra, toda a sentença enviada deve corresponder à entrada do dicionário de origem.  Se apenas uma parte da sentença corresponder, a entrada não será igual.  Quando uma correspondência é detectada, a entrada de destino do dicionário de frases será retornada.

## <a name="dictionary-only-trainings"></a>Treinamentos somente de dicionário
Você pode treinar um modelo usando apenas dados do dicionário. Para fazer isso, selecione apenas o documento do dicionário (ou vários documentos do dicionário) que você deseja incluir e toque em Criar modelo. Como esse é um treinamento somente de dicionário, não há um número mínimo de sentenças de treinamento necessárias. Seu modelo normalmente completará o treinamento muito mais rápido que um treinamento padrão.  Os modelos resultantes usarão os modelos de linha de base da Microsoft para tradução com a adição dos dicionários que você adicionou.  Você não obterá um relatório de teste.

>[!Note]
>O Custom Translator não faz a sentença alinhar os arquivos do dicionário, por isso é importante que haja um número igual de frases / frases de origem e de destino nos documentos do dicionário e que eles estejam precisamente alinhados.

## <a name="recommendations"></a>Recomendações

- Os dicionários não substituem um modelo treinado com dados de treinamento.  Dicionários essencialmente encontrar e substituir palavras ou frases.  Deixar o sistema aprender com seu material de treinamento em sentenças completas é geralmente uma escolha melhor do que usar um dicionário.
- O dicionário de frase deve ser usado com moderação. Quando uma frase dentro de uma frase é substituída, o contexto dentro dessa frase é perdido ou limitado para traduzir o resto da sentença. O resultado é que, enquanto a frase ou palavra dentro da sentença será traduzida de acordo com o dicionário de frases, a qualidade geral da tradução da frase frequentemente sofrerá.
- O dicionário de frases funciona bem para nomes compostos como nomes de produtos (“Microsoft SQL Server”), nomes próprios (“Cidade de Hamburgo”) ou recursos do produto (“tabela dinâmica”). Ele não funciona igualmente bem para verbos ou adjetivos, porque estes são tipicamente altamente flexionados na origem ou no idioma de destino. Evite entradas de dicionário de frase para qualquer coisa, exceto substantivos compostos.
- Ao usar um dicionário, a capitalização e a pontuação em suas traduções refletem as letras maiúsculas e a pontuação fornecidas no arquivo de destino. A capitalização e a pontuação são ignoradas ao tentar identificar correspondências entre sua sentença de entrada e as sentenças de origem em seu arquivo de dicionário. Por exemplo, digamos que treinamos um sistema de inglês para espanhol que usasse um dicionário que especificasse "Cidade de Hamburgo" no arquivo de origem e "Cidade de Hamburgo" no arquivo de destino. Se eu solicitasse a tradução de uma sentença que incluísse a frase “cidade de Hamburgo”, então “cidade de Hamburgo” corresponderia ao meu arquivo de dicionário para a entrada “Cidade de Hamburgo”, e mapearia para “Ciudad de hamburg” na minha final tradução.
- Se uma palavra aparecer mais de uma vez em um arquivo de dicionário, o sistema sempre usará a última entrada fornecida. O dicionário não deve conter várias traduções da mesma palavra.

## <a name="next-steps"></a>Próximas etapas

- Leia sobre as diretrizes [sobre formatos de documentos](document-formats-naming-convention.md).
