---
title: Tutorial – Criar uma pilha do MEAN em uma máquina virtual do Linux no Azure | Microsoft Docs
description: Neste tutorial, você aprenderá a criar uma pilha do MongoDB, Express, AngularJS e Node.js (MEAN) em uma VM Linux no Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/08/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6d870e5eedf362a6c929216735c8b5e9240aaa4f
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708491"
---
# <a name="tutorial-create-a-mongodb-express-angularjs-and-nodejs-mean-stack-on-a-linux-virtual-machine-in-azure"></a>Tutorial: Criar uma pilha do MongoDB, Express, AngularJS e Node.js (MEAN) em uma máquina virtual do Linux no Azure

Este tutorial mostra como implementar uma pilha do MongoDB, Express, AngularJS e Node.js (MEAN) em uma VM (máquina virtual) Linux no Azure. A pilha MEAN que será criada permite adição, exclusão e listagem de livros em um banco de dados. Você aprenderá como:

> [!div class="checklist"]
> * Criar uma VM do Linux
> * Instalar o Node. js
> * Instalar o MongoDB e configurar o servidor
> * Instalar rotas do Expresso e de configuração para o servidor
> * Acessar as rotas com AngularJS
> * Executar o aplicativo

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você execute a CLI do Azure versão 2.0.30 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).


## <a name="create-a-linux-vm"></a>Criar uma VM do Linux

Criar um grupo de recursos com o comando [az group create](https://docs.microsoft.com/cli/azure/group) e criar uma VM do Linux com o comando [az vm create](https://docs.microsoft.com/cli/azure/vm). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

O exemplo a seguir usa a CLI do Azure para criar um grupo de recursos chamado *myResourceGroupMEAN* no local *eastus*. Uma VM é criada com o nome *myVM* com chaves SSH, caso elas ainda não existam em um local de chave padrão. Para usar um conjunto específico de chaves, use a opção --ssh-key-value.

```azurecli-interactive
az group create --name myResourceGroupMEAN --location eastus
az vm create \
    --resource-group myResourceGroupMEAN \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password 'Azure12345678!' \
    --generate-ssh-keys
az vm open-port --port 3300 --resource-group myResourceGroupMEAN --name myVM
```

Quando a VM tiver sido criada, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir: 

```azurecli-interactive
{
  "fqdns": "",
  "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroupMEAN/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.72.77.9",
  "resourceGroup": "myResourceGroupMEAN"
}
```
Anote `publicIpAddress`. Esse endereço é usado para acessar a VM.

Use o comando a seguir para criar uma sessão SSH com a VM. Certifique-se de usar o endereço IP público correto. Em nosso exemplo acima, o endereço IP era 13.72.77.9.

```bash
ssh azureuser@13.72.77.9
```

## <a name="install-nodejs"></a>Instalar o Node. js

O [Node.js](https://nodejs.org/en/) é um tempo de execução de JavaScript baseado no mecanismo de JavaScript V8 do Chrome. O Node.js é usado neste tutorial para configurar as rotas do Expresso e os controladores AngularJS.

Na VM, usando o shell bash que você abriu com SSH, instale o Node.js.

```bash
sudo apt-get install -y nodejs
```

## <a name="install-mongodb-and-set-up-the-server"></a>Instalar o MongoDB e configurar o servidor
O [MongoDB](https://www.mongodb.com) armazena dados em documentos flexíveis, como JSON. Os campos em um banco de dados podem variar de um documento para outro e a estrutura de dados pode ser alterada ao longo do tempo. Em nosso aplicativo de exemplo, estamos adicionando registros de livros no MongoDB que contêm o nome do livro, o número ISBN, o autor e o número de páginas. 

1. Na VM, usando o shell bash que você abriu com SSH, defina a chave do MongoDB.

    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
    echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
    ```

2. Atualize o gerenciador de pacotes com a chave.
  
    ```bash
    sudo apt-get update
    ```

3. Instale o MongoDB.

    ```bash
    sudo apt-get install -y mongodb
    ```

4. Inicie o servidor.

    ```bash
    sudo service mongodb start
    ```

5. Também precisamos instalar o pacote [body-parser](https://www.npmjs.com/package/body-parser-json) para nos ajudar a processar o JSON passado nas solicitações ao servidor.

    Instale o gerenciador de pacotes npm.

    ```bash
    sudo apt-get install npm
    ```

    Instale o pacote body-parser.
    
    ```bash
    sudo npm install body-parser
    ```

6. Crie uma pasta chamada *Livros* e adicione nela um arquivo chamado *server.js*, que contém a configuração do servidor Web.

    ```javascript
    var express = require('express');
    var bodyParser = require('body-parser');
    var app = express();
    app.use(express.static(__dirname + '/public'));
    app.use(bodyParser.json());
    require('./apps/routes')(app);
    app.set('port', 3300);
    app.listen(app.get('port'), function() {
        console.log('Server up: http://localhost:' + app.get('port'));
    });
    ```

## <a name="install-express-and-set-up-routes-to-the-server"></a>Instalar rotas do Expresso e de configuração para o servidor

O [Expresso](https://expressjs.com) é uma estrutura de aplicativo Web mínima e flexível de Node.js que fornece recursos para aplicativos Web e móveis. O Expresso é usado neste tutorial para passar informações de livro para e do nosso banco de dados do MongoDB. [Mongoose](https://mongoosejs.com) fornece uma solução simples, com base em esquema para modelar seus dados de aplicativo. O Mongoose é usado neste tutorial para fornecer um esquema de livro para o banco de dados.

1. Instale o Expresso e o Mongoose.

    ```bash
    sudo npm install express mongoose
    ```

2. Na pasta *Livros*, crie uma pasta chamada *aplicativos* e adicione um arquivo chamado *routes.js* com as rotas expressas definidas.

    ```javascript
    var Book = require('./models/book');
    module.exports = function(app) {
      app.get('/book', function(req, res) {
        Book.find({}, function(err, result) {
          if ( err ) throw err;
          res.json(result);
        });
      }); 
      app.post('/book', function(req, res) {
        var book = new Book( {
          name:req.body.name,
          isbn:req.body.isbn,
          author:req.body.author,
          pages:req.body.pages
        });
        book.save(function(err, result) {
          if ( err ) throw err;
          res.json( {
            message:"Successfully added book",
            book:result
          });
        });
      });
      app.delete("/book/:isbn", function(req, res) {
        Book.findOneAndRemove(req.query, function(err, result) {
          if ( err ) throw err;
          res.json( {
            message: "Successfully deleted the book",
            book: result
          });
        });
      });
      var path = require('path');
      app.get('*', function(req, res) {
        res.sendfile(path.join(__dirname + '/public', 'index.html'));
      });
    };
    ```

3. Na pasta *aplicativos*, crie uma pasta chamada *modelos* e adicione um arquivo chamado *book.js* com a configuração de modelo de livro definida.  

    ```javascript
    var mongoose = require('mongoose');
    var dbHost = 'mongodb://localhost:27017/test';
    mongoose.connect(dbHost);
    mongoose.connection;
    mongoose.set('debug', true);
    var bookSchema = mongoose.Schema( {
      name: String,
      isbn: {type: String, index: true},
      author: String,
      pages: Number
    });
    var Book = mongoose.model('Book', bookSchema);
    module.exports = mongoose.model('Book', bookSchema); 
    ```

## <a name="access-the-routes-with-angularjs"></a>Acessar as rotas com AngularJS

O [AngularJS](https://angularjs.org) fornece uma estrutura da Web para criar exibições dinâmicas em seus aplicativos Web. Neste tutorial, usamos o AngularJS para conectar a nossa página da Web com o Expresso e realizar ações em nosso banco de dados de livro.

1. Altere o diretório de backup para *Livros* (`cd ../..`) e, em seguida, crie uma pasta chamada *pública* e adicione um arquivo chamado *script.js* com a configuração do controlador definida.

    ```javascript
    var app = angular.module('myApp', []);
    app.controller('myCtrl', function($scope, $http) {
      $http( {
        method: 'GET',
        url: '/book'
      }).then(function successCallback(response) {
        $scope.books = response.data;
      }, function errorCallback(response) {
        console.log('Error: ' + response);
      });
      $scope.del_book = function(book) {
        $http( {
          method: 'DELETE',
          url: '/book/:isbn',
          params: {'isbn': book.isbn}
        }).then(function successCallback(response) {
          console.log(response);
        }, function errorCallback(response) {
          console.log('Error: ' + response);
        });
      };
      $scope.add_book = function() {
        var body = '{ "name": "' + $scope.Name + 
        '", "isbn": "' + $scope.Isbn +
        '", "author": "' + $scope.Author + 
        '", "pages": "' + $scope.Pages + '" }';
        $http({
          method: 'POST',
          url: '/book',
          data: body
        }).then(function successCallback(response) {
          console.log(response);
        }, function errorCallback(response) {
          console.log('Error: ' + response);
        });
      };
    });
    ```
    
2. Na pasta *pública*, crie um arquivo chamado *index.html* com a página da Web definida.

    ```html
    <!doctype html>
    <html ng-app="myApp" ng-controller="myCtrl">
      <head>
        <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
        <script src="script.js"></script>
      </head>
      <body>
        <div>
          <table>
            <tr>
              <td>Name:</td> 
              <td><input type="text" ng-model="Name"></td>
            </tr>
            <tr>
              <td>Isbn:</td>
              <td><input type="text" ng-model="Isbn"></td>
            </tr>
            <tr>
              <td>Author:</td> 
              <td><input type="text" ng-model="Author"></td>
            </tr>
            <tr>
              <td>Pages:</td>
              <td><input type="number" ng-model="Pages"></td>
            </tr>
          </table>
          <button ng-click="add_book()">Add</button>
        </div>
        <hr>
        <div>
          <table>
            <tr>
              <th>Name</th>
              <th>Isbn</th>
              <th>Author</th>
              <th>Pages</th>
            </tr>
            <tr ng-repeat="book in books">
              <td><input type="button" value="Delete" data-ng-click="del_book(book)"></td>
              <td>{{book.name}}</td>
              <td>{{book.isbn}}</td>
              <td>{{book.author}}</td>
              <td>{{book.pages}}</td>
            </tr>
          </table>
        </div>
      </body>
    </html>
    ```

##  <a name="run-the-application"></a>Executar o aplicativo

1. Altere o diretório de backup para *Livros* (`cd ..`) e inicie o servidor executando este comando:

    ```bash
    nodejs server.js
    ```

2. Abra um navegador da Web no endereço que você registrou para a VM. Por exemplo, *http:\//13.72.77.9:3300*. Você verá algo semelhante à página a seguir:

    ![Registro de livro](media/tutorial-mean/meanstack-init.png)

3. Insira dados nas caixas de texto e clique em **Adicionar**. Por exemplo:

    ![Adicionar registro de livro](media/tutorial-mean/meanstack-add.png)

4. Depois de atualizar a página, você verá algo parecido com esta página:

    ![Listar registros de livro](media/tutorial-mean/meanstack-list.png)

5. Você pode clicar em **Excluir** e remover o registro de livro do banco de dados.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um aplicativo Web que controla registros de livros usando uma pilha MEAN em uma VM do Linux. Você aprendeu como:

> [!div class="checklist"]
> * Criar uma VM do Linux
> * Instalar o Node. js
> * Instalar o MongoDB e configurar o servidor
> * Instalar rotas do Expresso e de configuração para o servidor
> * Acessar as rotas com AngularJS
> * Executar o aplicativo

Vá para o próximo tutorial para saber como proteger servidores Web com certificados SSL.

> [!div class="nextstepaction"]
> [Proteger servidor Web com SSL](tutorial-secure-web-server.md)
