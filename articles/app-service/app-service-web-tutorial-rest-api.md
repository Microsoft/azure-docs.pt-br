---
title: "Aplicativo de API do Node.js no Serviço de Aplicativo do Azure | Microsoft Docs"
description: "Saiba como criar um pacote do aplicativo de API RESTful do Node.js e implantá-lo em um aplicativo de API no Serviço de Aplicativo do Azure."
services: app-service\api
documentationcenter: node
author: bradygaster
manager: erikre
editor: 
ms.assetid: a820e400-06af-4852-8627-12b3db4a8e70
ms.service: app-service-api
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/13/2017
ms.author: rachelap
ms.custom: mvc, devcenter
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 760434983978ff2233742ee4cef2de9762717620
ms.contentlocale: pt-br
ms.lasthandoff: 09/20/2017

---
# <a name="build-a-nodejs-restful-api-and-deploy-it-to-an-api-app-in-azure"></a>Criar uma API RESTful do Node. js e implantá-la em um aplicativo de API no Azure

Este guia de início rápido mostra como criar uma API REST escrita com Node.js [Express](http://expressjs.com/), usando uma definição [Swagger](http://swagger.io/) e implantá-la no Azure. Crie o aplicativo usando as ferramentas de linha de comando, configure os recursos com a [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) e implante o aplicativo usando o Git.  No fim, você tem uma API REST funcional de exemplo em execução no Azure.

## <a name="prerequisites"></a>Pré-requisitos

* [Git](https://git-scm.com/)
* [Node.js e NPM](https://nodejs.org/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá que você esteja executando a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

## <a name="prepare-your-environment"></a>Prepare o seu ambiente

1. Em uma janela de terminal, execute o seguinte comando para clonar o exemplo em seu computador local.

    ```bash
    git clone https://github.com/Azure-Samples/app-service-api-node-contact-list
    ```

2. Altere para o diretório que contém o código de exemplo.

    ```bash
    cd app-service-api-node-contact-list
    ```

3. Instale o [Swaggerize](https://www.npmjs.com/package/swaggerize-express) em seu computador local. Swaggerize é uma ferramenta que gera código Node.js para a API REST de uma definição de Swagger.

    ```bash
    npm install -g yo
    npm install -g generator-swaggerize
    ```

## <a name="generate-nodejs-code"></a>Como gerar código Node.js 

Esta seção do tutorial modela um fluxo de trabalho de desenvolvimento de API em que você cria primeiro os metadados do Swagger e os utiliza para gerar automaticamente o código de servidor para a API. 

Altere o diretório para a pasta *iniciar* e, em seguida, execute `yo swaggerize`. O Swaggerize cria um projeto de Node.js para sua API da definição de Swagger na *api.json*.

```bash
cd start
yo swaggerize --apiPath api.json --framework express
```

Quando Swaggerize solicita um nome de projeto, use *ContactList*.
   
   ```bash
   Swaggerize Generator
   Tell us a bit about your application
   ? What would you like to call this project: ContactList
   ? Your name: Francis Totten
   ? Your github user name: fabfrank
   ? Your email: frank@fabrikam.net
   ```
   
## <a name="customize-the-project-code"></a>Personalize o código do projeto

1. Copie a pasta *lib* na pasta *ContactList* criada pelo `yo swaggerize` e, em seguida, altere o diretório para *ContactList*.

    ```bash
    cp -r lib/ ContactList/
    cd ContactList
    ```

2. Instale os módulos NPM `jsonpath` e `swaggerize-ui`. 

    ```bash
    npm install --save jsonpath swaggerize-ui
    ```

3. Substitua o código no arquivo *handlers/contacts.js* pelo código a seguir: 
    ```javascript
    'use strict';

    var repository = require('../lib/contactRepository');

    module.exports = {
        get: function contacts_get(req, res) {
            res.json(repository.all())
        }
    };
    ```
    Esse código usa os dados JSON armazenados no *lib/contacts.json* atendido por *lib/contactRepository.js*. O novo código *contacts.js* retorna todos os contatos no repositório como um conteúdo JSON. 

4. Substitua o código no arquivo **handlers/contacts/{id}.js** pelo código a seguir:

    ```javascript
    'use strict';

    var repository = require('../../lib/contactRepository');

    module.exports = {
        get: function contacts_get(req, res) {
            res.json(repository.get(req.params['id']));
        }    
    };
    ```

    Esse código permite que você use uma variável de caminho para retornar apenas o contato com um ID especificado.

5. Substitua o código em **server.js** pelo código a seguir:

    ```javascript
    'use strict';

    var port = process.env.PORT || 8000; 

    var http = require('http');
    var express = require('express');
    var bodyParser = require('body-parser');
    var swaggerize = require('swaggerize-express');
    var swaggerUi = require('swaggerize-ui'); 
    var path = require('path');
    var fs = require("fs");
    
    fs.existsSync = fs.existsSync || require('path').existsSync;

    var app = express();

    var server = http.createServer(app);

    app.use(bodyParser.json());

    app.use(swaggerize({
        api: path.resolve('./config/swagger.json'),
        handlers: path.resolve('./handlers'),
        docspath: '/swagger' 
    }));

    // change four
    app.use('/docs', swaggerUi({
        docs: '/swagger'  
    }));

    server.listen(port, function () { 
    });
    ```   

    Este código faz pequenas alterações para viabilizar o trabalho com o Serviço de Aplicativo do Azure e expõe uma interface Web interativa para sua API.

### <a name="test-the-api-locally"></a>Como testar a API localmente

1. Inicie o aplicativo Node.js
    ```bash
    npm start
    ```
    
2. Navegue até http://localhost:8000/contacts para exibir o JSON para toda a lista de contatos.
   
   ```json
    {
        "id": 1,
        "name": "Barney Poland",
        "email": "barney@contoso.com"
    },
    {
        "id": 2,
        "name": "Lacy Barrera",
        "email": "lacy@contoso.com"
    },
    {
        "id": 3,
        "name": "Lora Riggs",
        "email": "lora@contoso.com"
    }
   ```

3. Navegue até http://localhost:8000/contacts/2 para exibir o contato com um `id` de dois.
   
    ```json
    { 
        "id": 2,
        "name": "Lacy Barrera",
        "email": "lacy@contoso.com"
    }
    ```

4. Teste a API usando a interface da Web de Swagger em http://localhost:8000/docs.
   
    ![Interface da Web de Swagger](media/app-service-web-tutorial-rest-api/swagger-ui.png)

## <a id="createapiapp"></a> Como criar um Aplicativo de API

Nesta seção, você pode usar a CLI 2.0 do Azure para criar os recursos para hospedar a API no Serviço de Aplicativo do Azure. 

1.  Faça logon na sua assinatura do Azure com o comando [az login](/cli/azure/#login) e siga as instruções na tela.

    ```azurecli-interactive
    az login
    ```

2. Se você tiver várias assinaturas do Azure, altere a assinatura padrão para a desejada.

    ````azurecli-interactive
    az account set --subscription <name or id>
    ````

3. [!INCLUDE [Create resource group](../../includes/app-service-api-create-resource-group.md)] 

4. [!INCLUDE [Create app service plan](../../includes/app-service-api-create-app-service-plan.md)]

5. [!INCLUDE [Create API app](../../includes/app-service-api-create-api-app.md)] 


## <a name="deploy-the-api-with-git"></a>Como implantar a API com o Git

Implante seu código para o aplicativo de API enviando confirmações de seu repositório Git local para o serviço de aplicativo do Azure.

1. [!INCLUDE [Configure your deployment credentials](../../includes/configure-deployment-user-no-h.md)] 

2. Inicialize um novo repositório no diretório *ContactList*. 

    ```bash
    git init .
    ```

3. Exclua o diretório *node_modules* criado pelo npm em uma etapa anterior no tutorial do Git. Crie um novo arquivo `.gitignore` no diretório atual e adicione o seguinte texto em uma nova linha, em qualquer lugar no arquivo.

    ```
    node_modules/
    ```
    Confirme se a pasta `node_modules` está sendo ignorada com `git status`.

4. Confirme as alterações no conjunto de registros.
    ```bash
    git add .
    git commit -m "initial version"
    ```

5. [!INCLUDE [Push to Azure](../../includes/app-service-api-git-push-to-azure.md)]  
 
## <a name="test-the-api--in-azure"></a>Como testar a API no Azure

1. Abra um navegador para http://app_name.azurewebsites.net/contacts. Você verá o mesmo JSON retornado como quando fez a solicitação localmente anteriormente no tutorial.

   ```json
   {
       "id": 1,
       "name": "Barney Poland",
       "email": "barney@contoso.com"
   },
   {
       "id": 2,
       "name": "Lacy Barrera",
       "email": "lacy@contoso.com"
   },
   {
       "id": 3,
       "name": "Lora Riggs",
       "email": "lora@contoso.com"
   }
   ```

2. Em um navegador, vá para o ponto de extremidade `http://app_name.azurewebsites.net/docs` para experimentar a IU do Swagger enquanto ela é executada no Azure.

    ![Swagger Ii](media/app-service-web-tutorial-rest-api/swagger-azure-ui.png)

    Agora, você pode implantar atualizações para a API de exemplo para o Azure enviando confirmações para o repositório Git do Azure.

## <a name="clean-up"></a>Limpar

Para limpar os recursos criados neste guia de início rápido, execute o seguinte comando da CLI do Azure:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-step"></a>Próxima etapa 
> [!div class="nextstepaction"]
> [Mapear um nome DNS personalizado existente para aplicativos Web do Azure](app-service-web-tutorial-custom-domain.md)


