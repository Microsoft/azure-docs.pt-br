---
title: Atualizando para o SDK de Gerenciamento do .NET do Azure Search versão 2 – Azure Search
description: Atualize para o SDK de Gerenciamento do .NET do Azure Search versão 2 de versões anteriores. Conheça as novidades e quais alterações de código são necessárias.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/15/2018
ms.custom: seodec2018
ms.openlocfilehash: 369e5283d7350729e0d8cc44f94f1f2e71b133c3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60817000"
---
# <a name="upgrading-to-the-azure-search-net-management-sdk-version-2"></a>Atualizar para o SDK de Gerenciamento do .NET do Azure Search versão 2
Se você estiver usando a versão 1.0.2 ou mais antiga do [SDK de Gerenciamento do .NET do Azure Search](https://aka.ms/search-mgmt-sdk), este artigo ajudará você a atualizar seu aplicativo para usar a versão 2, mais recente.

A versão 2 do SDK de Gerenciamento do .NET do Azure Search contém algumas alterações de versões anteriores. A maioria das alterações é leve e, portanto, a alteração do seu código não deve exigir muito. Confira [Etapas da atualização](#UpgradeSteps) para obter instruções sobre como alterar o seu código para usar a nova versão do SDK.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2"></a>Novidades na versão 2
A versão 2 do SDK de Gerenciamento do .NET do Azure Search tem como alvo a mesma versão disponível da API REST de Gerenciamento do Azure Search que as versões anteriores do SDK, especificamente 2015-08-19. As alterações no SDK são estritamente no lado do cliente, a fim de aumentar a usabilidade do próprio SDK. Essas alterações incluem o seguinte:

* Agora, o `Services.CreateOrUpdate` e suas versões assíncronas sondam automaticamente o provisionamento `SearchService` e não retornam até que o serviço de provisionamento esteja concluído. Isso evita a necessidade de escrever esse código de sondagem por conta própria.
* Se você ainda quiser sondar manualmente o provisionamento do serviço, use o novo método `Services.BeginCreateOrUpdate` ou uma de suas versões assíncronas.
* Novos métodos `Services.Update` e suas versões assíncronas foram adicionados ao SDK. Esses métodos usam HTTP PATCH para oferecer suporte à atualização incremental de um serviço. Por exemplo, agora você pode dimensionar um serviço passando uma instância `SearchService` para esses métodos contendo apenas as propriedades `partitionCount` e `replicaCount` desejadas. A forma antiga de chamar `Services.Get`, modificando o `SearchService` retornado, e passando-o para `Services.CreateOrUpdate` ainda tem suporte, mas não é mais necessária. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Etapas da atualização
Primeiro, atualize sua referência NuGet para `Microsoft.Azure.Management.Search` usando o Console do Gerenciador de Pacotes NuGet ou clicando com o botão direito do mouse nas referências do projeto e selecionando “Gerenciar pacotes NuGet...” no Visual Studio.

Depois que o NuGet tiver baixado os novos pacotes e suas dependências, recompile o projeto. Dependendo de como o código está estruturado, ele poderá ser recriado com êxito. Nesse caso, você está pronto para começar!

Se a compilação falhar, talvez você já tenha implementado algumas interfaces do SDK (por exemplo, para fins de teste de unidade), que foram alteradas. Para resolver isso, implemente os novos métodos, como `BeginCreateOrUpdateWithHttpMessagesAsync`.

Depois de corrigir os erros de build, será possível fazer alterações em seu aplicativo para tirar proveito da nova funcionalidade, se desejar. Novos recursos no SDK são detalhados em [Novidades na versão 2](#WhatsNew).

## <a name="conclusion"></a>Conclusão
Apreciamos os seus comentários sobre o SDK. Se tiver problemas, fique à vontade para solicitar ajuda no [Fórum do MSDN sobre o Azure Search](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Se encontrar um bug, você poderá apresentar um problema no [repositório GitHub sobre o SDK do .NET do Azure](https://github.com/Azure/azure-sdk-for-net/issues). Não deixe de colocar o prefixo "[Azure Search]" no título do problema.

Obrigado por usar o Azure Search!
