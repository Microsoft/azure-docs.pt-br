---
title: Diagrama de arquitetura de referência | Azure Marketplace
description: Como criar um diagrama de arquitetura de referência para uma oferta no Microsoft Commercial Marketplace.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: stmummer
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 02/18/2021
ms.openlocfilehash: 66e4d498ff7584188d680e35c89c6f10cc43c9cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604484"
---
# <a name="reference-architecture-diagram"></a>Diagrama de arquitetura de referência

O diagrama da arquitetura de referência é um modelo que representa a infraestrutura em que sua oferta depende. Para soluções de IP do Azure, o diagrama também deve mostrar como sua oferta usa os serviços de nuvem da Microsoft de acordo com os requisitos técnicos de venda conjunta de IP. Ele não foi projetado para avaliar a qualidade da arquitetura. Ele foi projetado para mostrar como sua solução usa os serviços da Microsoft.

O diagrama da arquitetura de referência pode ser criado por meio de várias ferramentas. Recomendamos o Microsoft Visio, pois ele tem vários estênceis que descrevem os modelos de arquitetura do Azure.

Um ponto de partida útil para criar diagramas de arquitetura de referência é aproveitar os [modelos de arquitetura do Azure](/azure/architecture/browse/).

## <a name="typical-components-of-a-reference-architecture-diagram"></a>Componentes típicos de um diagrama de arquitetura de referência

- Serviços de nuvem que hospedam e interagem com sua oferta, incluindo aqueles que consomem recursos do Azure
- Conexões de dados, camadas de dados e serviços de dados sendo consumidos pela sua oferta
- Serviços de nuvem usados para controlar a segurança, a autenticação e os usuários da oferta
- Interfaces do usuário e outros serviços que expõem a oferta aos usuários
- Conectividade híbrida ou local e integrações ou uma combinação de ambos

Esta captura de tela mostra um exemplo de diagrama de arquitetura de referência.

[![Esta imagem é um diagrama de arquitetura de revenda de exemplo.](./media/co-sell/co-sell-arch-diagram.png)](./media/co-sell/co-sell-arch-diagram.png#lightbox)

Este diagrama de arquitetura de referência de exemplo é para uma chatbot do setor vertical que pode ser integrada a sites de intranet para ajudar a prever cenários de demanda por meio de um algoritmo de aprendizado de máquina. Essa solução usa a cadeia de fornecedores e dados de agendamento de manufatura de sistemas ERP diferentes. O bot foi projetado para abordar perguntas sobre quando um vendedor pode confirmar as possíveis datas de entrega de um pedido.

## <a name="next-steps"></a>Próximas etapas

- [Configurar a televenda para uma oferta de Marketplace comercial](commercial-marketplace-co-sell.md)
