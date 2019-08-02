---
title: Arquivo de inclusão
description: Arquivo de inclusão
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/26/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 8d8f8021925ac9c9e427dd6571ecaa1a30c85497
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172544"
---
<!-- This is the instructions for creating a simulated device you can use for testing routing.-->

Em seguida, crie uma identidade de dispositivo e salve sua chave para uso posterior. Essa identidade de dispositivo é usada pelo aplicativo de simulação para enviar mensagens para o Hub IoT. Essa funcionalidade não está disponível no PowerShell nem ao usar um modelo do Azure Resource Manager. As etapas a seguir descrevem como criar o dispositivo simulado usando o [portal do Azure](https://portal.azure.com).

1. Abra o [portal do Azure](https://portal.azure.com) e faça logon na sua conta do Azure.

2. Selecione **Grupos de recursos** e escolha seu grupo de recursos. Este tutorial usa **ContosoResources**.

3. Na lista de recursos, selecione seu hub IoT. Este tutorial usa **ContosoTestHub**. Selecione **dispositivos IoT** do painel de Hub.

4. Selecione **+ Adicionar**. No painel Adicionar dispositivo, preencha a ID do dispositivo. Este tutorial usa **Contoso-Test-Device**. Deixe em branco as chaves e verifique **Gerar chaves automaticamente**. Certifique-se de que **Conectar o dispositivo ao Hub IoT** está habilitado. Clique em **Salvar**.

   ![Tela de adicionar dispositivo](./media/iot-hub-include-create-simulated-device-portal/add-device.png)

5. Agora que ela foi criada, selecione o dispositivo para ver as chaves geradas. Selecione o ícone Copiar na chave Primária e salve-o em algum lugar, como o Bloco de Notas para a fase de teste deste tutorial.

   ![Os detalhes do dispositivo, incluindo as chaves](./media/iot-hub-include-create-simulated-device-portal/device-details.png)