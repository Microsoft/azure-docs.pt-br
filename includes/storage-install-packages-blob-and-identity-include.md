---
title: arquivo de inclusão
description: arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: de79ea50d12ab322d1e28d0ad650df30ecc0c222
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "74806568"
---
## <a name="install-client-library-packages"></a>Instalar pacotes de biblioteca de cliente

> [!NOTE]
> Os exemplos mostrados aqui usam a biblioteca de cliente de armazenamento do Azure versão 12. A biblioteca de cliente da versão 12 faz parte do SDK do Azure. Para obter mais informações sobre o SDK do Azure, consulte o repositório do SDK do Azure no [GitHub](https://github.com/Azure/azure-sdk).

Para instalar o pacote de armazenamento de BLOBs, execute o seguinte comando no console do Gerenciador de pacotes NuGet:

```powershell
Install-Package Azure.Storage.Blobs
```

Os exemplos mostrados aqui também usam a versão mais recente da [biblioteca de cliente de identidade do Azure para .net](https://www.nuget.org/packages/Azure.Identity/) para autenticar com as credenciais do Azure AD. Para instalar o pacote, execute o seguinte comando no console do Gerenciador de pacotes NuGet:

```powershell
Install-Package Azure.Identity
```
