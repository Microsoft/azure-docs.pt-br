---
title: Implantação por push do zip para o Azure Functions
description: Use os recursos de implantação de arquivo .zip do serviço de implantação do Kudu para publicar seu Azure Functions.
ms.topic: conceptual
ms.date: 08/12/2018
ms.openlocfilehash: e104661dcdf1f6c6fd6dd5eb1024748980e7931f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018405"
---
# <a name="zip-deployment-for-azure-functions"></a>Implantação de zip para o Azure Functions

Este artigo descreve como implantar seus arquivos de projeto de aplicativo de função para o Azure de um arquivo .zip (compactado). Você aprenderá a realizar uma implantação de envio por push, usando a CLI do Azure e usando as APIs REST. As [Ferramentas básicas do Azure Functions](functions-run-local.md) também usam essas APIs de implantação ao publicar um projeto local no Azure.

O Azure Functions tem a gama completa de opções de implantação e integração contínuas que são fornecidas pelo Serviço de Aplicativo do Azure. Para saber mais, confira [Implantação contínua do Azure Functions](functions-continuous-deployment.md).

Para acelerar o desenvolvimento, você pode achar mais fácil implantar seus arquivos de projeto de aplicativo de funções diretamente de um arquivo. zip. A API de implantação do .zip extrai o conteúdo de um arquivo .zip para a pasta `wwwroot` do seu aplicativo de funções. Essa implantação de arquivo .zip usa o mesmo serviço Kudu que alimenta implementações baseadas em integração contínua, incluindo:

+ Exclusão de arquivos que foram mantidos de implantações anteriores.
+ Personalização da implantação, incluindo execução de scripts de implantação.
+ Logs de implantação.
+ Gatilhos de função de sincronização em um aplicativo de funções de [Plano de consumo](functions-scale.md).

Para obter mais informações, consulte a [referência de implantação do .zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

## <a name="deployment-zip-file-requirements"></a>Requisitos de implantação de arquivo .zip

O arquivo .zip que você usa para a implantação de push precisa conter todos os arquivos necessários para executar sua função.

>[!IMPORTANT]
> Quando você usa a implantação de .zip, todos os arquivos de uma implantação existente que não são encontrados no arquivo .zip são excluídos do seu aplicativo de funções.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Um aplicativo de funções contém todos os arquivos e pastas no diretório `wwwroot`. Uma implantação de arquivo. zip inclui o conteúdo do diretório `wwwroot`, mas não o diretório em si. Ao implantar um projeto de biblioteca de classes C#, você deve incluir os arquivos de biblioteca compilados e as dependências em uma subpasta `bin` em seu pacote .zip.

## <a name="download-your-function-app-files"></a>Baixe seus arquivos de aplicativo de funções

Quando você estiver desenvolvendo em um computador local, é fácil criar um arquivo .zip da pasta do projeto de aplicativo de funções no computador de desenvolvimento.

No entanto, você pode ter criado as suas funções usando o editor no Portal do Azure. Você pode baixar um projeto de aplicativo de funções existente de uma das seguintes maneiras:

+ **No portal do Azure:**

  1. Conecte-se no [Portal do Azure](https://portal.azure.com) e, em seguida, vá para o seu aplicativo de funções.

  2. Na guia **Visão Geral**, selecione **Baixar conteúdo do aplicativo**. Selecione as opções de download e, em seguida, selecione **Baixar**.

      ![Baixe o projeto de aplicativo de funções](./media/deployment-zip-push/download-project.png)

     O arquivo .zip baixado está no formato correto para ser republicado no seu aplicativo de funções usando a implantação por push do .zip. O download do portal também pode adicionar os arquivos necessários para abrir o aplicativo de funções diretamente no Visual Studio.

+ **Usando APIs REST:**

    Use a seguinte API GET de implantação para baixar os arquivos de seu projeto `<function_app>`: 

    ```http
    https://<function_app>.scm.azurewebsites.net/api/zip/site/wwwroot/
    ```

    Incluir `/site/wwwroot/` assegura que o arquivo zip inclua somente os arquivos de projeto do aplicativo de funções e não todo o site. Se ainda não tiver se conectado ao Azure, será solicitado que você faça isso.  

Você também pode baixar um arquivo .zip de um repositório do GitHub. Quando você baixa um repositório GitHub como um arquivo .zip, o GitHub adiciona um nível extra de pasta para o branch. Esse nível extra de pasta significa que você não pode implantar o arquivo .zip diretamente como baixou do GitHub. Se você estiver usando um repositório GitHub para manter seu aplicativo de funções, você deve usar a [integração contínua](functions-continuous-deployment.md) para implantar seu aplicativo.  

## <a name="deploy-by-using-azure-cli"></a><a name="cli"></a>Implantar usando a CLI do Azure

Você pode usar a CLI do Azure para disparar uma implantação de envio por push. Implante o arquivo .zip em seu aplicativo de funções usando o comando [az functionapp deployment source config-zip](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config-zip). Para usar esse comando, você deve usar a CLI do Azure versão 2.0.21 ou posterior. Para ver qual versão do CLI do Azure que você está usando, use o comando `az --version`.

No comando a seguir, substitua o espaço reservado `<zip_file_path>` pelo caminho para o local do seu arquivo .zip. Além disso, substitua `<app_name>` pelo nome exclusivo do seu aplicativo de funções e substitua `<resource_group>` pelo nome do seu grupo de recursos.

```azurecli-interactive
az functionapp deployment source config-zip -g <resource_group> -n \
<app_name> --src <zip_file_path>
```

Esse comando implanta os arquivos de projeto do arquivo .zip baixado para seu aplicativo de funções no Azure. Em seguida, ele reinicia o aplicativo. Para visualizar a lista de implantações para o aplicativo, é necessário usar as APIs REST.

Quando você estiver usando a CLI do Azure em seu computador local, `<zip_file_path>` é o caminho para o arquivo .zip em seu computador. Você também pode executar a CLI do Azure no [Azure Cloud Shell](../cloud-shell/overview.md). Quando você usa o Cloud Shell, você deve primeiro carregar seu arquivo .zip de implantação para a conta do Azure Files associada com o seu Cloud Shell. Nesse caso, `<zip_file_path>` é o local de armazenamento que a sua conta do Cloud Shell usa. Para obter mais informações, consulte [Persistir arquivos no Azure Cloud Shell](../cloud-shell/persisting-shell-storage.md).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

## <a name="run-functions-from-the-deployment-package"></a>Executar funções do pacote de implantação

Você também pode optar por executar suas funções diretamente do arquivo do pacote de implantação. Este método ignora a etapa de implantação de copiar os arquivos do pacote para o diretório `wwwroot` de seu aplicativo de funções. Em vez disso, o arquivo de pacote é montado pelo runtime do Azure Functions e o conteúdo do diretório `wwwroot` se torna somente leitura.  

A implantação de zip integra-se com esse recurso, que você pode habilitar definindo a configuração do aplicativo de funções `WEBSITE_RUN_FROM_PACKAGE` com um valor de `1`. Para obter mais informações, veja [Executar suas funções de um arquivo de pacote de implantação](run-functions-from-deployment-package.md).

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Implantação contínua para Azure Functions](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
