---
title: "Implantar serviços do Gerenciamento de API do Azure em várias regiões do Azure | Microsoft Docs"
description: "Saiba como implantar uma instância do serviço de Gerenciamento de API do Azure em múltiplas regiões do Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 47389ad6-f865-4706-833f-846115e22e4d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.translationtype: Human Translation
ms.sourcegitcommit: 94e13ac6fec09081484a2f7f5d7bc1871822743f
ms.openlocfilehash: 1c39fee739c2f5fd4b928e1e76e1ea57f072b5f8
ms.contentlocale: pt-br
ms.lasthandoff: 01/31/2017

---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Como implantar uma instância do serviço de Gerenciamento de API do Azure em múltiplas regiões do Azure
O Gerenciamento de API dá suporte à implantação multirregião, que permite a editores de API distribuir um único serviço de gerenciamento de API por qualquer número desejado de regiões do Azure. Isso ajuda a reduzir a solicitação de latência percebida pelos consumidores de API distribuídos geograficamente e também melhora a disponibilidade do serviço se uma região ficar offline. 

Quando um serviço de Gerenciamento de API é inicialmente criado, ele contém apenas uma [unidade][unit] e reside em uma única região do Azure, que é designada como a Região Primária. Regiões adicionais podem ser facilmente adicionadas por meio do Portal do Azure. Um servidor de gateway do Gerenciamento de API é implantado em cada região e o tráfego de chamada será encaminhado para o gateway mais próximo. Se uma região fica offline, o tráfego é automaticamente redirecionado para o gateway mais próximo entre os demais. 

> [!IMPORTANT]
> A implantação multirregião só está disponível na camada **[Premium][Premium]**.
> 
> 

## <a name="add-region"> </a>Implantar uma instância do serviço de Gerenciamento de API em uma nova região
> [!NOTE]
> Se ainda não criou uma instância de serviço de Gerenciamento de API, confira [Criar uma instância de serviço de Gerenciamento de API][Create an API Management service instance] no tutorial [Introdução ao Gerenciamento de API do Azure][Get started with Azure API Management].
> 
> 

No Portal do Azure, navegue até a página **Escala e preço** da sua instância do serviço de Gerenciamento de API. 

![Guia Escala][api-management-scale-service]

Para implantar uma nova região, clique em **+ Adicionar região** na barra de ferramentas.

![Adicionar região][api-management-add-region]

Selecione o local na lista suspensa e defina o número de unidades com o controle deslizante.

![Especificar unidades][api-management-select-location-units]

Clique em **Adicionar** para colocar sua seleção na tabela Locais. 

Repita esse processo até que todos os locais estejam configurados e clique em **Salvar** na barra de ferramentas para iniciar o processo de implantação.

## <a name="remove-region"> </a>Excluir uma instância do serviço de Gerenciamento de API de um local
No Portal do Azure, navegue até a página **Escala e preço** da sua instância do serviço de Gerenciamento de API. 

![Guia Escala][api-management-scale-service]

Para o local em que você deseja remover, abra o menu de contexto usando o botão **...** na extremidade direita da tabela. Selecione a opção **Excluir**.

![Remover região][api-management-remove-region]

Confirme a exclusão e clique em **Salvar** para aplicar as alterações.

[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Get started with Azure API Management]: api-management-get-started.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unit]: http://azure.microsoft.com/pricing/details/api-management/
[Premium]: http://azure.microsoft.com/pricing/details/api-management/


