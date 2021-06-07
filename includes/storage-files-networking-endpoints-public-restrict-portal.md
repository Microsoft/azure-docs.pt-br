---
title: incluir arquivo
description: incluir arquivo
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 01/25/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3c76988557bfa338bcfb606f568036422a536c1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98798544"
---
Navegue até a conta de armazenamento para a qual gostaria de restringir o ponto de extremidade público para redes virtuais específicas. No sumário da conta de armazenamento, selecione **Rede**. 

Na parte superior da página, selecione o botão de opção **Redes selecionadas**. Isso exibirá várias configurações para controlar a restrição do ponto de extremidade público. Clique em **+ Adicionar rede virtual existente** para selecionar a rede virtual específica que deve ter permissão para acessar a conta de armazenamento por meio do ponto de extremidade público. Para isso, será necessário selecionar uma rede virtual e uma sub-rede para essa rede virtual. 

Marque **Permitir que serviços Microsoft confiáveis acessem esta conta de serviço** para permitir que serviços Microsoft confiáveis, como a Sincronização de Arquivos do Azure, acessem a conta de armazenamento.

[![Captura de tela da folha Rede com uma rede virtual específica permitida para acessar a conta de armazenamento por meio do ponto de extremidade público](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png)](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png#lightbox)