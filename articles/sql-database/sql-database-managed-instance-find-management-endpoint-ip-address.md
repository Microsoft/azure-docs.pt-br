---
title: Descobrir ponto de extremidade de gerenciamento de Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba como obter o endereço IP público do ponto de extremidade de gerenciamento de Instância Gerenciada do Banco de Dados SQL do Azure e verificar sua proteção de firewall interno
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: b7eb9ecd6b94aad263346ad6b5c45b694e0bd46f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60699965"
---
# <a name="determine-the-management-endpoint-ip-address"></a>Determinar o endereço IP do ponto de extremidade de gerenciamento

O cluster virtual da Instância Gerenciada do Banco de Dados SQL do Azure contém um ponto de extremidade de gerenciamento que a Microsoft usa para operações de gerenciamento. O ponto de extremidade de gerenciamento é protegido com um firewall interno no nível da rede e verificação de certificado mútua no nível do aplicativo. Você pode determinar o endereço IP do ponto de extremidade de gerenciamento, mas não pode acessar esse ponto de extremidade.

## <a name="determine-ip-address"></a>Determinar o endereço IP

Vamos supor que o host de Instância Gerenciada seja `mi-demo.xxxxxx.database.windows.net`. Execute `nslookup` usando o nome do host.

![Resolução de nome de host interno](./media/sql-database-managed-instance-management-endpoint/01_find_internal_host.png)

Agora faça outra `nslookup` para remover o nome realçado no segmento `.vnet.`. Quando você executa esse comando, você obterá o endereço IP público.

![Resolução de endereço IP público](./media/sql-database-managed-instance-management-endpoint/02_find_public_ip.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre Instâncias Gerenciadas e conectividade, veja [Arquitetura de Conectividade da Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance-connectivity-architecture.md).
