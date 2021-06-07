---
title: Configurações de conectividade do Azure Synapse
description: Um artigo que ensina a definir as configurações de conectividade no Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 03/15/2021
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: e0d8a8e3320b49b6fbe3e8ab66c0b4569fac9afd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104587926"
---
# <a name="azure-synapse-analytics-connectivity-settings"></a>Configurações de conectividade do Azure Synapse Analytics

Este artigo explica as configurações de conectividade no Azure Synapse Analytics e como defini-las quando aplicáveis.


## <a name="connection-policy"></a>Política de Conexão
A política de conexão do SQL do Synapse no Azure Synapse Analytics é definida como *Padrão*. Não é possível alterar isso no Azure Synapse Analytics. Saiba mais sobre como isso afeta as conexões com o SQL do Synapse no Azure Synapse Analytics [aqui](../../azure-sql/database/connectivity-architecture.md#connection-policy). 

## <a name="minimal-tls-version"></a>Versão mínima do TLS
O SQL do Synapse no Azure Synapse Analytics permite conexões que usam todas as versões do TLS. Você não pode definir a versão mínima do TLS para o SQL do Synapse no Azure Synapse Analytics.

## <a name="next-steps"></a>Próximas etapas

Criar um [Workspace do Azure Synapse](./synapse-workspace-ip-firewall.md)