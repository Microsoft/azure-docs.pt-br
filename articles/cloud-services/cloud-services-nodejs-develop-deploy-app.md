---
title: Guia de introdução ao Node.js
description: Saiba como criar um aplicativo Web simples do Node.js e implantá-lo em um serviço de nuvem do Azure.
services: cloud-services
documentationcenter: nodejs
author: georgewallace
ms.service: cloud-services
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/17/2017
ms.author: gwallace
ms.openlocfilehash: 81ad3bce6c7e331fd67fba60b35de6b8a37c5da9
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359537"
---
# <a name="build-and-deploy-a-nodejs-application-to-an-azure-cloud-service"></a>Criar e implantar um aplicativo Node.js para um Serviço de Nuvem do Azure

Este tutorial mostra como criar um aplicativo simples do Node.js em execução em um Serviço de Nuvem do Azure. Os Serviços de Nuvem são os blocos de construção de aplicativos de nuvem escalonáveis no Azure. Eles permitem a separação e o gerenciamento independente e o dimensionamento dos componentes de front-end e back-end de seu aplicativo.  Os serviços de nuvem fornecem uma máquina virtual exclusiva robusta para hospedar cada função confiável.

Para saber mais sobre serviços de nuvem e como eles são comparados aos Sites do Azure e máquinas virtuais, consulte [Comparação de Sites do Azure, Serviços de Nuvem e Máquinas virtuais].

> [!TIP]
> Procurando desenvolver um site simples? Se o seu cenário envolve apenas um site de front-end simples, considere [usar um aplicativo Web leve]. Você pode atualizar facilmente para um serviço de nuvem conforme o aplicativo Web cresce e suas necessidades mudam.

Seguindo este tutorial, você irá criar um aplicativo da Web simples hospedado dentro de uma função Web. Você usará o emulador de computação para testar o aplicativo localmente e, em seguida, o implantará usando ferramentas de linha de comando do PowerShell.

O aplicativo é um aplicativo simples "hello world":

![Um navegador exibindo a página da Web Hello World][A web browser displaying the Hello World web page]

## <a name="prerequisites"></a>Pré-requisitos
> [!NOTE]
> Este tutorial usa o PowerShell do Azure, que requer o Windows.

* Instalar e configurar o [Powershell do Azure].
* Baixe e instale o [SDK do Azure para .NET 2.7]. Na configuração da instalação, selecione:
  * MicrosoftAzureAuthoringTools
  * MicrosoftAzureComputeEmulator

## <a name="create-an-azure-cloud-service-project"></a>Criar um projeto de Serviço de Nuvem do Azure
Execute as tarefas a seguir para criar um novo projeto do Serviço de Nuvem do Azure, juntamente com a estrutura básica do Node.js:

1. Execute o **Windows PowerShell** como administrador. No **Menu Iniciar** ou na tela **Início**, procure o **Windows PowerShell**.
2. [Conecte o PowerShell] à sua assinatura.
3. Insira o seguinte cmdlet do PowerShell para criar o projeto:

        New-AzureServiceProject helloworld

    ![O resultado do comando New-AzureService helloworld][The result of the New-AzureService helloworld command]

    O cmdlet **New-AzureServiceProject** gera uma estrutura básica para publicar um aplicativo do Node.js para um Serviço de Nuvem. Ele contém arquivos de configuração necessários para publicar no Azure. O cmdlet também altera o diretório de trabalho para o diretório de serviço.

    O cmdlet cria os seguintes arquivos:

   * **ServiceConfiguration.Cloud.cscfg**, **ServiceConfiguration.Local.cscfg** e **ServiceDefinition.csdef**: Arquivos específicos do Azure necessários para publicar seu aplicativo. Para saber mais, consulte [Visão geral da criação de um serviço hospedado para o Azure].
   * **deploymentSettings.json**: armazena configurações locais que são usadas pelos cmdlets de implantação do Azure PowerShell.
4. Digite o seguinte comando para adicionar uma nova função da Web:

       Add-AzureNodeWebRole

   ![A saída do comando Add-AzureNodeWebRole.][The output of the Add-AzureNodeWebRole command]

   O cmdlet **Add-AzureNodeWebRole** cria um aplicativo básico do Node.js. Ele também modifica os arquivos **.csfg** e **.csdef** para adicionar entradas de configuração para a nova função.

   > [!NOTE]
   > Se você não especificar um nome de função, um nome padrão será usado. Você pode fornecer um nome como o primeiro parâmetro do cmdlet: `Add-AzureNodeWebRole MyRole`

O aplicativo do Node.js é definido no arquivo **server.js**, localizado no diretório da função Web (**WebRole1**, por padrão). Eis o código:

    var http = require('http');
    var port = process.env.port || 1337;
    http.createServer(function (req, res) {
        res.writeHead(200, { 'Content-Type': 'text/plain' });
        res.end('Hello World\n');
    }).listen(port);

Esse código é basicamente o mesmo código do exemplo "Hello World" no site [nodejs.org] , com a exceção de que ele usa o número de porta atribuído pelo ambiente de nuvem.

## <a name="deploy-the-application-to-azure"></a>Implantar o aplicativo no Azure

> [!NOTE]
> Para concluir este tutorial, você precisa de uma conta do Azure. Você pode [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou [se inscrever para fazer uma conta gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A85619ABF).

### <a name="download-the-azure-publishing-settings"></a>Baixar as configurações de publicação do Azure
Para implantar seu aplicativo do Azure, você deve primeiro baixar as definições de publicação para sua assinatura do Azure.

1. Execute o seguinte cmdlet do PowerShell do Azure:

       Get-AzurePublishSettingsFile

   Isso irá usar o navegador para navegar para a página de download de configurações de publicação. Você precisará fazer logon com uma conta da Microsoft. Se fizer, use a conta associada com sua assinatura do Azure.

   Salve o perfil baixado para um local de arquivo, que você pode acessar facilmente.
2. Execute o seguinte cmdlet para importar o perfil de publicação que você baixou:

       Import-AzurePublishSettingsFile [path to file]

    > [!NOTE]
    > Depois de importar as configurações de publicação, considere a exclusão do arquivo .publishsettings baixado, pois ele contém informações que podem permitir que alguém acesse sua conta.

### <a name="publish-the-application"></a>Publicar o aplicativo
Para publicar, execute os seguintes comandos:

      $ServiceName = "NodeHelloWorld" + $(Get-Date -Format ('ddhhmm'))
    Publish-AzureServiceProject -ServiceName $ServiceName  -Location "East US" -Launch

* **-ServiceName** especifica o nome para a implantação. Esse deve ser um nome exclusivo, caso contrário, o processo de publicação falhará. O comando **Get-Date** usa uma cadeia de caracteres de data/hora que deve tornar o nome exclusivo.
* **-Location** especifica o datacenter no qual o aplicativo será hospedado. Para ver uma lista dos centros de dados disponíveis, use o cmdlet **Get-AzureLocation** .
* **-Launch** abre uma janela do navegador e navega para o serviço hospedado após a conclusão da implantação.

Após a publicação for bem-sucedida, você verá uma resposta semelhante à seguinte:

![A saída do comando Publish-AzureService][The output of the Publish-AzureService command]

> [!NOTE]
> Pode levar alguns minutos para o aplicativo ser implantado e tornar-se disponível quando for publicado pela primeira vez.

Depois que a implantação for concluída, uma janela do navegador será aberta e irá navegar para o serviço de nuvem.

![Uma janela do navegador exibindo a página hello world; a URL indica que a página está hospedada no Azure.][A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.]

Seu aplicativo está em execução no Azure.

O cmdlet **Publish-AzureServiceProject** executa as seguintes etapas:

1. Cria um pacote a ser implantado. O pacote contém todos os arquivos em sua pasta de aplicativos.
2. Cria uma nova **conta de armazenamento** se não existir. A conta de armazenamento do Azure é usada para armazenar o pacote de aplicativos durante a implantação. Você pode excluir com segurança a conta de armazenamento após a conclusão da implantação.
3. Cria um novo **serviço de nuvem** se não existir. Um **serviço de nuvem** é o contêiner no qual seu aplicativo é hospedado quando é implantado no Azure. Para saber mais, consulte [Visão geral da criação de um serviço hospedado para o Azure].
4. Publica o pacote de implantação do Azure.

## <a name="stopping-and-deleting-your-application"></a>Parando e excluindo seu aplicativo
Depois de implantar seu aplicativo, convém desativá-lo para que você possa evitar custos extras. O Azure cobra as instâncias de função web por hora de acordo com o tempo consumido do servidor. O tempo do servidor é consumido quando seu aplicativo é implantado, mesmo se as instâncias não estiverem sendo executadas e estiverem no estado parado.

1. Na janela do Windows PowerShell, interrompa a implantação do serviço criada na seção anterior com o seguinte cmdlet:

       Stop-AzureService

   Interromper o serviço pode levar alguns minutos. Quando o serviço for interrompido, você recebe uma mensagem indicando que foi interrompido.

   ![O status do comando Stop-AzureService][The status of the Stop-AzureService command]
2. Para excluir o serviço, chame o seguinte cmdlet:

       Remove-AzureService

   Quando solicitado, insira **Y** para excluir o serviço.

   Excluir o serviço pode levar alguns minutos. Após o serviço ter sido excluído, você recebe uma mensagem indicando que o serviço foi excluído.

   ![O status do comando Remove-AzureService][The status of the Remove-AzureService command]

   > [!NOTE]
   > Excluir o serviço não exclui a conta de armazenamento criada quando o serviço foi inicialmente publicado e você continuará a ser cobrado pelo armazenamento usado. Se ninguém mais está usando o repositório, convém excluí-lo.

## <a name="next-steps"></a>Próximas etapas
Para saber mais, confira o [Centro de desenvolvedores do Node. js].

<!-- URL List -->

[Comparação de Sites do Azure, Serviços de Nuvem e Máquinas virtuais]: /azure/architecture/guide/technology-choices/compute-decision-tree
[usar um aplicativo Web leve]: ../app-service/app-service-web-get-started-nodejs.md
[Powershell do Azure]: /powershell/azureps-cmdlets-docs
[SDK do Azure para .NET 2.7]: https://www.microsoft.com/en-us/download/details.aspx?id=48178
[Conecte o PowerShell]: /powershell/azureps-cmdlets-docs
[nodejs.org]: https://nodejs.org/
[Visão geral da criação de um serviço hospedado para o Azure]: https://azure.microsoft.com/documentation/services/cloud-services/
[Centro de desenvolvedores do Node. js]: https://azure.microsoft.com/develop/nodejs/

<!-- IMG List -->

[The result of the New-AzureService helloworld command]: ./media/cloud-services-nodejs-develop-deploy-app/node9.png
[The output of the Add-AzureNodeWebRole command]: ./media/cloud-services-nodejs-develop-deploy-app/node11.png
[A web browser displaying the Hello World web page]: ./media/cloud-services-nodejs-develop-deploy-app/node14.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node19.png
[A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.]: ./media/cloud-services-nodejs-develop-deploy-app/node21.png
[The status of the Stop-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node48.png
[The status of the Remove-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node49.png
