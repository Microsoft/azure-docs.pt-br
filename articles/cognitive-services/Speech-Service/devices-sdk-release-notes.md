---
title: Documentação do SDK dos Dispositivos de Fala
titleSuffix: Azure Cognitive Services
description: As notas de versão fornecem um log de atualizações, aprimoramentos, correções de bugs e alterações no SDK de dispositivos de fala. Este artigo é atualizado com cada versão do SDK dos dispositivos de fala.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: wellsi
ms.openlocfilehash: f9215469b1436ce9dc95c140e6969366d80d1ced
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607126"
---
# <a name="release-notes-speech-devices-sdk"></a>Notas de versão: SDK de dispositivos de fala

As seções a seguir listam as alterações nas versões mais recentes.

## <a name="speech-devices-sdk-1150"></a>SDK de dispositivos de fala 1.15.0:

- Atualizado para uma nova pilha de áudio da Microsoft (MAS) com beamforming e redução de ruído aprimoradas para fala.
- Reduziu o tamanho binário até 70%, dependendo do destino.
- Suporte para [áudio do Azure Percept](../../azure-percept/overview-azure-percept-audio.md) com [liberação binária](https://aka.ms/sdsdk-download-APAudio).
- Atualizado o componente [SDK de fala](./speech-sdk.md) para a versão 1.15.0. Para obter mais informações, consulte [notas de versão](./releasenotes.md).

## <a name="speech-devices-sdk-1110"></a>SDK de dispositivos de fala 1.11.0:

- Suporte para [geometrias de matriz de microfone arbitrária](how-to-devices-microphone-array-configuration.md) e definição do ângulo de trabalho por meio de um [arquivo de configuração](https://aka.ms/sdsdk-micarray-json).
- Suporte para [URBETTER DDK](http://www.urbetter.com/products_56/278.html).
- Binários liberados para o [GGEC palestrante](https://aka.ms/sdsdk-download-speaker) usado em nosso [exemplo de assistente de voz](https://aka.ms/sdsdk-speaker).
- Binários liberados para [Linux ARM32](https://aka.ms/sdsdk-download-linux-arm32) e [Linux ARM 64](https://aka.ms/sdsdk-download-linux-arm64) para Raspberry Pi e dispositivos semelhantes.
- Atualizado o componente [SDK de fala](./speech-sdk.md) para a versão 1.11.0. Para obter mais informações, consulte [notas de versão](./releasenotes.md).

## <a name="speech-devices-sdk-190"></a>SDK de dispositivos de fala 1.9.0:

- São fornecidos binários iniciais para [URBETTER DDK](https://aka.ms/sdsdk-download-urbetter) (Linux ARM64).
- O Roobo v1 agora usa o Maven para o SDK de fala
- Atualizado o componente [SDK de fala](./speech-sdk.md) para a versão 1.9.0. Para obter mais informações, consulte [notas de versão](./releasenotes.md).

## <a name="speech-devices-sdk-170"></a>SDK de dispositivos de fala 1.7.0:

- Agora há suporte para o ARM do Linux.
- Os binários iniciais para [Roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2) são fornecidos (Linux ARM64).
- Os usuários do Windows podem usar `AudioConfig.fromDefaultMicrophoneInput()` `AudioConfig.fromMicrophoneInput(deviceName)` o ou o para especificar o microfone a ser usado.
- O tamanho da biblioteca foi otimizado.
- Suporte para reconhecimento de Multiturn usando o mesmo objeto de reconhecedor de fala/intenção.
- Corrija o problema ocasional em que o processo deixaria de responder ao parar o reconhecimento.
- Os aplicativos de exemplo agora contêm um arquivo participantes. Properties de exemplo para demonstrar o formato do arquivo.
- Atualizado o componente [SDK de fala](./speech-sdk.md) para a versão 1.7.0. Para obter mais informações, consulte [notas de versão](./releasenotes.md).

## <a name="speech-devices-sdk-160"></a>SDK de dispositivos de fala 1.6.0:

- Suporte ao [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) no Windows e Linux com [aplicativo de exemplo](./speech-devices-sdk.md) comum
- Atualizado o componente [SDK de fala](./speech-sdk.md) para a versão 1.6.0. Para obter mais informações, consulte [notas de versão](./releasenotes.md).

## <a name="speech-devices-sdk-151"></a>SDK de dispositivos de fala 1.5.1:

- Inclua [transcrição de conversa](./conversation-transcription.md) no aplicativo de exemplo.
- Atualizado o componente [SDK de fala](./speech-sdk.md) para a versão 1.5.1. Para obter mais informações, consulte [notas de versão](./releasenotes.md).

## <a name="speech-devices-sdk-150-2019-may-release"></a>SDK do Speech Devices: 2019 – lançamento de maio

- O SDK de dispositivos de fala agora está disponível e não é mais uma visualização restrita.
- Atualizado o componente [SDK de fala](./speech-sdk.md) para a versão 1.5.0. Para obter mais informações, consulte [notas de versão](./releasenotes.md).
- A nova tecnologia de palavra-chave traz melhorias significativas de qualidade, consulte alterações significativas.
- Novo pipeline de processamento de áudio para um reconhecimento de campo aprimorado.

**Alterações interruptivas**

- Devido à nova palavra-chave Technology, todas as palavras-chave devem ser recriadas em nosso portal de palavra-chave aprimorado. Para remover totalmente as palavras-chave antigas do dispositivo, desinstale o aplicativo antigo.
  - desinstalação de ADB com. Microsoft. cognitivaservices. Speech. Samples. sdsdkstarterapp

## <a name="speech-devices-sdk-140-2019-apr-release"></a>SDK de dispositivos de fala 1.4.0:2019 – versão de abril

- Atualizado o componente [SDK de fala](./speech-sdk.md) para a versão 1.4.0. Para obter mais informações, consulte [notas de versão](./releasenotes.md).

## <a name="speech-devices-sdk-131-2019-mar-release"></a>SDK de dispositivos de fala 1.3.1:2019 – versão de mar

- Atualizado o componente [SDK de fala](./speech-sdk.md) para a versão 1.3.1. Para obter mais informações, consulte [notas de versão](./releasenotes.md).
- Tratamento de palavra-chave atualizado, consulte alterações significativas.
- O aplicativo de exemplo adiciona a opção de linguagem para reconhecimento de fala e tradução.

**Alterações interruptivas**

- [A instalação de uma palavra-chave](./custom-keyword-basics.md) foi simplificada, agora faz parte do aplicativo e não precisa de instalação separada no dispositivo.
- O reconhecimento de palavra-chave foi alterado e há suporte para dois eventos.
  - `RecognizingKeyword,` indica que o resultado de fala contém o texto da palavra-chave (não verificado).
  - `RecognizedKeyword`, indica que o reconhecimento de palavra-chave foi concluído reconhecendo a palavra-chave fornecida.

## <a name="speech-devices-sdk-110-2018-nov-release"></a>SDK 1.1.0 de dispositivos de fala: 2018 – versão de novembro

- Atualizado o [SDK de Fala](./speech-sdk.md) componente para a versão 1.1.0. Para obter mais informações, consulte [notas de versão](./releasenotes.md).
- A precisão de reconhecimento de fala de campo distante foi aprimorada com nosso algoritmo de processamento de áudio aprimorado.
- O aplicativo de exemplo adicionou suporte ao reconhecimento de fala chinês.

## <a name="speech-devices-sdk-101-2018-oct-release"></a>SDK de dispositivos de fala 1.0.1:2018 – versão de outubro

- O componente do [SDK de Fala](./speech-sdk.md) foi atualizado para a versão 1.0.1. Para obter mais informações, consulte [notas de versão](./releasenotes.md).
- A precisão do reconhecimento de fala será aprimorada com nosso algoritmo de processamento de áudio aprimorada
- Um bug da sessão de áudio de reconhecimento contínuo é corrigido.

**Alterações interruptivas**

- Com essa versão, várias alterações significativas foram introduzidas. Confira [esta página](https://aka.ms/csspeech/breakingchanges_1_0_0) para obter detalhes das APIs.
- Os arquivos de modelo de reconhecimento de palavra-chave não são compatíveis com o SDK 1.0.1 de dispositivos de fala. Os arquivos de palavra-chave existentes serão excluídos depois que os novos arquivos de palavra-chave forem gravados no dispositivo.

## <a name="speech-devices-sdk-050-2018-aug-release"></a>SDK de dispositivos de fala 0.5.0:2018 – versão de agosto

- Melhoria da precisão do reconhecimento de fala pela correção de um bug no código de processamento de áudio.
- O componente do [SDK de Fala](./speech-sdk.md) foi atualizado para a versão 0.5.0. Para obter mais informações, consulte [notas de versão](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release).

## <a name="speech-devices-sdk-0212733-2018-may-release"></a>SDK de dispositivos de fala 0.2.12733:2018-maio de lançamento

A primeira visualização pública do SDK dos Dispositivos de Fala dos Serviços Cognitivos.