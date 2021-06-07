---
title: Redundância de dados no Azure Data Factory | Microsoft Docs
description: Saiba mais sobre os mecanismos de redundância de metadados no Azure Data Factory
author: nabhishek
ms.reviewer: abnarain
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: abnarain
ms.openlocfilehash: 9d1c22b9ac6912f99838733a4326cb4082f49a6c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100369689"
---
# <a name="azure-data-factory-data-redundancy"></a>**Redundância de dados Azure Data Factory**

Os dados de Azure Data Factory incluem metadados (pipeline, conjuntos de dados, serviços vinculados, tempo de execução de integração e gatilhos) e de monitoramento (execuções de pipeline, gatilho e atividade). 

Em todas as regiões (exceto sul do Brasil e Sudeste Asiático), Azure Data Factory dados são armazenados e replicados na [região emparelhada](../best-practices-availability-paired-regions.md#azure-regional-pairs) para proteger contra perda de metadados. Durante as falhas do datacenter regional, a Microsoft pode iniciar um failover regional de sua instância de Azure Data Factory. Na maioria dos casos, nenhuma ação é necessária em sua parte. Quando o failover gerenciado pela Microsoft for concluído, você poderá acessar seu Azure Data Factory na região de failover. 

Devido aos requisitos de residência de dados no sul do Brasil e sudeste asiático, Azure Data Factory dados são armazenados [somente na região local](../storage/common/storage-redundancy.md#locally-redundant-storage). Para o Sudeste Asiático, todos os dados são armazenados em Cingapura. Para o sul do Brasil, todos os dados são armazenados no Brasil. Quando a região é perdida devido a um desastre significativo, a Microsoft não será capaz de recuperar seus dados de Azure Data Factory.  

> [!NOTE]
> O failover gerenciado pela Microsoft não se aplica ao SHIR (Integration Runtime de hospedagem interna), já que essa infraestrutura normalmente é gerenciada pelo cliente. Se o SHIR estiver configurado na VM do Azure, a recomendação é aproveitar o [Azure site Recovery](../site-recovery/site-recovery-overview.md) para lidar com o [failover de VM do Azure](../site-recovery/azure-to-azure-architecture.md) para outra região.



## <a name="using-source-control-in-azure-data-factory"></a>**Usando o controle do código-fonte no Azure Data Factory**

Para garantir que você possa acompanhar e auditar as alterações feitas em seus metadados de data factory do Azure, considere configurar o controle do código-fonte para seu Azure Data Factory. Ele também permitirá que você acesse seus arquivos JSON de metadados para pipelines, conjuntos de valores, serviços vinculados e gatilho. Azure Data Factory permite trabalhar com repositório git diferente (Azure DevOps e GitHub). 

 Saiba como configurar o [controle do código-fonte no Azure data Factory](./source-control.md). 

> [!NOTE]
> No caso de um desastre (perda de região), novos data factory podem ser provisionados manualmente ou de maneira automatizada. Depois que o novo data factory tiver sido criado, você poderá restaurar seus pipelines, conjuntos de os e serviços vinculados JSON do repositório git existente. 



## <a name="data-stores"></a>**Armazenamentos de dados**

Azure Data Factory permite mover dados entre armazenamentos de dados localizados localmente e na nuvem. Para garantir a continuidade dos negócios com seus armazenamentos de dados, consulte as recomendações de continuidade de negócios para cada um desses armazenamentos de dados. 

 

## <a name="see-also"></a>Veja também

- [Pares regionais do Azure](../best-practices-availability-paired-regions.md)
- [Residência de dados no Azure](https://azure.microsoft.com/global-infrastructure/data-residency/)