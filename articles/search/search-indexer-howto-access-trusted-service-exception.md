---
title: Acesso do indexador ao armazenamento do Azure usando exceção de serviço confiável
titleSuffix: Azure Cognitive Search
description: Habilite o acesso a dados por um indexador no Azure Pesquisa Cognitiva aos dados armazenados com segurança no armazenamento do Azure.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: e139c15ef6de00376a4e1a88000d263c3486994b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92101368"
---
# <a name="indexer-access-to-azure-storage-using-the-trusted-service-exception-azure-cognitive-search"></a>Acesso do indexador ao armazenamento do Azure usando a exceção de serviço confiável (Azure Pesquisa Cognitiva)

Os indexadores em um serviço de Pesquisa Cognitiva do Azure que acessam dados em contas de armazenamento do Azure podem usar a funcionalidade de [exceção de serviço confiável](../storage/common/storage-network-security.md#exceptions) para acessar dados com segurança. Esse mecanismo oferece aos clientes que não conseguem conceder [acesso ao indexador usando regras de firewall de IP](search-indexer-howto-access-ip-restricted.md) uma alternativa simples, segura e gratuita para acessar dados em contas de armazenamento.

> [!NOTE]
> O suporte para acessar dados em contas de armazenamento por meio de uma exceção de serviço confiável é limitado ao armazenamento de BLOBs do Azure e Azure Data Lake armazenamento de Gen2. Não há suporte para o armazenamento de tabelas do Azure.

## <a name="step-1-configure-a-connection-using-a-managed-identity"></a>Etapa 1: configurar uma conexão usando uma identidade gerenciada

Siga as instruções em [Configurar uma conexão com uma conta de armazenamento do Azure usando uma identidade gerenciada](search-howto-managed-identities-storage.md). Quando terminar, você terá registrado o serviço de pesquisa com Azure Active Directory como um serviço confiável e terá permissões no armazenamento do Azure que fornece direitos específicos de identidade de pesquisa para acessar dados ou informações.

## <a name="step-2-allow-trusted-microsoft-services-to-access-the-storage-account"></a>Etapa 2: permitir que os serviços confiáveis da Microsoft acessem a conta de armazenamento

Na portal do Azure, navegue até a guia **firewalls e redes virtuais** da conta de armazenamento. Verifique se a opção **permitir que os serviços confiáveis da Microsoft acessem esta conta de armazenamento** está marcada. Essa opção só permitirá a instância específica do serviço de pesquisa com acesso baseado em função apropriado para a conta de armazenamento (autenticação forte) para acessar dados na conta de armazenamento, mesmo que elas sejam protegidas por regras de firewall de IP.

![Exceção de serviço confiável](media\search-indexer-howto-secure-access\exception.png "Exceção de serviço confiável")

Os indexadores agora poderão acessar os dados na conta de armazenamento, mesmo se a conta estiver protegida por meio de regras de firewall de IP.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os indexadores de armazenamento do Azure:

- [Indexador de blobs do Azure](search-howto-indexing-azure-blob-storage.md)
- [Indexador do Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
- [Indexador de tabela do Azure](search-howto-indexing-azure-tables.md)