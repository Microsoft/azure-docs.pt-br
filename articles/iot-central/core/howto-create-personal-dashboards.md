---
title: Criar painéis pessoais do Azure IoT Central | Microsoft Docs
description: Como usuário, saiba como criar e gerenciar seus painéis pessoais.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 68bb4e2ac525270004a698471cd44955cd25fefc
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146102"
---
# <a name="create-and-manage-multiple-dashboards"></a>Criar e gerenciar vários painéis

O **Painel** é a página que é carregada quando você navega pela primeira vez no aplicativo. Um **construtor** no aplicativo define o painel do aplicativo padrão para todos os usuários. Além disso, você pode criar seu próprio painel de aplicativo personalizado. Você pode ter vários painéis que exibem dados diferentes e alternam entre eles.

Se você for um **administrador** do aplicativo, também pode criar até 10 painéis de nível de aplicativo para compartilhar com outros usuários do aplicativo. Somente **administradores** têm a capacidade de criar, editar e excluir painéis de nível de aplicativo.  

## <a name="create-dashboard"></a>Criar painel

A captura de tela a seguir mostra o painel em um aplicativo criado com base no modelo de **Aplicativo personalizado** . Você pode substituir o painel do aplicativo padrão por um painel pessoal ou, se você for um administrador, por outro painel de nível de aplicativo. Para fazer isso, selecione **+ Novo** na parte superior esquerda da página.

> [!div class="mx-imgBorder"]
> ![Painel para aplicativos com base no modelo de "Aplicativo personalizado"](media/howto-create-personal-dashboards/dashboard-custom-app.png)

Selecionar **+ Novo** abre o editor de painel. No editor, você pode dar um nome ao painel e escolher itens na biblioteca. A biblioteca contém os blocos e os primitivos de painel que você pode usar para personalizar o painel.

> [!div class="mx-imgBorder"]
> ![Biblioteca de painéis](media/howto-create-personal-dashboards/dashboard-library.png)

Se você for um **administrador** do aplicativo, tem a opção de criar um painel de nível pessoal ou um painel de nível de aplicativo. Se você criar um painel de nível pessoal, só você poderá vê-lo. Se você criar um painel de nível de aplicativo, todos os usuários do aplicativo poderão vê-lo. Depois de inserir um título e selecionar o tipo de painel que você deseja criar, pode salvar e adicionar blocos posteriormente. Ou então, se você estiver pronto agora e tiver adicionado um modelo de dispositivo e uma instância de dispositivo, pode criar o primeiro bloco.  

> [!div class="mx-imgBorder"]
> Formulário !["Configurar detalhes do dispositivo" com detalhes para temperatura](media/howto-create-personal-dashboards/device-details.png)

Por exemplo, você pode adicionar um bloco de **Telemetria** para a temperatura atual do dispositivo. Para fazer isso:

1. Selecionar um **Modelo de dispositivo**
1. Selecione um dispositivo em **Dispositivos** para o dispositivo que você deseja ver em um bloco do painel. Em seguida, você verá uma lista das propriedades do dispositivo que podem ser usadas no bloco.
1. Para criar o bloco no painel, clique em **Temperatura** e arraste para a área do painel. Você também pode clicar na caixa de seleção ao lado de **Temperatura** e clicar em **Adicionar bloco** . A captura de tela a seguir mostra a seleção de um dispositivo e modelo de dispositivo, criando um bloco de Telemetria de temperatura no painel.
1. Selecione **Salvar** na parte superior esquerda para salvar as alterações no painel.

> [!div class="mx-imgBorder"]
> Guia !["Painel" com detalhes para bloco de Temperatura](media/howto-create-personal-dashboards/temperature-tile-edit.png)

Agora, quando você exibir seu painel pessoal, verá o novo bloco com a configuração de **Temperatura** do dispositivo:

> [!div class="mx-imgBorder"]
> ![Captura de tela que mostra o novo bloco com a configuração de temperaturas para o dispositivo.](media/howto-create-personal-dashboards/temperature-tile-complete.png)

Você pode explorar outros tipos de bloco na biblioteca para descobrir como personalizar ainda mais seus painéis pessoais.

Para saber mais sobre como usar blocos no Azure IoT Central, confira [Adicionar blocos ao painel](howto-add-tiles-to-your-dashboard.md).

## <a name="manage-dashboards"></a>Gerenciar Painéis

Você pode ter vários painéis pessoais e alternar entre eles ou escolher um dos painéis do aplicativo padrão:

> [!div class="mx-imgBorder"]
> ![Alternar entre os painéis](media/howto-create-personal-dashboards/switch-dashboards.png)

Você pode editar seus painéis pessoais e excluir os painéis que não são mais necessários. Se você for um **administrador** , também pode editar ou excluir painéis de nível de aplicativo.

> [!div class="mx-imgBorder"]
> ![Excluir painéis](media/howto-create-personal-dashboards/delete-dashboards.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como criar e gerenciar painéis pessoais, você pode [Aprender a gerenciar as preferências do aplicativo](howto-manage-preferences.md).
