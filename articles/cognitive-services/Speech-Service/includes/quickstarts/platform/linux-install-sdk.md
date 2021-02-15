---
author: glecaros
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: gelecaro
ms.openlocfilehash: 785008633318ee6670170d66cba96bd5853f9ed9
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188148"
---
## <a name="install-speech-sdk"></a>Instalar SDK de Fala

O SDK de Fala para Linux pode ser usado para criar aplicativos de 64 bits e 32 bits. As bibliotecas necessárias e os arquivos de cabeçalho podem ser baixados como um arquivo tar de https://aka.ms/csspeech/linuxbinary.

Baixe e instale o SDK conforme a seguir:

1. Escolha um diretório para o qual os arquivos do SDK de Fala serão extraídos e defina a variável de ambiente `SPEECHSDK_ROOT` para apontar para esse diretório. Essa variável facilita fazer referência a esse diretório em futuros comandos. Por exemplo, se você quiser usar o diretório `speechsdk` no seu diretório base, use um comando semelhante ao seguinte:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Crie o diretório se ainda não existir.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Baixe e extraia os arquivos `.tar.gz` que contêm os binários do SDK de Fala:

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. Valide o conteúdo do diretório de nível superior do pacote extraído:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   A listagem do diretório conterá os arquivos de licença e notificações de terceiros, bem como um diretório `include` que contém os arquivos (`.h`) de cabeçalho e um diretório `lib` que contém bibliotecas.

   [!INCLUDE [Linux Binary Archive Content](~/includes/cognitive-services-speech-service-linuxbinary-content.md)]