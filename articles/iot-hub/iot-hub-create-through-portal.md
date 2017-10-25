---
title: Usar o Portal do Azure para criar um Hub IoT | Microsoft Docs
description: "Como criar, gerenciar e excluir Hubs IoT do Azure por meio do Portal do Azure. Inclui informações sobre tipos de preço, escala, segurança e configurações de mensagens."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 0909cd2b-4c1e-49e0-b68a-75532caf0a6a
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2017
ms.author: dobett
ms.openlocfilehash: bca7eea5f44bbed3b784b56edaac235161b43e5e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Criar um Hub IoT usando o portal do Azure

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Este artigo descreve:

* Como localizar o serviço de Hub IoT no Portal do Azure.
* Como criar e gerenciar Hubs IoT.

## <a name="where-to-find-the-iot-hub-service"></a>Onde encontrar o serviço de Hub IoT

Você pode encontrar o serviço de Hub IoT nos seguintes locais no portal:

* Escolha **+ Novo**, em seguida, escolha **Internet das Coisas**.
* No Marketplace, escolha **Internet das Coisas**.

## <a name="create-an-iot-hub"></a>Crie um hub IoT

Você pode criar um Hub IoT usando os métodos a seguir:

* A opção **+ Novo** abre a folha mostrada na captura de tela a seguir. As etapas para a criação do Hub IoT usando este método e pelo marketplace são idênticas.
* No Marketplace, escolha **Criar** para abrir a folha mostrada na captura de tela a seguir.

As seções a seguir descrevem as várias etapas para criar um hub IoT:

### <a name="choose-the-name-of-the-iot-hub"></a>Escolher o nome do Hub IoT

Para criar um Hub IoT, você deve dar um nome a ele. Esse nome deve ser exclusivo entre todos os Hubs IoT.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

### <a name="choose-the-pricing-tier"></a>Escolher a camada de preços

Você pode escolher entre quatro camadas: **Gratuita**, **Padrão 1**, **Padrão 2** e **Padrão S3**. O tipo gratuito só permite a conexão de 500 dispositivos ao Hub IoT e até 8000 mensagens por dia.

**Standard S1**: use a edição S1 para soluções IoT com um grande número de dispositivos, cada um dos quais gerará pequenas quantidades de dados. Cada unidade da edição S1 permite até 400.000 mensagens por dia entre todos os dispositivos conectados.

**Standard S2**: use a edição S2 do Hub IoT para soluções IoT nas quais os dispositivos geram grandes quantidades de dados. Cada unidade da edição S2 permite a transmissão de até seis milhões de mensagens por dia entre todos os dispositivos conectados.

**Standard S3**: use a edição S3 do Hub IoT para soluções IoT que geram grandes quantidades de dados. Cada unidade da edição S3 permite a transmissão de até 300 milhões de mensagens por dia entre todos os dispositivos conectados.

![][4]

> [!NOTE]
> O Hub IoT só permite um hub gratuito por assinatura do Azure.

### <a name="iot-hub-units"></a>Unidades do Hub IoT

O número de mensagens permitidas por unidade ao dia depende do tipo de preço do seu hub. Por exemplo, se você quiser que o Hub IoT dê suporte à entrada de 700.000 mensagens, escolha duas unidades da camada S1.

### <a name="device-to-cloud-partitions-and-resource-group"></a>Dispositivo para partições de nuvem e grupo de recursos

Você pode alterar o número de partições para um Hub IoT. O número padrão de partições é 4, você pode escolher um número diferente na lista suspensa.

Você não precisa criar explicitamente um grupo de recursos vazio. Ao criar um recurso, você pode optar por criar um novo grupo de recursos ou usar um existente.

![][5]

### <a name="choose-subscription"></a>Escolha uma assinatura

O Hub IoT do Azure lista automaticamente as assinaturas do Azure às quais a conta do usuário está vinculada. Você pode escolher a assinatura do Azure à qual associar o hub IoT.

### <a name="choose-the-location"></a>Escolher o local

A opção de localização oferece uma lista das regiões em que o Hub IoT é oferecido.

### <a name="create-the-iot-hub"></a>Criar o Hub IoT

Quando todas as etapas anteriores forem concluídas, você poderá criar o Hub IoT. Clique em **Criar** para iniciar o processo de back-end para criar e implantar o Hub IoT com as opções que você escolheu.

Pode levar alguns minutos para que o Hub IoT seja criado, já que a implantação de back-end leva tempo para executar nos servidores da localização apropriada.

## <a name="change-the-settings-of-the-iot-hub"></a>Alterar as configurações do Hub IoT

Você poderá alterar as configurações de um Hub IoT existente depois que ele for criado na folha Hub IoT.

![][8]

**Políticas de acesso compartilhado**: essas políticas definem as permissões para que dispositivos e serviços se conectem ao Hub IoT. Você pode acessar essas políticas clicando em **Políticas de acesso compartilhado** em **Geral**. Nessa folha, você pode modificar as políticas existentes ou adicionar uma nova política.

### <a name="create-a-policy"></a>Criar uma política

* Clique em **Adicionar** para abrir uma folha. Aqui, você poderá inserir o nome da nova política e as permissões que quer associar a essa política, como mostrado na seguinte figura:

    Há várias permissões que podem ser associadas a essas políticas compartilhadas. As políticas **Leitura do Registro** e **Gravação do Registro** concedem direitos de acesso de leitura e de gravação registro de identidade. A escolha da opção de gravação escolhe automaticamente a opção de leitura.

    A política **Conectar serviço** concede permissão para acessar pontos de extremidade de serviço como **Receber o dispositivo para nuvem**. A política **Conectar dispositivo** concede permissões para enviar e receber mensagens usando os pontos de extremidade do lado do dispositivo do Hub IoT.

* Clique em **Criar** para adicionar essa política recém-criada à lista existente.

![][10]

## <a name="endpoints"></a>Pontos de extremidade

Clique em **Pontos de extremidade** para exibir uma lista de pontos de extremidade do Hub IoT que está sendo alterado. Há dois tipos principais de ponto de extremidade: aqueles que são criados no Hub IoT e aqueles que você adicionou ao Hub IoT depois de sua criação.

![][11]

### <a name="built-in-endpoints"></a>Pontos de extremidade internos

Há dois pontos de extremidade internos principais: **Comentários da nuvem para dispositivo** e **Eventos**.

* Configurações de **Comentários da nuvem para dispositivo**: essa configuração tem duas subconfigurações: **TTL (vida útil) da Nuvem para Dispositivo** e **Período de retenção** (em horas) para as mensagens. Ao criar um Hub IoT, ambas essas configurações têm o valor padrão de uma hora. Para ajustar essas configurações, use os controles deslizantes ou digite os valores.
* Configurações de **Eventos**: essa configuração tem várias subconfigurações, algumas das quais são somente leitura. A lista a seguir descreve cada uma:

  * **Partições**: defina um valor padrão durante a criação do Hub IoT. Você pode alterar a quantidade de partições nesta configuração.

  * **Nome compatível e ponto de extremidade do Hub de Eventos**: quando o Hub IoT é criado, um Hub de Eventos é criado internamente e talvez você tenha que acessá-lo sob determinadas circunstâncias. Não é possível personalizar os valores de nome e ponto de extremidade compatíveis com o Hub de evento, mas você pode copiá-los clicando em **Copiar**.

  * **Período de retenção**: definido como um dia por padrão, mas pode ser personalizado para outros valores usando a lista suspensa. Esse valor é em dias para a configuração de dispositivo para a nuvem.

  * **Grupos de Consumidores**: grupos de consumidores permitem que vários leitores leiam mensagens independentemente do Hub IoT. Todos os Hub IoT são criados com um grupo de consumidores padrão. No entanto, você pode adicionar ou excluir grupos de consumidores em seus Hubs IoT com esta configuração.

  > [!NOTE]
  > O grupo de consumidores padrão não pode ser editado ou excluído.

### <a name="custom-endpoints"></a>Pontos de extremidade personalizados

Você pode adicionar pontos de extremidade personalizados ao Hub IoT usando o portal. Na folha **Pontos de extremidade**, clique em **Adicionar** na parte superior para abrir a folha **Adicionar ponto de extremidade**. Insira as informações necessárias e clique em **OK**. O ponto de extremidade personalizado é exibido na folha **Pontos de extremidade** principal.

![][13]

Você pode ler mais sobre pontos de extremidade personalizados em [Referência — Pontos de extremidade do Hub IoT][lnk-devguide-endpoints].

## <a name="routes"></a>Rotas

Clique em **Rotas** para gerenciar como o Hub IoT envia suas mensagens do dispositivo para a nuvem.

![][14]

Você pode adicionar rotas ao Hub IoT clicando em **Adicionar** na parte superior da folha **Rotas*** inserindo as informações necessárias e clicando em **OK**. A rota é listada na folha **Rotas** principal. Para editar uma rota, clique nela na lista de rotas. Para habilitar uma rota, clique nela na lista de rotas e defina o botão **Habilitar/Desabilitar** como **Desabilitar**. Clique em **OK** na parte inferior da folha para salvar a alteração.

![][15]

## <a name="pricing-and-scale"></a>Preços e dimensionamento

O preço de um Hub IoT existente pode ser alterado por meio das configurações de **Preço** , com as seguintes exceções:

* Na implementação atual, um Hub IoT com um SKU gratuito não pode trocar camadas com um dos SKUs pagos ou vice-versa.
* Só pode haver um Hub IoT de camada gratuita na assinatura do Azure.

![][12]

Você poderá passar de uma camada superior para outra inferior somente quando o número de mensagens enviadas nesse dia exceder a cota para a camada inferior. Por exemplo, se o número de mensagens por dia exceder 400.000, a camada do Hub IoT poderá ser alterada. No entanto, se você alterar para a camada S1, o Hub IoT estará limitado para esse dia.

## <a name="delete-the-iot-hub"></a>Excluir o Hub IoT

Você pode navegar até o Hub IoT que deseja excluir clicando em **Procurar**e escolhendo o hub apropriado para excluir. Clique no botão **Excluir** abaixo do nome do Hub IoT para excluí-lo.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o gerenciamento do Hub IoT do Azure, siga estes links:

* [Gerenciamento em massa de dispositivos IoT][lnk-bulk]
* [Métricas do Hub IoT][lnk-metrics]
* [Monitoramento de operações][lnk-monitor]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia do desenvolvedor do Hub IoT][lnk-devguide]
* [Simulando um dispositivo com IoT Edge][lnk-iotedge]
* [Proteger sua solução de IoT desde o início][lnk-securing]

[4]: ./media/iot-hub-create-through-portal/create-iothub.png
[5]: ./media/iot-hub-create-through-portal/location1.png
[8]: ./media/iot-hub-create-through-portal/portal-settings.png
[10]: ./media/iot-hub-create-through-portal/shared-access-policies.png
[11]: ./media/iot-hub-create-through-portal/messaging-settings.png
[12]: ./media/iot-hub-create-through-portal/pricing-error.png
[13]: ./media/iot-hub-create-through-portal/endpoint-creation.png
[14]: ./media/iot-hub-create-through-portal/routes-list.png
[15]: ./media/iot-hub-create-through-portal/route-edit.png

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
