---
title: Gerenciar recursos e cotas
titleSuffix: Azure Purview
description: Saiba mais sobre cotas e limites de recursos do Azure alcance e como solicitar aumentos de cota.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 57cb1c405e00acb346421d64190a71e9211d21ff
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938827"
---
# <a name="manage-and-increase-quotas-for-resources-with-azure-purview"></a>Gerenciar e aumentar cotas para recursos com o Azure alcance
 
O Azure alcance é um serviço de nuvem para uso por usuários de dados. Você usa o Azure alcance para gerenciar centralmente a governança de dados em todo o seu estado de dados, abrangendo ambientes de nuvem e locais. O serviço permite que os analistas de negócios pesquisem dados relevantes usando termos de negócios significativos. Para aumentar os limites até o máximo para sua assinatura, entre em contato com o suporte.
 
## <a name="azure-purview-limits"></a>Limites do Azure Purview
 
|**Recurso**|  **Limite padrão**  |**Limite máximo**|
|---|---|---|
|Contas do alcance por região, por locatário (todas as assinaturas combinadas)|3|Contatar o suporte|
|vCores disponível para verificação, por conta *|160|160|
|Verificações simultâneas, por conta, em um determinado ponto. O limite é baseado no tipo de fontes de dados examinadas *|5 | 10 |
|Tempo máximo durante o qual uma verificação pode ser executada|7 dias|7 dias|
|Chamadas de API, por conta|APIs 10 milhões/mês para 4 unidades de capacidade tamanho da plataforma. <br>APIs 40M/mês para 16 unidades de capacidade tamanho da plataforma |APIs 10 milhões/mês para 4 unidades de capacidade tamanho da plataforma. <br>APIs 40M/mês para 16 unidades de capacidade tamanho da plataforma|
|Armazenamento, por conta|10 GB para 4 unidades de capacidade tamanho da plataforma. <br>40 GB para 16 unidades de capacidade tamanho da plataforma |10 GB para 4 unidades de capacidade tamanho da plataforma. <br> 40 GB para 16 unidades de capacidade tamanho da plataforma |
|Tamanho dos ativos por conta|ativos físicos de 100 ms |Contatar o suporte|
|Tamanho máximo de um ativo em um catálogo|2 MB|2 MB|
|Comprimento máximo de um nome de ativo e nome de classificação|4 KB|4 KB|
|Comprimento máximo do nome e valor da Propriedade do ativo|32 KB|32 KB|
|Comprimento máximo do nome e valor do atributo de classificação|32 KB|32 KB|
|Número máximo de termos do glossário, por conta|100K|100K|
 
* Cenários de tempo de execução de integração auto-hospedado estão fora do escopo para os limites definidos na tabela acima. 
 
## <a name="next-steps"></a>Próximas etapas
 
> [!div class="nextstepaction"]
>[Tutorial: verificar dados com o Azure alcance](tutorial-scan-data.md)

> [!div class="nextstepaction"]
>[Tutorial: navegar na home page e pesquisar um ativo](tutorial-asset-search.md)

> [!div class="nextstepaction"]
>[Tutorial: procurar ativos e exibir sua linhagem](tutorial-browse-and-view-lineage.md)
