---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 01/11/2019
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: ac6b008597b6d6e557a0cc412c00c2202231bc3d
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172562"
---
> [!NOTE]
> Normalmente, solicitações com várias partes requerem três partes:
> * Um **Content-Type** cabeçalho:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * Um **Content-Disposition**:
>   * `form-data; name="metadata"`
> * O conteúdo do arquivo que será carregado
>
> **Content-Type** e **Content-Disposition** variam dependendo do cenário de uso.

Solicitações com várias partes podem ser feitas de forma programática (usando C#), por meio de um cliente REST ou de uma ferramenta como [Postman](https://docs.microsoft.com/azure/digital-twins/how-to-configure-postman#multi). Ferramentas de cliente REST podem ter diferentes níveis de suporte para solicitações com várias partes complexas. As definições de configuração também podem variar ligeiramente de uma ferramenta para outra. Verifique qual ferramenta é mais adequada às suas necessidades.

> [!IMPORTANT]
> As solicitações com várias partes feitas para as APIs de Gerenciamento dos Gêmeos Digitais do Azure normalmente são divididas em duas partes:
> * metadados de blob (como um tipo MIME associado), declarados por **Content-Type** e/ou **Content-Disposition**
> * Conteúdo do blob, que inclui o conteúdo não estruturado de um arquivo a ser carregado
>
> Nenhuma das duas partes é necessária para solicitações de **PATCH**. Ambos são necessários para **POST** ou criam operações.

O [Código-fonte do Início Rápido da Ocupação](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs) contém exemplos de C# completos que demonstram como fazer solicitações com várias partes para as APIs de Gerenciamento dos Gêmeos Digitais do Azure.