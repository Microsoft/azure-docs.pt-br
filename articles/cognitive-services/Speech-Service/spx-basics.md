---
title: Guia de início rápido da CLI de Fala – serviço de Fala
titleSuffix: Azure Cognitive Services
description: Introdução à CLI de Fala do Azure. Você pode interagir com os serviços de Fala (como conversão de fala em texto, conversão de texto em fala e tradução de fala) sem necessidade de escrever código.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: trbye
ms.openlocfilehash: 53138a22c58e89ade4af234630e9429a19738a6a
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556461"
---
# <a name="get-started-with-the-azure-speech-cli"></a>Introdução à CLI de Fala do Azure

Neste artigo, você aprenderá a usar a CLI de Fala, uma interface de linha de comando, para acessar serviços de Fala (como conversão de fala em texto, conversão de texto em fala e tradução de fala) sem necessidade de escrever código. A CLI de Fala está pronta para produção e pode ser usada para automatizar fluxos de trabalho simples no serviço de Fala, usando scripts de shell ou `.bat`.

Este artigo pressupõe que você tem conhecimento sobre o prompt de comando, o terminal ou o PowerShell.

[!INCLUDE [](includes/spx-setup.md)]

## <a name="basic-usage"></a>Uso básico

Esta seção mostra alguns comandos de SPX básicos que geralmente são úteis para teste e experimentação na primeira vez. Comece examinando a ajuda interna da ferramenta executando o comando a seguir.

```console
spx
```

Você pode pesquisar os tópicos de ajuda por palavra-chave. Por exemplo, digite o seguinte comando para ver uma lista de exemplos de uso da CLI de Fala:

```console
spx help find --topics "examples"
```

Insira o seguinte comando para ver opções do comando Recognize:

```console
spx help recognize
```

Comandos de ajuda adicionais listados na coluna à direita. Você pode inserir esses comandos para obter ajuda detalhada sobre os subcomandos.

## <a name="speech-to-text-speech-recognition"></a>Conversão de fala em texto (reconhecimento de fala)

Vamos usar a CLI de Fala para realizar conversão de fala em texto (reconhecimento de fala) usando o microfone padrão do sistema. Depois de inserir o comando, o SPX começará a escutar áudio no dispositivo de entrada ativo atual e será interrompido depois que você pressionar **ENTER**. A fala gravada é reconhecida e convertida em texto na saída do console.

>[!IMPORTANT]
> Se você estiver usando um contêiner do Docker, `--microphone` não vai funcionar.

Execute este comando:

```console
spx recognize --microphone
```

Com a CLI de Fala, você também pode reconhecer a fala de um arquivo de áudio.

```console
spx recognize --file /path/to/file.wav
```

> [!TIP]
> Se você estiver reconhecendo a fala de um arquivo de áudio em um contêiner do Docker, verifique se o arquivo de áudio está localizado no diretório que você montou na etapa anterior.

Não se esqueça, se você tiver dificuldades ou quiser saber mais sobre as opções de reconhecimento da CLI de Fala, basta digitar:

```console
spx help recognize
```

## <a name="text-to-speech-speech-synthesis"></a>Conversão de texto em fala (síntese de fala)

A execução do comando a seguir usará o texto como entrada e produzirá a fala sintetizada no dispositivo de saída ativo atual (por exemplo, os alto-falantes do seu computador).

```console
spx synthesize --text "Testing synthesis using the Speech CLI" --speakers
```

Você também pode salvar a saída sintetizada em um arquivo. Neste exemplo, criaremos um arquivo chamado `my-sample.wav` no diretório em que o comando é executado.

```console
spx synthesize --text "Enjoy using the Speech CLI." --audio output my-sample.wav
```

Esses exemplos presumem que você esteja testando em inglês. No entanto, damos suporte à síntese de fala em vários idiomas. Você pode obter uma lista completa de vozes com este comando ou visitando a [página de suporte ao idioma](./language-support.md).

```console
spx synthesize --voices
```

Veja aqui como usar uma das vozes que você descobriu.

```console
spx synthesize --text "Bienvenue chez moi." --voice fr-CA-Caroline --speakers
```

Não se esqueça, se você tiver dificuldades ou quiser saber mais sobre as opções de síntese da CLI de Fala, basta digitar:

```console
spx help synthesize
```

## <a name="speech-to-text-translation"></a>Tradução de fala em texto

Com a CLI de Fala, você também pode realizar tradução de fala em texto. Execute este comando para capturar áudio do microfone padrão e gerar a tradução como texto. Tenha em mente que você precisa fornecer o idioma `source` e `target` com o comando `translate`.

```console
spx translate --microphone --source en-US --target ru-RU
```

Ao traduzir para vários idiomas, separe os códigos de idioma com `;`.

```console
spx translate --microphone --source en-US --target ru-RU;fr-FR;es-ES
```

Se você quiser salvar a saída de sua tradução, use o sinalizador `--output`. Neste exemplo, você também lerá de um arquivo.

```console
spx translate --file /some/file/path/input.wav --source en-US --target ru-RU --output file /some/file/path/russian_translation.txt
```

> [!NOTE]
> Consulte o [artigo idioma e localidade](language-support.md) para obter uma lista de todos os idiomas compatíveis, com os respectivos códigos de localidade.

Não se esqueça, se você tiver dificuldades ou quiser saber mais sobre as opções de tradução da CLI de Fala, basta digitar:

```console
spx help translate
```

## <a name="next-steps"></a>Próximas etapas

* [Opções de configuração da CLI de Fala](./spx-data-store-configuration.md)
* [Operações em lote com a CLI de Fala](./spx-batch-operations.md)
