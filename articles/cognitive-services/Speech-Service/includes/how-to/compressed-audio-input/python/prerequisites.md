---
author: amitkumarshukla
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: amishu
ms.openlocfilehash: b8dda0347e5713ef35705425b54f29a110803488
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97821512"
---
O tratamento de áudio compactado é implementado usando o [GStreamer](https://gstreamer.freedesktop.org). Por motivos de licenciamento, os binários GStreamer não são compilados e vinculados ao SDK de fala. Os desenvolvedores precisam instalar várias dependências e plug-ins, consulte [instalando no Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) ou [instalando no Linux](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c). Os binários GStreamer precisam estar no caminho do sistema, para que o SDK de fala possa carregar os binários durante o tempo de execução. Por exemplo, no Windows, se o SDK de fala for capaz de encontrar `libgstreamer-1.0-0.dll` durante o tempo de execução, isso significará que os binários GStreamer estão no caminho do sistema.

