---
title: Gerenciar vários locatários com Video Indexer - Azure
description: Este artigo sugere diferentes opções de integração para gerenciar vários locatários com Video Indexer.
services: media-services
documentationcenter: ''
author: ika-microsoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/15/2019
ms.author: ikbarmen
ms.openlocfilehash: 18f2cf3daa281400151ba223e1735e7138d97e8e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "76990497"
---
# <a name="manage-multiple-tenants"></a>Gerenciar vários locatários

Este artigo aborda diferentes opções para gerenciar vários locatários com Video Indexer. Escolha um método que seja mais adequado ao seu cenário:

* Conta do Video Indexer por locatário
* Conta única do Video Indexer para todos os locatários
* Assinatura do Azure por locatário

## <a name="video-indexer-account-per-tenant"></a>Conta do Video Indexer por locatário

Ao usar essa arquitetura, uma conta do Video Indexer é criada para cada locatário. Os locatários têm isolamento total na camada de computação e persistente.  

![Conta do Video Indexer por locatário](./media/manage-multiple-tenants/video-indexer-account-per-tenant.png)

### <a name="considerations"></a>Considerações

* Clientes não compartilham contas de armazenamento (exceto se forem configuradas manualmente pelo cliente).
* Clientes não compartilham computação (unidades reservadas) e não afetam os tempos de trabalho do processamento uns dos outros.
* É possível remover facilmente um locatário do sistema, excluindo a conta do Video Indexer.
* Não há capacidade para compartilhar modelos personalizados entre os locatários.

    Certifique-se de que não há requisitos de negócios para compartilhar modelos personalizados.
* Difícil de gerenciar devido a várias contas do Video Indexer (e Serviços de Mídia associados) por locatário.

> [!TIP]
> Crie um usuário administrador para o sistema no [Portal do Desenvolvedor do Video Indexer](https://api-portal.videoindexer.ai/) e use a API de Autorização para fornecer aos locatários o [token de acesso à conta](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token) relevante.

## <a name="single-video-indexer-account-for-all-users"></a>Conta única do Video Indexer para todos os usuários

Ao usar essa arquitetura, o cliente será responsável pelo isolamento dos locatários. Todos os locatários deverão usar uma conta única do Video Indexer com uma conta única dos Serviços de Mídia do Azure. Ao fazer upload, pesquisar ou excluir conteúdo, o cliente precisará filtrar os resultados adequados para esse locatário.

![Conta única do Video Indexer para todos os usuários](./media/manage-multiple-tenants/single-video-indexer-account-for-all-users.png)

Com essa opção, modelos de personalização (Pessoal, Idioma e Marcas) poderão ser compartilhados ou isolados entre os locatários, filtrando os modelos por locatário.

Ao [carregar vídeos](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?), você poderá especificar um atributo de partição diferente por locatário. Isso permitirá o isolamento na [API de pesquisa](https://api-portal.videoindexer.ai/docs/services/operations/operations/Search-videos?). Ao especificar o atributo de partição na API de pesquisa, você somente obterá os resultados da partição especificada. 

### <a name="considerations"></a>Considerações

* Capacidade de compartilhar conteúdo e modelos de personalização entre locatários.
* Um locatário afeta o desempenho de outros locatários.
* O cliente deverá criar uma camada de gerenciamento complexa na parte superior do Video Indexer.

> [!TIP]
> Você pode usar o atributo [priority](upload-index-videos.md) para priorizar os trabalhos dos locatários.

## <a name="azure-subscription-per-tenant"></a>Assinatura do Azure por locatário 

Ao usar essa arquitetura, cada locatário terá sua própria assinatura do Azure. Para cada usuário, você criará uma nova conta do Video Indexer na assinatura do locatário.

![Assinatura do Azure por locatário](./media/manage-multiple-tenants/azure-subscription-per-tenant.png)

### <a name="considerations"></a>Considerações

* Essa é a única opção que permite a separação de cobrança.
* Essa integração tem mais sobrecarga de gerenciamento do que a conta do Video Indexer por locatário. Se a cobrança não for um requisito, é recomendável usar uma das outras opções descritas neste artigo.

## <a name="next-steps"></a>Próximas etapas

[Visão geral](video-indexer-overview.md)
