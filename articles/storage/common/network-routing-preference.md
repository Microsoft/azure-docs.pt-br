---
title: Configurar preferência de roteamento de rede (versão prévia)
titleSuffix: Azure Storage
description: Configure a preferência de roteamento de rede (versão prévia) para sua conta de armazenamento do Azure para especificar como o tráfego de rede é roteado para sua conta de clientes pela Internet.
services: storage
author: santoshc
ms.service: storage
ms.topic: conceptual
ms.date: 05/12/2020
ms.author: santoshc
ms.reviewer: tamram
ms.subservice: common
ms.custom: references_regions
ms.openlocfilehash: 601c8dfb4b4e2f16da5c560f67e2d251a5d3072a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100362736"
---
# <a name="configure-network-routing-preference-for-azure-storage-preview"></a>Configurar preferência de roteamento de rede para o Armazenamento do Microsoft Azure (versão prévia)

Você pode configurar a [preferência de roteamento](../../virtual-network/routing-preference-overview.md) (versão prévia) para sua conta de armazenamento do Azure para especificar como o tráfego de rede é roteado para sua conta de clientes pela Internet. Por padrão, o tráfego da Internet é roteado para o ponto de extremidade público de sua conta de armazenamento sobre a [rede global da Microsoft](../../networking/microsoft-global-network.md). O Armazenamento do Microsoft Azure fornece opções adicionais para configurar como o tráfego é roteado para sua conta de armazenamento.

Configurar a preferência de roteamento oferece a flexibilidade para otimizar o tráfego para um desempenho de rede Premium ou para otimizar custos. Ao configurar uma preferência de roteamento, você especifica como o tráfego será direcionado para o ponto de extremidade público para sua conta de armazenamento por padrão. Você também pode publicar pontos de extremidade específicos de rota para sua conta de armazenamento.

> [!NOTE]
> Não há suporte para esse recurso em contas de armazenamento configuradas para usar o nível de desempenho Premium ou o armazenamento com redundância de zona (ZRS).

## <a name="microsoft-global-network-versus-internet-routing"></a>Rede global da Microsoft versus roteamento da Internet

Por padrão, os clientes fora do ambiente do Azure acessam sua conta de armazenamento pela rede global da Microsoft. A rede global da Microsoft é otimizada para seleção de caminho de baixa latência para fornecer um desempenho de rede Premium com alta confiabilidade. Tanto o tráfego de entrada quanto de saída são roteados pelo ponto de presença (POP) mais próximo do cliente. Essa configuração de roteamento padrão garante que o tráfego de e para sua conta de armazenamento percorra a rede global da Microsoft em grande parte de seu caminho, maximizando o desempenho da rede.

Você pode alterar a configuração de roteamento para sua conta de armazenamento para que o tráfego de entrada e de saída seja roteado para e de clientes fora do ambiente do Azure por meio do POP mais próximo da conta de armazenamento. Essa rota minimiza a passagem de seu tráfego pela rede global da Microsoft, entregando-a para o ISP de trânsito na primeira oportunidade. Usar essa configuração de roteamento reduz os custos de rede.

O diagrama a seguir mostra como o tráfego flui entre o cliente e a conta de armazenamento para cada preferência de roteamento:

![Visão geral das opções de roteamento para o Armazenamento do Microsoft Azure](media/network-routing-preference/routing-options-diagram.png)

Para obter mais informações sobre a preferência de roteamento no Azure, consulte [O que é preferência de roteamento (visualização)?](../../virtual-network/routing-preference-overview.md).

## <a name="routing-configuration"></a>Configuração de roteamento

Você pode escolher entre a rede global da Microsoft e o roteamento da Internet como a preferência de roteamento padrão para o ponto de extremidade público da sua conta de armazenamento. A preferência de roteamento padrão se aplica a todo o tráfego de clientes fora do Azure e afeta os pontos de extremidade para o Azure Data Lake Storage Gen2, armazenamento de blobs, Arquivos do Azure e sites estáticos. Não há suporte para configurar a preferência de roteamento para Filas do Azure ou Tabelas do Azure.

Você também pode publicar pontos de extremidade específicos de rota para sua conta de armazenamento. Quando você publica pontos de extremidade específicos a uma rota, o Armazenamento do Microsoft Azure cria novos pontos de extremidade públicos para sua conta de armazenamento que roteiam o tráfego no caminho desejado. Essa flexibilidade permite direcionar o tráfego para sua conta de armazenamento em uma rota específica sem alterar a preferência de roteamento padrão.

Por exemplo, a publicação de um ponto de extremidade específico de uma rota da Internet para o “StorageAccountA” publicará os seguintes pontos de extremidades para sua conta de armazenamento:

| Serviço de armazenamento        | Pontos de extremidade específicos à rota                                  |
| :--------------------- | :------------------------------------------------------- |
| Serviço Blob           | `StorageAccountA-internetrouting.blob.core.windows.net`  |
| Armazenamento do Data Lake Gen2 | `StorageAccountA-internetrouting.dfs.core.windows.net`   |
| Serviço de arquivos           | `StorageAccountA-internetrouting.file.core.windows.net`  |
| Sites estáticos        | `StorageAccountA-internetrouting.web.core.windows.net`   |

Se você tiver um armazenamento com redundância geográfica com acesso de leitura (RA-GRS) ou uma conta de armazenamento com redundância de zona geográfica com acesso de leitura (RA-GZRS), a publicação de pontos de extremidade específicos da rota também criará automaticamente os pontos de extremidade correspondentes na região secundária para acesso de leitura.

| Serviço de armazenamento        | Ponto de extremidade secundário somente leitura específico à rota                        |
| :--------------------- | :----------------------------------------------------------------- |
| Serviço Blob           | `StorageAccountA-internetrouting-secondary.blob.core.windows.net`  |
| Armazenamento do Data Lake Gen2 | `StorageAccountA-internetrouting-secondary.dfs.core.windows.net`   |
| Serviço de arquivos           | `StorageAccountA-internetrouting-secondary.file.core.windows.net`  |
| Sites estáticos        | `StorageAccountA-internetrouting-secondary.web.core.windows.net`   |

As cadeias de conexão para os pontos de extremidade específicos à rota publicados podem ser copiadas pelo [portal do Azure](https://portal.azure.com). Essas cadeias de conexão podem ser usadas para a autorização de chave compartilhada com todos os SDKs e APIs do Armazenamento do Microsoft Azure existentes.

## <a name="about-the-preview"></a>Sobre a visualização

A preferência de roteamento para o Armazenamento do Microsoft Azure está disponível nas seguintes regiões:

- Centro dos EUA 
- EUA Central EUAP
- Leste dos EUA 
- Leste dos EUA 2
- Leste dos EUA 2 
- Leste dos EUA 2 EUAP
- Centro-Sul dos Estados Unidos
- Centro-Oeste dos EUA
- Oeste dos EUA 
- Oeste dos EUA 2 
- França Central 
- Sul da França 
- Norte da Alemanha 
- Centro-Oeste da Alemanha 
- Centro-Norte dos EUA
- Norte da Europa 
- Leste da Noruega 
- Norte da Suíça
- Oeste da Suíça
- Sul do Reino Unido 
- Oeste do Reino Unido 
- Europa Ocidental 
- EAU Central
- Leste da Ásia 
- Sudeste Asiático 
- Japan East 
- Oeste do Japão 
- Oeste da Índia
- Leste da Austrália 
- Sudeste da Austrália 

Os seguintes problemas conhecidos afetam a visualização de preferência do roteamento para o Armazenamento do Microsoft Azure:

- As solicitações de acesso para o ponto de extremidade específico à rota para a rede global da Microsoft falham com o erro HTTP 404 ou equivalente. O roteamento pela rede global da Microsoft funciona conforme o esperado quando é definido como a preferência de roteamento padrão para o ponto de extremidade público.

## <a name="pricing-and-billing"></a>Preços e cobrança

Para obter os detalhes de preços e cobranças, consulte a seção **Preços** em [O que é preferência de roteamento (visualização)?](../../virtual-network/routing-preference-overview.md#pricing).

## <a name="next-steps"></a>Próximas etapas

- [O que é preferência de roteamento (visualização)?](../../virtual-network/routing-preference-overview.md)
- [Configurar redes virtuais e firewalls do Armazenamento do Microsoft Azure](storage-network-security.md)
- [Recomendações de segurança para o armazenamento de blobs](../blobs/security-recommendations.md)
