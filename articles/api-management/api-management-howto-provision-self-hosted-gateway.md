---
title: Provisionar um gateway auto-hospedado no gerenciamento de API do Azure | Microsoft Docs
description: Saiba como provisionar um gateway auto-hospedado no gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/31/2020
ms.author: apimpm
ms.openlocfilehash: e79248e16ca21ae84022f8ac7f280d93f489c6a4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87050352"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>Provisionar um gateway auto-hospedado no gerenciamento de API do Azure

O provisionamento de um recurso de gateway em sua instância de gerenciamento de API do Azure é um pré-requisito para implantar um gateway de hospedagem interna. Este artigo percorre as etapas para provisionar um recurso de gateway no gerenciamento de API.

## <a name="prerequisites"></a>Pré-requisitos

Conclua o início rápido a seguir: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="provision-a-self-hosted-gateway"></a>Provisionar um gateway auto-hospedado

1. Selecione os **gateways** em **implantação e infraestrutura**.
2. Clique em **+ Adicionar**.
3. Insira o **nome** e a **região** do gateway.
> [!TIP]
> **Region** especifica o local pretendido dos nós de gateway que serão associados a esse recurso de gateway. É semanticamente equivalente a uma propriedade semelhante associada a qualquer recurso do Azure, mas pode ser atribuído um valor de cadeia de caracteres arbitrário.

4. Opcionalmente, insira uma **Descrição** do recurso de gateway.
5. Opcionalmente, selecione **+** em **APIs** para associar uma ou mais APIs a esse recurso de gateway.
> [!IMPORTANT]
> Por padrão, nenhuma das APIs existentes será associada ao novo recurso de gateway. Portanto, as tentativas de chamá-las por meio do novo gateway resultarão em `404 Resource Not Found` respostas.

6. Clique em **Adicionar**.

Agora o recurso de gateway foi provisionado na sua instância de gerenciamento de API. Você pode continuar a implantar o gateway.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o gateway auto-hospedado, confira [visão geral do gateway de gerenciamento de API do Azure-hospedado](self-hosted-gateway-overview.md)
* Saiba mais sobre como [implantar um gateway auto-hospedado no kubernetes](how-to-deploy-self-hosted-gateway-kubernetes.md)
* Saiba mais sobre como [implantar um gateway auto-hospedado no Docker](how-to-deploy-self-hosted-gateway-docker.md)
