---
title: Definir configurações de banco de dados na API do Azure para FHIR
description: Este artigo descreve como definir configurações de banco de dados na API do Azure para FHIR
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/15/2019
ms.author: matjazl
ms.openlocfilehash: 3fe4118a8ecf4479732ba4073b342d3ec9f941b8
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398191"
---
# <a name="configure-database-settings"></a>Definir configurações de banco de dados 

A API do Azure para o FHIR usa o banco de dados para armazenar os seus dados. O desempenho do banco de dados subjacente depende do número de unidades de solicitação (RU) selecionado durante o provisionamento do serviço ou nas configurações do banco de dados depois que o serviço tiver sido provisionado.

A API do Azure para FHIR empresta o conceito de RUs de Cosmos DB (consulte [unidades de solicitação em Azure Cosmos DB](../cosmos-db/request-units.md)) ao definir o desempenho do banco de dados subjacente. 

A taxa de transferência deve ser provisionada para garantir que recursos suficientes do sistema estejam disponíveis para o seu banco de dados em todos os momentos. A quantidade de RUs que você precisa para seu aplicativo depende das operações que você executa. As operações podem variar desde leituras simples e gravações até consultas mais complexas. 

> [!NOTE]
> Conforme operações diferentes consomem um número diferente de RU, retornamos o número real de RUs consumidas em cada chamada à API no cabeçalho de resposta. Dessa forma, você pode criar o perfil do número de RUs consumidas pelo seu aplicativo.

## <a name="update-throughput"></a>Atualizar a taxa de transferência

Para alterar essa configuração na portal do Azure, navegue até a API do Azure para FHIR e abra a folha banco de dados. Em seguida, altere a taxa de transferência provisionada para o valor desejado dependendo das suas necessidades de desempenho. Você pode alterar o valor até um máximo de 10.000 RU/s. Se você precisar de um valor mais alto, entre em contato com o suporte do Azure.

Se a taxa de transferência do banco de dados for maior do que 10.000 RU/s ou se o dado armazenado no banco de dados tiver mais de 50 GB, seu aplicativo cliente deverá ser capaz de lidar com tokens de continuação. Uma nova partição é criada no banco de dados para cada aumento de taxa de transferência de 10.000 RU/s ou se a quantidade de dados armazenados for maior que 50 GB. Várias partições cria uma resposta de várias páginas na qual a paginação é implementada usando tokens de continuação.

> [!NOTE] 
> Maior valor significa maior API do Azure para taxa de transferência FHIR e custo mais alto do serviço.

![Cosmos DB de configuração](media/database/database-settings.png)

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a atualizar sua RUs para a API do Azure para FHIR. Para saber mais sobre como configurar chaves gerenciadas pelo cliente como uma configuração de banco de dados:

>[!div class="nextstepaction"]
>[Configurar chaves gerenciadas pelo cliente](customer-managed-key.md)

Ou você pode implantar uma API do Azure totalmente gerenciada para FHIR:
 
>[!div class="nextstepaction"]
>[Implantar a API do Azure para FHIR](fhir-paas-portal-quickstart.md)