---
title: Permitir acesso a intervalos de IP do indexador
titleSuffix: Azure Cognitive Search
description: Configure as regras de firewall IP para permitir o acesso a dados por um indexador Pesquisa Cognitiva do Azure.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 01a88be9c54a2701130daace26c44159ee364e4c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98757236"
---
# <a name="configure-ip-firewall-rules-to-allow-indexer-connections-azure-cognitive-search"></a>Configurar regras de firewall IP para permitir conexões do indexador (Pesquisa Cognitiva do Azure)

As regras de firewall de IP nos recursos do Azure, como contas de armazenamento, contas de Cosmos DB e servidores SQL do Azure, só permitem o tráfego proveniente de intervalos de IP específicos para acessar dados.

Este artigo descreve como configurar as regras de IP, via portal do Azure, para uma conta de armazenamento para que os indexadores do Azure Pesquisa Cognitiva possam acessar os dados com segurança. Embora seja específico ao armazenamento do Azure, a abordagem também funciona para outros recursos do Azure que usam regras de firewall de IP para proteger o acesso aos dados.

> [!NOTE]
> As regras de firewall IP para a conta de armazenamento só serão efetivadas se a conta de armazenamento e o serviço de pesquisa estiverem em regiões diferentes. Se a instalação não permitir isso, recomendamos utilizar a opção de [exceção de serviço confiável](search-indexer-howto-access-trusted-service-exception.md).

## <a name="get-the-ip-address-of-the-search-service"></a>Obter o endereço IP do serviço de pesquisa

Obtenha o FQDN (nome de domínio totalmente qualificado) do seu serviço de pesquisa. Isso se parecerá `<search-service-name>.search.windows.net` . Você pode descobrir o FQDN procurando o serviço de pesquisa no portal do Azure.

   ![Obter o FQDN do serviço](media\search-indexer-howto-secure-access\search-service-portal.png "Obter o FQDN do serviço")

O endereço IP do serviço de pesquisa pode ser obtido executando um `nslookup` (ou um `ping` ) do FQDN. No exemplo a seguir, você adicionaria "150.0.0.1" a uma regra de entrada no firewall do armazenamento do Azure. Pode levar até 15 minutos depois que as configurações do firewall forem atualizadas para que o indexador do serviço de pesquisa possa acessar a conta de armazenamento do Azure.

```azurepowershell

nslookup contoso.search.windows.net
Server:  server.example.org
Address:  10.50.10.50

Non-authoritative answer:
Name:    <name>
Address:  150.0.0.1
Aliases:  contoso.search.windows.net
```

## <a name="get-the-ip-address-ranges-for-azurecognitivesearch-service-tag"></a>Obter os intervalos de endereços IP para a marca de serviço "AzureCognitiveSearch"

Endereços IP adicionais são usados para solicitações originadas do [ambiente de execução multilocatário](search-indexer-securing-resources.md#indexer-execution-environment)do indexador. Você pode obter esse intervalo de endereços IP na marca de serviço.

Os intervalos de endereços IP para a `AzureCognitiveSearch` marca de serviço podem ser obtidos por meio da [API de descoberta (versão prévia)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) ou do [arquivo JSON baixável](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

Para este passo a passos, supondo que o serviço de pesquisa seja a nuvem pública do Azure, o [arquivo JSON público do Azure](https://www.microsoft.com/download/details.aspx?id=56519) deve ser baixado.

   ![Baixar arquivo JSON](media\search-indexer-howto-secure-access\service-tag.png "Baixar arquivo JSON")

No arquivo JSON, supondo que o serviço de pesquisa está no Oeste EUA Central, a lista de endereços IP para o ambiente de execução do indexador multilocatário está listada abaixo.

```json
    {
      "name": "AzureCognitiveSearch.WestCentralUS",
      "id": "AzureCognitiveSearch.WestCentralUS",
      "properties": {
        "changeNumber": 1,
        "region": "westcentralus",
        "platform": "Azure",
        "systemService": "AzureCognitiveSearch",
        "addressPrefixes": [
          "52.150.139.0/26",
          "52.253.133.74/32"
        ]
      }
    }
```

Para endereços IP de/32, descarte o "/32" (52.253.133.74/32-> 52.253.133.74), outros podem ser usados literalmente.

## <a name="add-the-ip-address-ranges-to-ip-firewall-rules"></a>Adicionar os intervalos de endereços IP às regras de firewall de IP

A maneira mais fácil de adicionar intervalos de endereços IP à regra de firewall de uma conta de armazenamento é por meio do portal do Azure. Localize a conta de armazenamento no portal e navegue até a guia **firewalls e redes virtuais** .

   ![Firewall e redes virtuais](media\search-indexer-howto-secure-access\storage-firewall.png "Firewall e redes virtuais")

Adicione os três endereços IP obtidos anteriormente (1 para o IP do serviço de pesquisa, 2 para a `AzureCognitiveSearch` marca de serviço) no intervalo de endereços e selecione **salvar**.

   ![Regras de IP de firewall](media\search-indexer-howto-secure-access\storage-firewall-ip.png "Regras de IP de firewall")

As regras de firewall levam 5-10 minutos para serem atualizadas, após os quais os indexadores devem ser capazes de acessar os dados na conta de armazenamento.

## <a name="next-steps"></a>Próximas etapas

- [Configurar firewalls do armazenamento do Azure](../storage/common/storage-network-security.md)
- [Configurar o firewall IP para Cosmos DB](../cosmos-db/how-to-configure-firewall.md)
- [Configurar o firewall IP para o Azure SQL Server](../azure-sql/database/firewall-configure.md)