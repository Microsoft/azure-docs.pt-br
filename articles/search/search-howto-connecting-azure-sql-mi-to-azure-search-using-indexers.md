---
title: Conexão do SQL Instância Gerenciada do Azure para indexação de pesquisa
titleSuffix: Azure Cognitive Search
description: Habilite o ponto de extremidade público para permitir conexões com instâncias gerenciadas do SQL de um indexador no Azure Pesquisa Cognitiva.
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9e8625724f67caac99ae799674f9db9399e11ad8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89294247"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-managed-instance"></a>Configurar uma conexão de um indexador Pesquisa Cognitiva do Azure para o SQL Instância Gerenciada

Conforme observado na [conexão do banco de dados SQL do Azure ao azure pesquisa cognitiva usando indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), a criação de indexadores em **instâncias gerenciadas do SQL** é suportada pelo Azure pesquisa cognitiva por meio do ponto de extremidade público.

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>Criar Instância Gerenciada do SQL do Azure com ponto de extremidade público
Crie um Instância Gerenciada SQL com a opção **habilitar ponto de extremidade público** selecionada.

   ![Habilitar o ponto de extremidade público](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "Habilitar o ponto de extremidade público")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Habilitar o ponto de extremidade público do Azure SQL Instância Gerenciada
Você também pode habilitar o ponto de extremidade público em um instância gerenciada SQL existente no  >  ponto de extremidade público da **rede virtual** de segurança  >    >  **habilitar**.

   ![Habilitar ponto de extremidade público usando VNET de instância gerenciada](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "Habilitar o ponto de extremidade público")

## <a name="verify-nsg-rules"></a>Verificar regras do NSG
Verifique se o grupo de segurança de rede tem as **regras de segurança de entrada** corretas que permitem conexões de serviços do Azure.

   ![Regra de segurança de entrada do NSG](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "Regra de segurança de entrada do NSG")

> [!NOTE]
> Os indexadores ainda exigem que o SQL Instância Gerenciada seja configurado com um ponto de extremidade público para ler os dados.
> No entanto, você pode optar por restringir o acesso de entrada para esse ponto de extremidade público substituindo a regra atual ( `public_endpoint_inbound` ) pelas duas regras a seguir:
>
> * Permitindo o acesso de entrada da `AzureCognitiveSearch` [marca de serviço](../virtual-network/service-tags-overview.md#available-service-tags) ("Source" = `AzureCognitiveSearch` , "Name" = `cognitive_search_inbound` )
>
> * Permitir o acesso de entrada do endereço IP do serviço de pesquisa, que pode ser obtido por meio do ping de seu nome de domínio totalmente qualificado (por exemplo, `<your-search-service-name>.search.windows.net` ). ("SOURCE" = `IP address` , "Name" = `search_service_inbound` )
>
> Para cada uma dessas duas regras, defina "porta" = `3342` , "protocolo" = `TCP` , "destino" = `Any` , "ação" = `Allow`

## <a name="get-public-endpoint-connection-string"></a>Obter cadeia de conexão de ponto de extremidade público
Certifique-se de usar a cadeia de conexão para o **ponto de extremidade público** (porta 3342, não porta 1433).

   ![Cadeia de conexão de ponto de extremidade público](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "Cadeia de conexão de ponto de extremidade público")

## <a name="next-steps"></a>Próximas etapas
Com a configuração fora do caminho, agora você pode especificar um SQL Instância Gerenciada como a fonte de dados para um indexador de Pesquisa Cognitiva do Azure usando o portal ou a API REST. Consulte [conectando o banco de dados SQL do Azure ao azure pesquisa cognitiva usando indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) para obter mais informações.