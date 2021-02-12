---
title: Verificar a segurança da porta no firewall interno
description: Saiba como verificar a proteção de firewall interna no Azure SQL Instância Gerenciada.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: d8d46518415dc56e2792ef090b0be654d538426f
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96853264"
---
# <a name="verify-the-azure-sql-managed-instance-built-in-firewall"></a>Verificar o firewall interno da Instância Gerenciada de SQL do Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

As [regras de segurança de entrada obrigatórias](connectivity-architecture-overview.md#mandatory-inbound-security-rules-with-service-aided-subnet-configuration) do SQL do Azure instância gerenciada exigem que as portas de gerenciamento 9000, 9003, 1438, 1440 e 1452 sejam abertas de **qualquer fonte** no NSG (grupo de segurança de rede) que protege o SQL instância gerenciada. Embora essas portas estejam abertas no nível do NSG, elas são protegidas no nível da rede pelo firewall interno.

## <a name="verify-firewall"></a>Verificar o firewall

Para verificar essas portas, use qualquer ferramenta de verificador de segurança para testá-las. A captura de tela a seguir mostra como usar uma dessas ferramentas.

![Verificando o firewall interno](./media/management-endpoint-verify-built-in-firewall/03_verify_firewall.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o SQL Instância Gerenciada e conectividade, consulte [arquitetura de conectividade do sql instância gerenciada do Azure](connectivity-architecture-overview.md).
