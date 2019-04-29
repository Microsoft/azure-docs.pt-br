---
title: Oferta de imagem de contêineres do Azure | Microsoft Docs
description: Visão geral do processo de publicação de uma oferta de contêiner no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: pbutlerm
ms.openlocfilehash: e40e83e16ab2bfd43c3bb5fa38e52a778694e90e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473071"
---
# <a name="containers"></a>Contêineres

<table> <tr> <td>Esta seção explica como publicar uma imagem de contêiner no <a href="https://azuremarketplace.microsoft.com">Azure Marketplace</a>.  
O tipo de oferta de contêiner oferece suporte a imagens de contêiner do Docker provisionadas como <a href="https://docs.microsoft.com/azure/aks/index">Serviço de Kubernetes do Azure</a> ou <a href="https://docs.microsoft.com/azure/container-instances/container-instances-overview">Instâncias do Contêiner do Azure</a> e hospedadas em um repositório do <a href="https://docs.microsoft.com/azure/container-registry">Registro do Contêiner do Azure</a>. </td> <td><img src="./media/container-icon.png"  alt="Azure container icon" /></td> </tr> </table>

## <a name="offer-components"></a>Componentes de oferta

Esta seção descreve os elementos de publicação de um contêiner e pretende ser um guia para o editor do Azure Marketplace. Publicação é dividida nas seguintes partes principais:

- [Pré-requisitos](./cpp-prerequisites.md) - lista os requisitos técnicos e de negócios antes de criar ou publicar uma oferta de contêiner.
- [Criar a oferta](./cpp-create-offer.md) -lista as etapas necessárias para criar uma nova entrada de oferta de contêiner usando o Portal do Cloud Partner.
- [Prepare os recursos técnicos](./cpp-create-technical-assets.md) - como criar os recursos técnicos para uma solução de contêiner como uma oferta no Azure Marketplace.
- [Publique a oferta](./cpp-publish-offer.md) - como enviar a oferta para publicação no Azure Marketplace.

## <a name="container-publishing-process"></a>Processo de publicação do contêiner

O diagrama a seguir ilustra as etapas de alto nível na publicação de uma oferta de VM.
![Passos para publicar uma oferta](./media/containers-offer-process.png)

As etapas de alto nível para publicar uma oferta de contêiner são:

1. Crie a oferta - forneça informações detalhadas sobre a oferta. Essas informações incluem: a descrição da oferta, materiais de marketing, informações de suporte e especificações de ativos.
2. Crie os ativos comerciais e técnicos - Crie os ativos de negócios (documentos legais e materiais de marketing) e os recursos técnicos da solução associada (as imagens de contêiner hospedadas em um Registro de Contêiner do Azure.
3. Criar o SKU - Crie o (s) SKU (s) associado (s) à oferta. Uma SKU exclusiva é necessária para cada imagem que você estiver planejando publicar.
4. Certificar e publicar a oferta - Depois que a oferta e os recursos técnicos forem concluídos, você poderá enviar a oferta. Esse envio inicia o processo de publicação. Durante esse processo, a solução é testada, validada, certificada e, em seguida, "entra em operação" no Azure Marketplace.

## <a name="next-steps"></a>Próximas etapas

Antes de considerar essas etapas, você deve atender aos [requisitos técnicos e comerciais](./cpp-prerequisites.md) para publicar um contêiner no Microsoft Azure Marketplace.