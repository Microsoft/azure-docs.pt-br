---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: f8c7b9ee90e947534d6f938b1eb22f58b57270e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91376604"
---
:::row:::
    :::column span="3":::
        O SDK de fala para JavaScript está disponível como um pacote NPM, consulte <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Microsoft-cognitivaservices-Speech- <span class="docon docon-navigate-external x-hidden-focus"></span> SDK</a> e seu repositório do GitHub complementar <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">cognitiva-Services-Speech-SDK <span class="docon docon-navigate-external x-hidden-focus"></span> -js </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Embora o SDK de fala para JavaScript esteja disponível como um pacote NPM, portanto, tanto os navegadores da Web do cliente quanto os Node.js podem consumi-lo-considere as várias implicações de arquitetura de cada ambiente. Por exemplo, o <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">modelo de objeto de documento ( <span class="docon docon-navigate-external x-hidden-focus"></span> dom)</a> não está disponível para aplicativos do lado do servidor, assim como o <a href="https://nodejs.org/api/fs.html" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> sistema de arquivos</a> não está disponível para aplicativos do lado do cliente.

### <a name="nodejs-package-manager-npm"></a>Gerenciador de pacotes do Node.js (NPM)

Para instalar o SDK de fala para JavaScript, execute o seguinte `npm install` comando abaixo.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>Marca de script HTML

Como alternativa, você poderia incluir diretamente uma `<script>` marca no elemento HTMLs `<head>` , contando com o <a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank"> **JSDelivr** NPM sindicate <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@latest/distrib/browser/microsoft.cognitiveservices.speech.sdk.bundle-min.js">
</script>
```

Para obter mais informações, consulte o guia de <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">início rápido <span class="docon docon-navigate-external x-hidden-focus"></span> do SDK de fala do navegador da Web </a>.
