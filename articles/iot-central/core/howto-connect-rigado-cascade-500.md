---
title: Conectar um rigado em cascata 500 no Azure IoT Central | Microsoft Docs
description: Saiba como conectar um dispositivo de gateway rigado Cascade 500 em cascata ao seu aplicativo IoT Central.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: 0000e7690ab92f469a7417e82cb375c524e0b343
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014755"
---
# <a name="connect-a-rigado-cascade-500-gateway-device-to-your-azure-iot-central-application"></a>Conectar um dispositivo de gateway rigado Cascade 500 em cascata ao seu aplicativo IoT Central do Azure

*Este artigo se aplica a construtores de solução.*

Este artigo descreve como, como um Solution Builder, você pode conectar um dispositivo de gateway rigado Cascade 500 em cascata ao seu aplicativo Microsoft Azure IoT Central. 

## <a name="what-is-cascade-500"></a>O que é o Cascade 500?

O gateway IoT em cascata 500 é uma oferta de hardware do rigado que é incluída como parte da solução de borda como serviço em cascata. Ele fornece um projeto de IoT comercial e equipes de produto com potência de computação de ponta flexível, um ambiente de aplicativo em contêiner robusto e uma ampla variedade de opções de conectividade de dispositivo sem fio, incluindo Bluetooth 5, LTE & Wi-Fi.

A Cascade 500 é previamente certificada para o Azure IoT Plug and Play (versão prévia), permitindo que nossos integradores de solução carreguem facilmente o dispositivo em suas soluções de ponta a ponta. O gateway em cascata permite que você se conecte sem fio a uma variedade de sensores de monitoramento de condição que estão em proximidade com o dispositivo de gateway. Esses sensores podem ser integrados ao IoT Central por meio do dispositivo de gateway.

## <a name="prerequisites"></a>Pré-requisitos
Para percorrer este guia de instruções, você precisa dos seguintes recursos:

* Um dispositivo rigado Cascade 500 em cascata. Para obter mais informações, visite [rigado](https://www.rigado.com/).
* Um aplicativo Azure IoT Central. Para obter mais informações, consulte [criar um novo aplicativo](./quick-deploy-iot-central.md).

## <a name="add-a-device-template"></a>Adicionar um modelo de dispositivo

Para carregar um dispositivo de gateway 500 em cascata em sua instância do aplicativo IoT Central do Azure, você precisará configurar um modelo de dispositivo correspondente em seu aplicativo.

Para adicionar um modelo de dispositivo 500 em cascata: 

1. Navegue até a guia ***modelos de dispositivo** _ no painel esquerdo, selecione _ * + novo * *: ![ criar novo modelo de dispositivo](./media/howto-connect-rigado-cascade-500/device-template-new.png)
1. A página oferece uma opção para **_criar um modelo personalizado_* _ ou _*_usar um modelo de dispositivo pré-configurado_*_
1. Selecione o modelo de dispositivo C500 na lista de modelos de dispositivo pré-configurados, conforme mostrado abaixo: ![ selecionar modelo de dispositivo C500](./media/howto-connect-rigado-cascade-500/device-template-preconfigured.png)
1. Selecione _*_Avançar: Personalizar_*_ para continuar na próxima etapa. 
1. Na próxima tela, selecione _*_criar_*_ para carregar o modelo de dispositivo C500 em seu aplicativo IOT central.

## <a name="retrieve-application-connection-details"></a>Recuperar detalhes de conexão do aplicativo

Agora, você precisará recuperar o _ *Scope ID** e a **chave primária** para seu aplicativo de IOT central do Azure para conectar o dispositivo 500 em cascata. 

1. Navegue até **Administração**  no painel esquerdo e clique em **conexão do dispositivo**. 
2. Anote a **ID do escopo** do seu aplicativo IOT central.
![ID do escopo do aplicativo](./media/howto-connect-rigado-cascade-500/app-scope-id.png)
3. Agora clique em **Exibir chaves** e anote a **Primary key** 
 ![ chave primária da chave primária](./media/howto-connect-rigado-cascade-500/primary-key-sas.png)  

## <a name="contact-rigado-to-connect-the-gateway"></a>Contate a rigado para conectar o gateway 

Para conectar o dispositivo 500 em cascata ao seu aplicativo IoT Central, você precisará entrar em contato com o rigado e fornecer os detalhes de conexão do aplicativo das etapas acima. 

Depois que o dispositivo estiver conectado à Internet, o rigado poderá enviar por push uma atualização de configuração para o dispositivo de gateway 500 em cascata por meio de um canal seguro. 

Essa atualização aplicará os detalhes de conexão do IoT Central no dispositivo em cascata 500 e ele aparecerá na sua lista de dispositivos. 

![Lista de dispositivos](./media/howto-connect-rigado-cascade-500/devices-list-c500.png)  

Agora você está pronto para usar seu dispositivo C500 em seu aplicativo IoT Central!

## <a name="next-steps"></a>Próximas etapas

Se você for um desenvolvedor de dispositivos, algumas próximas etapas sugeridas são:

- Leia sobre [Conectividade de dispositivo no Azure IoT Central](./concepts-get-connected.md)
- Saiba como [Monitorar a conectividade do dispositivo usando o CLI do Azure](./howto-monitor-devices-azure-cli.md)
