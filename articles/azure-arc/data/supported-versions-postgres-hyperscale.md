---
title: Versões com suporte postgres com a hiperescala PostgreSQL habilitada para Arc do Azure
description: Versões com suporte postgres com a hiperescala PostgreSQL habilitada para Arc do Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0f3f9e7f4566800bdea6871cae1c5bafb3b37b95
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90933220"
---
# <a name="supported-versions-of-postgres-with-azure-arc-enabled-postgresql-hyperscale"></a>Versões com suporte do Postgres com a Hiperescala PostgreSQL habilitada para Azure Arc

Este artigo explica quais versões do postgres estão disponíveis com a hiperescala PostgreSQL habilitada para o Arc do Azure.
A lista de versões com suporte evolui ao longo do tempo. Hoje, as principais versões com suporte são:
- Postgres 12 (padrão)
- Postgres 11

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="how-to-chose-between-versions"></a>Como escolher entre as versões?
É recomendável que você examine para quais versões seus aplicativos foram projetados e quais são os recursos de cada uma das versões. Para saber mais, leia sobre cada versão no site oficial do postgres:
- [Postgres 12 (padrão)](https://www.postgresql.org/docs/12/index.html)
- [Postgres 11](https://www.postgresql.org/docs/11/index.html)

## <a name="how-to-create-a-particular-version-in-azure-arc-enabled-postgresql-hyperscale"></a>Como criar uma versão específica em uma hiperescala PostgreSQL habilitada para o Arc do Azure?
No momento da criação, você tem a possibilidade de indicar qual versão deve ser criada passando o parâmetro _--Engine-Version_ . Se você não indicar informações de versão, por padrão, um grupo de servidores de postgres versão 12 será criado.

## <a name="how-do-be-notified-when-other-versions-are-available"></a>Como notificar quando outras versões estiverem disponíveis?
Volte e leia este artigo. Ele será atualizado conforme apropriado. Você também pode listar os tipos de definições de recursos personalizados (CRD) no controlador de dados ARC em seu cluster kubernetes.
Execute o comando a seguir:
```console
kubectl get crds
```

Retornará uma saída como:
```console
NAME                                            CREATED AT
datacontrollers.arcdata.microsoft.com           2020-08-31T20:15:16Z
postgresql-11s.arcdata.microsoft.com            2020-08-31T20:15:16Z
postgresql-12s.arcdata.microsoft.com            2020-08-31T20:15:16Z
sqlmanagedinstances.sql.arcdata.microsoft.com   2020-08-31T20:15:16Z
```

Neste exemplo, essa saída indica que há dois tipos de CRD relacionados a postgres:
- postgresql-12s.arcdata.microsoft.com é CRD para postgres 12
- postgresql-11s.arcdata.microsoft.com é CRD para postgres 11

Esses são CRDs, não grupos de servidores. A presença de um CRD não é uma indicação de que você tem ou não criado um grupo de servidores dessa versão.
O CRD é uma indicação de que tipo de recursos pode ser criado.

## <a name="next-steps"></a>Próximas etapas:
- [Leia sobre como criar a hiperescala do PostgreSQL habilitado para Arc do Azure](create-postgresql-hyperscale-server-group.md)
- [Leia sobre como obter uma lista dos grupos de servidores de hiperescala PostgreSQL habilitados para Arc do Azure criados em seu controlador de dados de arco](list-server-groups-postgres-hyperscale.md)
