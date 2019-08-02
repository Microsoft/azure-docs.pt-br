---
title: Contrato padrão | Azure
description: Contrato padrão no Azure Marketplace e AppSource
services: Azure, Marketplace, Compute, Storage, Networking
author: qianw211
ms.service: marketplace
ms.topic: article
ms.date: 07/05/2019
ms.author: ellacroi
ms.openlocfilehash: 80c157423572d356026f257e81d52650ce01d3e8
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620348"
---
# <a name="standard-contract"></a>Contrato Standard

Para simplificar o processo de aquisição para clientes e reduzir a complexidade legal para fornecedores de software, a Microsoft oferece um modelo de contrato padrão para ajudar a facilitar a uma transação no marketplace. Em vez de criar termos e condições personalizados, do Azure Marketplace pode optar por oferecer seus softwares sob o contrato padrão, o que os clientes só precisam verificar e aceitar uma vez. O contrato padrão podem ser encontrado aqui: [ https://go.microsoft.com/fwlink/?linkid=2041178 ](https://go.microsoft.com/fwlink/?linkid=2041178). 

Os termos e condições para uma oferta são definidas na guia Marketplace durante a criação de uma oferta no Portal do Cloud Partner. A opção de contrato padrão está habilitada, alterando a configuração para Sim.

![Habilitar a opção de contrato padrão](media/marketplace-publishers-guide/standard-contract.png)

>[!Note] 
>Se você optar por usar o padrão de contrato, termos e condições separados ainda são necessárias para o [provedor de soluções de nuvem](./cloud-solution-providers.md) channel.

## <a name="standard-contract-amendments"></a>Adendo do contrato padrão

Adendo do contrato padrão permitem que os editores selecionar o contrato padrão para manter a simplicidade e com os termos personalizados para seus produtos ou negócios.  Os clientes só precisam revisar o adendo do contrato, se já tiverem revisado e aceito o contrato padrão da Microsoft.

Há dois tipos de aditamentos disponíveis para os editores do Azure Marketplace:

* Aditamentos universais: Essas correções são aplicadas universalmente do contrato padrão para todos os clientes. Aditamentos universais são mostrados para todos os clientes do produto no fluxo de compra.

![Aditamentos universais](media/marketplace-publishers-guide/universal-amendaments.png)

* Aditamentos personalizados: O Azure Marketplace também tem uma provisão para aditamentos personalizados direcionados para locatários. Eles são aditamentos especiais do contrato padrão que são destinados a somente determinados clientes. Editores podem escolher o locatário que desejam de destino. Os clientes desse locatário seriam comprar o produto em contrato padrão e as destino emendas.

![Aditamentos personalizados](media/marketplace-publishers-guide/custom-amendaments.png)

>[!Note] 
>Clientes-alvo com emendas personalizadas também obterão o Aditamento universal com os termos padrão durante a compra.

>[!Note]
>Os seguintes tipos de oferta de suportam aditamentos de contrato padrão: Aplicativos do Azure (modelos de solução e aplicativos gerenciados), máquinas virtuais, contêineres, aplicativos de contêiner.

### <a name="customer-experience"></a>Experiência do cliente

Durante o processo de compra no portal do Azure, os clientes poderão ver os termos associados ao produto como o contrato padrão da Microsoft e as emendas.

![A experiência do cliente do portal do Azure.](media/marketplace-publishers-guide/ibiza-customer-experience.png)

### <a name="api"></a>API

Os clientes poderão usar `Get-AzureRmMarketplaceTerms` para recuperar os termos de uma oferta e aceitá-lo. O contrato padrão e aditamentos associados serão retornados na saída do cmdlet.

---
