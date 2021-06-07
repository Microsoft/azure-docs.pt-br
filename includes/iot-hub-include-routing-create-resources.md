---
title: incluir arquivo
description: incluir arquivo
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 5680bd1c3fadab8edcde1634c708ca8683c0e99c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "76021139"
---
## <a name="create-base-resources"></a>Criar recursos de base

Antes de configurar o roteamento de mensagens, você precisa criar um hub IoT, uma conta de armazenamento e uma fila do Barramento de Serviço. Esses recursos podem ser criados usando um dos quatro artigos disponíveis para a Parte 1 deste tutorial: o portal do Azure, um modelo do Azure Resource Manager, a CLI do Azure ou o Azure PowerShell.

Use o mesmo grupo de recursos e o local para todos os recursos. Então, no final, você pode remover todos os recursos em uma única etapa excluindo o grupo de recursos.

Abaixo há um resumo das etapas a serem seguidas nestas seções: 

1. Crie um [grupo de recursos](../articles/azure-resource-manager/management/overview.md).

2. Criar um Hub IoT na camada S1. Adicionar um grupo de consumidores ao Hub IoT. O grupo de consumidores é usado pelo Azure Stream Analytics ao recuperar dados.

   > [!NOTE]
   > Você precisa usar um hub IoT em uma faixa paga para concluir este tutorial. A faixa gratuita só permite que você configure um ponto de extremidade e este tutorial requer vários pontos de extremidade.
   > 

3. Crie uma conta de armazenamento padrão V1 com replicação Standard_LRS.

4. Criar um namespace do Barramento de Serviço e da fila.

5. Crie uma identidade de dispositivo para o dispositivo simulado que envia mensagens para o hub. Salve a chave para a fase de teste. (Se estiver criando um modelo do Resource Manager, isso será feito depois de implantar o modelo.)