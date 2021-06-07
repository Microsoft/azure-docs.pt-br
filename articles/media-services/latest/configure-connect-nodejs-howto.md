---
title: Conectar-se à API dos serviços de mídia do Azure v3-Node.js
description: Este artigo demonstra como se conectar à API dos serviços de mídia v3 com Node.js.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/17/2021
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: 33d84ca86ac3cd4696dce3797b015b861884182a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102216421"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Conectar-se à API dos serviços de mídia v3-Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artigo mostra como se conectar ao SDK de node.js do Azure Media Services V3 usando o método de entrada da entidade de serviço. Você trabalhará com arquivos no repositório de exemplos *Media-Services-V3-node-Tutorials* . O exemplo *HelloWorld-ListAssets* contém o código para a conexão e lista os ativos na conta.

## <a name="prerequisites"></a>Pré-requisitos

- Uma instalação do Visual Studio Code.
- Instale o [Node.js](https://nodejs.org/en/download/).
- Instale o [typescript](https://www.typescriptlang.org/download).
- [Crie uma conta de Serviços de Mídia](./create-account-howto.md). Lembre-se do nome do grupo de recursos e da conta dos Serviços de Mídia.
- Crie uma entidade de serviço para seu aplicativo. Consulte [APIs de acesso](./access-api-howto.md).<br/>**Dica de pro!** Mantenha essa janela aberta ou Copie tudo na guia JSON para o bloco de notas. 
- Certifique-se de obter a versão mais recente do [SDK do AzureMediaServices para JavaScript](https://www.npmjs.com/package/@azure/arm-mediaservices).

> [!IMPORTANT]
> Examine as [convenções de nomenclatura](media-services-apis-overview.md#naming-conventions) dos serviços de mídia do Azure para entender as principais restrições de nomenclatura em entidades.

## <a name="clone-the-nodejs-samples-repo"></a>Clonar o repositório de exemplos de Node.JS

Você trabalhará com alguns arquivos nos exemplos do Azure. Clone o repositório de exemplos de Node.JS.

```git
git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
```

## <a name="install-the-packages"></a>Instalar os pacotes

### <a name="install-azurearm-mediaservices"></a>Instalar @azure/arm-mediaservices

```bash
npm install @azure/arm-mediaservices
```

### <a name="install-azurems-rest-nodeauth"></a>Instalar @azure/ms-rest-nodeauth

Instale a versão mínima de " @azure/ms-rest-nodeauth ": "^ 3.0.0".

```bash
npm install @azure/ms-rest-nodeauth@"^3.0.0"
```

Para este exemplo, você usará os seguintes pacotes no `package.json` arquivo.

|Pacote|Descrição|
|---|---|
|`@azure/arm-mediaservices`|SDK dos serviços de mídia do Azure. <br/>Para verificar se você está usando o pacote mais recente dos serviços de mídia do Azure, verifique [NPM instalar @azure/arm-mediaservices ](https://www.npmjs.com/package/@azure/arm-mediaservices).|
|`@azure/ms-rest-nodeauth` | Necessário para autenticação do AAD usando entidade de serviço ou identidade gerenciada|
|`@azure/storage-blob`|SDK de armazenamento. Usado ao carregar arquivos em ativos.|
|`@azure/ms-rest-js`| Usado para entrar.|
|`@azure/storage-blob` | Usado para carregar e baixar arquivos em ativos nos serviços de mídia do Azure para codificação.|
|`@azure/abort-controller`| Usado junto com o cliente de armazenamento para atingir o tempo limite de operações de download de longa execução|

### <a name="create-the-packagejson-file"></a>Criar o package.jsno arquivo

1. Crie um `package.json` arquivo usando seu editor favorito.
1. Abra o arquivo e cole o seguinte código:

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.ts",
  "dependencies": {
    "@azure/arm-mediaservices": "^8.0.0",
    "@azure/abort-controller": "^1.0.2",
    "@azure/ms-rest-nodeauth": "^3.0.6",
    "@azure/storage-blob": "^12.4.0",
  }
}
```

## <a name="connect-to-nodejs-client-using-typescript"></a>Conectar-se a Node.js cliente usando o TypeScript



### <a name="sample-env-file"></a>Arquivo *. env* de exemplo

Copie o conteúdo desse arquivo para um arquivo chamado *. env*. Ele deve ser armazenado na raiz do seu repositório de trabalho. Esses são os valores obtidos na página de acesso à API para sua conta dos serviços de mídia no Portal.

Depois de criar o arquivo *. env* , você pode começar a trabalhar com os exemplos.

```nodejs
# Values from the API Access page in the portal
AZURE_CLIENT_ID=""
AZURE_CLIENT_SECRET= ""
AZURE_TENANT_ID= ""

# Change this to match your AAD Tenant domain name. 
AAD_TENANT_DOMAIN = "microsoft.onmicrosoft.com"

# Set this to your Media Services Account name, resource group it is contained in, and location
AZURE_MEDIA_ACCOUNT_NAME = ""
AZURE_LOCATION= ""
AZURE_RESOURCE_GROUP= ""

# Set this to your Azure Subscription ID
AZURE_SUBSCRIPTION_ID= ""

# You must change these if you are using Gov Cloud, China, or other non standard cloud regions
AZURE_ARM_AUDIENCE= "https://management.core.windows.net"
AZURE_ARM_ENDPOINT="https://management.azure.com"

# DRM Testing
DRM_SYMMETRIC_KEY="add random base 64 encoded string here"
```

## <a name="run-the-sample-application-helloworld-listassets"></a>Executar o aplicativo de exemplo *HelloWorld-ListAssets*

1. Altere o diretório para a pasta *AMSv3Samples*

```bash
cd AMSv3Samples
```

2. Instale os pacotes usados no arquivo *packages.json*.

```
npm install 
```

3. Altere o diretório para a pasta *HelloWorld-ListAssets* .

```bash
cd HelloWorld-ListAssets
```

4. Inicie o Visual Studio Code na pasta AMSv3Samples. Isso é necessário para iniciar na pasta em que a pasta ". vscode" e tsconfig.jsnos arquivos estão localizados

```dotnetcli
cd ..
code .
```

Abra a pasta para *HelloWorld-ListAssets* e abra o arquivo *index. TS* no editor de Visual Studio Code.

Enquanto estiver no arquivo *index.ts*, pressione F5 para iniciar o depurador. Você deverá ver uma lista de ativos exibidos se já tiver ativos na conta. Se a conta estiver vazia, você verá uma lista vazia.  

Para ver rapidamente os ativos listados, use o portal para carregar alguns arquivos de vídeo. Os ativos serão automaticamente criados cada um e executar esse script novamente retornará seus nomes.

### <a name="a-closer-look-at-the-helloworld-listassets-sample"></a>Uma análise mais detalhada do exemplo *HelloWorld-ListAssets*

O exemplo *HelloWorld-ListAssets* mostra como se conectar ao cliente dos serviços de mídia com uma entidade de serviço e listar os ativos na conta. Consulte os comentários no código para obter uma explicação detalhada do que ele faz.

```ts
import * as msRestNodeAuth from "@azure/ms-rest-nodeauth";
import { AzureMediaServices } from '@azure/arm-mediaservices';
import { AzureMediaServicesOptions } from "@azure/arm-mediaservices/esm/models";
// Load the .env file if it exists
import * as dotenv from "dotenv";
dotenv.config();

export async function main() {
  // Copy the samples.env file and rename it to .env first, then populate it's values with the values obtained 
  // from your Media Services account's API Access page in the Azure portal.
  const clientId = process.env.AZURE_CLIENT_ID as string;
  const secret = process.env.AZURE_CLIENT_SECRET as string;
  const tenantDomain = process.env.AAD_TENANT_DOMAIN as string;
  const subscriptionId = process.env.AZURE_SUBSCRIPTION_ID as string;
  const resourceGroup = process.env.AZURE_RESOURCE_GROUP as string;
  const accountName = process.env.AZURE_MEDIA_ACCOUNT_NAME as string;

  let clientOptions: AzureMediaServicesOptions = {
    longRunningOperationRetryTimeout: 5, // set the time out for retries to 5 seconds
    noRetryPolicy: false // use the default retry policy.
  }

  const creds = await msRestNodeAuth.loginWithServicePrincipalSecret(clientId, secret, tenantDomain);
  const mediaClient = new AzureMediaServices(creds, subscriptionId, clientOptions);

  // List Assets in Account
  console.log("Listing Assets Names in account:")
  var assets = await mediaClient.assets.list(resourceGroup, accountName);

  assets.forEach(asset => {
    console.log(asset.name);
  });

  if (assets.odatanextLink) {
    console.log("There are more than 1000 assets in this account, use the assets.listNext() method to continue listing more assets if needed")
    console.log("For example:  assets = await mediaClient.assets.listNext(assets.odatanextLink)");
  }
}

main().catch((err) => {
  console.error("Error running sample:", err.message);
});
```

## <a name="more-samples"></a>Mais exemplos

Os exemplos a seguir estão disponíveis no [repositório](https://github.com/Azure-Samples/media-services-v3-node-tutorials)

|Nome do projeto|Caso de uso|
|---|---|
|Live/index. TS| Exemplo básico de transmissão ao vivo. **Aviso**, certifique-se de verificar se todos os recursos foram limpos e não há mais cobrança no portal ao usar o Live|
|StreamFilesSample/index. TS| Exemplo básico para carregar um arquivo local ou uma codificação de uma URL de origem. Exemplo mostra como usar o SDK de armazenamento para baixar conteúdo e mostra como transmitir para um player |
|StreamFilesWithDRMSample/index. TS| Demonstra como codificar e transmitir usando o Widevine e o DRM do PlayReady |
|VideoIndexerSample/index. TS| Exemplo de como usar o analisador de vídeo e áudio predefinições para gerar metadados e informações de um arquivo de vídeo ou áudio |

## <a name="see-also"></a>Veja também

- [Documentação de referência para os módulos dos serviços de mídia do Azure para Node.js](/javascript/api/overview/azure/media-services)
- [Azure para desenvolvedores do JavaScript e do Node.js](/azure/developer/javascript/)
- [Código-fonte dos Serviços de Mídia no repositório do GitHub @azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Documentação do Pacote do Azure para desenvolvedores do Node.js](/javascript/api/overview/azure/)
- [Conceitos dos Serviços de Mídia](concepts-overview.md)
- [npm install @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Azure para desenvolvedores do JavaScript e do Node.js](/azure/developer/javascript/)
- [Código-fonte dos Serviços de Mídia no repositório @azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>Próximas etapas

Explore a documentação de [referência do Node.js](/javascript/api/overview/azure/mediaservices/management) dos Serviços de Mídia e confira as [amostras](https://github.com/Azure-Samples/media-services-v3-node-tutorials) que explicam como usar a API dos Serviços de Mídia com o Node.js.
