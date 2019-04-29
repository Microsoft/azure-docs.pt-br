---
title: Sobre o SDK de Fala - Serviços de Fala
titleSuffix: Azure Cognitive Services
description: O SDK (Speech Software Development Kit) oferece aos aplicativos acesso nativo às funções do serviço de fala, facilitando o desenvolvimento de software. Este artigo fornece detalhes adicionais sobre o SDK para Windows, Linux e Android.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: wolfma
ms.openlocfilehash: bb28972469ca33a7fb33a50a8ce2e9bf326222b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61460399"
---
# <a name="about-the-speech-sdk"></a>Sobre o SDK de Fala

Fala Software Development Kit (SDK) fornece o acesso de aplicativos para as funções dos serviços de fala, tornando mais fácil de desenvolver software habilitado para fala. Atualmente, os SDKs fornecem acesso aos **fala em texto**, **texto em fala**, **tradução de fala**, e **reconhecimento de intenção de**. Uma visão geral sobre os recursos e as plataformas com suporte pode ser encontrada na documentação do [página de entrada](https://aka.ms/csspeech).

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Obter o SDK

### <a name="windows"></a> Windows

Para o Windows, suportamos os seguintes idiomas:

* C# (UWP e .NET), C++: você pode referenciar e usar a versão mais recente do nosso pacote NuGet do SDK de Fala. O pacote inclui bibliotecas de clientes de 32 e 64 bits e bibliotecas gerenciadas (.NET). O SDK pode ser instalado no Visual Studio usando o NuGet. Pesquise **Microsoft.CognitiveServices.Speech**.

* Java: você pode fazer referência e usar a versão mais recente do nosso pacote do Maven de SDK de Fala, que dá suporte apenas ao Windows x64. Em seu projeto Maven, adicione `https://csspeechstorage.blob.core.windows.net/maven/`como um repositório adicional e faça referência`com.microsoft.cognitiveservices.speech:client-sdk:1.4.0` como uma dependência.

### <a name="linux"></a>Linux

> [!NOTE]
> Atualmente, só oferecemos suporte ao Ubuntu 16.04 e 18.04 em um PC (x86 ou x64 para desenvolvimento C++ e x64 para .NET Core, Java e Python).

Verifique se você tem o compilador e as bibliotecas necessários instalados executando os seguintes comandos do shell:

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

* C#: você pode referenciar e usar a versão mais recente do nosso pacote NuGet do SDK de Fala. Para referenciar o SDK, adicione a seguinte referência de pacote ao seu projeto:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.4.0" />
  ```

* Java: você pode referenciar e usar a versão mais recente do nosso pacote Maven do SDK de Fala. Em seu projeto Maven, adicione `https://csspeechstorage.blob.core.windows.net/maven/`como um repositório adicional e faça referência`com.microsoft.cognitiveservices.speech:client-sdk:1.4.0` como uma dependência.

* C++: baixe o SDK como um [pacote .tar](https://aka.ms/csspeech/linuxbinary) e descompacte os arquivos em um diretório de sua escolha. A tabela a seguir mostra a estrutura da pasta do SDK:

  |Caminho|DESCRIÇÃO|
  |-|-|
  |`license.md`|Licença|
  |`ThirdPartyNotices.md`|Avisos de terceiros|
  |`include`|Arquivos de cabeçalho C e C++|
  |`lib/x64`|Biblioteca x64 nativa para vinculação ao seu aplicativo|
  |`lib/x86`|Biblioteca x86 nativa para vinculação ao seu aplicativo|

  Para criar um aplicativo, copie ou mova os binários necessários (e bibliotecas) para o seu ambiente de desenvolvimento. Incluí-las conforme necessário no processo de compilação.

### <a name="android"></a>Android

O SDK do Java para Android é empacotado como um [AAR (Biblioteca Android)](https://developer.android.com/studio/projects/android-library), que inclui as bibliotecas necessárias e as permissões necessárias do Android. Está hospedado em um repositório Maven em `https://csspeechstorage.blob.core.windows.net/maven/` como pacote `com.microsoft.cognitiveservices.speech:client-sdk:1.4.0`.

Para consumir o pacote do seu projeto do Android Studio, faça as seguintes alterações:

* No arquivo build.gradle no nível do projeto, inclua o seguinte na seção `repository`:

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* No arquivo build.gradle de nível de módulo, inclua o seguinte na seção `dependencies`:

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.4.0'
  ```

O SDK do Java também faz parte do [SDK dos Dispositivos de Fala](speech-devices-sdk.md).

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Próximos passos

* [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como reconhecer fala em C#](quickstart-csharp-dotnet-windows.md)
