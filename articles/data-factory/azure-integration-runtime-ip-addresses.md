---
title: Endereços de IP do Azure Integration Runtime
description: Saiba de quais endereços IP você precisa permitir o tráfego de entrada, a fim de configurar corretamente os firewalls para proteger o acesso à rede para armazenamentos de dados.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/06/2020
ms.openlocfilehash: 7b663c8d6e5849d39bb8366c82f45e0fd66d77dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100371389"
---
# <a name="azure-integration-runtime-ip-addresses"></a>Endereços de IP do Azure Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Os endereços IP que o Azure Integration Runtime usa depende da região em que o Azure Integration Runtime está localizado. *Todos* os runtimes de integração do Azure que estão na mesma região usam os mesmos intervalos de endereços IP.

> [!IMPORTANT]  
> Os fluxos de dados e o Azure Integration Runtime que habilitam a Rede Virtual Gerenciada não dão suporte ao uso de intervalos de IP fixos.
>
> Você pode usar esses intervalos de IP para a execução das atividades Externas, de Movimentação de Dados e de Pipeline. Esses intervalos de IP podem ser usados para filtragem em armazenamentos de dados/NSG (Grupo de Segurança de Rede)/firewalls para acesso de entrada do Azure Integration Runtime. 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>Endereços IP do Azure Integration Runtime: regiões específicas

Permita o tráfego dos endereços IP listados no Azure Integration Runtime na região específica do Azure em que os recursos estão localizados. Você pode obter uma lista de intervalos IP das marcas de serviço no [link de download do intervalo IP das marcas de serviço](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files). Por exemplo, se a região do Azure for **AustraliaEast**, você poderá obter uma lista de intervalos de IP em **DataFactory.AustraliaEast**.


## <a name="known-issue-with-azure-storage"></a>Problema conhecido com o Armazenamento do Azure

* Ao se conectar à conta de Armazenamento do Azure, as regras de rede de IP não têm nenhum efeito nas solicitações originadas do Azure Integration Runtime na mesma região em que a conta de armazenamento. Para obter mais detalhes, [confira este artigo](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range). 

  Em vez disso, sugerimos o uso dos [serviços confiáveis durante a conexão com o Armazenamento do Azure](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993). 

## <a name="next-steps"></a>Próximas etapas

* [Considerações sobre segurança para movimentação de dados no Azure Data Factory](data-movement-security-considerations.md)