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
ms.openlocfilehash: c5304c62b29d842f9beeadb34eba1cb53048d179
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302292"
---
# <a name="determine-the-management-endpoint-ip-address"></a>Determinar o endereço IP do ponto de extremidade de gerenciamento

O cluster virtual da Instância Gerenciada do Banco de Dados SQL do Azure contém um ponto de extremidade de gerenciamento que a Microsoft usa para operações de gerenciamento. O ponto de extremidade de gerenciamento é protegido com um firewall interno no nível da rede e verificação de certificado mútua no nível do aplicativo. Você pode determinar o endereço IP do ponto de extremidade de gerenciamento, mas não pode acessar esse ponto de extremidade.

Para determinar o endereço IP de gerenciamento, faça uma pesquisa de DNS no FQDN da instância `mi-name.zone_id.database.windows.net`gerenciada:. Isso retornará uma entrada DNS como `trx.region-a.worker.vnet.database.windows.net`essa. Em seguida, você pode fazer uma pesquisa de DNS nesse FQDN com a ". vnet" removida. Isso retornará o endereço IP de gerenciamento. 

Esse PowerShell fará tudo por você se você substituir \<o FQDN\> de mi pela entrada DNS da sua instância gerenciada: `mi-name.zone_id.database.windows.net`:
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Para obter mais informações sobre Instâncias Gerenciadas e conectividade, veja [Arquitetura de Conectividade da Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance-connectivity-architecture.md).
