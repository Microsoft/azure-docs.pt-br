---
title: Limitações de hiperescala PostgreSQL habilitada para Arc do Azure
description: Limitações de hiperescala PostgreSQL habilitada para Arc do Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: b1a56c8acf1789690c01f1c16b7c37a237720e39
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564867"
---
# <a name="limitations-of-azure-arc-enabled-postgresql-hyperscale"></a>Limitações de hiperescala PostgreSQL habilitada para Arc do Azure

Este artigo descreve as limitações da hiperescala do PostgreSQL habilitada para o Arc do Azure. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="backup-and-restore"></a>Backup e restauração

- Não há suporte para a restauração pontual (como restaurar para data e hora específicas) para o mesmo grupo de servidores. Ao fazer uma restauração pontual, você deve restaurar em um grupo de servidores diferente que você implantou antes da restauração. Depois de restaurar para o novo grupo de servidores, você poderá excluir o grupo de origem do servidor.
- A restauração do conteúdo inteiro de um backup (em vez de restaurar até um ponto específico no tempo) para o mesmo grupo de servidores tem suporte para PostgreSQL versão 12. Não há suporte para o PostgreSQL versão 11 devido a uma limitação do mecanismo PostgreSQL com linhas do tempo. Para restaurar todo o conteúdo de um backup para um grupo de servidores PostgreSQL da versão 11, você deve restaurá-lo para um grupo de servidores diferente.


## <a name="databases"></a>Bancos de dados

Não há suporte para hospedar mais de um banco de dados em um grupo de servidores.


## <a name="security"></a>Segurança

Não há suporte para o gerenciamento de usuários e funções. Por enquanto, continue a usar o usuário padrão Postgres.

## <a name="roles-and-responsibilities"></a>Funções e responsabilidades

As funções e as responsabilidades entre a Microsoft e seus clientes diferem entre os serviços de PaaS do Azure (plataforma como serviço) e híbrido do Azure (como hiperescala PostgreSQL habilitada para Arc do Azure). 

### <a name="frequently-asked-questions"></a>Perguntas frequentes

A tabela a seguir resume as respostas para perguntas frequentes sobre funções e responsabilidades de suporte.

| Pergunta                      | PaaS (plataforma como serviço) do Azure | Serviços híbridos de arco do Azure |
|:----------------------------------|:------------------------------------:|:---------------------------:|
| Quem fornece a infraestrutura?  | Microsoft                          | Cliente                  |
| Quem fornece o software? *       | Microsoft                          | Microsoft                 |
| Quem faz as operações? | Microsoft                          | Cliente                  |
| A Microsoft fornece SLAs?      | Sim                                | Não                        |
| Quem é responsável pelos SLAs? | Microsoft                          | Cliente                  |

\* Serviços do Azure

__Por que a Microsoft não fornece SLAs nos serviços híbridos do Azure Arc?__ Porque a Microsoft não possui a infraestrutura e não a Opera. Os clientes fazem.

## <a name="next-steps"></a>Próximas etapas

- **Experimente.** Comece rapidamente com o [Azure Arc JumpStart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) no Azure kubernetes Service (AKs), AWS elástico kubernetes Service (EKS), Google Cloud kubernetes Engine (GKE) ou em uma VM do Azure. 

- **Crie seus próprios.** Siga estas etapas para criar em seu próprio cluster kubernetes: 
   1. [Instalar as ferramentas de cliente](install-client-tools.md)
   2. [Criar o controlador de dados de arco do Azure](create-data-controller.md)
   3. [Criar um grupo de servidores de hiperescala do banco de dados do Azure para PostgreSQL no arco do Azure](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Leia mais sobre os serviços de dados habilitados para Arc do Azure](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Leia sobre o arco do Azure](https://aka.ms/azurearc)
