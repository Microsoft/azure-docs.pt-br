---
title: Stop/Start-portal do Azure-banco de dados do Azure para PostgreSQL servidor flexível
description: Este artigo descreve como parar/iniciar operações no banco de dados do Azure para PostgreSQL por meio do portal do Azure.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 4c393e0048a0058ebe0fbf2b0ee65f6ae2e184c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90933154"
---
# <a name="stopstart-an-azure-database-for-postgresql---flexible-server-preview"></a>Parar/iniciar um banco de dados do Azure para PostgreSQL – servidor flexível (visualização)

> [!IMPORTANT]
> Banco de dados do Azure para PostgreSQL-o servidor flexível está atualmente em versão prévia.

Este artigo fornece instruções passo a passo para parar e iniciar um servidor flexível.

## <a name="pre-requisites"></a>Pré-requisitos

Para concluir este guia de instruções, você precisa:

-   Você deve ter um servidor flexível do banco de dados do Azure para PostgreSQL.

## <a name="stop-a-running-server"></a>Parar um servidor em execução

1.  Na [portal do Azure](https://portal.azure.com/), escolha o servidor flexível que você deseja parar.

2.  Na página **visão geral** , clique no botão **parar** na barra de ferramentas.

> [!NOTE]
> Depois que o servidor for interrompido, outras operações de gerenciamento não estarão disponíveis para o servidor flexível.

Observe que os servidores interrompidos serão automaticamente iniciados novamente após sete dias. Todas as atualizações de manutenção pendentes serão aplicadas quando o servidor for iniciado na próxima vez.

## <a name="start-a-stopped-server"></a>Iniciar um servidor interrompido

1.  Na [portal do Azure](https://portal.azure.com/), escolha o servidor flexível que você deseja iniciar.

2.  Na página **visão geral** , clique no botão **Iniciar** na barra de ferramentas.

> [!NOTE]
> Depois que o servidor é iniciado, todas as operações de gerenciamento agora estão disponíveis para o servidor flexível.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [as opções de computação e armazenamento no banco de dados do Azure para PostgreSQL servidor flexível](./concepts-compute-storage.md).
