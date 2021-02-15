---
title: Solucionar problemas do SDK de dispositivos de fala-serviço de fala
titleSuffix: Azure Cognitive Services
description: Este artigo fornece informações para ajudá-lo a resolver problemas que você pode encontrar ao usar o SDK de dispositivos de fala.
services: cognitive-services
author: mswellsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wellsi
ms.openlocfilehash: c590e0972de289a36890a75b220eddbded701ea8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "74815572"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>Solução de problemas do SDK de Dispositivos de Fala

Este artigo fornece informações para ajudá-lo a resolver problemas que você pode encontrar ao usar o SDK de dispositivos de fala.

## <a name="certificate-failures"></a>Falhas de certificado

Se você obtiver falhas de certificado ao usar o serviço de fala, verifique se o dispositivo tem a data e a hora corretas:

1. Vá para **Configurações**. Em **Sistema**, selecione **Data e hora**.

    ![Em Configurações, selecione a Data e hora](media/speech-devices-sdk/qsg-12.png)

1. Mantenha a opção selecionada **Data e hora automática**. Em **Selecionar fuso horário**, selecione seu fuso horário atual.

    ![Selecione as opções de data e fuso horário](media/speech-devices-sdk/qsg-13.png)

    Quando você vir que o tempo do kit de desenvolvimento corresponde o tempo em seu computador, o kit de desenvolvimento estará conectado à internet.

## <a name="next-steps"></a>Próximas etapas

* [Examinar as notas de versão](devices-sdk-release-notes.md)
