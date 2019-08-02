---
title: Limites de recursos no Azure NetApp Files | Microsoft Docs
description: Descreve os limites de recursos do Azure NetApp Files, incluindo limites para contas do NetApp, pools de capacidade, volumes, instantâneos e a sub-rede delegada.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: b-juche
ms.openlocfilehash: fe8d46c897ef68563f2e0e5a1da106174ae504c5
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424078"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Limites de recursos no Azure NetApp Files

O reconhecimento dos limites de recursos no Azure NetApp Files ajuda você a gerenciar seus volumes.

## <a name="resource-limits"></a>Limites de recursos

A tabela a seguir descreve os limites de recursos para Azure NetApp Files:

|  Recurso  |  Limite padrão  |  Ajustável por meio de solicitação de suporte  |
|----------------|---------------------|--------------------------------------|
|  Número de contas do NetApp por assinatura do Azure   |  10    |  Sim   |
|  Número de pools de capacidade por conta do NetApp   |    25     |   Sim   |
|  Número de volumes por pool de capacidade     |    500   |    Sim     |
|  Número de instantâneos por volume       |    255     |    Não        |
|  Número de sub-redes delegadas a Azure NetApp Files (Microsoft. NetApp/volumes) por rede virtual do Azure    |   1   |    Não    |
|  Número máximo de IPs em uso em uma VNet (incluindo VNets emparelhados) com Azure NetApp Files    |    1000   |    Não   |
|  Tamanho mínimo de um pool de capacidade única   |  4 TiB     |    Não  |
|  Tamanho máximo de um pool de capacidade única    |  500 TiB   |   Não   |
|  Tamanho mínimo de um único volume    |    100 GiB    |    Não    |
|  Cota máxima atribuída de um único volume *   |   92 TiB   |    Não   |
|  Tamanho máximo de um único volume *     |    100 TiB    |    Não       |

\* Um volume pode ser criado manualmente ou redimensionado para o máximo de 92 TiB. No entanto, um volume pode crescer até 100 TiB em um cenário excedente. Consulte [modelo de custo para Azure NetApp files](azure-netapp-files-cost-model.md) para obter detalhes sobre a capacidade excedente. 

## <a name="request-limit-increase"></a>Aumento do limite de solicitação 

Você pode criar uma solicitação de suporte do Azure para aumentar os limites ajustáveis da tabela acima. 

Do plano de navegação portal do Azure: 

1. Clique em **ajuda + suporte**.
2. Clique em **+ nova solicitação de suporte**.
3. Na guia noções básicas, forneça as seguintes informações: 
    1. Tipo de problema: Selecione **limites de serviço e assinatura (cotas)** .
    2. Assinaturas: Selecione a assinatura para o recurso que você precisa que a cota tenha aumentado.
    3. Tipo de cota: Selecionar **armazenamento: Limites**de Azure NetApp files.
    4. Clique em **Avançar: Soluções**.
4. Na guia detalhes:
    1. Na caixa Descrição, forneça as seguintes informações para o tipo de recurso correspondente:

        |  Recurso  |    Recursos pai      |    Novos limites solicitados     |    Motivo para aumento de cota       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Conta |  *ID da assinatura*   |  *Número de Nova **conta** máxima solicitado*    |  *Qual cenário ou caso de uso solicitou a solicitação?*  |
        |  Pool    |  *ID da assinatura, URI da conta*  |  *Novo número máximo de **pool** solicitado*   |  *Qual cenário ou caso de uso solicitou a solicitação?*  |
        |  Volume  |  *ID da assinatura, URI da conta, URI do pool*   |  *Novo número de **volume** máximo solicitado*     |  *Qual cenário ou caso de uso solicitou a solicitação?*  |

    2. Especifique o método de suporte apropriado e forneça suas informações de contrato.

    3. Clique em **Avançar: Examine + criar** para criar a solicitação. 


## <a name="next-steps"></a>Próximas etapas  

- [Compreender a hierarquia de armazenamento do Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Modelo de custo para Azure NetApp Files](azure-netapp-files-cost-model.md)
