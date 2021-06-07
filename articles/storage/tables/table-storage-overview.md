---
title: Introdução ao armazenamento de Tabelas – Armazenamento de objetos no Azure | Microsoft Docs
description: Armazene dados estruturados na nuvem usando o Armazenamento de Tabelas do Azure, um repositório de dados NoSQL.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.devlang: dotnet
ms.topic: overview
ms.date: 01/07/2021
ms.subservice: tables
ms.openlocfilehash: 292104651c6bb9b63e9d8cbe26fea2bf8c6acbf1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98027733"
---
# <a name="what-is-azure-table-storage-"></a>O que é o armazenamento de Tabelas do Azure? 

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

O armazenamento de Tabelas do Azure é um serviço que armazena dados estruturados não relacionais (também conhecidos como dados NoSQL estruturados) na nuvem, fornecendo um repositório de chave/atributo com um design sem esquema. Como o armazenamento de Tabelas não tem um esquema, é fácil adaptar seus dados à medida que as necessidades de seu aplicativo evoluem. O acesso aos dados do Armazenamento de Tabelas é rápido e econômico para muitos tipos de aplicativos e normalmente tem um custo mais baixo que o SQL tradicional para volumes de dados semelhantes.

Você pode usar o armazenamento de tabelas para armazenar conjuntos de dados flexíveis, como dados de usuário para aplicativos web, catálogos de endereços, informações sobre dispositivos ou outros tipos de metadados exigidos pelo serviço. Você pode armazenar qualquer número de entidades em uma tabela e uma conta de armazenamento pode conter um número ilimitado de tabelas, até o limite de capacidade da conta de armazenamento.

[!INCLUDE [storage-table-concepts-include](../../../includes/storage-table-concepts-include.md)]

## <a name="next-steps"></a>Próximas etapas

* [O Gerenciador de Armazenamento do Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md) é um aplicativo autônomo e gratuito da Microsoft que possibilita o trabalho visual com os dados do Armazenamento do Azure no Windows, MacOS e Linux.

* [Introdução ao Armazenamento de Tabelas do Azure no .NET](../../cosmos-db/tutorial-develop-table-dotnet.md)

* Consulte a documentação de referência do serviço Tabela para obter detalhes completos sobre as APIs disponíveis:

    * [Referência à Biblioteca de Cliente de Armazenamento para .NET](/dotnet/api/overview/azure/storage)

    * [Referência da API REST](/rest/api/storageservices/)