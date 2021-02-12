---
title: O que é a hiperescala PostgreSQL habilitada para o Arc do Azure?
description: O que é a hiperescala PostgreSQL habilitada para o Arc do Azure?
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 10f21067f48155a394ac20337d77e3e82aae64d8
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98985930"
---
# <a name="what-is-azure-arc-enabled-postgresql-hyperscale"></a>O que é a hiperescala PostgreSQL habilitada para o Arc do Azure?

A hiperescala do PostgreSQL habilitado para Arc do Azure é um dos serviços de banco de dados disponíveis como parte do Azure Arc habilitado para os serviços. O Azure Arc possibilita a execução de serviços de dados do Azure no local, na borda e em nuvens públicas usando o Kubernetes e a infraestrutura de sua escolha. A proposta de valor dos serviços de dados habilitados para Arc do Azure é articulada:
- Sempre atual
- Escala elástica
- Provisionamento de autoatendimento
- Gerenciamento unificado
- Suporte a cenários desconectados

Leia mais detalhes em:
- [O que são serviços de dados habilitados para Azure Arc](overview.md)
- [Requisitos e modos de conectividade](connectivity.md)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="compare-solutions"></a>Comparar soluções

Esta seção descreve como a hiperescala do PostgreSQL habilitada para o Azure Arc difere do banco de dados do Azure para PostgreSQL Citus (hiperescala)?

## <a name="azure-database-for-postgresql-hyperscale-citus"></a>Banco de Dados do Azure para PostgreSQL – Hiperescala (Citus)

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale.png" alt-text="Citus (Azure SQL Database para PostgreSQL de hiperescala)":::

Esse é o fator forma de hiperescala do mecanismo de banco de dados Postgres disponível como um banco de dados como um serviço no Azure (PaaS). Ele é alimentado pela extensão Citus que habilita a experiência de hiperescala. Nesse fator forma, o serviço é executado nos datacenters da Microsoft e é operado pela Microsoft.

## <a name="azure-arc-enabled-postgresql-hyperscale"></a>Hiperescala do PostgreSQL habilitado para Arc do Azure

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale-arc.png" alt-text="Hiperescala do PostgreSQL habilitado para Arc do Azure":::

Esse é o fator forma de hiperescala do mecanismo de banco de dados do postgres que está disponível com o Azure Arc habilitados para o Microsoft Data Services. Ele também é equipado com a extensão Citus que habilita a experiência de hiperescala. Nesse fator forma, nossos clientes fornecem a infraestrutura que hospeda os sistemas e os opera.

## <a name="next-steps"></a>Próximas etapas
- **Experimente.** Comece rapidamente com o [Azure Arc JumpStart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) no Azure kubernetes Service (AKs), AWS elástico kubernetes Service (EKS), Google Cloud kubernetes Engine (GKE) ou em uma VM do Azure. 

- **Crie seus próprios.** Siga estas etapas para criar em seu próprio cluster kubernetes: 
   1. [Instalar as ferramentas de cliente](install-client-tools.md)
   2. [Criar o controlador de dados de arco do Azure](create-data-controller.md)
   3. [Criar um grupo de servidores de hiperescala do banco de dados do Azure para PostgreSQL no arco do Azure](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Leia mais sobre os serviços de dados habilitados para Arc do Azure](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Leia sobre o arco do Azure](https://aka.ms/azurearc)
