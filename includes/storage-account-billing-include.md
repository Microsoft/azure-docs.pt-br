---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/19/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ed7bfca6095dbb03042efd14456f34556f74a843
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172144"
---
Você é cobrado pelo Armazenamento do Microsoft Azure com base no seu uso da conta de armazenamento. Todos os objetos em uma conta de armazenamento são cobrados juntos como um grupo. 

Os custos de armazenamento são calculados de acordo com os seguintes fatores: região/local, tipo de conta, camada de acesso, capacidade de armazenamento, esquema de replicação, transações de armazenamento e saída de dados.

* **Região** refere-se à região geográfica na qual sua conta está baseada.
* **Tipo de conta** refere-se ao tipo de conta de armazenamento que você está usando. 
* **Camada de acesso** refere-se ao padrão de uso de dados que você especificou para sua v2 de uso geral ou a conta de armazenamento de Blobs.
* A **capacidade** do armazenamento refere-se a quanto de sua alocação de conta de armazenamento você está usando para armazenar dados.
* A **replicação** determina quantas cópias dos seus dados serão mantidas de uma só vez e em quais locais.
* As **transações** referem-se a todas as operações de leitura e gravação no Armazenamento do Microsoft Azure.
* A **saída de dados** refere-se a dados transferidos para fora de uma região do Azure. Quando os dados em sua conta de armazenamento são acessados por um aplicativo que não está sendo executado na mesma região, você é cobrado pela saída de dados. Para obter informações sobre como usar grupos de recursos para agrupar seus dados e serviços na mesma região para limitar os encargos de saída, consulte [O que é um grupo de recursos do Azure?](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group). 

A página [Preços de Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) fornece informações detalhadas de preços com base no tipo de conta, capacidade de armazenamento, replicação e transações. A página [Detalhes de preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/) fornece informações detalhadas de preços para saída de dados. Você pode usar a [Calculadora de preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) para ajudar a estimar os custos.

