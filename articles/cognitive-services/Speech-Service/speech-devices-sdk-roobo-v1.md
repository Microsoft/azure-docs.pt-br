---
title: Speech Devices SDK Roobo Smart Audio dev kit v1 – Speech Service
titleSuffix: Azure Cognitive Services
description: Pré-requisitos e instruções para começar a usar o SDK de dispositivos de fala, kit de desenvolvimento de áudio inteligente Roobo v1.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 73eb1225ffc5fd01f9a27ca99ad2b059d45a36cf
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015283"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>Dispositivo: kit de desenvolvimento de áudio inteligente Roobo

Este artigo fornece informações específicas do dispositivo para o kit de desenvolvimento de áudio inteligente do Roobo.

## <a name="set-up-the-development-kit"></a>Configurar o kit de desenvolvimento

1. O kit de desenvolvimento tem dois conectores micro USB. O conector à esquerda serve para alimentar o kit de desenvolvimento e está realçado como Ligar/Desligar na imagem abaixo. O da direita serve para controlá-lo e está marcado como Depurar na imagem.

    ![Conectar o kit de desenvolvimento](media/speech-devices-sdk/qsg-1.png)

1. Ligue o kit de desenvolvimento usando um cabo micro USB para conectar a porta de energia a um PC ou adaptador de energia. Um indicador de energia verde acenderá sob o quadro superior.

1. Para controlar o kit de desenvolvimento, conecte a porta de depuração a um computador usando um segundo cabo micro USB. É essencial usar um cabo de alta qualidade para garantir comunicações confiáveis.

1. Oriente seu kit de desenvolvimento para configuração circular ou linear.

    |Configuração do kit de desenvolvimento|Orientation|
    |-----------------------------|------------|
    |Circular|Na vertical, com microfones voltada para o teto|
    |Linear|Em seu lado, com os microfones voltados para o usuário (mostrado na imagem a seguir)|

    ![Orientação do kit de desenvolvimento linear](media/speech-devices-sdk/qsg-2.png)

1. Instale os certificados e defina as permissões do dispositivo de som. Digite os seguintes comandos em uma janela de Prompt de comando:

   ```powershell
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > Esses comandos usam a ponte de depuração do Android, `adb.exe`, que faz parte da instalação do Android Studio. Essa ferramenta está localizada em C:\Users\[user name]\AppData\Local\Android\Sdk\platform-tools. Você pode adicionar esse diretório para o caminho para que seja mais conveniente para invocar `adb`. Caso contrário, você deve especificar o caminho completo para a instalação do adb.exe em cada comando que invoca `adb`.
    >
    > Se você vir um erro `no devices/emulators found` , verifique se o cabo USB está conectado e se é um cabo de alta qualidade. Você pode usar `adb devices` para verificar se o computador pode se comunicar com o kit de desenvolvimento, já que ele retornará uma lista de dispositivos.
    >
    > [!TIP]
    > Coloque o microfone e alto-falante no mudo para certificar-se de que você está trabalhando com microfones do kit de desenvolvimento de seu PC. Dessa forma, você não dispara acidentalmente o dispositivo com o áudio do PC.

1. Se você quiser anexar um palestrante ao kit de desenvolvimento, poderá conectá-lo à linha de saída de áudio. Você deve escolher um palestrante de boa qualidade com um plugue analógico de 3,5 mm.

    ![Áudio do Vysor](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>Informações de desenvolvimento

Para obter mais informações de desenvolvimento, consulte o [Guia de desenvolvimento do Roobo](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio"></a>Áudio

O Roobo fornece uma ferramenta que captura todos os áudios para memória flash. Isso pode ajudá-lo a solucionar problemas de áudio. Uma versão da ferramenta é fornecida para cada configuração do kit de desenvolvimento. No  [site do Roobo](http://ddk.roobo.com/), selecione seu dispositivo e, em seguida, selecione o link **Ferramentas do Roobo** na parte inferior da página.

## <a name="next-steps"></a>Próximas etapas

* [Executar o aplicativo de exemplo do Android](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android)