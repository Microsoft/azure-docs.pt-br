---
title: Conectar-se ao Percept do Azure DK em série
description: Saiba como configurar uma conexão serial para o Azure Percept DK com reinicialização e um cabo serial de USB para TTL
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 93b8ab0ce53202402e86b059abe3c600590d549e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101661732"
---
# <a name="connect-to-your-azure-percept-dk-over-serial"></a>Conectar-se ao Percept do Azure DK em série

Siga as etapas abaixo para configurar uma conexão serial para o Azure Percept [DK por meio](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)de uma reinicialização.

> [!WARNING]
> **Não** tente conectar seu devkit por série, exceto em casos extremos de falha (por exemplo, você bricku seu dispositivo). Separar o compartimento da placa Carrier para conectar o cabo serial é muito difícil e quebrará seus cabos de antena Wi-Fi.

## <a name="prerequisites"></a>Pré-requisitos

- [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
- PC host
- Azure Percept DK
- [Cabo serial de USB para TTL](https://www.adafruit.com/product/954)

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/usb-serial-cable.png" alt-text="Cabo serial de USB para TTL.":::

## <a name="initiate-the-serial-connection"></a>Iniciar a conexão serial

1. Se sua placa de operadora estiver conectada a um trilho 80/20, remova-a do trilho usando a chave Hex (incluída no cartão de boas-vindas do devkit).

1. Remova os parafusos na parte inferior do compartimento da placa da operadora e extraia a placa-mãe.

    > [!WARNING]
    > A remoção da placa-mãe interromperá o Wi-Fi cabos de antena. **Não** continue com a conexão serial, a menos que seja o último recurso para recuperar seu dispositivo.

1. Remova o dissipador de calor.

1. Remova a placa de jumper dos pinos do GPIO.

    > [!TIP]
    > Observe a orientação da placa de jumper antes de removê-la. Por exemplo, desenhe uma seta ou anexe um adesivo ao quadro de jumpers apontando para o circuito para referência. A placa de jumper não está com chave e pode ser conectada acidentalmente ao remontar sua placa de operadora.

1. Conecte o [USB ao cabo serial TTL](https://www.adafruit.com/product/954) aos pins do GPIO na placa-mãe, conforme mostrado abaixo. Observe que a transmissão vermelha não está conectada.

    - Conecte o cabo preto (GND) ao pino 6.
    - Conecte o cabo branco (RX) ao pino 8.
    - Conecte o cabo verde (TX) ao pino 10.

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/serial-connection-carrier-board.png" alt-text="Conexões de PIN serial da placa do carro.":::

1. Ligue seu devkit e conecte o lado USB do cabo serial ao seu PC.

1. No Windows, vá para **Iniciar**  ->  **Windows Update configurações**  ->  **Exibir atualizações opcionais**  ->  **atualizações de driver**. Procure uma atualização de serial para USB na lista, marque a caixa ao lado dela e clique em **baixar e instalar**.  

1. Em seguida, abra o Gerenciador de dispositivos do Windows (**Iniciar**  ->  **Gerenciador de dispositivos**). Vá para **portas** e clique em **USB para UART** para abrir **Propriedades**. Observe a qual porta COM seu dispositivo está conectado.

1. Clique na guia **configurações de porta** . Certifique-se de que **bits por segundo** esteja definido como 115200.

1. Abra o PuTTY. Insira o seguinte e clique em **abrir** para se conectar ao seu devkit por meio de série:

    1. Linha serial: COM [porta #]
    1. Velocidade: 115200
    1. Tipo de conexão: serial

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/putty-serial-session.png" alt-text="Janela de sessão de saída com parâmetros de série selecionados.":::

## <a name="next-steps"></a>Próximas etapas

Para atualizar um dispositivo não inicializável em série com o [cabo serial do USB para TTL](https://www.adafruit.com/product/954), consulte o guia de atualização USB para situações não padrão.

[comment]: # (Adicionar link ao guia de atualização USB quando disponível.)