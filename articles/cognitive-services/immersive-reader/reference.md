---
title: Referência do SDK do leitor de imersão
titleSuffix: Azure Cognitive Services
description: Referência para o SDK do leitor de imersão
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 1a616bce8c161825853b1966769d9505595d95de
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688323"
---
# <a name="immersive-reader-sdk-reference"></a>Referência do SDK do leitor de imersão

O SDK do leitor de imersão é uma biblioteca JavaScript que permite integrar o leitor de imersão ao seu aplicativo Web.

## <a name="functions"></a>Funções

O SDK expõe uma única função, `ImmersiveReader.launchAsync(token, subdomain, content, options)`.

### <a name="launchasync"></a>launchAsync

Inicia o leitor de imersão dentro `iframe` de um em seu aplicativo Web.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<HTMLDivElement>;
```

#### <a name="parameters"></a>Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- |------------ |
| `token` | cadeia de caracteres | O token de autenticação do Azure AD. Consulte o [How-to de autenticação do Azure ad](./azure-active-directory-authentication.md). |
| `subdomain` | cadeia de caracteres | O subdomínio personalizado do seu recurso de leitor de imersão no Azure. Consulte o [How-to de autenticação do Azure ad](./azure-active-directory-authentication.md). |
| `content` | [Conteúdo](#content) | Um objeto que contém o conteúdo a ser mostrado no leitor de imersão. |
| `options` | [Opções](#options) | Opções para configurar determinados comportamentos do leitor de imersão. Opcional. |

#### <a name="returns"></a>Retornos

Retorna um `Promise<HTMLDivElement>` que resolve quando o leitor de imersão é carregado. O `Promise` resolve para um `div` elemento cujo único filho é um `iframe` elemento que contém a página do leitor de imersão.

#### <a name="exceptions"></a>Exceções

O retornado `Promise` será rejeitado com um [`Error`](#error) objeto se o leitor de imersão não for carregado. Para obter mais informações, consulte os [códigos de erro](#error-codes).

## <a name="types"></a>Tipos

### <a name="content"></a>Conteúdo

Contém o conteúdo a ser mostrado no leitor de imersão.

```typescript
{
    title?: string;      // Title text shown at the top of the Immersive Reader (optional)
    chunks: [ {          // Array of chunks
        content: string; // Plain text string
        lang?: string;   // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
        mimeType?: string; // MIME type of the content (optional). Defaults to 'text/plain' if not specified.
    } ];
}
```

#### <a name="supported-mime-types"></a>Tipos MIME com suporte

| Tipo MIME | Descrição |
| --------- | ----------- |
| texto/sem formatação | Texto sem formatação. |
| aplicativo/MathML + XML | MathML (matematica Markup Language). [Saiba mais](https://developer.mozilla.org/en-US/docs/Web/MathML).

### <a name="options"></a>Opções

Contém propriedades que configuram determinados comportamentos do leitor de imersão.

```typescript
{
    uiLang?: string;   // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;  // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number; // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean; // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
}
```

### <a name="error"></a>Erro

Contém informações sobre o erro.

```typescript
{
    code: string;    // One of a set of error codes
    message: string; // Human-readable representation of the error
}
```

#### <a name="error-codes"></a>Códigos de erro

| Código | Descrição |
| ---- | ----------- |
| BadArgument | O argumento fornecido é inválido, `message` consulte para obter detalhes. |
| Tempo limite | Falha ao carregar o leitor de imersão no tempo limite especificado. |
| TokenExpired| O token fornecido expirou. |

## <a name="launching-the-immersive-reader"></a>Iniciando o leitor de imersão

O SDK fornece o estilo padrão para o botão iniciar o leitor de imersão. Use o `immersive-reader-button` atributo Class para habilitar esse estilo.

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Atributos opcionais

Use os atributos a seguir para configurar a aparência do botão.

| Atributo | Descrição |
| --------- | ----------- |
| `data-button-style` | Define o estilo do botão. Pode ser `icon`, `text` ou `iconAndText`. Assume o padrão de `icon`. |
| `data-locale` | Define a localidade, por exemplo `en-US`, `fr-FR`. O padrão é inglês. |
| `data-icon-px-size` | Define o tamanho do ícone em pixels. O padrão é 20px. |

## <a name="browser-support"></a>Suporte ao navegador

Use as versões mais recentes dos seguintes navegadores para obter a melhor experiência com o leitor de imersão.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>Próximas etapas

* Explorar o [SDK da Leitura Avançada no GitHub](https://github.com/microsoft/immersive-reader-sdk)
* [Início Rápido: Criar um aplicativo Web que inicia o leitor de imersãoC#()](./quickstart.md)