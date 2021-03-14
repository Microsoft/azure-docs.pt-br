---
title: Desembalar e montar os componentes do Azure Percept DK
description: Saiba como desembalar, conectar e ligar o Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/16/2021
ms.custom: template-quickstart
ms.openlocfilehash: 40751401760d877fe3feab39f3fea1f2fbeee54b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101664153"
---
# <a name="quickstart-unbox-and-assemble-your-azure-percept-dk-components"></a>Início Rápido: desembalar e montar os componentes do Azure Percept DK

Depois de receber o Azure Percept DK, confira este guia para obter informações sobre como conectar os componentes e ligar o dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

- Azure Percept DK (kit de desenvolvimento)
- Chave de fenda P7 (opcional, usada para proteger o conector do cabo de alimentação para a placa base)

## <a name="unbox-and-assemble-your-device"></a>Desembalar e montar o dispositivo

1. Desembale os componentes do Azure Percept DK.

    O devkit contém uma placa base, o SoM de pesquisa visual do Azure Percept, uma caixa de acessórios contendo antenas e cabos e um cartão de boas-vindas com uma chave hexagonal.

1. Conecte os componentes do devkit.

    > [!NOTE]
    > A porta do adaptador de energia está localizada no lado direito da placa base. As portas restantes (2x USB-A, 1x USB-C, 1x HDMI e 1x Ethernet) e o botão de redefinição estão localizados no lado esquerdo da placa base.

    1. Rosqueie as duas antenas de Wi-Fi na placa base.

    1. Conecte o SoM de pesquisa visual à porta USB-C da placa base com o cabo USB-C.

    1. Conecte o cabo de alimentação ao adaptador de energia.

    1. Remova todos os pacotes plásticos restantes dos dispositivos.

    1. Conecte o cabo/adaptador de energia à placa base e a uma tomada de parede. Para proteger totalmente o conector do cabo de alimentação para a placa base, use uma chave de fenda P7 (não incluída no devkit) para apertar os parafusos do conector.

    1. Depois de conectar o cabo de alimentação a uma tomada de parede, o dispositivo será ligado automaticamente. O botão de reinicialização no lado esquerdo da placa base será iluminado. Aguarde algum tempo para o dispositivo inicializar.

        > [!NOTE]
        > O botão reinicializar é para desligar ou reinicializar o dispositivo enquanto ele estiver conectado a uma tomada de energia. No caso de uma interrupção de energia, o dispositivo será reinicializado automaticamente e voltará a ligar.

## <a name="next-steps"></a>Próximas etapas

Agora que seu devkit está conectado e ligado, confira a experiência de configuração do Azure Percept DK para concluir a configuração do dispositivo. A experiência de configuração permite que você conecte o devkit a uma rede Wi-Fi, configure um logon SSH, crie um Hub IoT e provisione o devkit para a sua conta do Azure. Depois que concluir a configuração do dispositivo, você estará pronto para começar a criar protótipos.