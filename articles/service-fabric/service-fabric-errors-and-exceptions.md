---
title: Exceções comuns do FabricClient lançadas | Microsoft Docs
description: Descreve as exceções e erros comuns que podem ser gerados pelas APIs FabricClient ao executar operações de gerenciamento de aplicativos e clusters.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: chackdan
editor: ''
ms.assetid: bb821313-b221-479f-b08e-36cf07e60a07
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/20/2018
ms.author: oanapl
ms.openlocfilehash: 5bf17f4ced6bb01d8b62b6fa40ed1aeffe6f712f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60946518"
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Exceções e erros comuns ao trabalhar com as APIs FabricClient
As APIs [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) permitem que administradores de clusters e aplicativos executem tarefas administrativas em um aplicativo, serviço ou cluster do Service Fabric. Por exemplo, implantação, atualização e remoção de aplicativos, verificação da integridade de clusters ou teste de serviços. Desenvolvedores de aplicativos e administradores de clusters podem usar as APIs FabricClient para desenvolver ferramentas para gerenciar clusters e aplicativos do Service Fabric.

Há muitos tipos de operações diferentes que podem ser executados usando o FabricClient.  Cada método pode gerar exceções para erros decorrentes de entrada incorreta, erros de tempo de execução ou problemas de infra-estrutura transitórios.  Consulte a documentação de referência da API para localizar quais exceções são geradas por um método específico. Há algumas exceções, no entanto, que podem ser lançadas por várias APIs [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) diferentes. A tabela a seguir lista as exceções que são comuns entre as APIs FabricClient.

| Exceção | Gerada quando |
| --- |:--- |
| [System.Fabric.FabricObjectClosedException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricobjectclosedexception) |O objeto [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) está em um estado fechado. Descarte o objeto [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) que você está usando e instancie um novo objeto [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient). |
| [System.TimeoutException](https://docs.microsoft.com/dotnet/core/api/system.timeoutexception) |A operação atingiu o tempo limite. [OperationTimedOut](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) é retornado quando a operação leva mais do que o MaxOperationTimeout para ser concluída. |
| [System.UnauthorizedAccessException](https://docs.microsoft.com/dotnet/core/api/system.unauthorizedaccessexception) |Ocorreu uma falha na verificação de acesso para a operação. E_ACCESSDENIED é retornado. |
| [System.Fabric.FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) |Ocorreu um erro de tempo de execução ao executar a operação. Qualquer um dos métodos FabricClient pode gerar [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception); a propriedade [ErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception.ErrorCode) indica a causa exata da exceção. Códigos de erro são definidos na enumeração [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode). |
| [System.Fabric.FabricTransientException](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception) |A operação falhou devido a algum tipo de condição de erro transitório. Por exemplo, uma operação pode falhar porque um quorum de réplicas está inacessível temporariamente. Exceções temporárias correspondem a operações com falha que podem ser tentadas novamente. |

Alguns erros [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) comuns que podem ser retornados em uma [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception):

| Erro | Condição |
| --- |:--- |
| CommunicationError |Um erro de comunicação fez com que a operação falhasse e fosse tentada novamente. |
| InvalidCredentialType |O tipo de credencial é inválido. |
| InvalidX509FindType |O X509FindType é inválido. |
| InvalidX509StoreLocation |O local do repositório X509 é inválido. |
| InvalidX509StoreName |O nome do repositório X509 é inválido. |
| InvalidX509Thumbprint |A cadeia de caracteres de impressão digital do certificado X509 é inválida. |
| InvalidProtectionLevel |O nível de proteção é inválido. |
| InvalidX509Store |Não é possível abrir o repositório de certificados X509. |
| InvalidSubjectName |O nome da entidade é inválido. |
| InvalidAllowedCommonNameList |O formato da cadeia de caracteres de lista de nome comum é inválido. Ele deve ser uma lista separada por vírgulas. |

