---
title: Configurar o Azure Percept Audio
description: Saiba como conectar seu dispositivo do Áudio do Azure Percept ao Azure Percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 03/25/2021
ms.openlocfilehash: fa3dad8cdd38e6db621d8194cc9472430c7c5008
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605783"
---
# <a name="azure-percept-audio-setup"></a>Configuração do Áudio do Azure Percept

O Áudio do Azure Percept vem pronto para uso com o Azure Percept DK. Nenhuma configuração especial é necessária.

## <a name="prerequisites"></a>Pré-requisitos

- Azure Percept DK (kit de desenvolvimento)
- Áudio do Azure Percept
- [Assinatura do Azure](https://azure.microsoft.com/free/)
- [Experiência de instalação do Azure Percept DK](./quickstart-percept-dk-set-up.md): você conectou o kit de desenvolvimento a uma rede Wi-Fi, criou um Hub IoT e conectou o kit de desenvolvimento ao Hub IoT
- Alto-falante ou fones de ouvido que possam ser conectados à entrada de áudio de 3,5 mm (opcional)

## <a name="connecting-your-devices"></a>Como conectar os dispositivos

1. Conecte o dispositivo de áudio do Azure Percept à placa base do Azure Percept DK com o cabo Micro USB para o cabo USB tipo A incluído. Conecte a extremidade do Micro USB do cabo à placa intermediária (desenvolvedor) do Audio e a extremidade do Tipo A à placa base do Percept DK.

1. (Opcional) Conecte o alto-falante ou os fones de ouvido ao dispositivo do Azure Percept Audio por meio da entrada de áudio, que está rotulada como "Saída de linha". Isso permitirá que você ouça respostas de áudio.

1. Ligue o devkit. O LED L02 na placa intermediária do Audio mudará para branco intermitente a fim de indicar que o dispositivo foi ligado e que o SoM do Audio está sendo autenticado.

1. Aguarde até que o processo de autenticação seja concluído, o que pode levar até 3 minutos.

1. Quando vir um dos seguintes indicadores, você estará pronto para começar a criar protótipos:

    - O LED L02 mudará para branco e contínuo: isso indica que a autenticação foi concluída e que o devkit ainda não foi configurado com uma palavra-chave.
    - Os três LEDs ficam azuis: isso indica que a autenticação foi concluída e que o devkit foi configurado com uma palavra-chave.

## <a name="next-steps"></a>Próximas etapas

Criar uma [solução de fala sem código](./tutorial-no-code-speech.md) no [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).
