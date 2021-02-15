---
title: arquivo de inclusão
description: arquivo de inclusão
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 98d765e2f6909f00f8dfe76d06aef017aad67adf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "71174925"
---
## <a name="terminology"></a>Terminologia

Uma imagem do Marketplace no Azure tem os seguintes atributos:

* **Publicador**: a organização que criou a imagem. Exemplos: Canonical, MicrosoftWindowsServer
* **Oferta**: Nome de um grupo de imagens relacionadas criadas por um publicador. Exemplos: UbuntuServer, WindowsServer
* **SKU**: uma instância de uma oferta, como uma versão principal de uma distribuição. Exemplos: 18, 4-LTS, 2019-datacenter
* **Versão**: o número de versão de uma imagem de SKU. 

Para identificar uma imagem do Marketplace quando implantar uma VM de forma programática, forneça esses valores individualmente como parâmetros, ou algumas ferramentas aceitam a imagem URN. Algumas ferramentas aceitam uma imagem *URN*, que combina esses valores, separados por dois pontos (:) caractere: *Publicador*: *Oferta*: *Sku* : *versão*. Em uma URN, substitua o número de versão por "mais recente", o que seleciona a versão mais recente da imagem. 

Se o editor de imagens fornecer licenças adicionais e termos de compra, você deverá aceitar esses termos e ativar a implantação programática. Você também precisará fornecer *parâmetros do plano de compra* ao implantar uma VM programaticamente. Consulte [Implantar uma imagem com termos do Marketplace](#deploy-an-image-with-marketplace-terms).
