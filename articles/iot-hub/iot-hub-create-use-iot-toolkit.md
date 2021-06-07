---
title: Criar um Hub IoT usando as Ferramentas IoT do Azure para VS Code | Microsoft Docs
description: Saiba como usar as ferramentas de IoT do Azure para Visual Studio Code para criar um hub IoT do Azure em um grupo de recursos.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: 668087ae596688e86b7b84a16bc5c0fd0f9fcef6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "75912231"
---
# <a name="create-an-iot-hub-using-the-azure-iot-tools-for-visual-studio-code"></a>Criar um Hub IoT usando as Ferramentas IoT do Azure para Visual Studio Code

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Este artigo mostra como usar as [Ferramentas IoT do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para criar um Hub IoT do Azure. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para concluir este artigo, você precisa do seguinte:

- Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- [Visual Studio Code](https://code.visualstudio.com/)

- [Ferramentas IoT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para Visual Studio Code.

## <a name="create-an-iot-hub"></a>Crie um hub IoT

1. No Visual Studio Code, abra a exibição do **Explorer**.

2. Na parte inferior do Explorer, expanda a seção **Dispositivos do Hub IoT**. 

   ![Expanda Dispositivos do Azure Hub IoT](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. Clique em **...** no cabeçalho da seção **Dispositivos do Hub IoT**. Se você não visualizar as reticências, passe o mouse sobre o cabeçalho. 

4. Escolha **Criar Hub IoT**.

5. Um pop-up aparecerá no canto inferior direito para permitir que você entre no Azure pela primeira vez.

6. Selecione a assinatura do Azure. 

7. Selecione o grupo de recursos.

8. Selecione o local.

9. Selecione o tipo de preço.

10. Insira um nome exclusivo globalmente para o Hub IoT.

11. Aguarde alguns minutos até que o Hub IoT seja criado.

## <a name="next-steps"></a>Próximas etapas

Agora você implantou um Hub IoT usando as Ferramentas IoT do Azure para Visual Studio Code. Para explorar mais, confira os artigos a seguir:

* [Use as Ferramentas IoT do Azure para Visual Studio Code para enviar e receber mensagens entre o seu dispositivo e o Hub IoT](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

* [Use as Ferramentas IoT do Azure para Visual Studio Code para gerenciar dispositivos Hub IoT do Azure](iot-hub-device-management-iot-toolkit.md)

* [Consulte a página wiki do Hub IOT do Azure para vs Code](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki).
