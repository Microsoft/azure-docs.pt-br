---
title: FQDN de infraestrutura para o Firewall do Azure
description: O Firewall do Azure inclui uma coleção de regras internas para FQDNs de infraestrutura que têm permissão por padrão.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: d4de81bad1ddda2f5b54f47c1be78925ad4cdbf3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "74168972"
---
# <a name="infrastructure-fqdns"></a>FQDNs de infraestrutura

O Firewall do Azure inclui uma coleção de regras internas para FQDNs de infraestrutura que têm permissão por padrão. Esses FQDNs são específicos da plataforma e não podem ser usados para outras finalidades. 

Os serviços a seguir estão inclusos na coleção de regras internas:

- Acesso de computação ao PIR (Repositório de Imagem da Plataforma)
- Acesso ao armazenamento de status de discos gerenciados
- MDS (Diagnóstico do Microsoft Azure e Registro em Log)

## <a name="overriding"></a>Substituição 

Você pode substituir essa coleção de regras de infraestrutura interna criando uma coleção de regras de aplicativo negar tudo, que é processada por último. Ela sempre será processada antes da coleção de regras de infraestrutura. Tudo o que não estiver na coleção de regras de infraestrutura será negado por padrão.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [implantar e configurar um firewall do Azure](tutorial-firewall-deploy-portal.md).