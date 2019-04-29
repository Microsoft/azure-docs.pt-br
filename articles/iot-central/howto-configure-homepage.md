---
title: Configurar o painel do aplicativo do Azure IoT Central | Microsoft Docs
description: Como um construtor, saiba como configurar o painel do aplicativo padrão do Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/13/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 3168bbbf70c1ffeb3827482459febbcea256eda6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60886931"
---
# <a name="configure-the-application-dashboard"></a>Configurar o painel do aplicativo

O **Dashboard** é a página que é carregado quando os usuários que têm acesso ao aplicativo navegam para a URL do aplicativo. Se você selecionou o **exemplo Contoso** ou **exemplo Devkits** modelo de aplicativo para criar seu aplicativo, seu aplicativo tem um painel predefinido. Se você tiver escolhido o **aplicativo personalizado** modelo de aplicativo, o painel está em branco.

> [!NOTE]
> Os usuários também podem [criar seus próprios painéis pessoais](howto-personalize-dashboard.md) para usar em vez do painel de aplicativo padrão.

## <a name="add-tiles"></a>Adicionar blocos

Captura de tela a seguir mostra o painel em um aplicativo criado a partir de **Contoso exemplo** modelo. Para personalizar o painel padrão para seu aplicativo, selecione **editar** na parte superior direita da página.

![Painel para aplicativos baseados no modelo "Exemplo Contoso"](media/howto-configure-homepage/image1.png)

Selecionando **editar**, abre o painel de biblioteca. A biblioteca contém os blocos e os primitivos de painel, que você pode usar para personalizar o painel.

![Biblioteca de painéis](media/howto-configure-homepage/image2.png)

Por exemplo, você pode adicionar um **configurações do dispositivo e propriedades** lado a lado para mostrar uma seleção dos valores atuais de configurações e propriedades para um dispositivo. Para fazer isso, primeiro selecione um **Modelo de dispositivo**, em seguida, selecione uma **Instância de dispositivo**. Depois disso, dê um título ao título e selecione uma **Configuração** ou uma **Propriedade** para exibir. Captura de tela a seguir mostra as configurações e as propriedades selecionadas para adicionar ao bloco. Selecione **feito** para salvar a alteração no painel.

![Formulário "Configurar detalhes do dispositivo" com detalhes para configurações e propriedades](media/howto-configure-homepage/image3.png)

Agora, quando um operador exibe o painel do aplicativo padrão, eles veem o bloco de novo com o **definir temperatura** configuração para o dispositivo:

![Guia "Painel" com as configurações e propriedades exibidas para o bloco](media/howto-configure-homepage/image4.png)

Você pode explorar os outros tipos de bloco na biblioteca para descobrir como personalizar ainda mais o painel do aplicativo padrão.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como configurar o painel de aplicativo padrão do Azure IoT Central, você pode:

> [!div class="nextstepaction"]
> [Saiba como preparar e carregar imagens](howto-prepare-images.md)
