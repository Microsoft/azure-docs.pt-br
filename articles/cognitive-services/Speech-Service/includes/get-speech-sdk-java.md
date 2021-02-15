---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 2ab636679e59536a2ddfaa8603dc2da45811cd2f
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99214602"
---
:::row:::
    :::column span="3":::
        O SDK do Java para Android é empacotado como um <a href="https://developer.android.com/studio/projects/android-library" target="_blank">aar (biblioteca do <span class="docon docon-navigate-external x-hidden-focus"></span> Android) </a>, que inclui as bibliotecas necessárias e as permissões do Android necessárias. Está hospedado em um repositório Maven em `https://csspeechstorage.blob.core.windows.net/maven/` como pacote `com.microsoft.cognitiveservices.speech:client-sdk:1.15.0`.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Java" src="https://docs.microsoft.com/media/logos/logo_java.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

Para consumir o pacote do seu projeto do Android Studio, faça as seguintes alterações:

1. No arquivo *Build. gradle* de nível de projeto, adicione o seguinte à `repositories` seção:
  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

2. No arquivo *Build. gradle* de nível de módulo, adicione o seguinte à `dependencies` seção:
  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.15.0'
  ```

O SDK do Java também faz parte do [SDK dos Dispositivos de Fala](../speech-devices-sdk.md).

#### <a name="additional-resources"></a>Recursos adicionais

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android" target="_blank">Código-fonte do início rápido de Java específico do Android <span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre" target="_blank">Código-fonte de início rápido do JRE (Java Runtime) <span class="docon docon-navigate-external x-hidden-focus"></span></a>
