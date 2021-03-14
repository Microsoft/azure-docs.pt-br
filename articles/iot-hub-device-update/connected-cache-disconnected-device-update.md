---
title: Entender o suporte para atualização de dispositivo desconectada usando o cache conectado da Microsoft | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Entender o suporte para atualização de dispositivo desconectada usando o cache conectado da Microsoft
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e2b27934f58402ecfb7dabf5560dc43e45f3f7dd
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101678978"
---
# <a name="understand-support-for-disconnected-device-updates"></a>Entender o suporte para atualizações de dispositivo desconectadas

Em um cenário de gateway transparente, um ou mais dispositivos podem passar suas mensagens por meio de um único dispositivo de gateway que mantém a conexão com o Hub IoT do Azure. Nesses casos, os dispositivos filho podem não ter conectividade com a Internet ou podem não ter permissão para baixar conteúdo da Internet. O módulo IoT Edge de visualização do cache conectado da Microsoft fornecerá a atualização do dispositivo para os clientes do Hub IoT do Azure com a capacidade de um cache em rede inteligente, que permite atualizações baseadas em imagem e pacote de dispositivos baseados no sistema operacional Linux atrás e IoT Edge gateway (dispositivos IoT downstream) e também ajudará a economizar largura de banda para a atualização do dispositivo para clientes do Hub IoT do Azure

## <a name="how-does-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub-work"></a>Como funciona a visualização do cache conectado da Microsoft para atualização de dispositivo para o Hub IoT do Azure?

O cache conectado da Microsoft é um cache inteligente e transparente para o conteúdo publicado para a atualização do dispositivo para o conteúdo do Hub IoT do Azure e pode ser personalizado para armazenar em cache o conteúdo de outras fontes como repositórios de pacotes também. O cache conectado da Microsoft é um cache frio que é quente por solicitações de cliente para os intervalos de arquivos exatos solicitados pelo cliente de otimização de entrega e não faz a semente prévia do conteúdo. O diagrama e a descrição passo a passo abaixo explica como o cache conectado da Microsoft funciona na atualização do dispositivo para a infraestrutura do Hub IoT do Azure.

>[!Note]
>Ao definir esse fluxo, supõe-se que o gateway de IoT Edge tenha conectividade com a Internet. Para o cenário do gateway de IoT Edge downstream (borda aninhada), a "rede de distribuição de conteúdo" (CDN) pode ser considerada o MCC hospedado no gateway de IoT Edge pai.

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Atualização de dispositivo desconectada" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. O cache conectado da Microsoft é implantado como um módulo IoT Edge no servidor local.
2. A atualização de dispositivo para clientes do Hub IoT do Azure está configurada para baixar conteúdo do cache conectado da Microsoft em virtude do atributo GatewayHostName da cadeia de conexão do dispositivo para dispositivos IoT folha **ou** parent_hostname definido em config. toml para IOT Edge dispositivos filho.
3. A atualização de dispositivo para clientes do Hub IoT do Azure em ambos os casos recebe comandos de download de conteúdo de atualização da atualização do dispositivo para o serviço do Hub IoT do Azure e solicita o conteúdo de atualização ao cache conectado da Microsoft em vez da CDN. O cache conectado da Microsoft, por padrão, é configurado para escutar na porta http 80, e o cliente de otimização de entrega faz a solicitação de conteúdo na porta 80 para que o pai deva ser configurado para escutar nessa porta.  Somente o protocolo http tem suporte no momento.
4. O servidor de cache conectado da Microsoft baixa o conteúdo da CDN, propaga seu cache local armazenado em disco e entrega o conteúdo para a atualização do dispositivo para o cliente do Hub IoT do Azure.
   
>[!Note]
>Ao usar atualizações baseadas em pacote, o servidor de cache conectado da Microsoft será configurado pelo administrador com o nome de host do pacote necessário.

5. As solicitações subsequentes de outras atualizações de dispositivo para clientes do Hub IoT do Azure para o mesmo conteúdo de atualização agora serão provenientes do cache e o cache conectado da Microsoft não fará solicitações para o CDN para o mesmo conteúdo.

### <a name="supporting-industrial-iot-iiot-with-parentchild-hosting-scenarios"></a>Suporte ao IoT industrial (IIoT) com cenários de hospedagem pai/filho

Quando um gateway de IoT Edge downstream ou filho estiver hospedando o servidor de cache conectado da Microsoft, ele será configurado para solicitar o conteúdo de atualização do gateway de IoT Edge pai, hospedando o servidor de cache conectado da Microsoft. Isso é necessário para quantos níveis forem necessários antes de atingir o gateway de IoT Edge pai que hospeda um servidor de cache conectado da Microsoft que tenha acesso à Internet. A partir do servidor conectado à Internet, o conteúdo é solicitado da CDN no ponto em que o conteúdo é entregue de volta para o gateway de IoT Edge filho que originalmente solicitou o conteúdo. O conteúdo será armazenado em disco em todos os níveis.

## <a name="access-to-the-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub"></a>Acesso à visualização do cache conectado da Microsoft para atualização de dispositivo para o Hub IoT do Azure

O módulo IoT Edge de cache conectado da Microsoft é lançado como uma visualização para clientes que estão implantando soluções usando a atualização de dispositivo para o Hub IoT do Azure. O acesso à visualização é por convite. [Solicite acesso](https://aka.ms/MCCForDeviceUpdateForIoT) à versão prévia do cache conectado da Microsoft para a atualização do dispositivo para IOT desligar e forneça as informações solicitadas se você quiser acessar o módulo.
