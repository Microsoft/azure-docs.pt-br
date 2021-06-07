---
author: ericmitt
ms.author: ericmitt
ms.service: iot-pnp
ms.topic: include
ms.date: 07/13/2020
ms.openlocfilehash: 3ab9161c006a88a254b247a921512698ed46fa95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87351673"
---
1. Abra o Explorador de IoT do Azure.

1. Na página **Hubs IoT**, se você ainda não tiver adicionado uma conexão ao seu Hub IoT, selecione **+ Adicionar conexão**. Insira a cadeia de conexão para o Hub IoT que você criou anteriormente e selecione **Salvar**.

1. Na página **Configurações de IoT Plug and Play**, selecione **+ Adicionar > Pasta local** e selecione a pasta local *modelos* em que você salvou os arquivos de modelo.

1. Na página **Hubs IoT**, clique no nome do hub com o qual você deseja trabalhar. Você verá uma lista de dispositivos registrados no Hub IoT.

1. Clique na **ID do dispositivo** do dispositivo que você criou anteriormente.

1. O menu à esquerda mostra os diferentes tipos de informações disponíveis para o dispositivo.

1. Selecione **Componentes IoT Plug and Play** para ver as informações de modelo do seu dispositivo.

1. Você pode ver os vários componentes do dispositivo. O componente padrão e qualquer outro. Selecione um componente com o qual trabalhar.

1. Selecione a página **Telemetria** e escolha **Iniciar** para ver os dados telemétricos que o dispositivo está enviando para esse componente.

1. Selecione a página **Propriedades (somente leitura)** para ver as propriedades somente leitura relatadas para esse componente.

1. Selecione a página **Propriedades (graváveis)** para ver as propriedades graváveis que podem ser atualizadas para esse componente.

1. Selecione uma propriedade pelo respectivo **nome**, insira um novo valor para ela e selecione **Atualizar o valor desejado**.

1. Para que o novo valor apareça, selecione o botão **Atualizar**.

1. Selecione a página **Comandos** para ver todos os comandos para esse componente.

1. Selecione o comando que você deseja testar e defina o parâmetro correspondente, se houver. Selecione **Enviar comando** para chamar o comando no dispositivo. É possível ver que o dispositivo responde ao comando na janela de prompt de comando em que o código de exemplo está sendo executado.
