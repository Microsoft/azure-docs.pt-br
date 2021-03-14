---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 8c77efe9e3e301573b032d1dc1dd32bb4a5ab1a1
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2021
ms.locfileid: "103417684"
---
O tratamento de áudio compactado é implementado usando o [GStreamer](https://gstreamer.freedesktop.org). Por motivos de licenciamento, os binários GStreamer não são compilados e vinculados ao SDK de fala. Os desenvolvedores precisam instalar várias dependências e plug-ins, consulte [instalando no Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) ou [instalando no Linux](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c). Os binários GStreamer precisam estar no caminho do sistema, para que o SDK de fala possa carregar os binários durante o tempo de execução. Por exemplo, no Windows, se o SDK de fala for capaz de localizar `libgstreamer-1.0-0.dll` ou `gstreamer-1.0-0.dll` (para o GStreamer mais recente) durante o tempo de execução, significa que os binários GStreamer estão no caminho do sistema.

