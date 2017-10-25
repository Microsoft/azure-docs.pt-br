---
title: Criar uma regra com base no caminho para um gateway de aplicativo - portal do Azure | Microsoft Docs
description: Saiba como criar uma regra com base no caminho para um gateway de aplicativo usando o portal do Azure.
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 87bd93bc-e1a6-45db-a226-555948f1feb7
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/03/2017
ms.author: davidmu
ms.openlocfilehash: b207e7e7bd83e56db68288190c7bedafa8b5b7fa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-path-based-rule-for-an-application-gateway-by-using-the-azure-portal"></a>Criar uma regra com base em caminho para um gateway de aplicativo usando o portal do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-url-route-portal.md)
> * [PowerShell do Azure Resource Manager](application-gateway-create-url-route-arm-ps.md)
> * [CLI 2.0 do Azure](application-gateway-create-url-route-cli.md)

Com o roteamento com base em caminho de URL, você associa rotas com base no caminho de URL das solicitações HTTP. Ele verifica se há uma rota para um pool de servidores back-end configurado para a URL listada no gateway de aplicativo e envia o tráfego de rede para o pool definido. Um uso comum para o roteamento com base em caminho de URL é balancear a carga das solicitações para tipos de conteúdo diferentes para pools de servidores back-end diferentes.

Os gateways de aplicativo têm dois tipos de regra: básica e com base no caminho de URL. O tipo de regra básica fornece serviço round robin para os pools de back-end. As regras com base no caminho, além da distribuição round robin, também usam o padrão de caminho da URL de solicitação ao escolher o pool de back-ends apropriado.

## <a name="scenario"></a>Cenário

O cenário a seguir cria uma regra com base no caminho em um gateway de aplicativo existente.
Esse cenário pressupõe que você já executou as etapas em [Criar um gateway de aplicativo com o Portal](application-gateway-create-gateway-portal.md).

![Roteamento de URL][scenario]

## <a name="createrule"></a>Criar a regra com base no caminho

Uma regra com base no caminho exige seu próprio ouvinte. Antes de criar a regra, lembre-se de verificar se você tem um ouvinte disponível para usar.

### <a name="step-1"></a>Etapa 1

Acesse o [portal do Azure](http://portal.azure.com) e selecione um gateway de aplicativo existente. Clique em **Regras**.

![Visão geral do Gateway de Aplicativo][1]

### <a name="step-2"></a>Etapa 2

Clique no botão **Com base no caminho** para adicionar uma nova regra com base no caminho.

### <a name="step-3"></a>Etapa 3

A folha **Adicionar regra com base no caminho** tem duas seções. A primeira seção é onde você definiu o ouvinte, o nome da regra e as configurações de caminho padrão. As configurações do caminho padrão são para rotas que não se ajustam à rota com base no caminho personalizada. A segunda seção da folha **Adicionar regra com base no caminho** é onde você define as regras com base no caminho em si.

**Configurações básicas**

* **Nome**: um nome amigável para a regra que seja acessível no portal.
* **Ouvinte**: o ouvinte usado para a regra.
* **Pool de back-ends padrão**: o back-end a ser usado para a regra padrão.
* **Configurações HTTP padrão**: as configurações HTTP a serem usadas para a regra padrão.

**Configurações de regra com base em caminho**

* **Nome**: um nome amigável para a regra com base em caminho.
* **Caminhos**: o caminho no qual a regra procura ao encaminhar o tráfego.
* **Pool de back-ends**: o back-end a ser usado para a regra.
* **Configuração HTTP**: as configurações HTTP a serem usadas para a regra.

> [!IMPORTANT]
> A configuração **Caminhos** especifica a lista de padrões de caminho para correspondência. Cada padrão deve começar com uma barra, e um asterisco só é permitido no final. Exemplos válidos: /xyz, /xyz *,e /xyz/*.  

![Folha Adicionar regra com base no caminho com informações preenchidas][2]

Adicionar uma regra com base no caminho a um gateway de aplicativo existente é um processo fácil por meio do portal do Azure. Depois de criar uma regra com base em caminho, você pode editá-la para incluir regras adicionais. 

![Adicionar outras regras com base no caminho][3]

Essa etapa configura uma rota com base no caminho. É importante entender que solicitações não são reescritas. À medida que as solicitações chegam, o gateway de aplicativo inspeciona a solicitação e, com base no padrão de URL, envia a solicitação para o pool de back-ends apropriado.

## <a name="next-steps"></a>Próximas etapas

Para saber como configurar o descarregamento de SSL com o Gateway de Aplicativo do Azure, veja [Configurar um gateway de aplicativo para descarregamento de SSL usando o portal do Azure](application-gateway-ssl-portal.md).

[1]: ./media/application-gateway-create-url-route-portal/figure1.png
[2]: ./media/application-gateway-create-url-route-portal/figure2.png
[3]: ./media/application-gateway-create-url-route-portal/figure3.png
[scenario]: ./media/application-gateway-create-url-route-portal/scenario.png
