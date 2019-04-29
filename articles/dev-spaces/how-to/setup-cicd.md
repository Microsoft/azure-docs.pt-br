---
title: Usar CI/CD com o Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: conceptual
manager: yuvalm
description: Desenvolvimento rápido de Kubernetes com contêineres e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Serviço de Contêiner do Azure, contêineres
ms.openlocfilehash: 983af0dd75e6ae62630c85d04ac3819c7e260439
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60687241"
---
# <a name="use-cicd-with-azure-dev-spaces"></a>Usar CI/CD com o Azure Dev Spaces

Este artigo orienta você pela configuração da integração contínua/implantação contínua (CI/CD) ao Serviço de Kubernetes do Azure (AKS) com o Dev Spaces habilitado. A CI/CD para o AKS permite que as atualizações de aplicativo sejam implantadas automaticamente sempre que o código confirmado é enviado para seu repositório de origem. Usar a CI/CD em conjunto com um cluster habilitado para o Dev Spaces é útil porque pode manter uma linha de base do aplicativo atualizada para o trabalho da equipe.

![Exemplo de diagrama CI/CD](../media/common/ci-cd-simple.png)

Embora este artigo oriente você com o Azure DevOps, os mesmos conceitos se aplicariam aos sistemas de CI/CD, como Jenkins, TeamCity, etc.

## <a name="prerequisites"></a>Pré-requisitos
* [Cluster do Serviço de Kubernetes do Azure (AKS) com o Azure Dev Spaces habilitado](../get-started-netcore.md)
* [CLI do Azure Dev Spaces instalado](upgrade-tools.md)
* [Organização do Azure DevOps com um projeto](https://docs.microsoft.com/azure/devops/user-guide/sign-up-invite-teammates?view=vsts)
* [Registro de Contêiner do Azure (ACR)](../../container-registry/container-registry-get-started-azure-cli.md)
    * Detalhes disponíveis da [conta do administrador](../../container-registry/container-registry-authentication.md#admin-account) do Registro de Contêiner do Azure
* [Autorizar o cluster do AKS para efetuar pull de seu Registro de Contêiner do Azure](../../container-registry/container-registry-auth-aks.md)

## <a name="download-sample-code"></a>Baixar código de exemplo
Por uma questão de tempo, vamos criar uma bifurcação do nosso repositório GitHub de código de amostra. Acesse https://github.com/Azure/dev-spaces e selecione **Bifurcação**. Quando o processo de bifurcação estiver completo, **Clone** sua versão com bifurcação do repositório localmente. Por padrão o branch _mestre_ será verificado, mas incluímos algumas alterações de economia de tempo no branch _azds_updates_, que também deve ter sido transferido durante o processo de bifurcação. O branch _azds_updates_ contém atualizações que pedimos que você faça manualmente nas seções do tutorial do Dev Spaces, bem como alguns arquivos YAML e JSON pré-criados para simplificar a implantação do sistema CI/CD. Você pode usar um comando como `git checkout -b azds_updates origin/azds_updates` para verificar o branch _azds_updates_ em seu repositório local.

## <a name="dev-spaces-setup"></a>Configuração do Dev Spaces
Crie um novo espaço chamado _dev_ usando o comando `azds space select`. O espaço _dev_ será usado por seu pipeline de CI/CD para enviar suas alterações de código. Ele também será usado para criar _espaços filhos_ baseados no _dev_.

```cmd
azds space select -n dev
```

Quando solicitado a selecionar um espaço de desenvolvimento pai, selecione _\<none\>_.

Depois que seu espaço de desenvolvimento tiver sido criado, você precisa determinar o sufixo de host. Use o `azds show-context` comando para mostrar o sufixo de host do controlador de ingresso do Azure Dev espaços.

```cmd
$ azds show-context
Name   ResourceGroup    DevSpace  HostSuffix
-----  ---------------  --------  ----------------------
MyAKS  MyResourceGroup  dev       fedcba098.eus.azds.io
```

No exemplo acima, o sufixo de host é _fedcba098.eus.azds.io_. Esse valor é usado posteriormente durante a criação de sua definição de versão.

O espaço _dev_ sempre conterá o estado mais recente do repositório, uma linha de base, para que os desenvolvedores possam criar _espaços filho_ a partir do _dev_ para testar suas alterações isoladas dentro do contexto do aplicativo maior. Esse conceito é discutido com mais detalhes nos tutoriais do Dev Spaces.

## <a name="creating-the-build-definition"></a>Criar a definição de build
Abra seu projeto de equipe do Azure DevOps em um navegador da Web e navegue até a seção _Pipelines_. Primeiro, clique na sua foto de perfil no canto superior direito do site do Azure DevOps, abra o painel de versões prévias do recurso e desative a opção _Nova experiência de criação de pipeline YAML_:

![Abertura do painel de versões prévias do recurso](../media/common/preview-feature-open.png)

A opção para desabilitar:

![Opção Nova experiência de criação de pipeline YAML](../media/common/yaml-pipeline-preview-feature.png)

> [!Note]
> A versão prévia do recurso do Azure DevOps _Nova experiência de criação de pipeline YAML_ entra em conflito com a criação de pipelines de build pré-definidos no momento. É preciso desabilitá-lo por enquanto para implantar nosso pipeline de build predefinido.

No branch _azds_updates_, incluímos um simples [Azure Pipeline YAML](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=vsts&tabs=schema) que define as etapas de compilação necessárias para *mywebapi* e *webfrontend*.

Dependendo da linguagem escolhida, o pipeline YAML foi registrado em um caminho semelhante a: `samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml`

Para criar um pipeline desse arquivo:
1. Na sua página principal do projeto de DevOps, navegue até Pipelines > se baseia.
1. Selecione a opção para criar uma **New** compilar o pipeline.
1. Selecione **GitHub** como a origem, autorizar com sua conta do GitHub se necessário e selecione o _azds_updates_ branch da versão bifurcado do repositório sampleapp espaços de desenvolvimento.
1. Selecione **configuração como código**, ou **YAML**, como o seu modelo.
1. Você agora verá uma página de configuração para o pipeline de build. Conforme mencionado acima, navegue até o caminho específico do idioma para o **caminho do arquivo YAML** usando o **...**  botão. Por exemplo, `samples/dotnetcore/getting-started/azure-pipelines.dotnet.yml`.
1. Vá para o **variáveis** guia.
1. Adicione manualmente _dockerId_ como uma variável, que é o nome de usuário de sua [conta de administrador do Registro de Contêiner do Azure](../../container-registry/container-registry-authentication.md#admin-account). (Mencionado nos pré-requisitos do artigo)
1. Adicione manualmente _dockerPassword_ como uma variável, que é a senha da sua conta do administrador do [Registro de Contêiner do Azure](../../container-registry/container-registry-authentication.md#admin-account). Certifique-se de especificar _dockerPassword_ como um Segredo (selecionando o ícone de bloqueio) para fins de segurança.
1. Selecione **salvar e enfileirar**.

Agora você tem uma solução de IC que irá criar automaticamente os itens *mywebapi* e *webfrontend* para qualquer atualização enviada para o branch _azds_updates_ para a sua bifurcação do GitHub. Você pode verificar as imagens do Docker foram empurrados para navegar até o portal do Azure, selecionando o registro de contêiner do Azure e navegando o **repositórios** guia. Pode levar vários minutos para que as imagens criar e aparecem em seu registro de contêiner.

![Repositórios de Registro de Contêiner do Azure](../media/common/ci-cd-images-verify.png)

## <a name="creating-the-release-definition"></a>Criar a definição da versão

1. Na página principal do projeto DevOps, navegue até Pipelines > Versões
1. Se você estiver trabalhando em um projeto DevOps totalmente novo que ainda não contenha uma definição de versão, primeiro será necessário criar uma definição de versão vazia antes de continuar. A opção Importar não é exibida na interface do usuário até que você tenha uma definição de versão existente.
1. À esquerda, clique no **+ novo** botão e, em seguida, clique em **importar de um pipeline**.
1. Clique em **navegue** e selecione `samples/release.json` do seu projeto.
1. Clique em **OK**. Observe que o painel Pipeline foi carregado com a página de edição da definição de versão. Observe também que há alguns ícones de aviso vermelhos indicando detalhes específicos do cluster que ainda precisam ser configurados.
1. À esquerda do painel Pipeline, clique na bolha **Adicionar um artefato**.
1. No **origem** lista suspensa, selecione o build pipeline que você criou anteriormente.
1. Para o **versão padrão**, escolha **mais recente da ramificação de padrão de pipeline de compilação com marcas**.
1. Deixe **marcas** vazio.
1. Defina o **Alias de Origem** como `drop`. O **alias de origem** valor é usado pelas tarefas de liberação predefinidas, portanto, ele deve ser definido.
1. Clique em **Adicionar**.
1. Agora clique no ícone de raio na origem de artefatos `drop` recém-criada, conforme mostrado abaixo:

    ![Configuração da implantação contínua de artefato de versão](../media/common/release-artifact-cd-setup.png)
1. Habilitar o **gatilho de implantação contínua**.
1. Passe o mouse sobre o **tarefas** guia lado **Pipeline** e clique em _dev_ para editar o _dev_ estágio de tarefas.
1. Verifique se **do Azure Resource Manager** está selecionado em **tipo de Conexão.** e você verá os três controles de lista suspensa realçados em vermelho: ![Definição de versão da instalação](../media/common/release-setup-tasks.png)
1. Selecione a assinatura do Azure que você estiver usando espaços de desenvolvimento do Azure. Você também terá de clicar em **autorizar**.
1. Selecione o grupo de recursos e o cluster que você estiver usando espaços de desenvolvimento do Azure.
1. Clique em **trabalho do agente**.
1. Selecione **hospedado Ubuntu 1604** sob **pool de agentes**.
1. Passe o mouse sobre o **tarefas** seletor na parte superior, clique em _prod_ para editar o _prod_ estágio de tarefas.
1. Verifique se **do Azure Resource Manager** está selecionado em **tipo de Conexão.** e selecione a assinatura do Azure, o grupo de recursos e o cluster que você estiver usando espaços de desenvolvimento do Azure.
1. Clique em **trabalho do agente**.
1. Selecione **hospedado Ubuntu 1604** sob **pool de agentes**.
1. Clique o **variáveis** guia para atualizar as variáveis para sua versão.
1. Atualizar o valor de **DevSpacesHostSuffix** partir **UPDATE_ME** ao sufixo de host. O sufixo de host é exibido quando você executou o `azds show-context` comando anteriormente.
1. Clique em **Salvar** no canto superior direito e em **OK**.
1. Clique em **+ Versão** (ao lado do botão Salvar) e em **Criar uma versão**.
1. Sob **artefatos**, verifique se a compilação mais recente do seu pipeline de compilação está selecionada.
1. Clique em **Criar**.

Agora, um processo de versão automatizado será iniciado, implantando os gráficos *mywebapi* e *webfrontend* em seu cluster do Kubernetes no espaço de nível superior _dev_. Você pode monitorar o progresso da sua versão no portal da web do Azure DevOps:

1. Navegue até a **Releases** seção sob **Pipelines**.
1. Clique no pipeline de lançamento para o aplicativo de exemplo.
1. Clique no nome da versão mais recente.
1. Passe o mouse sobre **dev** caixa sob **estágios** e clique em **Logs**.

A versão é feita quando todas as tarefas forem concluídas.

> [!TIP]
> Se a sua versão falhar com uma mensagem de erro como *FALHA NO UPGRADE: expirou aguardando a condição*, tente inspecionar os pods em seu cluster [usando o painel do Kubernetes](../../aks/kubernetes-dashboard.md). Se você observar a falha de início dos pods, com mensagens de erro como *Falha ao efetuar pull da imagem "azdsexample.azurecr.io/mywebapi:122": rpc error: code = Unknown desc = Error response from daemon: Obter https://azdsexample.azurecr.io/v2/mywebapi/manifests/122: não autorizado: autenticação necessária*, pode ser que seu cluster não tenha sido autorizado a efetuar pull do seu Registro de Contêiner do Azure. Certifique-se de ter concluído o pré-requisito [Autorizar o cluster do AKS a receber do seu Registro de Contêiner do Azure](../../container-registry/container-registry-auth-aks.md).

Agora você tem um pipeline de CI/CD totalmente automatizado para sua bifurcação do GitHub dos aplicativos de amostra do Dev Spaces. Sempre que você confirmar e enviar um código, o pipeline de build criará e enviará as imagens *mywebapi* e *webfrontend* para sua instância de ACR personalizada. Em seguida, o pipeline de versão implantará o gráfico de Helm para cada aplicativo no espaço _dev_ em seu cluster habilitado para Dev Spaces.

## <a name="accessing-your-dev-services"></a>Acessar seus serviços _dev_
Após a implantação, a versão _dev_ do *webfrontend* pode ser acessada com uma URL pública como: `http://dev.webfrontend.fedcba098.eus.azds.io`. Você pode encontrar essa URL, executando o `azds list-uri` comando: 

```cmd
$ azds list-uris

Uri                                           Status
--------------------------------------------  ---------
http://dev.webfrontend.fedcba098.eus.azds.io  Available
```

## <a name="deploying-to-production"></a>Implantação em Produção

Para promover manualmente uma versão específica para _prod_ usando o sistema CI/CD criado neste tutorial:
1. Navegue até a **Releases** seção sob **Pipelines**.
1. Clique no pipeline de lançamento para o aplicativo de exemplo.
1. Clique no nome da versão mais recente.
1. Passe o mouse sobre o **prod** caixa sob **estágios** e clique em **implantar**.
    ![Promover para produção](../media/common/prod-promote.png)
1. Passe o mouse sobre **prod** caixa novamente sob **estágios** e clique em **Logs**.

A versão é feita quando todas as tarefas forem concluídas.

O _prod_ estágio do pipeline de CI/CD usa um balanceador de carga, em vez do controlador de entrada de espaços de desenvolvimento para fornecer acesso aos _prod_ serviços. Serviços implantados na _prod_ estágio são acessíveis como endereços IP em vez de nomes DNS. Em um ambiente de produção, você pode optar por criar seu próprio controlador de entrada para hospedar seus serviços com base em sua própria configuração de DNS.

Para determinar o IP do serviço webfrontend, clique no **imprimir o IP público do webfrontend** etapa para expandir a saída de log. Usar o IP exibido no log de saída para o acesso a **webfrontend** aplicativo.

```cmd
...
2019-02-25T22:53:02.3237187Z webfrontend can be accessed at http://52.170.231.44
2019-02-25T22:53:02.3320366Z ##[section]Finishing: Print webfrontend public IP
...
```

## <a name="dev-spaces-instrumentation-in-production"></a>Instrumentação do Dev Spaces na produção
Embora a instrumentação do Dev Spaces tenha sido projetada para _não_ atrapalhar a operação normal de seu aplicativo, recomendamos executar suas cargas de trabalho de produção em um namespace do Kubernetes que não esteja habilitado com Dev Spaces. Usar esse tipo de namespace do Kubernetes significa que você deve criar seu namespace de produção usando a CLI do `kubectl` ou permitir que seu sistema de CI/CD o crie durante a primeira implantação de Helm. _Selecionar_ ou criar um espaço usando a ferramenta Dev Spaces adicionará instrumentação do Dev Spaces a esse namespace.

Aqui está um exemplo de estrutura de namespace que dá suporte ao desenvolvimento de recursos, o ambiente 'dev', _e_ produção, tudo em um único cluster do Kubernetes:

![Exemplo de estrutura de namespace](../media/common/cicd-namespaces.png)

> [!Tip]
> Se você já tiver criado um espaço `prod` e simplesmente gostaria de excluí-lo da instrumentação do Dev Spaces (sem excluí-lo!), poderá fazê-lo com o seguinte comando da CLI do Dev Spaces:
>
> `azds space remove -n prod --no-delete`
>
> Talvez seja necessário excluir todos os pods no namespace `prod` depois de fazer isso, para que possam ser recriados sem a instrumentação do Dev Spaces.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre o desenvolvimento da equipe usando o Azure Dev Spaces](../team-development-netcore.md)