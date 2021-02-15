---
title: Contas do Lote e contas do Armazenamento do Microsoft Azure
description: Saiba mais sobre as Contas do Lote do Azure e como elas são usadas do ponto de vista de desenvolvimento.
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: 83108a265f91c9feef2fab424f1819939c2d58c9
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896741"
---
# <a name="batch-accounts-and-azure-storage-accounts"></a>Contas do Lote e contas do Armazenamento do Microsoft Azure

Uma conta do Lote é uma entidade identificada exclusivamente no serviço Lote. A maioria das soluções em Lote usa o [Armazenamento do Microsoft Azure](../storage/index.yml) para armazenar arquivos de recursos e arquivos de saída; portanto, cada conta do Lote geralmente é associada a uma conta de armazenamento correspondente.

## <a name="batch-accounts"></a>Contas do Lote

Todo o processamento e recursos estão associados a uma conta do Lote. Quando o aplicativo faz uma solicitação ao serviço de Lote, ele autentica a solicitação usando o nome de conta do Lote do Azure, a URL da conta e uma chave de acesso ou um token do Azure Active Directory.

Você pode executar várias cargas de trabalho do Lote em uma única conta do Lote. Você também pode distribuir suas cargas de trabalho entre contas do Lote que estão na mesma assinatura, mas localizadas em diferentes regiões do Azure.

Você pode criar uma conta do Lote usando o [portal do Azure](batch-account-create-portal.md) ou por meio de programação, como com a [biblioteca .NET do Gerenciamento de Lote](batch-management-dotnet.md). Ao criar a conta, você pode associar uma conta de armazenamento do Azure para armazenar dados de entrada e saída ou aplicativos relacionados ao trabalho.

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]


## <a name="azure-storage-accounts"></a>Contas de Armazenamento do Microsoft Azure

A maioria das soluções do Lote usa o Armazenamento do Azure para armazenar arquivos de recurso e de saída. Por exemplo, as tarefas do Lote (incluindo as tarefas padrão, tarefas iniciais, tarefas de preparação do trabalho e tarefas de liberação do trabalho) especificam tipicamente os arquivos de recurso que residem nas contas de armazenamento. As contas de armazenamento também armazenam os dados processados e os dados de saída gerados.

O Lote é compatível com as seguintes opções de conta de Armazenamento do Azure:

- Contas de v2 (GPv2) de uso geral
- Contas v1 (GPv1) de uso geral
- Contas de armazenamento de Blobs (atualmente com suporte para pools de configuração de máquina virtual)

Para saber mais sobre as contas de armazenamento, confira [Visão geral da conta de armazenamento do Azure](../storage/common/storage-account-overview.md).

Você pode associar uma conta de armazenamento à sua conta do Lote durante a criação da conta do Lote, ou depois. Considere seus requisitos de desempenho e custo ao escolher uma conta de armazenamento. Por exemplo, as opções de conta de armazenamento de blob e GPv2 dão suporte a [limites maiores de capacidade e escalabilidade](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/) em comparação com a GPv1. (Contate o Suporte do Azure para solicitar um aumento do limite de armazenamento.) Essas opções de conta podem melhorar o desempenho das soluções do Lote que contêm uma grande quantidade de tarefas paralelas lidas ou gravadas na conta de armazenamento.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [Nós e pools](nodes-and-pools.md).
- Saiba como criar e gerenciar contas do lote usando o [portal do Azure](batch-account-create-portal.md) ou o [.net de gerenciamento do lote](batch-management-dotnet.md).
- Saiba como usar [pontos de extremidade privados](private-connectivity.md) com contas do lote do Azure.
