---
title: Solucionar problemas de uma falha de criação do cluster Data Explorer do Azure
description: Este artigo descreve etapas de solução de problemas para criar um cluster no Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 9e6b3f53f07ac86d6b648a8562be4ef45879c37e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829269"
---
# <a name="troubleshoot-failed-cluster-creation-of-azure-data-explorer"></a>Solucionar problemas: Criação do cluster com falha do Gerenciador de dados do Azure

No caso improvável de falha na criação de cluster no Azure Data Explorer, siga estas etapas.

1. Certifique-se de ter permissões adequadas. Para criar um cluster, você deve ser um membro da função *Contributor* ou *Owner* para a assinatura do Azure. Se necessário, trabalhe com o administrador da sua assinatura para que eles possam adicionar você à função apropriada.

1. Verifique se não há erros de validação relacionados ao nome do cluster digitado em **Criar cluster** no portal do Azure.

1. Verifique as [painel de integridade do serviço do Azure](https://azure.microsoft.com/status/). Procure o status do Azure Data Explorer na região em que você está tentando criar o cluster.

    Se o status não for **Bom** (marca de seleção verde), tente criar o cluster após a melhoria do status.

1. Se você ainda precisar de ajuda para resolver seu problema, abra uma solicitação de suporte no [Portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
