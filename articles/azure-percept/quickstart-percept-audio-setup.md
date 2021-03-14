---
title: Introdução ao Áudio do Azure Percept
description: Saiba como conectar seu dispositivo do Áudio do Azure Percept ao Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/18/2021
ms.custom: template-quickstart
ms.openlocfilehash: 660f03ce248a27a00fdd443964fbdba2fe3adeb0
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179265"
---
# <a name="azure-percept-audio-setup"></a>Configuração do Áudio do Azure Percept

O Áudio do Azure Percept vem pronto para uso com o Azure Percept DK. Nenhuma configuração especial é necessária.

## <a name="prerequisites"></a>Pré-requisitos

- Azure Percept DK (kit de desenvolvimento)
- Áudio do Azure Percept
- [Assinatura do Azure](https://azure.microsoft.com/free/)
- [Experiência de instalação do Azure Percept DK](./quickstart-percept-dk-set-up.md): você conectou o kit de desenvolvimento a uma rede Wi-Fi, criou um Hub IoT e conectou o kit de desenvolvimento ao Hub IoT
- Alto-falante ou fones de ouvido que podem ser conectados à entrada de áudio de 3,5 mm (opcional)

## <a name="connecting-your-devices"></a>Como conectar os dispositivos

1. Conecte o dispositivo de áudio do Azure Percept à placa base do Azure Percept DK com o cabo Micro USB para o cabo USB tipo A incluído. Conecte a extremidade do Micro USB do cabo à placa intermediária (desenvolvedor) e a extremidade tipo A à placa base do Percept DK.
1. (Opcional) Conecte seu alto-falante ou fones de ouvido ao áudio do Azure Percept por meio da entrada de áudio, que está rotulada como "Saída de linha". Isso lhe permitirá ouvir as respostas de áudio do seu assistente de voz. Se você não conectar um orador ou fone de ouvido, ainda poderá ver as respostas como texto na janela de demonstração. 

1. Ligue o devkit. O LED L02 na placa intermediária será alterado para piscando em branco para indicar que o dispositivo foi ligado e que o SoM de áudio está sendo autenticado.

1. Aguarde até que o processo de autenticação seja concluído, o que pode levar até 3 minutos.

1. Quando vir um dos seguintes indicadores, você estará pronto para começar a criar protótipos:

    - O LED L02 será alterado para branco sólido. Isso indica que a autenticação foi concluída e que o devkit ainda não foi configurado com uma palavra-chave.
    - Todos os três LEDs ficam azuis. Isso indica que a autenticação foi concluída e que o devkit foi configurado com uma palavra-chave.

## <a name="next-steps"></a>Próximas etapas

Criar uma [solução de fala sem código](./tutorial-no-code-speech.md) no [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).
