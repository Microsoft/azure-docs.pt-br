---
title: Disponibilidade do cenário-serviço de fala
titleSuffix: Azure Cognitive Services
description: Referência para regiões do serviço de Fala.
services: cognitive-services
author: chrisbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cbasoglu
ms.openlocfilehash: 94fd415909e86a43916ee2f510732a6a6d9c5ed3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552960"
---
# <a name="scenario-availability"></a>Disponibilidade do cenário

O SDK do serviço de fala apresenta muitos cenários em uma ampla variedade de linguagens de programação e ambientes.  Nem todos os cenários estão atualmente disponíveis em todas as linguagens de programação ou todos os ambientes ainda.  Listada abaixo está a disponibilidade de cada cenário.

- **Reconhecimento de fala (SR), lista de frases, intenção, tradução e contêineres locais**
  - Todas as linguagens de programação/ambientes em que há um link de seta <img src="media/index/link.jpg" height="15" width="15"></img> na tabela de início rápido [aqui](https://aka.ms/csspeech).
- **Conversão de texto em fala (TTS)**
  - C++/Windows & Linux
  - C#/Windows & UWP & Unity
  - A API REST de TTS pode ser usada em todas as outras situações.
- **Ativar palavra (palavra-chave spotter/KWS)**
  - C++/Windows & Linux
  - C#/Windows & Linux
  - Python/Windows & Linux
  - Java/Windows & Linux & Android (SDK de dispositivos de fala)
  - A funcionalidade wake Word (keyword spotter/KWS) pode funcionar com qualquer tipo de microfone, o suporte oficial de KWS, no entanto, está limitado atualmente às matrizes de microfone encontradas no hardware do Azure Kinect DK ou no SDK de dispositivos de fala
- **Assistente virtual de voz-primeiro**
  - C++/Windows & Linux & macOS
  - C#/Windows
  - Java/Windows & Linux & macOS & Android (SDK de dispositivos de fala)
- **Transcrição de Conversas**
  - C++/Windows & Linux
  - C#(Framework & .NET Core)/Windows & UWP & Linux
  - Java/Windows & Linux & Android (SDK de dispositivos de fala)
- **Transcrição do Call Center**
  - API REST e pode ser usada em qualquer situação
- **Entrada de áudio compactada por codec**
  - C++/Linux
  - C#/Linux
  - Java/Linux & Android
