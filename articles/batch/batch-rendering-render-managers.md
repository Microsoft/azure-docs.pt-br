---
title: Suporte do gerenciador de renderização
description: Como usar a integração do gerenciador de renderização do Lote do Azure. Saiba mais sobre o suporte interno ou complementos para gerenciadores de renderização populares.
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: c44cbf86d8bf2fe83a6dc91dee1c4f58eec156c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "83726444"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>Usar o Lote do Azure com gerenciadores de farm de renderização

Se você estiver usando um farm de renderização local existente, é altamente provável que um gerenciador de renderização controle a capacidade do farm de renderização e renderize trabalhos.

O Azure fornece complementos ou suporte interno a gerenciadores de renderização populares. Em seguida, será possível adicionar e remover VMs do Azure, incluindo VMs com o licenciamento de aplicativo de pagamento por uso e VMs de baixa prioridade.

Há suporte aos seguintes gerenciadores de renderização:

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Royal Render](https://www.royalrender.de/)
* [Thinkbox Deadline](https://deadline.thinkboxsoftware.com/)

## <a name="azure-render-hub"></a>Hub de Renderização do Azure

O Hub de Renderização do Azure simplifica a criação e o gerenciamento de farms de renderização do Azure.  O Hub de Renderização tem suporte nativo para PipelineFx Qube e Deadline 10.  Para obter mais informações e instruções detalhadas, confira [o repositório do GitHub](https://github.com/Azure/azure-render-hub).

## <a name="using-azure-with-pipelinefx-qube"></a>Usar Azure com PipelineFX Qube

O Hub de Renderização do Azure dá suporte a gerenciadores de renderização populares, incluindo o Deadline.  Para obter instruções sobre como implantar e usar o Hub de renderização, confira [o repositório do GitHub](https://github.com/Azure/azure-render-hub).

Scripts e instruções para habilitar que VMs de pool do Lote do Azure sejam usadas como trabalhos do Qube também estão disponíveis no [repositório GitHub](https://github.com/Azure/azure-qube).

## <a name="using-azure-with-royal-render"></a>Usar Azure com Royal Render

O Royal Render tem integração do Azure e Lote do Azure interna, permitindo que você estenda um farm de renderização com VMs baseadas no Azure. Para um resumo, consulte [os arquivos de ajuda](https://www.royalrender.de/help8/index.html?Cloudrendering.html).

Para um exemplo de um cliente Royal Render usando a integração do Azure, consulte a [história do cliente Jellyfish Pictures](https://customers.microsoft.com/story/jellyfishpictures).

## <a name="using-azure-with-thinkbox-deadline"></a>Usar Azure com Thinkbox Deadline

O Hub de Renderização do Azure dá suporte a gerenciadores de renderização populares, incluindo o Deadline.  Para obter instruções sobre como implantar e usar o Hub de renderização, confira [o repositório do GitHub](https://github.com/Azure/azure-render-hub).

## <a name="next-steps"></a>Próximas etapas

Experimente a integração do Lote do Azure para o gerenciador de renderização, usando o plug-in apropriado e as instruções no GitHub, onde aplicável.
