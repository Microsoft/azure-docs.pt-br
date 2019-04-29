---
title: Introdução ao SDK da CDN do Azure para Node.js | Microsoft Docs
description: Aprenda a escrever aplicativos do Node.js para gerenciar a CDN do Azure.
services: cdn
documentationcenter: nodejs
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c4bb6a61-de3d-4f0c-9dca-202554c43dfa
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 48a84520a61d19968b467091871459e21898dd5e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564043"
---
# <a name="get-started-with-azure-cdn-development"></a>Introdução ao desenvolvimento de CDN do Azure
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

Você pode usar o [SDK da CDN do Azure para Node.js](https://www.npmjs.com/package/azure-arm-cdn) para automatizar a criação e o gerenciamento de pontos de extremidade e perfis de CDN.  Este tutorial o orienta na criação de um aplicativo de console simples do Node.js, que demonstra várias operações disponíveis.  Este tutorial não se destina a descrever em detalhes todos os aspectos do SDK da CDN do Azure para Node.js.

Para concluir este tutorial, você já deve ter o [Node.js](https://www.nodejs.org) **4.x.x** ou posterior instalado e configurado.  Você pode usar qualquer editor de texto que desejar para criar o aplicativo do Node.js.  Para escrever este tutorial, usei o [Visual Studio Code](https://code.visualstudio.com).  

> [!TIP]
> O [projeto concluído deste tutorial](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74) está disponível para download no MSDN.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>Criar o projeto e adicionar dependências de NPM
Agora que criamos um grupo de recursos para nossos perfis CDN e demos permissão para o nosso aplicativo Azure AD gerenciar os perfis CDN e os pontos de extremidade neste grupo, podemos começar a criação do nosso aplicativo.

Crie uma pasta para armazenar o aplicativo.  Em um console com as ferramentas do Node.js no caminho atual, defina o local atual para essa nova pasta e inicialize o projeto executando:

    npm init

Em seguida, será apresentada uma série de perguntas para inicializar o projeto.  Para o **ponto de entrada**, este tutorial usa *app.js*.  Você pode ver minhas outras opções no exemplo a seguir.

![Saída de NPM init](./media/cdn-app-dev-node/cdn-npm-init.png)

Agora, o projeto é inicializado com um arquivo *packages.json* .  Nosso projeto usará algumas bibliotecas do Azure contidas em pacotes NPM.  Usaremos a Execução do Cliente do Azure para Node.js (ms-rest-azure) e a Biblioteca de Clientes de CDN do Azure para Node.js (azure-arm-cd).  Vamos adicioná-los ao projeto como dependências.

    npm install --save ms-rest-azure
    npm install --save azure-arm-cdn

Depois dos pacotes terminarem de ser instalados, o arquivo *package.json* deverá ser semelhante a este exemplo (os números de versão podem diferir):

``` json
{
  "name": "cdn_node",
  "version": "1.0.0",
  "description": "Azure CDN Node.js tutorial project",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Cam Soper",
  "license": "MIT",
  "dependencies": {
    "azure-arm-cdn": "^0.2.1",
    "ms-rest-azure": "^1.14.4"
  }
}
```

Finalmente, usando o editor de texto, crie um arquivo de texto em branco e salve-o na raiz da pasta do projeto como *app.js*.  Agora estamos prontos para começar a escrever o código.

## <a name="requires-constants-authentication-and-structure"></a>Requisitos, constantes, autenticação e estrutura
Com *app.js* aberto no editor, vamos escrever a estrutura básica do programa.

1. Adicione os "requisitos" para os pacotes de NPM na parte superior com o seguinte:
   
    ``` javascript
    var msRestAzure = require('ms-rest-azure');
    var cdnManagementClient = require('azure-arm-cdn');
    ```
2. Precisamos definir algumas constantes que serão usadas nos nossos métodos.  Adicione o seguinte.  Substitua os espaços reservados, inclusive os **&lt;sinais maior e menor que&gt;**, por seus próprios valores, conforme necessário.
   
    ``` javascript
    //Tenant app constants
    const clientId = "<YOUR CLIENT ID>";
    const clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    const tenantId = "<YOUR TENANT ID>";
   
    //Application constants
    const subscriptionId = "<YOUR SUBSCRIPTION ID>";
    const resourceGroupName = "CdnConsoleTutorial";
    const resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```
3. Em seguida, instanciaremos o cliente de gerenciamento de CDN e forneceremos as credenciais.
   
    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Se você estiver usando a autenticação de usuário individual, essas duas linhas terão uma aparência ligeiramente diferente.
   
   > [!IMPORTANT]
   > Use este exemplo de código somente se optar pela autenticação de usuário individual, em vez de uma entidade de serviço.  Tenha muito cuidado para proteger suas credenciais de usuário individuais e mantê-las secretas.
   > 
   > 
   
    ``` javascript
    var credentials = new msRestAzure.UserTokenCredentials(clientId, 
        tenantId, '<username>', '<password>', '<redirect URI>');
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Substitua os itens nos **&lt;sinais maior e menor que&gt;** pelas informações corretas.  Para `<redirect URI>`, use o URI de redirecionamento que você inseriu quando registrou o aplicativo no Azure AD.
4. O aplicativo de console do Node.js usará alguns parâmetros da linha de comando.  Validaremos que pelo menos um parâmetro foi passado.
   
   ```javascript
   //Collect command-line parameters
   var parms = process.argv.slice(2);
   
   //Do we have parameters?
   if(parms == null || parms.length == 0)
   {
       console.log("Not enough parameters!");
       console.log("Valid commands are list, delete, create, and purge.");
       process.exit(1);
   }
   ```
5. Isso nos leva para a parte principal do programa, na qual fazemos a ramificação para outras funções com base nos parâmetros passados.
   
    ```javascript
    switch(parms[0].toLowerCase())
    {
        case "list":
            cdnList();
            break;
   
        case "create":
            cdnCreate();
            break;
   
        case "delete":
            cdnDelete();
            break;
   
        case "purge":
            cdnPurge();
            break;
   
        default:
            console.log("Valid commands are list, delete, create, and purge.");
            process.exit(1);
    }
    ```
6. Em vários locais no programa, é necessário verificar se o número correto de parâmetros foi passado e exibir ajuda se eles não estiverem corretos.  Vamos criar funções para fazer isso.
   
   ```javascript
   function requireParms(parmCount) {
       if(parms.length < parmCount) {
           usageHelp(parms[0].toLowerCase());
           process.exit(1);
       }
   }
   
   function usageHelp(cmd) {
       console.log("Usage for " + cmd + ":");
       switch(cmd)
       {
           case "list":
               console.log("list profiles");
               console.log("list endpoints <profile name>");
               break;
   
           case "create":
               console.log("create profile <profile name>");
               console.log("create endpoint <profile name> <endpoint name> <origin hostname>");
               break;
   
           case "delete":
               console.log("delete profile <profile name>");
               console.log("delete endpoint <profile name> <endpoint name>");
               break;
   
           case "purge":
               console.log("purge <profile name> <endpoint name> <path>");
               break;
   
           default:
               console.log("Invalid command.");
       }
   }
   ```
7. Finalmente, as funções que usaremos no cliente de gerenciamento de CDN são assíncronas, assim, precisam de um método de retorno de chamada quando são concluídas.  Vamos criar um que possa exibir a saída do cliente de gerenciamento de CDN (se houver) e sair do programa normalmente.
   
    ```javascript
    function callback(err, result, request, response) {
        if (err) {
            console.log(err);
            process.exit(1);
        } else {
            console.log((result == null) ? "Done!" : result);
            process.exit(0);
        }
    }
    ```

Agora que a estrutura básica do programa foi escrita, devemos criar as funções chamadas com base em nossos parâmetros.

## <a name="list-cdn-profiles-and-endpoints"></a>Relacione os perfis CDN e os pontos de extremidade
Vamos começar com o código para listar os perfis e os pontos de extremidade existentes.  Os comentários do código fornecem a sintaxe esperada para que saibamos onde fica cada parâmetro.

```javascript
// list profiles
// list endpoints <profile name>
function cdnList(){
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profiles":
            console.log("Listing profiles...");
            cdnClient.profiles.listByResourceGroup(resourceGroupName, callback);
            break;

        case "endpoints":
            requireParms(3);
            console.log("Listing endpoints...");
            cdnClient.endpoints.listByProfile(parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>Criar perfis CDN e pontos de extremidade
Em seguida, escreveremos as funções para criar perfis e pontos de extremidade.

```javascript
function cdnCreate() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profile":
            cdnCreateProfile();
            break;

        case "endpoint":
            cdnCreateEndpoint();
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}

// create profile <profile name>
function cdnCreateProfile() {
    requireParms(3);
    console.log("Creating profile...");
    var standardCreateParameters = {
        location: resourceLocation,
        sku: {
            name: 'Standard_Verizon'
        }
    };

    cdnClient.profiles.create(parms[2], standardCreateParameters, resourceGroupName, callback);
}

// create endpoint <profile name> <endpoint name> <origin hostname>        
function cdnCreateEndpoint() {
    requireParms(5);
    console.log("Creating endpoint...");
    var endpointProperties = {
        location: resourceLocation,
        origins: [{
            name: parms[4],
            hostName: parms[4]
        }]
    };

    cdnClient.endpoints.create(parms[3], endpointProperties, parms[2], resourceGroupName, callback);
}
```

## <a name="purge-an-endpoint"></a>Limpar um ponto de extremidade
Supondo que o ponto de extremidade tenha sido criado, uma tarefa comum que convém executar no programa é limpar o conteúdo no ponto de extremidade.

```javascript
// purge <profile name> <endpoint name> <path>
function cdnPurge() {
    requireParms(4);
    console.log("Purging endpoint...");
    var purgeContentPaths = [ parms[3] ];
    cdnClient.endpoints.purgeContent(parms[2], parms[1], resourceGroupName, purgeContentPaths, callback);
}
```

## <a name="delete-cdn-profiles-and-endpoints"></a>Excluir perfis CDN e pontos de extremidade
A última função que incluiremos exclui pontos de extremidade e perfis.

```javascript
function cdnDelete() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        // delete profile <profile name>
        case "profile":
            requireParms(3);
            console.log("Deleting profile...");
            cdnClient.profiles.deleteIfExists(parms[2], resourceGroupName, callback);
            break;

        // delete endpoint <profile name> <endpoint name>
        case "endpoint":
            requireParms(4);
            console.log("Deleting endpoint...");
            cdnClient.endpoints.deleteIfExists(parms[3], parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="running-the-program"></a>Executando o programa
Agora podemos executar o programa do Node.js usando o depurador que preferirmos ou no console.

> [!TIP]
> Se você estiver usando o Código do Visual Studio como depurador, precisará configurar o ambiente para passar os parâmetros da linha de comando.  O Visual Studio Code faz isso no arquivo **launch.json**.  Procure uma propriedade denominada **args** e adicione uma matriz de valores da cadeia de caracteres aos parâmetros, para que seja semelhante a isto: `"args": ["list", "profiles"]`.
> 
> 

Vamos começar listando os perfis.

![Listar perfis](./media/cdn-app-dev-node/cdn-list-profiles.png)

Obtivemos uma matriz vazia.  Como não há perfis no grupo de recursos, isso é esperado.  Vamos criar um perfil agora.

![Criar perfil](./media/cdn-app-dev-node/cdn-create-profile.png)

Agora, vamos adicionar um ponto de extremidade.

![Criar ponto de extremidade](./media/cdn-app-dev-node/cdn-create-endpoint.png)

Por fim, vamos excluir o perfil.

![Excluir perfil](./media/cdn-app-dev-node/cdn-delete-profile.png)

## <a name="next-steps"></a>Próximas etapas
Para ver o projeto concluído desse passo a passo, [baixe o exemplo](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74).

Para ver a referência do SDK da CDN do Azure para Node.js, exiba a [referência](https://azure.github.io/azure-sdk-for-node/azure-arm-cdn/latest/).

Para localizar documentação adicional sobre o SDK do Azure para Node.js, exiba a [referência completa](https://azure.github.io/azure-sdk-for-node/).

Gerencie seus recursos CDN com o [PowerShell](cdn-manage-powershell.md).

