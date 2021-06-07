---
title: incluir arquivo
description: incluir arquivo
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/19/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9151a84aa03e9b87b02278672622d458fbc5281e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99538657"
---
Para criar um Hub IoT usando o Portal do Azure:

1. Entre no [portal do Azure](https://portal.azure.com).

1. Na página inicial do Azure, selecione **Criar um recurso** e insira *Hub IoT* em **Pesquisar no Marketplace**.

1. Selecione **Hub IoT** nos resultados da pesquisa e, em seguida, selecione **Criar**.

1. Na guia **Básico**, preencha os campos da seguinte maneira:

   - **Assinatura**: Selecione a assinatura a ser usada para o hub.

   - **Grupo de Recursos**: Selecione um grupo de recursos ou crie um novo. Para criar um novo, selecione **Criar novo** e preencha o nome que deseja usar. Para usar um grupo de recursos existente, selecione esse grupo de recursos. Para obter mais informações, veja [Gerenciar grupos de recursos do Azure Resource Manager](../articles/azure-resource-manager/management/manage-resource-groups-portal.md). Este tutorial usa o nome **tutoriais-iot hub rg**.

   - **Região**: Selecione a região na qual você deseja que o hub esteja localizado. Selecione a localização mais próxima de você. Alguns recursos, como os [Fluxos de dispositivo do Hub IoT](../articles/iot-hub/iot-hub-device-streams-overview.md), estão disponíveis apenas em regiões específicas. Para esses recursos limitados, você deve selecionar uma das regiões com suporte. Este tutorial usa a região **Oeste dos EUA**.

   - **Nome do Hub IoT**: Digite um nome para o seu hub. Esse nome deve ser globalmente exclusivo. Este tutorial usa o nome **tutorials-iot-hub**. Você precisa escolher seu próprio nome exclusivo ao criar seu hub.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-basics.png" alt-text="Criar um hub no portal do Azure":::

1. Selecione **Avançar: Rede** para continuar criando o hub.

   Escolha os pontos de extremidade que podem se conectar ao Hub IoT. Você pode selecionar a configuração padrão **Ponto de extremidade público (todas as redes)** ou escolher **Ponto de extremidade público (intervalos IP selecionados)** ou **Ponto de extremidade privado**. Aceite a configuração padrão para este tutorial.

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-networking.png" alt-text="Escolha os pontos de extremidade que podem se conectar.":::

1. Selecione **Avançar: Gerenciamento** para continuar criando o hub.

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-management.png" alt-text="Defina o tamanho e a escala para um novo hub usando o portal do Azure.":::

   Você pode aceitar as configurações padrão aqui. Se desejar, você poderá modificar qualquer um dos seguintes campos:

   - **Tipo e escala de preço**: Seu nível selecionado. Escolha a camada gratuita. A camada gratuita destina-se a testes e avaliação. Ela permite que 500 dispositivos sejam conectados ao hub e até 8 mil mensagens por dia. Cada assinatura do Azure pode criar um hub IoT na Camada gratuita.

   - **Unidades do Hub IoT**: O número de mensagens permitidas por unidade ao dia depende do tipo de preço do seu hub. Por exemplo, se você quiser que o hub dê suporte à entrada de 700 mil mensagens, escolha duas unidades do nível S1.
   Cada assinatura do Azure pode criar um hub IoT na Camada gratuita. Para obter detalhes sobre as outras opções da camada, consulte [Escolher a camada certa do Hub IoT](../articles/iot-hub/iot-hub-scaling.md).

   - **Defender para IoT**: Ative essa opção para adicionar uma camada extra de proteção contra ameaças à IoT e aos seus dispositivos. Ela não está disponível para hubs na camada gratuita. Para mais informações sobre esse recurso, veja [Central de Segurança do Azure para IoT](/azure/asc-for-iot/).

   - **Configurações avançadas** > **Partições de dispositivo para nuvem**: Essa propriedade está relacionada a mensagens de dispositivo para nuvem para o número de leitores simultâneos das mensagens. A maioria dos hubs precisa apenas de quatro partições. Um hub de camada gratuita é limitado a duas partições.

1. Selecione **Avançar: Marcas** para avançar para a próxima tela.

   Marcas são pares nome/valor. Você pode atribuir a mesma marca a vários recursos e grupos de recursos para categorizá-los e consolidar a cobrança. Para obter mais informações, veja [Usar marcas para organizar os recursos do Azure](../articles/azure-resource-manager/management/tag-resources.md).

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-tags.png" alt-text="Atribua marcas para o hub usando o portal do Azure.":::

1. Selecione **Avançar: Analisar + criar** para examinar suas escolhas. Você verá algo semelhante a esta tela, mas com os valores que você selecionou ao criar o hub.

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-create.png" alt-text="Examine as informações para criar o hub.":::

1. Anote o nome do hub IoT escolhido. Você usará esse valor posteriormente no tutorial.
