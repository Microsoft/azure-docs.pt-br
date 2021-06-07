---
title: Criar e implantar um aplicativo Node.js Express nos serviços de nuvem do Azure (clássico)
description: Use este tutorial para criar um novo aplicativo usando o módulo Express, que fornece uma estrutura MVC para criar Node.js aplicativos Web.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: e15af589b3a3c496738c97c0c2c6429ba708ba7e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743331"
---
# <a name="build-and-deploy-a-nodejs-web-application-using-express-on-an-azure-cloud-services-classic"></a>Criar e implantar um aplicativo Web Node.js usando o Express em um serviço de nuvem do Azure (clássico)

> [!IMPORTANT]
> Os [serviços de nuvem do Azure (suporte estendido)](../cloud-services-extended-support/overview.md) são um novo modelo de implantação baseado em Azure Resource Manager para o produto de serviços de nuvem do Azure.Com essa alteração, os serviços de nuvem do Azure em execução no modelo de implantação baseado no Azure Service Manager foram renomeados como serviços de nuvem (clássicos) e todas as novas implantações devem usar os [serviços de nuvem (suporte estendido)](../cloud-services-extended-support/overview.md).

O Node.js inclui um conjunto mínimo de funcionalidades em runtime básico.
Os desenvolvedores normalmente usam módulos de terceiros para fornecer funcionalidade adicional ao desenvolver um aplicativo do Node.js. Neste tutorial, você vai criar um novo aplicativo usando o módulo [Express](https://github.com/expressjs/express), que fornece uma estrutura MVC para criar aplicativos web do Node.js.

Abaixo, uma captura de tela do aplicativo concluído:

![Um navegador da web exibindo Bem-vindo ao Express no Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="create-a-cloud-service-project"></a>Criar um projeto de Serviço de Nuvem
[!INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

Execute as seguintes etapas para criar um novo projeto de serviço de nuvem chamado 'expressapp':

1. Do **Menu Iniciar** ou na **Tela Iniciar**, procure **Windows PowerShell**. Por fim, clique com botão direito em **Windows PowerShell** e selecione **Executar Como Administrador**.

    ![Ícone PowerShell do Azure](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)
2. Altere os diretórios para o diretório **c:\\node** e digite os seguintes comandos para criar uma nova solução denominada **expressapp** e uma função web denominada **WebRole1**:

   ```powershell
   PS C:\node> New-AzureServiceProject expressapp
   PS C:\Node\expressapp> Add-AzureNodeWebRole
   PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21
   ```

   > [!NOTE]
   > Por padrão, o **Add-AzureNodeWebRole** usa uma versão mais antiga do Node.js. A instrução **Set-AzureServiceProjectRole** acima instrui o Azure usar a versão v0.10.21 do Node.  Observe que os parâmetros diferenciam maiúsculas de minúsculas.  Você pode verificar se a versão correta do Node.js foi selecionada verificando a propriedade **engines** em **WebRole1\package.json**.
>
>

## <a name="install-express"></a>Instalar o Express
1. Instale o gerador Express emitindo o comando a seguir:

    ```powershell
    PS C:\node\expressapp> npm install express-generator -g
    ```

    A saída do comando npm deve ser semelhante ao resultado abaixo.

    ![Windows PowerShell exibindo a saída do comando npm install express.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)
2. Altere os diretórios para o diretório **WebRole1** e use o comando express para gerar um novo aplicativo:

    ```powershell
    PS C:\node\expressapp\WebRole1> express
    ```

    Você será solicitado a substituir o aplicativo anterior. Digite **s** ou **sim** para continuar. O Express gerará o arquivo app.js e uma estrutura de pastas para criar seu aplicativo.

    ![A saída do comando express](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)
3. Para instalar dependências adicionais definidas no arquivo package.json, digite o seguinte comando:

    ```powershell
    PS C:\node\expressapp\WebRole1> npm install
    ```

   ![A saída do comando npm install](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)
4. Use o seguinte comando para copiar o arquivo **bin/www** no **server.js**. Isso é para que o serviço de nuvem possa encontrar o ponto de entrada para este aplicativo.

    ```powershell
    PS C:\node\expressapp\WebRole1> copy bin/www server.js
    ```

   Após este comando tiver concluído, você deve ter um arquivo **server.js** no diretório WebRole1.
5. Modifique o **server.js** para remover um dos caracteres '.' da seguinte linha.

    ```js
    var app = require('../app');
    ```

   Após fazer esta modificação, a linha deve aparecer da seguinte maneira.

    ```js
    var app = require('./app');
    ```

   Esta alteração é requerida porque movemos o arquivo (anteriormente **bin/www**,) ao mesmo diretório conforme o arquivo do aplicativo seja requerido. Após fazer de fazer essa alteração, salve o arquivo **server.js** .
6. Use o seguinte comando para executar o aplicativo no emulador do Azure:

    ```powershell
    PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch
    ```

    ![Uma página da web que contém as boas-vindas ao express.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## <a name="modifying-the-view"></a>Modificando a exibição
Agora modifique a exibição para exibir a mensagem “Bem-vindo ao Express no Azure”.

1. Digite o comando a seguir para abrir o arquivo index.jade:

    ```powershell
    PS C:\node\expressapp\WebRole1> notepad views/index.jade
    ```

   ![O conteúdo do arquivo index.jade.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)

   Jade é o mecanismo de exibição padrão usado por aplicativos do Express. Para obter mais informações sobre o mecanismo de exibição do jade, consulte [http://jade-lang.com][http://jade-lang.com] .
2. Modifique a última linha de texto acrescentando **no Azure**.

   ![No arquivo index.jade, a última linha diz: p Bem-vindo ao \#{título} no Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)
3. Salve o arquivo e saia do Bloco de Notas.
4. Atualize o navegador para ver as alterações.

   ![Uma janela do navegador, a página contém Bem-vindo ao Express no Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Depois de testar o aplicativo, use o cmdlet **Stop-AzureEmulator** para parar o emulador.

## <a name="publishing-the-application-to-azure"></a>Publicar o Aplicativo no Azure
Na janela PowerShell do Azure, use o cmdlet **Publish-AzureServiceProject** para implantar o aplicativo em um serviço de nuvem

```powershell
PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch
```

Quando a operação de implantação estiver concluída, o navegador será aberto e exibirá a página da web.

![Um navegador da web exibindo a página do Express. A URL indica que agora a página está hospedada no Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="next-steps"></a>Próximas etapas
Para saber mais, confira o [Centro de desenvolvedores do Node.js](/azure/developer/javascript/).

[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Express]: https://expressjs.com/
[http://jade-lang.com]: http://jade-lang.com