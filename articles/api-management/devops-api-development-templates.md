---
title: CI/CD para gerenciamento de API do Azure usando modelos ARM
description: Introdução à API DevOps com o gerenciamento de API do Azure, usando modelos de Azure Resource Manager para gerenciar implantações de API em um pipeline de CI/CD
services: api-management
author: miaojiang
ms.service: api-management
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: apimpm
ms.openlocfilehash: 62f163b9ce649cd5ddb52b4325682570633dfb92
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183151"
---
# <a name="cicd-for-api-management-using-azure-resource-manager-templates"></a>CI/CD para gerenciamento de API usando modelos de Azure Resource Manager

Este artigo mostra como usar a API DevOps com o gerenciamento de API do Azure, por meio de modelos de Azure Resource Manager. Com o valor estratégico das APIs, um pipeline de CI/CD (integração contínua e implantação contínua) se tornou um aspecto importante do desenvolvimento de API. Ele permite que as organizações automatizem a implantação de alterações de API sem etapas manuais propensas a erros, detectem problemas antes e, em última análise, forneçam valor aos usuários mais rapidamente. 

Para obter detalhes, ferramentas e exemplos de código para implementar a abordagem de DevOps descrita neste artigo, consulte o kit de [recursos do DevOps de gerenciamento de API do Azure](https://github.com/Azure/azure-api-management-devops-resource-kit) de software livre no github. Como os clientes trazem uma ampla gama de culturas de engenharia e soluções de automação existentes, a abordagem não é uma solução de tamanho único.

## <a name="the-problem"></a>O problema

Hoje, as organizações normalmente têm vários ambientes de implantação (como desenvolvimento, teste e produção) e usam instâncias de gerenciamento de API separadas para cada ambiente. Algumas instâncias são compartilhadas por várias equipes de desenvolvimento, que são responsáveis por diferentes APIs com cadências de versão diferentes.

Como resultado, os clientes enfrentam os seguintes desafios:

* Como automatizar a implantação de APIs no gerenciamento de API
* Como migrar configurações de um ambiente para outro
* Como evitar a interferência entre diferentes equipes de desenvolvimento que compartilham a mesma instância de gerenciamento de API

## <a name="manage-configurations-in-resource-manager-templates"></a>Gerenciar configurações em modelos do Resource Manager

A imagem a seguir ilustra a abordagem proposta. 

:::image type="content" source="media/devops-api-development-templates/apim-devops.png" alt-text="Diagrama que ilustra o DevOps com o gerenciamento de API.":::

Neste exemplo, há dois ambientes de implantação: *desenvolvimento* e *produção*. Cada uma tem sua própria instância de gerenciamento de API. 

* Os desenvolvedores de API têm acesso à instância de desenvolvimento e podem usá-lo para desenvolver e testar suas APIs. 
* Uma equipe designada chamada *editores de API* gerencia a instância de produção.

A chave nessa abordagem proposta é manter todas as configurações de gerenciamento de API em [modelos de Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md). A organização deve manter esses modelos em um sistema de controle do código-fonte, como o git. Conforme ilustrado na imagem, um repositório do Publicador contém todas as configurações da instância de gerenciamento de API de produção em uma coleção de modelos:

|Modelo  |Descrição  |
|---------|---------|
|Modelo de serviço     | Configurações de nível de serviço da instância de gerenciamento de API, como tipo de preço e domínios personalizados.         |
|Modelos compartilhados     |  Recursos compartilhados em uma instância de gerenciamento de API, como grupos, produtos e agentes.    |
|Modelos de API     |  Configurações de APIs e seus subrecursos: operações, políticas, configurações de diagnóstico.        |
|Modelo mestre (principal)     |   Une tudo isso [vinculando](../azure-resource-manager/templates/linked-templates.md) -se a todos os modelos e implantando-os na ordem. Para implantar todas as configurações em uma instância de gerenciamento de API, implante o modelo principal. Você também pode implantar cada modelo individualmente.       |

Os desenvolvedores de API criarão o bifurcação do repositório do Publicador para um repositório de desenvolvedores e trabalharão nas alterações de suas APIs. Na maioria dos casos, eles se concentram nos modelos de API para suas APIs e não precisam alterar os modelos de serviço ou compartilhados.

## <a name="migrate-configurations-to-templates"></a>Migrar configurações para modelos
Os desenvolvedores de API enfrentam desafios ao trabalhar com modelos do Resource Manager:

* Os desenvolvedores de API geralmente trabalham com a [especificação openapi](https://github.com/OAI/OpenAPI-Specification) e podem não estar familiarizados com os esquemas do Resource Manager. A criação manual de modelos pode ser propenso a erros. 

   Uma ferramenta chamada [criador](https://github.com/Azure/azure-api-management-devops-resource-kit/blob/master/src/APIM_ARMTemplate/README.md#Creator) no Resource Kit pode ajudar a automatizar a criação de modelos de API com base em um arquivo de especificação de API aberta. Além disso, os desenvolvedores podem fornecer políticas de gerenciamento de API para uma API em formato XML. 

* Para clientes que já estão usando o gerenciamento de API, outro desafio é extrair configurações existentes para modelos do Resource Manager. Para esses clientes, uma ferramenta chamada [extrator](https://github.com/Azure/azure-api-management-devops-resource-kit/blob/master/src/APIM_ARMTemplate/README.md#extractor) no Resource Kit pode ajudar a gerar modelos, extraindo as configurações de suas instâncias de gerenciamento de API.  

## <a name="workflow"></a>Fluxo de trabalho

* Depois que os desenvolvedores de API concluírem o desenvolvimento e o teste de uma API e tiverem gerado os modelos de API, eles poderão enviar uma solicitação de pull para mesclar as alterações para o repositório do Publicador. 

* Os editores de API podem validar a solicitação de pull e garantir que as alterações sejam seguras e em conformidade. Por exemplo, eles podem verificar se apenas HTTPS tem permissão para se comunicar com a API. A maioria das validações pode ser automatizada como uma etapa no pipeline de CI/CD.

* Depois que as alterações forem aprovadas e mescladas com êxito, os editores de API poderão optar por implantá-las na instância de produção, seja no agendamento ou sob demanda. A implantação dos modelos pode ser automatizada usando [ações do GitHub](https://github.com/Azure/apimanagement-devops-samples), [Azure pipelines](/azure/devops/pipelines), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md), [CLI do Azure](../azure-resource-manager/templates/deploy-cli.md)ou outras ferramentas.


Com essa abordagem, uma organização pode automatizar a implantação de alterações de API em instâncias de gerenciamento de API e é fácil promover alterações de um ambiente para outro. Como as diferentes equipes de desenvolvimento de API estarão trabalhando em diferentes conjuntos de arquivos e modelos de API, isso impede a interferência entre diferentes equipes.

## <a name="video"></a>Vídeo

> [!VIDEO https://www.youtube.com/embed/4Sp2Qvmg6j8]

## <a name="next-steps"></a>Próximas etapas

- Consulte o [Kit de recursos do DevOps de gerenciamento de API do Azure](https://github.com/Azure/azure-api-management-devops-resource-kit) de software livre para obter informações adicionais, ferramentas e modelos de exemplo.