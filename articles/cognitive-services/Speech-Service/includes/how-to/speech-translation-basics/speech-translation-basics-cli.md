---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: v-demjoh
ms.openlocfilehash: a5796dfb066ea8ac7c59c02b29ef67b184c67edb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94425120"
---
Um dos principais recursos do serviço de fala é a capacidade de reconhecer a fala humana e traduzi-la para outras linguagens. Neste início rápido, você aprende a usar o SDK de fala em seus aplicativos e produtos para executar a tradução de fala de alta qualidade. Este início rápido converte a fala do microfone em texto em outro idioma.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tem uma conta do Azure e uma assinatura do Serviço de Fala. Se você não tiver uma conta e uma assinatura, [experimente o serviço de Fala gratuitamente](../../../overview.md#try-the-speech-service-for-free).

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="set-source-and-target-language"></a>Definir idioma de origem e de destino

Esse comando chama a CLI de fala para traduzir a fala do microfone de italiano para francês.

```shell
 spx translate --microphone --source it-IT --target fr
```