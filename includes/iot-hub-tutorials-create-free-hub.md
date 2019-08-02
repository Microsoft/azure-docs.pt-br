---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/19/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 367a0b1d17f8d5ebe4f46835ace963b00e75354e
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229229"
---
Para criar um Hub IoT usando o Portal do Azure:

1. Entre no [Portal do Azure](https://portal.azure.com).

1. Selecione **Criar um recurso** > **Internet das Coisas** > **Hub IoT**.

    ![Selecione para instalar o Hub IoT](media/iot-hub-tutorials-create-free-hub/selectiothub.png)

1. Para criar o hub IoT gratuito, use os valores na tabela abaixo:

    | Configuração | Valor |
    | ------- | ----- |
    | Subscription | Na lista suspensa, selecione sua assinatura do Azure. |
    | Resource group | Criar novo. Este tutorial usa o nome **tutoriais-iot hub rg**. |
    | Região | Este tutorial usa **Oeste dos EUA**. Escolha a região mais próxima a você. |
    | NOME | A captura de tela a seguir usa o nome **tutorials-iot-hub**. Você precisa escolher seu próprio nome exclusivo ao criar seu hub. |

    ![Configurações de hub 1](media/iot-hub-tutorials-create-free-hub/hubdefinition-1.png)

    | Configuração | Valor |
    | ------- | ----- |
    | Tipo e escala de preço | F1 Gratuito. Você só pode ter um hub de camada gratuita em uma assinatura. |
    | Unidades do Hub IoT | 1 |

    ![Configurações de hub 2](media/iot-hub-tutorials-create-free-hub/hubdefinition-2.png)

1. Clique em **Criar**. Pode demorar alguns minutos para que o hub seja criado.

    ![Configurações de hub 3](media/iot-hub-tutorials-create-free-hub/hubdefinition-3.png)

1. Anote o nome do hub IoT escolhido. Você usará esse valor posteriormente no tutorial.
