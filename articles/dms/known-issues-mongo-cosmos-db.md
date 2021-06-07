---
title: 'Problemas conhecidos: migrar do MongoDB para o Azure CosmosDB'
titleSuffix: Azure Database Migration Service
description: Saiba mais sobre problemas conhecidos e limitações de migração com migrações do MongoDB para Azure Cosmos DB usando o serviço de migração de banco de dados do Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 02/27/2020
ms.openlocfilehash: be6d9d3b8a20e11c874234baae26629ce0187e4f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91291803"
---
# <a name="known-issuesmigration-limitations-with-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Problemas conhecidos/limitações de migração com migrações do MongoDB para a API do Azure Cosmos DB para MongoDB

Os problemas conhecidos e as limitações associadas a migrações do MongoDB para a API de Cosmos DB para o MongoDB são descritos nas seções a seguir.

## <a name="migration-fails-as-a-result-of-using-the-incorrect-ssl-cert"></a>A migração falha como resultado do uso do certificado SSL incorreto

* **Sintoma**: esse problema é aparente quando um usuário não pode se conectar ao servidor de origem do MongoDB. Apesar de todas as portas de firewall serem abertas, o usuário ainda não pode se conectar.

| Causa         | Resolução |
| ------------- | ------------- |
| O uso de um certificado autoassinado no serviço de migração de banco de dados do Azure pode levar à falha de migração devido ao certificado SSL incorreto. A mensagem de erro pode incluir "o certificado remoto é inválido de acordo com o procedimento de validação". | Use um certificado original da AC.  Os certificados autoassinados geralmente são usados apenas em testes internos. Ao instalar um certificado original de uma autoridade de certificação, você pode usar SSL no serviço de migração de banco de dados do Azure sem problema (conexões para Cosmos DB usar SSL na API Mongo).<br><br> |

## <a name="unable-to-get-the-list-of-databases-to-map-in-dms"></a>Não é possível obter a lista de bancos de dados a serem mapeados no DMS

* **Sintoma**: não é possível obter a lista de BD na folha de **configuração do banco** de dados ao usar **data do modo de armazenamento do Azure** na folha **selecionar origem** .

| Causa         | Resolução |
| ------------- | ------------- |
| A cadeia de conexão da conta de armazenamento não tem as informações de SAS e, portanto, não pode ser autenticada. | Crie a SAS no contêiner de BLOB em Gerenciador de Armazenamento e use a URL com informações de SAS do contêiner como a cadeia de conexão de detalhes de origem.<br><br> |

## <a name="using-an-unsupported-version-of-the-database"></a>Usando uma versão sem suporte do banco de dados

* **Sintoma**: falha na migração.

| Causa         | Resolução |
| ------------- | ------------- |
| Você tenta migrar para Azure Cosmos DB de uma versão sem suporte do MongoDB. | À medida que novas versões do MongoDB são lançadas, elas são testadas para garantir a compatibilidade com o serviço de migração de banco de dados do Azure e o serviço está sendo atualizado periodicamente para aceitar as versões mais recentes. Se houver uma necessidade imediata de migrar, como solução alternativa, você poderá exportar os bancos de dados/coleções para o armazenamento do Azure e, em seguida, apontar a origem para o despejo resultante. Crie a SAS no contêiner de BLOB em Gerenciador de Armazenamento e, em seguida, use a URL com informações de SAS do contêiner como a cadeia de conexão de detalhes de origem.<br><br> |

## <a name="next-steps"></a>Próximas etapas

* Veja o tutorial [migrar o MongoDB para a API do Azure Cosmos DB para o MongoDB online usando o DMS](tutorial-mongodb-cosmos-db-online.md).
* Veja o tutorial [migrar o MongoDB para a API do Azure Cosmos DB para MongoDB offline usando DMS](tutorial-mongodb-cosmos-db.md).