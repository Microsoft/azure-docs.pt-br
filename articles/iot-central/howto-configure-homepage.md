---
title: Configurar o painel do aplicativo IoT Central do Azure | Microsoft Docs
description: Como um construtor, saiba como configurar o painel padrão do aplicativo IoT Central do Azure.
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 94ad51ac11687dfe060176132e2030d61b8d4ffc
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850207"
---
# <a name="configure-the-application-dashboard"></a>Configurar o painel do aplicativo

O **painel** é a página que é carregada quando os usuários que têm acesso ao aplicativo navegam até a URL do aplicativo. Se você tiver selecionado o modelo exemplo de aplicativo **contoso** ou **exemplo do devkits** para criar seu aplicativo, seu aplicativo terá um painel predefinido. Se você escolher o modelo aplicativo de **aplicativo personalizado** , seu painel ficará em branco.

> [!NOTE]
> Os usuários também podem [criar seus próprios painéis pessoais](howto-personalize-dashboard.md) para usar em vez do painel de aplicativo padrão.

## <a name="add-tiles"></a>Adicionar blocos

A captura de tela a seguir mostra o painel em um aplicativo criado a partir do modelo de **exemplo da Contoso** . Para personalizar o painel padrão para seu aplicativo, selecione **Editar** na parte superior direita da página.

![Painel para aplicativos com base no modelo "exemplo de contoso"](media/howto-configure-homepage/image1a.png)

Selecionar **Editar**, abre o painel Biblioteca de painéis. A biblioteca contém os blocos e os primitivos de painel que você pode usar para personalizar o painel.

![Biblioteca de painéis](media/howto-configure-homepage/image2a.png)

Por exemplo, você pode adicionar um bloco **configurações de dispositivo e propriedades** para mostrar uma seleção das configurações atuais e os valores de propriedades de um dispositivo. Para fazer isso, primeiro selecione um **Modelo de dispositivo**, em seguida, selecione uma **Instância de dispositivo**. Depois disso, dê um título ao título e selecione uma **Configuração** ou uma **Propriedade** para exibir. A captura de tela a seguir mostra as configurações e as propriedades selecionadas para adicionar ao bloco. Selecione **concluído** para salvar a alteração no painel.

![Formulário "Configurar detalhes do dispositivo" com detalhes para configurações e propriedades](media/howto-configure-homepage/image3a.png)

Agora, quando um operador exibir o painel de aplicativo padrão, ele verá o novo bloco com a configuração **definir temperatura** para o dispositivo:

![Guia "Painel" com as configurações e propriedades exibidas para o bloco](media/howto-configure-homepage/image4a.png)

Você pode explorar outros tipos de bloco na biblioteca para descobrir como personalizar ainda mais o painel de aplicativo padrão.

Para saber mais sobre como usar blocos no Azure IoT Central, confira [usar blocos do Dashboard](howto-use-tiles.md).

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como configurar seu painel de aplicativo padrão do Azure IoT Central, você pode:

> [!div class="nextstepaction"]
> [Saiba como preparar e carregar imagens](howto-prepare-images.md)
