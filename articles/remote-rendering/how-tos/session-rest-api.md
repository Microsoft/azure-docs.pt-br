---
title: A API REST de gerenciamento de sessão
description: Descreve como gerenciar sessões
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 4323884b2dee3eeccfe71ec7817d92467450e88e
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950018"
---
# <a name="use-the-session-management-rest-api"></a>Usar a API REST de gerenciamento de sessão

Para usar a funcionalidade de renderização remota do Azure, você precisa criar uma *sessão*. Cada sessão corresponde a um servidor que está sendo alocado no Azure ao qual um dispositivo cliente pode se conectar. Quando um dispositivo se conecta, o servidor renderiza os dados solicitados e serve o resultado como um fluxo de vídeo. Durante a criação da sessão, você escolheu em qual [tipo de servidor](../reference/vm-sizes.md) deseja executar, o que determina os preços. Depois que a sessão não for mais necessária, ela deverá ser interrompida. Se não for interrompido manualmente, ele será desligado automaticamente quando o *tempo de concessão* da sessão expirar.

## <a name="rest-api-reference"></a>Referência da API REST

A referência da API REST pode ser encontrada [aqui](/rest/api/mixedreality/2021-01-01preview/remoterendering) e as definições de Swagger [aqui](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mixedreality/data-plane/Microsoft.MixedReality).
Fornecemos um script do PowerShell no [repositório de exemplos do arr](https://github.com/Azure/azure-remote-rendering) na pasta *Scripts* , chamada *RenderingSession.ps1*, que demonstra o uso de nosso serviço. O script e sua configuração são descritos aqui: [exemplo de scripts do PowerShell](../samples/powershell-example-scripts.md).
Também fornecemos SDKs para [.net](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/remoterendering/Azure.MixedReality.RemoteRendering/README.md) e [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/remoterendering/azure-mixedreality-remoterendering/README.md).

> [!IMPORTANT]
> A latência é um fator importante ao usar a renderização remota. Para obter a melhor experiência, crie sessões na região mais próxima de você. O [teste de latência do Azure](https://www.azurespeed.com/Azure/Latency) pode ser usado para determinar qual região está mais próxima de você.

> [!IMPORTANT]
> Um SDK do ARR Runtime é necessário para que um dispositivo cliente se conecte a uma sessão de renderização. Esses SDKs estão disponíveis em [.net](/dotnet/api/microsoft.azure.remoterendering) e [C++](/cpp/api/remote-rendering/). Além da conexão com o serviço, esses SDKs também podem ser usados para iniciar e parar sessões.

## <a name="next-steps"></a>Próximas etapas

* [Usar as APIs de front-end do Azure para autenticação](frontend-apis.md)
* [Scripts de exemplo do PowerShell](../samples/powershell-example-scripts.md)