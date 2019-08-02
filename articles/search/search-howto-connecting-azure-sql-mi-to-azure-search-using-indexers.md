---
title: Conexão do SQL Instância Gerenciada do Azure para indexação de pesquisa-Azure Search
description: Habilite o ponto de extremidade público para permitir conexões com instâncias gerenciadas do SQL de um indexador em Azure Search.
author: vl8163264128
manager: briansmi
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: victliu
ms.openlocfilehash: ae947e85f600b3bc380898ad5820239aa823794d
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229122"
---
# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-managed-instance"></a>Configurar uma conexão de um indexador Azure Search ao SQL Instância Gerenciada
Conforme observado na [conexão do banco de dados SQL do Azure para Azure Search usando indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), a criação de indexadores em **instâncias gerenciadas do SQL** é suportada por Azure Search por meio do ponto de extremidade público.

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>Criar Instância Gerenciada do SQL do Azure com ponto de extremidade público
Crie um Instância Gerenciada SQL com a opção **habilitar ponto de extremidade público** selecionada.

   ![Habilitar ponto de extremidade público](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "Habilitar ponto de extremidade público")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Habilitar o ponto de extremidade público do Azure SQL Instância Gerenciada
Você também pode habilitar o ponto de extremidade público em um instância gerenciada SQL existente no**ponto de extremidade** > público da**rede** > virtual de **segurança** > **habilitar**.

   ![Habilitar ponto de extremidade público](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "Habilitar ponto de extremidade público")

## <a name="verify-nsg-rules"></a>Verificar regras do NSG
Verifique se o grupo de segurança de rede tem as **regras de segurança de entrada** corretas que permitem conexões de serviços do Azure.

   ![Regra de segurança de entrada do NSG](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "Regra de segurança de entrada do NSG")

## <a name="get-public-endpoint-connection-string"></a>Obter cadeia de conexão de ponto de extremidade público
Certifique-se de usar a cadeia de conexão para o **ponto de extremidade público** (porta 3342, não porta 1433).

   ![Cadeia de conexão de ponto de extremidade público](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "Cadeia de conexão de ponto de extremidade público")

## <a name="next-steps"></a>Próximas etapas
Com a configuração fora do caminho, agora você pode especificar um SQL Instância Gerenciada como a fonte de dados para um indexador de Azure Search usando o portal ou a API REST. Confira [Conectando o banco de dados SQL do Azure ao Azure Search usando indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) para obter mais informações.
