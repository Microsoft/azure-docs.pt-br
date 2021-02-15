---
title: Visão geral do Azure Functions Runtime
description: Visão geral da visualização do Azure Functions Runtime
author: apwestgarth
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: 61e3b82e497afcdc8239a9f4fda3e4f739166a1f
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108432"
---
# <a name="azure-functions-runtime-overview-preview"></a>Visão geral do Azure Functions Runtime (versão prévia)

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

O Azure Functions Runtime (versão prévia) fornece a você uma nova maneira para aproveitar a simplicidade e a flexibilidade do modelo de programação do Azure Functions local. Compilado sobre as mesmas raízes de código-fonte aberto que o Azure Functions, o Azure Functions Runtime é implantado localmente a fim de fornecer uma experiência de desenvolvimento quase idêntica ao serviço de nuvem.

![Portal de visualização do Azure Functions Runtime][1]

O Azure Functions Runtime permite que você experimente o Azure Functions antes de se comprometer com a nuvem. Dessa forma, você levar seus ativos de código compilados para a nuvem quando decidir migrar.  O runtime também disponibiliza novas opções, como o uso do poder de computação sobressalente de seus computadores locais para executar processos em lote durante a noite. Você também pode usar os dispositivos de sua organização para enviar condicionalmente dados a outros sistemas, locais e na nuvem.

O Azure Functions Runtime é formado por duas partes:

* Função de Gerenciamento do Azure Functions Runtime
* Função de Trabalho do Azure Functions Runtime

## <a name="azure-functions-management-role"></a>Função de Gerenciamento do Azure Functions

A função de gerenciamento do Azure Functions fornece um host para gerenciar o Functions localmente. Essa função executa as seguintes tarefas:

* Hospedar o Portal de Gerenciamento do Azure Functions, que é o mesmo que aparece no [Portal do Azure](https://portal.azure.com). O portal fornece uma experiência consistente que permite desenvolver funções da mesma maneira que você faria no portal do Azure.
* Distribuição de funções por vários trabalhadores do Functions.
* Fornecer um ponto de extremidade de publicação para que seja possível publicar suas funções diretamente do Microsoft Visual Studio baixando e importando o perfil de publicação.

## <a name="azure-functions-worker-role"></a>Função de Trabalho do Azure Functions

As funções de trabalho do Azure Functions são implantadas em contêineres do Windows e é nelas que o código de função será executado.  Você pode implantar várias funções de trabalho em toda a organização e essa opção é uma importante maneira pela qual os clientes podem usar o poder da computação sobressalente.  Um exemplo de computação sobressalente em muitas organizações são as máquinas que ficam ligadas o tempo todo, mas que não são usadas por longos períodos.

## <a name="minimum-requirements"></a>Requisitos mínimos

Para começar a usar o Azure Functions Runtime, você deve ter um computador com Windows Server 2016 ou com a Atualização do Windows 10 para Criadores com acesso a uma instância do SQL Server.

## <a name="next-steps"></a>Próximas etapas

Instalar a versão [prévia do Azure Functions Runtime](./functions-runtime-install.md)

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png