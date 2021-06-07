---
title: Guia de publicação para ofertas de contêiner no Azure Marketplace
description: Este artigo descreve os requisitos para publicar ofertas de contêiner no Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 11/30/2020
ms.openlocfilehash: 83c575aa40b80d9a8e39263e89a5e7860c8f8774
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95741654"
---
# <a name="publishing-guide-for-azure-container-offers"></a>Guia de publicação para ofertas de contêiner do Azure

O contêiner do Azure oferece ajuda para publicar sua imagem de contêiner no Azure Marketplace. Use este guia para entender os requisitos para esse tipo de oferta.

Ofertas de contêiner do Azure são ofertas de transações que são implantadas e cobradas por meio do Azure Marketplace. A opção de listagem que um usuário vê é "obter agora".

Use o tipo de oferta de contêiner do Azure quando sua solução for uma imagem de contêiner do Docker configurada como uma instância de contêiner do Azure baseada em kubernetes.

> [!NOTE]
> Uma instância de contêiner do Azure é uma instância do Docker de tempo de execução que fornece a maneira mais rápida e simples de executar um contêiner no Azure, sem precisar gerenciar máquinas virtuais e sem precisar adotar um serviço de nível superior. As instâncias de contêiner podem ser implantadas diretamente no Azure ou orquestradas pelos serviços Kubernetess do Azure ou pelo mecanismo de serviço kubernetes do Azure.  

A Microsoft atualmente dá suporte a modelos de licenciamento BYOL (traga sua própria licença) e gratuito.

## <a name="container-offer-requirements"></a>Requisitos da oferta de contêiner

| Requisito | Detalhes |  
|:--- |:--- |  
| Cobrança e medição | Suporte a qualquer modelo de cobrança gratuito ou BYOL.<br><br> |  
| Imagem criada a partir de um Dockerfile | As imagens de contêiner devem ser baseadas na especificação de imagem do Docker e criadas a partir de um Dockerfile.<br> <br>Para obter mais informações sobre a criação de imagens do Docker, consulte a seção "uso" da [referência de Dockerfile](https://docs.docker.com/engine/reference/builder/#usage).<br><br> |  
| Hospedando em um repositório do registro de contêiner do Azure | As imagens de contêiner devem ser hospedadas em um repositório do registro de contêiner do Azure.<br> <br>Para obter mais informações sobre como trabalhar com o registro de contêiner do Azure, consulte [início rápido: criar um registro de contêiner privado usando o portal do Azure](../container-registry/container-registry-get-started-portal.md).<br><br> |  
| Marcação de imagens | As imagens de contêiner devem conter pelo menos uma marca (número máximo de marcas: 16).<br><br>Para obter mais informações sobre como marcar uma imagem, consulte a `docker tag` página no site de [documentação do Docker](https://docs.docker.com/engine/reference/commandline/tag) .<br><br> |  

## <a name="next-steps"></a>Próximas etapas

- Para preparar ativos técnicos para uma oferta de contêiner, consulte [criar ativos técnicos de contêiner do Azure](create-azure-container-technical-assets.md).

- Para criar uma oferta de contêiner do Azure, consulte [criar uma oferta de contêiner do Azure no Azure Marketplace](create-azure-container-offer.md) para obter mais informações.
