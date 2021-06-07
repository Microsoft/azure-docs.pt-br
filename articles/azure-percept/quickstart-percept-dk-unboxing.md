---
title: Desembalar e montar os componentes do Azure Percept DK
description: Saiba como desembalar, conectar e ligar o Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/16/2021
ms.custom: template-quickstart
ms.openlocfilehash: efa255ba38f7e00785335bf458ecc0ed91da646b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608173"
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
    > A porta do adaptador de energia está localizada no lado direito da placa base. As portas restantes (duas USB-A, uma USB-C e uma Ethernet) e o botão de energia estão localizados no lado esquerdo da placa base.

    1. Rosqueie as duas antenas de Wi-Fi na placa base.

    1. Conecte o SoM de pesquisa visual à porta USB-C da placa base com o cabo USB-C.

    1. Conecte o cabo de alimentação ao adaptador de energia.

    1. Remova todos os pacotes plásticos restantes dos dispositivos.

    1. Conecte o cabo/adaptador de energia à placa base e a uma tomada de parede. Para proteger totalmente o conector do cabo de alimentação para a placa base, use uma chave de fenda P7 (não incluída no devkit) para apertar os parafusos do conector.

    1. Depois de conectar o cabo de alimentação a uma tomada de parede, o dispositivo será ligado automaticamente. O botão de energia no lado esquerdo da placa base ficará iluminado. Aguarde algum tempo para o dispositivo inicializar.

        > [!NOTE]
        > O botão de energia serve para desligar ou reinicializar o dispositivo enquanto ele estiver conectado a uma tomada. No caso de uma interrupção de energia, o dispositivo será reinicializado automaticamente.

Para obter uma demonstração visual do assembly do kit de desenvolvimento, assista aos trechos 0:00 a 0:50 do seguinte vídeo:

</br>

> [!VIDEO https://www.youtube.com/embed/-dmcE2aQkDE]

## <a name="next-steps"></a>Próximas etapas

Agora que o seu kit de desenvolvimento está conectado e ativado, confira o [passo a passo de experiência de configuração do Azure Percept DK](./quickstart-percept-dk-set-up.md) para concluir a configuração do dispositivo. A experiência de configuração permite que você conecte o devkit a uma rede Wi-Fi, configure um logon SSH, crie um Hub IoT e provisione o devkit para a sua conta do Azure. Depois que concluir a configuração do dispositivo, você estará pronto para começar a criar protótipos.