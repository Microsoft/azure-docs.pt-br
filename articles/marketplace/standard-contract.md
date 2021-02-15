---
title: Contrato Standard para o Marketplace comercial da Microsoft
description: Contrato Standard para o Azure Marketplace e o AppSource na Central de Parceiros
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 05/20/2020
ms.openlocfilehash: 20a257bde6022249fd7b2ab875b94f356234b490
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94488869"
---
# <a name="standard-contract-for-microsoft-commercial-marketplace"></a>Contrato Standard para o Marketplace comercial da Microsoft

A Microsoft oferece um Contrato Standard para o Marketplace comercial da Microsoft. Isso simplifica o processo de aquisição dos clientes, reduz a complexidade jurídica para fornecedores de software e facilita as transações no Marketplace. Em vez de criar termos e condições personalizados, você, como editor do Marketplace comercial, pode optar por oferecer seu software sob o [Contrato Standard,](https://go.microsoft.com/fwlink/?linkid=2041178) que os clientes precisam analisar e aceitar apenas uma vez.

Os termos e condições de uma oferta são definidos ao criar a oferta na Central de Parceiros. Você pode optar por usar o Contrato Standard do Marketplace comercial da Microsoft em vez de fornecer seus próprios termos e condições personalizados.

>[!Note]
>Depois de publicar uma oferta usando o contrato padrão do Microsoft Commercial Marketplace, você não poderá usar seus próprios termos e condições personalizados. Você pode oferecer uma solução sob o Contrato Standard *ou* os seus termos e condições. Os termos e condições personalizados são definidos no nível da oferta e se aplicam a todos os planos; Escreva seus termos e condições personalizados na página **Propriedades** de sua oferta no Partner Center. Se você quiser modificar os termos do contrato padrão, poderá fazer isso por meio de emendas de contrato padrão.

## <a name="standard-contract-amendments"></a>Aditamentos do Contrato Standard

Os aditamentos do Contrato Standard permitem que os editores selecionem o Contrato Standard para simplificar e termos personalizados para seus produtos ou negócios. Os clientes precisarão examinar os aditamentos do contrato somente se já tiverem examinado e aceitado o Contrato Standard da Microsoft.

Há dois tipos de aditamentos disponíveis para editores do marketplace comercial:

* Aditamentos universais: Esses aditamentos são aplicados universalmente ao Contrato Standard para todos os clientes. Os aditamentos universais são mostrados para todos os clientes da oferta no fluxo de compra. Os clientes precisam aceitar os termos do Contrato Standard e o aditamento para usar sua oferta.

* Aditamentos personalizados: São aditamentos especiais ao Contrato Standard destinados apenas a clientes específicos por meio de IDs de locatário do Azure. Os editores podem escolher o locatário que desejam usar como destino. Somente os clientes do locatário serão apresentados com os termos de aditamento personalizados no fluxo de compra da oferta.  Os clientes precisam aceitar os termos do Contrato Standard e os aditamentos para usar sua oferta.

>[!Note]
>Esses dois tipos de aditamentos se sobrepõem. Os clientes aos quais os aditamentos personalizados são direcionados também terão o aditamento universal para o Contrato Standard durante a compra. As emendas são limitadas a 4000 caracteres, incluindo espaços.

Você pode aproveitar o Contrato Standard do Marketplace comercial da Microsoft para os seguintes tipos de oferta:  Aplicativos do Azure (modelos de solução e aplicativos gerenciados), máquinas virtuais e SaaS.

## <a name="customer-experience"></a>Experiência do usuário

Durante a experiência de descoberta no Azure Marketplace ou no AppSource, os clientes poderão ver os termos associados à oferta, como Contrato Standard do Marketplace comercial da Microsoft e todos os aditamentos universais.

![A experiência de descoberta do cliente do portal do Azure.](media/marketplace-publishers-guide/azure-discovery-process.png)

Durante o processo de compra no portal do Azure, os clientes poderão ver os termos associados à oferta como o Contrato Standard do Marketplace comercial da Microsoft e todos os aditamentos universais e/ou específicos do locatário.

![A experiência de compra do cliente no portal do Azure.](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>API

Os clientes podem usar Get-AzureRmMarketplaceTerms para recuperar os termos de uma oferta e aceitá-los. O Contrato Standard e os aditamentos associados serão retornados na saída do cmdlet.
