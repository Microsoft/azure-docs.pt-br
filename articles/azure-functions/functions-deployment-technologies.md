---
title: Tecnologias de implantação no Azure Functions | Microsoft Docs
description: Conheça as diferentes maneiras como você pode implantar código para Azure Functions.
services: functions
documentationcenter: .net
author: ColbyTresness
manager: dariac
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: cotresne
ms.openlocfilehash: 7f931a72eab534bc2856e9e545b684d2b8ae7a60
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444035"
---
# <a name="deployment-technologies-in-azure-functions"></a>Tecnologias de implantação no Azure Functions

Você pode usar algumas tecnologias diferentes para implantar o código do projeto Azure Functions no Azure. Este artigo fornece uma lista completa dessas tecnologias, descreve quais tecnologias estão disponíveis para quais tipos de funções, explica o que acontece quando você usa cada método e fornece recomendações para o melhor método a ser usado em vários cenários . As várias ferramentas que dão suporte à implantação em Azure Functions são ajustadas para a tecnologia certa com base em seu contexto.

## <a name="deployment-technology-availability"></a>Disponibilidade de tecnologia de implantação

O Azure Functions dá suporte ao desenvolvimento local de plataforma cruzada e à hospedagem no Windows e no Linux. Atualmente, três planos de hospedagem estão disponíveis:

+ [Utilização](functions-scale.md#consumption-plan)
+ [Premium](functions-scale.md#premium-plan)
+ [Dedicado (serviço de aplicativo)](functions-scale.md#app-service-plan)

Cada plano tem comportamentos diferentes. Nem todas as tecnologias de implantação estão disponíveis para cada tipo de Azure Functions. O gráfico a seguir mostra quais tecnologias de implantação têm suporte para cada combinação de sistema operacional e plano de hospedagem:

| Tecnologia de implantação | Consumo do Windows | Windows Premium (versão prévia) | Windows dedicado  | Consumo do Linux (versão prévia) | Linux dedicado |
|-----------------------|:-------------------:|:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| URL do pacote externo<sup>1</sup> |✔|✔|✔|✔|✔|
| Implantação de zip |✔|✔|✔| |✔|
| Recipiente de docker | | | | |✔|
| Implantação da Web |✔|✔|✔| | |
| Controle do código-fonte |✔|✔|✔| |✔|
| Git local<sup>1</sup> |✔|✔|✔| |✔|
| Sincronização de nuvem<sup>1</sup> |✔|✔|✔| |✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|
| Edição do portal |✔|✔|✔| |✔<sup>2</sup>|

<sup>1</sup> tecnologia de implantação que requer [sincronização de gatilho manual](#trigger-syncing).  
<sup>2</sup> a edição do portal é habilitada apenas para gatilhos http e de temporizador para funções no Linux usando planos Premium e dedicados.

## <a name="key-concepts"></a>Principais conceitos

Alguns conceitos importantes são essenciais para entender como as implantações funcionam em Azure Functions.

### <a name="trigger-syncing"></a>Disparar sincronização

Quando você altera qualquer um de seus gatilhos, a infraestrutura do Functions deve estar ciente das alterações. A sincronização ocorre automaticamente para muitas tecnologias de implantação. No entanto, em alguns casos, você deve sincronizar manualmente seus gatilhos. Ao implantar suas atualizações referenciando uma URL de pacote externo, git local, sincronização de nuvem ou FTP, você deve sincronizar manualmente seus gatilhos. Você pode sincronizar gatilhos de uma destas três maneiras:

* Reinicie seu aplicativo de funções no portal do Azure
* Envie uma solicitação HTTP post para `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` usando a [chave mestra](functions-bindings-http-webhook.md#authorization-keys).
* Envie uma solicitação HTTP POST para `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`. Substitua os espaços reservados por sua ID de assinatura, nome do grupo de recursos e o nome do seu aplicativo de funções.

## <a name="deployment-technology-details"></a>Detalhes da tecnologia de implantação 

Os métodos de implantação a seguir estão disponíveis no Azure Functions.

### <a name="external-package-url"></a>URL do pacote externo

Você pode usar uma URL de pacote externo para fazer referência a um arquivo de pacote remoto (. zip) que contém seu aplicativo de funções. O arquivo é baixado da URL fornecida e o aplicativo é executado em execução no modo [de pacote](run-functions-from-deployment-package.md) .

>__Como usá-lo:__ Adicione `WEBSITE_RUN_FROM_PACKAGE` às configurações do aplicativo. O valor dessa configuração deve ser uma URL (o local do arquivo de pacote específico que você deseja executar). Você pode adicionar configurações [no portal](functions-how-to-use-azure-function-app-settings.md#settings) ou [usando o CLI do Azure](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set). 
>
>Se você usar o armazenamento de BLOBs do Azure, use um contêiner privado com uma [assinatura de acesso compartilhado (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) para dar acesso às funções ao pacote. Sempre que o aplicativo for reiniciado, ele buscará uma cópia do conteúdo. Sua referência deve ser válida durante o tempo de vida do aplicativo.

>__Quando usá-lo:__ A URL do pacote externo é o único método de implantação com suporte para Azure Functions em execução no Linux no plano de consumo (versão prévia). Ao atualizar o arquivo de pacote que um aplicativo de funções referencia, você deve [sincronizar os gatilhos manualmente](#trigger-syncing) para informar ao Azure que seu aplicativo foi alterado.

### <a name="zip-deploy"></a>Implantação de zip

Use a implantação de zip para enviar por push um arquivo. zip que contém seu aplicativo de funções para o Azure. Opcionalmente, você pode definir seu aplicativo para iniciar em [executar a partir do modo de pacote](run-functions-from-deployment-package.md) .

>__Como usá-lo:__ Implante usando sua ferramenta de cliente favorita: [Vs Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure), [Visual Studio](functions-develop-vs.md#publish-to-azure)ou o [CLI do Azure](functions-create-first-azure-function-azure-cli.md#deploy-the-function-app-project-to-azure). Para implantar manualmente um arquivo. zip em seu aplicativo de funções, siga as instruções em [implantar de um arquivo. zip ou de uma URL](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).
>
>Ao implantar usando a implantação de zip, você pode definir seu aplicativo para ser executado em execução no modo [de pacote](run-functions-from-deployment-package.md) . Para definir executar do modo de pacote, defina `WEBSITE_RUN_FROM_PACKAGE` o valor de configuração `1`de aplicativo como. Recomendamos a implantação de zip. Ele produz tempos de carregamento mais rápidos para seus aplicativos e é o padrão para VS Code, o Visual Studio e o CLI do Azure.

>__Quando usá-lo:__ A implantação de zip é a tecnologia de implantação recomendada para funções em execução no Windows e no Linux no plano Premium ou dedicado.

### <a name="docker-container"></a>Recipiente de docker

Você pode implantar uma imagem de contêiner do Linux que contém seu aplicativo de funções.

>__Como usá-lo:__ Crie um aplicativo de funções do Linux no plano Premium ou dedicado e especifique a imagem de contêiner a ser executada. É possível fazer isso de duas formas:
>
>* Crie um aplicativo de funções do Linux em um plano de serviço Azure App no portal do Azure. Para **publicar**, selecione **imagem**do Docker e configure o contêiner. Insira o local onde a imagem está hospedada.
>* Crie um aplicativo de funções do Linux em um plano do serviço de aplicativo usando o CLI do Azure. Para saber como, consulte [criar uma função no Linux usando uma imagem personalizada](functions-create-function-linux-custom-image.md#create-and-deploy-the-custom-image).
>
>Para implantar em um aplicativo existente usando um contêiner personalizado, em [Azure Functions Core Tools](functions-run-local.md), use o [`func deploy`](functions-run-local.md#publish) comando.

>__Quando usá-lo:__ Use a opção de contêiner do Docker quando precisar de mais controle sobre o ambiente do Linux em que seu aplicativo de funções é executado. Esse mecanismo de implantação está disponível somente para funções em execução no Linux em um plano do serviço de aplicativo.

### <a name="web-deploy-msdeploy"></a>Implantação da Web (MSDeploy)

Implantação da Web pacotes e implanta seus aplicativos do Windows em qualquer servidor IIS, incluindo seus aplicativos de função em execução no Windows no Azure.

>__Como usá-lo:__ Use as [Ferramentas do Visual Studio para Azure Functions](functions-create-your-first-function-visual-studio.md). Desmarque a caixa **de seleção Executar do arquivo de pacote (recomendado)** .
>
>Você também pode baixar [implantação da Web 3,6](https://www.iis.net/downloads/microsoft/web-deploy) e chamar `MSDeploy.exe` diretamente.

>__Quando usá-lo:__ Implantação da Web tem suporte e não tem problemas, mas o mecanismo preferencial é a [implantação de zip com a execução do pacote habilitado](#zip-deploy). Para saber mais, consulte o [Guia de desenvolvimento do Visual Studio](functions-develop-vs.md#publish-to-azure).

### <a name="source-control"></a>Controle do código-fonte

Use o controle do código-fonte para conectar seu aplicativo de funções a um repositório git. Uma atualização de código nesse repositório dispara a implantação. Para obter mais informações, consulte o [wiki do kudu](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments).

>__Como usá-lo:__ Use a central de implantação na área funções do portal para configurar a publicação do controle do código-fonte. Para saber mais, confira [Implantação contínua do Azure Functions](functions-continuous-deployment.md).

>__Quando usá-lo:__ Usar o controle do código-fonte é a melhor prática para as equipes que colaboram com seus aplicativos de funções. O controle do código-fonte é uma boa opção de implantação que permite pipelines de implantação mais sofisticados.

### <a name="local-git"></a>Git local

Você pode usar o Git local para enviar código por push do computador local para Azure Functions usando o git.

>__Como usá-lo:__ Siga as instruções em [implantação do git local para Azure app serviço](../app-service/deploy-local-git.md).

>__Quando usá-lo:__ Em geral, recomendamos que você use um método de implantação diferente. Ao publicar do git local, você deve [sincronizar os gatilhos manualmente](#trigger-syncing).

### <a name="cloud-sync"></a>Sincronização de nuvem

Use a sincronização de nuvem para sincronizar seu conteúdo do Dropbox e do OneDrive para Azure Functions.

>__Como usá-lo:__ Siga as instruções em [sincronizar conteúdo de uma pasta de nuvem](../app-service/deploy-content-sync.md).

>__Quando usá-lo:__ Em geral, recomendamos outros métodos de implantação. Ao publicar usando a sincronização de nuvem, você deve [sincronizar os gatilhos manualmente](#trigger-syncing).

### <a name="ftp"></a>FTP

Você pode usar o FTP para transferir arquivos diretamente para o Azure Functions.

>__Como usá-lo:__ Siga as instruções em [implantar conteúdo usando FTP/s](../app-service/deploy-ftp.md).

>__Quando usá-lo:__ Em geral, recomendamos outros métodos de implantação. Ao publicar usando FTP, você deve sincronizar os [gatilhos manualmente](#trigger-syncing).

### <a name="portal-editing"></a>Edição do portal

No editor baseado em portal, você pode editar diretamente os arquivos que estão em seu aplicativo de funções (essencialmente implantando sempre que você salvar suas alterações).

>__Como usá-lo:__ Para poder editar suas funções no portal do Azure, você deve ter [criado suas funções no portal](functions-create-first-azure-function.md). Para preservar uma única fonte de verdade, usar qualquer outro método de implantação torna sua função somente leitura e impede a edição continuada do Portal. Para retornar a um estado no qual você pode editar os arquivos na portal do Azure, você pode ativar manualmente o modo de edição para `Read/Write` e remover quaisquer configurações de aplicativo relacionadas à implantação (como `WEBSITE_RUN_FROM_PACKAGE`). 

>__Quando usá-lo:__ O portal é uma boa maneira de começar a usar o Azure Functions. Para um trabalho de desenvolvimento mais intenso, recomendamos que você use uma das seguintes ferramentas de cliente:
>
>* [Visual Studio Code](functions-create-first-function-vs-code.md)
>* [Azure Functions Core Tools (linha de comando)](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

A tabela a seguir mostra os sistemas operacionais e idiomas que dão suporte à edição do portal:

| | Consumo do Windows | Windows Premium (versão prévia) | Windows dedicado | Consumo do Linux (versão prévia) | Linux Premium (versão prévia)| Linux dedicado |
|-|:-----------------: |:-------------------------:|:-----------------:|:---------------------------:|:---------------:|:---------------:|
| C# | | | | | |
| Script do C# |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python (Versão Prévia) | | | | | | |
| PowerShell (visualização) |✔|✔|✔| | | |
| TypeScript (Node.js) | | | | | | |

<sup>*</sup>A edição do portal é habilitada apenas para gatilhos HTTP e de temporizador para funções no Linux usando planos Premium e dedicados.

## <a name="deployment-slots"></a>Slots de implantação

Ao implantar seu aplicativo de funções no Azure, você pode implantá-lo em um slot de implantação separado em vez de implantá-lo diretamente na produção. Para obter mais informações sobre slots de implantação, consulte [Azure app slots de serviço](../app-service/deploy-staging-slots.md).

### <a name="deployment-slots-levels-of-support"></a>Níveis de suporte dos slots de implantação

Há dois níveis de suporte para slots de implantação:

* **Disponibilidade geral (GA)** : Suporte completo e aprovado para uso em produção.
* **Versão Prévia**: Ainda não tem suporte, mas é esperado alcançar o status de GA no futuro.

| Sistema operacional/plano de hospedagem | Nível de suporte |
| --------------- | ------ |
| Consumo do Windows | Visualizar |
| Windows Premium (versão prévia) | Visualizar |
| Windows dedicado | Disponibilidade Geral |
| Consumo do Linux | Sem suporte |
| Linux Premium (versão prévia) | Visualizar |
| Linux dedicado | Disponibilidade Geral |

## <a name="next-steps"></a>Próximas etapas

Leia estes artigos para saber mais sobre como implantar seus aplicativos de funções: 

+ [Implantação contínua para Azure Functions](functions-continuous-deployment.md)
+ [Entrega contínua usando o Azure DevOps](functions-how-to-azure-devops.md)
+ [Implantações zip para Azure Functions](deployment-zip-push.md)
+ [Executar o Azure Functions de um arquivo de pacote](run-functions-from-deployment-package.md)
+ [Automatizar a implantação de recursos para seu aplicativo de funções no Azure Functions](functions-infrastructure-as-code.md)
