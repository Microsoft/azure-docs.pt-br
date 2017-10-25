---
title: "Usar o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure para provisionar dispositivos entre Hubs IoT com balanceamento de carga | Microsoft Docs"
description: "Provisionamento automático de dispositivos do DPS entre Hubs IoT com balanceamento de carga no Portal do Azure"
services: iot-dps
keywords: 
author: sethmanheim
ms.author: sethm
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 4842944cd0d980fb7e817165da23b9c3c4037e94
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="provision-devices-across-load-balanced-iot-hubs"></a>Provisionar dispositivos entre Hubs IoT com balanceamento de carga

Este tutorial mostra como provisionar dispositivos para vários Hubs IoT com balanceamento de carga usando o DPS (Serviço de Provisionamento de Dispositivos). Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Usar o Portal do Azure para provisionar um segundo dispositivo a um segundo Hub IoT 
> * Adicionar uma entrada da lista de registro para o segundo dispositivo
> * Definir a política de alocação do DPS para **distribuição uniforme**
> * Vincular o novo Hub IoT ao DPS

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial baseia-se no tutorial anterior [Provisionar o dispositivo em um hub](tutorial-provision-device-to-hub.md).

## <a name="use-the-azure-portal-to-provision-a-second-device-to-a-second-iot-hub"></a>Usar o Portal do Azure para provisionar um segundo dispositivo a um segundo Hub IoT

Siga as etapas no tutorial [Provisionar o dispositivo em um hub](tutorial-provision-device-to-hub.md) para provisionar um segundo dispositivo em outro Hub IoT.

## <a name="add-an-enrollment-list-entry-to-the-second-device"></a>Adicionar uma entrada da lista de registro para o segundo dispositivo

A lista de registro informa ao DPS qual método de atestado (o método para confirmar a identidade de um dispositivo) ele está usando com o dispositivo. A próxima etapa é adicionar uma entrada da lista de registro para o segundo dispositivo. 

1. Na página do seu DPS, clique em **Gerenciar registros**. A página **Adicionar entrada da lista de registro** será exibida. 
2. Na parte superior da página, clique em **Adicionar**.
2. Preencha os campos e, em seguida, clique em **Salvar**.

## <a name="set-the-dps-allocation-policy"></a>Definir a política de alocação do DPS

A política de alocação é uma configuração do DPS no Hub IoT que determina como os dispositivos são atribuídos a um Hub IoT. Há três políticas de alocação com suporte: 

1. **Menor latência**: dispositivos são provisionados para um Hub IoT com base no hub com a menor latência para o dispositivo.
2. **Distribuição igualmente ponderada** (padrão): Hubs IoT vinculados têm probabilidades iguais de ter dispositivos provisionados a eles. Esta é a configuração padrão. Se estiver provisionando dispositivos a apenas um Hub IoT, você poderá manter essa configuração. 
3. **Configuração estática por meio da lista de registro**: a especificação do Hub IoT desejado na lista de registro tem prioridade sobre a política de alocação no nível do DPS.

Siga estas etapas para definir a política de alocação:

1. Para definir a política de alocação, na página do DPS, clique em **Gerenciar política de alocação**.
2. Defina a política de alocação para **Distribuição uniformemente ponderada**.
3. Clique em **Salvar**.

## <a name="link-the-new-iot-hub-to-dps"></a>Vincular o novo Hub IoT ao DPS

Vincule o DPS e o Hub IoT de modo que o DPS possa registrar dispositivos nesse hub.

1. Na página **Todos os recursos**, clique no DPS criado anteriormente.
2. Na página do DPS, clique em **Hubs IoT vinculados**.
3. Clique em **Adicionar**.
4. Na página **Adicionar link para o Hub IoT**, use os botões de opção para especificar se o Hub IoT vinculado está localizado na assinatura atual ou em uma diferente. Em seguida, escolha o nome do hub IoT da caixa **Hub IoT**.
5. Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Usar o Portal do Azure para provisionar um segundo dispositivo a um segundo Hub IoT 
> * Adicionar uma entrada da lista de registro para o segundo dispositivo
> * Definir a política de alocação do DPS para **distribuição uniforme**
> * Vincular o novo Hub IoT ao DPS

<!-- Advance to the next tutorial to learn how to 
 Replace this .md
> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps](app-service-web-tutorial-custom-ssl.md)
-->
