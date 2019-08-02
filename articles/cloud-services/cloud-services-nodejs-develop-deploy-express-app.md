---
title: Criar e implantar um aplicativo Node.js Express para Serviços de Nuvem do Microsoft Azure
description: Criar e implantar um aplicativo Express.js para Serviços de Nuvem do Microsoft Azure
services: cloud-services
documentationcenter: nodejs
author: georgewallace
ms.service: cloud-services
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: gwallace
ms.openlocfilehash: b3d8e364a982f85ad9df6e48dc4d28da1e8efb40
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359056"
---
# <a name="build-and-deploy-a-nodejs-web-application-using-express-on-an-azure-cloud-services"></a>Criar um aplicativo Web do Node.js usando o Express em um Serviço de Nuvem do Microsoft Azure

O Node.js inclui um conjunto mínimo de funcionalidades em tempo de execução básico.
Os desenvolvedores normalmente usam módulos de terceiros para fornecer funcionalidade adicional ao desenvolver um aplicativo do Node.js. Neste tutorial, você vai criar um novo aplicativo usando o módulo [Express](https://github.com/expressjs/express) que fornece uma estrutura MVC para criar aplicativos web do Node.js.

Abaixo, uma captura de tela do aplicativo concluído:

![Um navegador da web exibindo Bem-vindo ao Express no Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="create-a-cloud-service-project"></a>Criar um projeto de Serviço de Nuvem
[!INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

Execute as seguintes etapas para criar um novo projeto de serviço de nuvem chamado 'expressapp':

1. Do **Menu Iniciar** ou na **Tela Iniciar**, procure **Windows PowerShell**. Por fim, clique com botão direito em **Windows PowerShell** e selecione **Executar Como Administrador**.
   
    ![Ícone PowerShell do Azure](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)
2. Altere os diretórios para o diretório **c:\\node** e digite os seguintes comandos para criar uma nova solução denominada **expressapp** e uma função web denominada **WebRole1**:
   
        PS C:\node> New-AzureServiceProject expressapp
        PS C:\Node\expressapp> Add-AzureNodeWebRole
        PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21
   
    > [!NOTE]
    > Por padrão, o **Add-AzureNodeWebRole** usa uma versão mais antiga do Node.js. A instrução **Set-AzureServiceProjectRole** acima instrui o Azure usar a versão v0.10.21 do Node.  Observe que os parâmetros diferenciam maiúsculas de minúsculas.  Você pode verificar se a versão correta do Node.js foi selecionada verificando a propriedade **engines** em **WebRole1\package.json**.
    > 
    > 

## <a name="install-express"></a>Instalar o Express
1. Instale o gerador Express emitindo o comando a seguir:
   
        PS C:\node\expressapp> npm install express-generator -g
   
    A saída do comando npm deve ser semelhante ao resultado abaixo. 
   
    ![Windows PowerShell exibindo a saída do comando npm install express.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)
2. Altere os diretórios para o diretório **WebRole1** e use o comando express para gerar um novo aplicativo:
   
        PS C:\node\expressapp\WebRole1> express
   
    Você será solicitado a substituir o aplicativo anterior. Digite **s** ou **sim** para continuar. O Express gerará o arquivo app.js e uma estrutura de pastas para criar seu aplicativo.
   
    ![A saída do comando express](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)
3. Para instalar dependências adicionais definidas no arquivo package.json, digite o seguinte comando:
   
       PS C:\node\expressapp\WebRole1> npm install
   
   ![A saída do comando npm install](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)
4. Use o seguinte comando para copiar o arquivo **bin/www** no **server.js**. Isso é para que o serviço de nuvem possa encontrar o ponto de entrada para este aplicativo.
   
       PS C:\node\expressapp\WebRole1> copy bin/www server.js
   
   Após este comando tiver concluído, você deve ter um arquivo **server.js** no diretório WebRole1.
5. Modifique o **server.js** para remover um dos caracteres '.' da seguinte linha.
   
       var app = require('../app');
   
   Após fazer esta modificação, a linha deve aparecer da seguinte maneira.
   
       var app = require('./app');
   
   Esta alteração é requerida porque movemos o arquivo (anteriormente **bin/www**,) ao mesmo diretório conforme o arquivo do aplicativo seja requerido. Após fazer de fazer essa alteração, salve o arquivo **server.js** .
6. Use o seguinte comando para executar o aplicativo no emulador do Azure:
   
       PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch
   
    ![Uma página da web que contém as boas-vindas ao express.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## <a name="modifying-the-view"></a>Modificando a exibição
Agora modifique a exibição para exibir a mensagem “Bem-vindo ao Express no Azure”.

1. Digite o comando a seguir para abrir o arquivo index.jade:
   
       PS C:\node\expressapp\WebRole1> notepad views/index.jade
   
   ![O conteúdo do arquivo index.jade.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)
   
   Jade é o mecanismo de exibição padrão usado por aplicativos do Express. Para obter mais informações sobre o mecanismo de exibição do [http://jade-lang.com][http://jade-lang.com]Jade, consulte.
2. Modifique a última linha de texto acrescentando **no Azure**.
   
   ![No arquivo index.jade, a última linha diz: p Bem-vindo ao \#{título} no Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)
3. Salve o arquivo e saia do Bloco de Notas.
4. Atualize o navegador para ver as alterações.
   
   ![Uma janela do navegador, a página contém Bem-vindo ao Express no Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Depois de testar o aplicativo, use o cmdlet **Stop-AzureEmulator** para parar o emulador.

## <a name="publishing-the-application-to-azure"></a>Publicar o Aplicativo no Azure
Na janela PowerShell do Azure, use o cmdlet **Publish-AzureServiceProject** para implantar o aplicativo em um serviço de nuvem

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

Quando a operação de implantação estiver concluída, o navegador será aberto e exibirá a página da web.

![Um navegador da web exibindo a página do Express. A URL indica que agora a página está hospedada no Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="next-steps"></a>Próximas etapas
Para saber mais, confira o [Centro de desenvolvedores do Node.js](https://docs.microsoft.com/javascript/azure/?view=azure-node-latest).

[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Express]: https://expressjs.com/
[http://jade-lang.com]: http://jade-lang.com


