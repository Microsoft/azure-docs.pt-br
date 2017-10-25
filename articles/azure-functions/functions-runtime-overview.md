---
title: "Visão geral do Azure Functions Runtime | Microsoft Docs"
description: "Visão geral da visualização do Azure Functions Runtime"
services: functions
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/08/2017
ms.author: anwestg
ms.openlocfilehash: cb98d5f2aaa526555820c15ba5a32fb7e78ffc5a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-runtime-overview"></a>Visão geral do Azure Functions Runtime

O Azure Functions Runtime fornece a você uma nova maneira para aproveitar a simplicidade e a flexibilidade do modelo de programação do Azure Functions local. Compilado sobre as mesmas raízes de código-fonte aberto que o Azure Functions, o Azure Functions Runtime é implantado localmente a fim de fornecer uma experiência de desenvolvimento quase idêntica ao serviço de nuvem.

![Portal de visualização do Azure Functions Runtime][1]

O Azure Functions Runtime permite que você experimente o Azure Functions antes de se comprometer com a nuvem. Dessa forma, você levar seus ativos de código compilados para a nuvem quando decidir migrar.  O runtime também disponibiliza novas opções, como o uso do poder de computação sobressalente de seus computadores locais para executar processos em lote durante a noite. Você também pode usar os dispositivos de sua organização para enviar condicionalmente dados a outros sistemas, locais e na nuvem.

O Azure Functions Runtime é formado por duas partes:
* Função de Gerenciamento do Azure Functions Runtime
* Função de Trabalho do Azure Functions Runtime

## <a name="azure-functions-management-role"></a>Função de Gerenciamento do Azure Functions

A função de gerenciamento do Azure Functions fornece um host para gerenciar o Functions localmente. Essa função executa as seguintes tarefas:

* Hospedagem do Portal de Gerenciamento do Azure Functions, que é o mesmo do [Portal do Azure](https://portal.azure.com). Isso permite que você desenvolva suas funções da mesma maneira que faria no Portal do Azure.
* Distribuição de funções por vários trabalhadores do Functions.
* Fornecimento de um ponto de extremidade de publicação, para que você possa publicar suas funções diretamente do Microsoft Visual Studio.

## <a name="azure-functions-worker-role"></a>Função de Trabalho do Azure Functions

As Funções de Trabalho do Azure Functions são implantadas em Contêineres do Windows, e é nesse local que o código da função é executado.  Você pode implantar várias Funções de Trabalho em toda sua organização, e essa é uma maneira importante com a qual os clientes podem usar o poder da computação sobressalente.

## <a name="minimum-requirements"></a>Requisitos mínimos

Para começar com o Azure Functions Runtime, você deve ter um computador com **Windows Server 2016 ou com a Atualização do Windows 10 para Criadores** com acesso a uma instância do **SQL Server**.

## <a name="next-steps"></a>Próximas etapas

Instalar a [visualização do Azure Functions Runtime](https://aka.ms/azafr)

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png
