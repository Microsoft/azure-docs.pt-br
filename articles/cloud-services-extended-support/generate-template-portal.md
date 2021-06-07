---
title: Gerar modelo ARM para serviços de nuvem (suporte estendido) usando o portal do Azure
description: Gerar e baixar o modelo do ARM e o arquivo de parâmetro para serviços de nuvem (suporte estendido) usando o portal do Azure
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 03/07/2021
ms.custom: ''
ms.openlocfilehash: 9d40bbd7e08d8d3869166827a22f3f08536532bb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104590697"
---
# <a name="generate-arm-template-for-cloud-services-extended-support-using-the-azure-portal"></a>Gerar modelo ARM para serviços de nuvem (suporte estendido) usando o portal do Azure

Este artigo explica como baixar o modelo ARM e o arquivo de parâmetro do [portal do Azure](https://portal.azure.com) após a implantação do serviço de nuvem (suporte estendido). O modelo ARM e o arquivo de parâmetro podem ser usados em implantações futuras para atualizar ou atualizar um serviço de nuvem (suporte estendido)

## <a name="get-arm-template-via-portal"></a>Obter modelo do ARM por meio do portal

  1. Vá para o grupo de recursos e selecione implantações.
  :::image type="content" source="media/generate-template-portal-1.png" alt-text="Imagem mostra a seleção de implantações no grupo de recursos na portal do Azure.":::
  
  2. Selecione seu serviço de nuvem (suporte estendido) e clique em modelo.
  :::image type="content" source="media/generate-template-portal-2.png" alt-text="Imagem mostra a seleção de modelo em serviço de nuvem (suporte estendido) no portal do Azure.":::
  
  3. Baixe seus arquivos de modelo e parâmetro. Eles podem ser usados para implantações futuras por meio do PowerShell.
  :::image type="content" source="media/generate-template-portal-3.png" alt-text="Imagem mostra o download do arquivo de modelo no portal do Azure.":::
  
## <a name="next-steps"></a>Próximas etapas 
- Examine as [perguntas frequentes](faq.md) sobre os Serviços de Nuvem (suporte estendido).
- Implantar um serviço de nuvem (suporte estendido) usando o [portal do Azure](deploy-portal.md)
  
