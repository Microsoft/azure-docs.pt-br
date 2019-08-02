---
title: Arquivo de inclusão
description: Arquivo de inclusão
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 710bb8cba7fbbe4bc9b9fdc52b0767c96f97fe72
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172541"
---
## <a name="create-base-resources"></a>Criar recursos de base

Antes de configurar o roteamento de mensagens, você precisa criar um hub IoT, uma conta de armazenamento e uma fila do Barramento de Serviço. Esses recursos podem ser criados usando um dos quatro artigos que estão disponíveis para a parte 1 deste tutorial: a CLI do Azure, o Azure PowerShell, o portal do Azure ou um modelo do Azure Resource Manager.

Use o mesmo grupo de recursos e o local para todos os recursos. Então, no final, você pode remover todos os recursos em uma única etapa excluindo o grupo de recursos.

As seções a seguir descrevem as etapas a serem executadas.

1. Crie um [grupo de recursos](../articles/azure-resource-manager/resource-group-overview.md).

2. Criar um Hub IoT na camada S1. Adicionar um grupo de consumidores ao Hub IoT. O grupo de consumidores é usado pelo Azure Stream Analytics ao recuperar dados.

   > [!NOTE]
   > Você precisa usar um hub IoT em uma faixa paga para concluir este tutorial. A faixa gratuita só permite que você configure um ponto de extremidade e este tutorial requer vários pontos de extremidade.
   > 

3. Crie uma conta de armazenamento padrão V1 com replicação Standard_LRS.

4. Criar um namespace do Barramento de Serviço e da fila.

5. Crie uma identidade de dispositivo para o dispositivo simulado que envia mensagens para o hub. Salve a chave para a fase de teste. (Se estiver criando um modelo do Resource Manager, isso será feito depois de implantar o modelo.)