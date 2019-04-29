---
title: FQDN de infraestrutura para o Firewall do Azure
description: Saiba mais sobre FQDNs de infraestrutura no Firewall do Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: 34201a0eb4139de64261f77f285096a2aa2dd3aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61066312"
---
# <a name="infrastructure-fqdns"></a>FQDNs de infraestrutura

O Firewall do Azure inclui uma coleção de regras internas para FQDNs de infraestrutura que têm permissão por padrão. Esses FQDNs são específicos da plataforma e não podem ser usados para outras finalidades. 

Os serviços a seguir estão inclusos na coleção de regras internas:

- Acesso de computação ao PIR (Repositório de Imagem da Plataforma)
- Acesso ao armazenamento de status de discos gerenciados
- MDS (Diagnóstico do Microsoft Azure e Registro em Log)
- Azure Active Directory

## <a name="overriding"></a>Substituição 

Você pode substituir essa coleção de regras de infraestrutura interna criando uma coleção de regras de aplicativo negar tudo, que é processada por último. Ela sempre será processada antes da coleção de regras de infraestrutura. Tudo o que não estiver na coleção de regras de infraestrutura será negado por padrão.

## <a name="next-steps"></a>Próximas etapas

- Aprenda a [Implantar e configurar um Firewall do Azure](tutorial-firewall-deploy-portal.md).