---
title: Carregar imagens para o aplicativo Azure IoT Central | Microsoft Docs
description: Como um construtor, saiba como preparar e carregar imagens para o aplicativo Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 255033f03acd40580fb4d4a92b0aa9b3e16969f3
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850156"
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>Preparar e carregar imagens para o aplicativo Azure IoT Central

Este artigo descreve como, um construtor, você pode personalizar o aplicativo Central do  Microsoft IoT Central fazendo o upload de imagens personalizadas. Por exemplo, é possível personalizar um painel do dispositivo com uma imagem do dispositivo.

## <a name="before-you-begin"></a>Antes de começar

Para concluir as etapas neste artigo, você precisa do seguinte:

1. Um aplicativo Azure IoT Central. Para obter mais informações, consulte o [Criar um início rápido de aplicativo](quick-deploy-iot-central.md).
1. Uma ferramenta para dimensionar e redimensionar arquivos de imagens.

## <a name="choose-where-to-use-custom-images"></a>Escolher onde usar imagens personalizadas

É possível adicionar imagens personalizadas aos seguintes locais em um aplicativo Azure IoT Central:

* A página **meus aplicativos**

    ![Imagem na página do gerenciador de aplicativos](media/howto-prepare-images/applicationmanager.png)

* O painel do aplicativo

    ![Imagem no painel do aplicativo](media/howto-prepare-images/homepage.png)

* Modelo de dispositivo

    ![Imagem no modelo de dispositivo](media/howto-prepare-images/devicetemplate.png)

* Bloco em um painel do dispositivo

    ![Imagem no bloco de dispositivo](media/howto-prepare-images/devicetile.png)

* Um bloco em um painel de conjunto de dispositivos

    ![Imagem no bloco do conjunto de dispositivos](media/howto-prepare-images/devicesettile.png)

## <a name="prepare-the-images"></a>Preparar as imagens

Nos quatro locais, é possível usar imagens PNG, GIF ou JPEG.

A tabela a seguir resume os tamanhos de imagem que podem ser utilizados:

| Location | Tamanhos |
| -------- | ------ |
| Gerenciador de aplicativos | 268x160 px |
| Modelo de dispositivo | 64x64 px |
| Blocos de painel | O bloco de tamanho menor é 200x200 px, os blocos maiores podem ser múltiplos retangulares ou quadrados de blocos pequenos. Por exemplo 200x400 px, 400x200 px ou 400x400 px |

Para a melhor exibição no aplicativo, é necessário criar imagens que correspondam às dimensões mostradas na tabela anterior.

## <a name="upload-the-images"></a>Carregar as imagens

As seções a seguir descrevem como fazer o upload das imagens nos diferentes locais:

### <a name="application-manager"></a>Gerenciador de Aplicativos

Para carregar uma imagem a ser usada na página **meus aplicativos** , navegue até a página **configurações do aplicativo** na seção **Administração** . Você deve ser um administrador para concluir essa tarefa:

![Carregar imagem de aplicativo](media/howto-prepare-images/uploadapplicationmanager.png)

Selecione o bloco **imagem do aplicativo** para carregar uma imagem (268x160 px) do computador local.

### <a name="application-dashboard"></a>Painel do aplicativo

Para carregar uma imagem no painel do aplicativo, navegue até a página **painel** do seu aplicativo e selecione **Editar**. Você deve ser um construtor para concluir essa tarefa:

![Carregar imagem do painel](media/howto-prepare-images/uploadhomepage.png)

Em **Configurar imagem**, selecione o bloco **imagem** para carregar uma imagem do computador local. O bloco de tamanho menor é 200x200 px, os blocos maiores podem ser múltiplos retangulares ou quadrados de blocos pequenos. Por exemplo, 200 x 400 px, x 200 a 400 px ou 400x400 px.

**Salvar** a imagem carregada. Você pode redimensioná-la enquanto estiver no modo de edição. Selecione **concluído** quando terminar.

### <a name="device-template"></a>Modelo de dispositivo

Para fazer o upload de uma imagem em um modelo de dispositivo, navegue até **Modelos de dispositivo** e escolha o modelo. Você deve ser um construtor para concluir essa tarefa:

![Carregar imagem do modelo de dispositivo](media/howto-prepare-images/uploaddevicetemplate.png)

Selecione o bloco imagem para carregar uma imagem (64 x 64 px) do computador local.

### <a name="device-dashboard"></a>Painel do dispositivo

Para fazer o upload de uma imagem em um dashboard de dispositivo, navegue até **Modelos de dispositivo** e escolha o modelo. Depois escolha a guia **Dashboard**. Você deve ser um construtor para concluir essa tarefa:

![Carregar imagem do painel do dispositivo](media/howto-prepare-images/uploaddevicedashboard.png)

Em **Configurar imagem**, selecione o bloco **imagem** e, em seguida, escolha o arquivo a ser carregado em seu computador local. O bloco de tamanho menor é 200x200 px, os blocos maiores podem ser múltiplos retangulares ou quadrados de blocos pequenos. Por exemplo, 200 x 400 px, x 200 a 400 px ou 400x400 px.

**Salvar** a imagem carregada. Você pode redimensionar e reposicionar enquanto estiver no modo de edição. Selecione **concluído** quando terminar.

### <a name="device-set-dashboard"></a>Painel do conjunto de dispositivos

Para fazer upload de uma imagem em um painel de conjunto de dispositivos, navegue até **Conjuntos de dispositivos** e escolha o conjunto de dispositivos e, em seguida, um dispositivo. Em seguida, escolha a página **painel** e selecione **Editar**:

![Carregar imagem do painel do conjunto de dispositivos](media/howto-prepare-images/uploaddevicesetdashboard.png)

Em **Configurar imagem**, selecione o bloco **imagem** para carregar uma imagem do computador local. O bloco de tamanho menor é 200x200 px, os blocos maiores podem ser múltiplos retangulares ou quadrados de blocos pequenos. Por exemplo, 200 x 400 px, x 200 a 400 px ou 400x400 px.

**Salvar** a imagem carregada. Você pode redimensionar e reposicionar enquanto estiver no modo de edição. Selecione **concluído** quando terminar.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como preparar e carregar imagens para seu aplicativo IoT Central do Azure, aqui estão as próximas etapas sugeridas:

* [Personalizar a interface do usuário do IoT Central do Azure](./howto-customize-ui.md)
* [Configurar o painel do aplicativo](./howto-configure-homepage.md)
* [Gerenciar dispositivos no aplicativo Azure IoT Central](howto-manage-devices.md)