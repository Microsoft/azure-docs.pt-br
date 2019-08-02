---
title: Solução de problemas
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/11/2018
ms.topic: conceptual
description: Desenvolvimento rápido de Kubernetes com contêineres e microsserviços no Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Serviço de Kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, k8s '
ms.openlocfilehash: 2434507ac89d631bb96ae9633403075801879a37
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277410"
---
# <a name="troubleshooting-guide"></a>Guia de Solução de Problemas

Este guia contém informações sobre problemas comuns que você pode ter ao usar o Azure Dev Spaces.

Se você tiver um problema ao usar Azure Dev Spaces, crie um [problema no repositório GitHub Azure dev Spaces](https://github.com/Azure/dev-spaces/issues).

## <a name="enabling-detailed-logging"></a>Ativando o log detalhado

Para solucionar problemas de maneira mais eficaz, pode ser útil criar logs mais detalhados para revisão.

Para ver a extensão do Visual Studio, defina a variável de ambiente `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` como 1. Certifique-se de reiniciar o Visual Studio para que a variável de ambiente tenha efeito. Uma vez habilitado, os logs detalhados são gravados no seu diretório `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools`.

Na CLI, você pode gerar mais informações durante a execução do comando usando o switch `--verbose`. Também é possível pesquisar logs mais detalhados em `%TEMP%\Azure Dev Spaces`. Em um Mac, o diretório TEMP pode ser encontrado executando `echo $TMPDIR` em uma janela de terminal. Em um computador Linux, o diretório TEMP é geralmente `/tmp`.

## <a name="debugging-services-with-multiple-instances"></a>Serviços de depuração com várias instâncias

Neste momento, o Azure Dev Spaces funciona melhor ao depurar uma única instância ou pod. O arquivo azds.yaml contém uma configuração, *replicaCount*, que indica o número de pods que o Kubernetes executa para seu serviço. Se você alterar o replicaCount para configurar seu aplicativo para executar vários pods para um determinado serviço, o depurador será anexado ao primeiro pod, quando listados em ordem alfabética. O depurador será anexado a um pod diferente quando o pod original for reciclado, provavelmente causando um comportamento inesperado.

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Erro "Falha ao criar o controlador do Azure Dev Spaces"

### <a name="reason"></a>Reason
Você pode ver esse erro quando algo der errado com a criação do controlador. Se esse for um erro transitório, exclua e recrie o controlador para corrigi-lo.

### <a name="try"></a>Experimente

Exclua o controlador:

```bash
azds remove -g <resource group name> -n <cluster name>
```

Você deve usar a CLI do Azure Dev Spaces para excluir um controlador. Não é possível excluir um controlador do Visual Studio. Você também não pode instalar o Azure Dev Spaces CLI no Azure Cloud Shell para que não possa excluir um controlador do Azure Cloud Shell.

Se você não tiver a CLI do Azure Dev Spaces instalada, você poderá primeiro instalá-la usando o comando a seguir e excluir o controlador:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

Recriar o controlador pode ser feito a partir do CLI ou Visual Studio. Consulte os guias de início rápido de [desenvolvimento da equipe](quickstart-team-development.md) ou [desenvolver com o .NET Core](quickstart-netcore-visualstudio.md) para obter exemplos.

## <a name="error-service-cannot-be-started"></a>Erro "O serviço não pode ser iniciado."

Você pode ver esse erro quando seu código de serviço não pode ser iniciado. Frequentemente, a causa está no código do usuário. Para obter mais informações de diagnóstico, faça as seguintes alterações em suas configurações e comandos:

### <a name="try"></a>Experimente:

Na linha de comando:

Quando estiver usando _azds.exe_, use a opção de linha de comando -- verbose e use a opção de linha de comando --output para especificar o formato de saída.
 
```cmd
azds up --verbose --output json
```

No Visual Studio:

1. Abra **Ferramentas > Opções** e, em **Projetos e Soluções**, escolha **Compilar e executar**.
2. Altere as configurações de **Detalhamento da saída de compilação do projeto no MSBuild** para **Detalhado** ou **Diagnóstico**.

    ![Captura de tela da caixa de diálogo Opções de ferramentas](media/common/VerbositySetting.PNG)
    
### <a name="multi-stage-dockerfiles"></a>Dockerfiles de vários estágios:
Você recebe um erro *Não é possível iniciar o serviço* durante o uso de um Dockerfile de vários estágios. Nessa situação, a saída detalhada contém o texto a seguir:

```cmd
$ azds up -v
Using dev space 'default' with target 'AksClusterName'
Synchronizing files...6s
Installing Helm chart...2s
Waiting for container image build...10s
Building container image...
Step 1/12 : FROM [imagename:tag] AS base
Error parsing reference: "[imagename:tag] AS base" is not a valid repository/tag: invalid reference format
Failed to build container image.
Service cannot be started.
```

Esse erro ocorre porque os nós do AKS executam uma versão mais antiga do Docker que não é compatível com builds de vários estágios. Reescreva seu Dockerfile para evitar builds de vários estágios.

### <a name="rerunning-a-service-after-controller-re-creation"></a>Executar novamente um serviço após a recriação do controlador
Você recebe um erro *O serviço não pode ser iniciado* ao tentar executar novamente um serviço após ter removido e, em seguida, recriado o controlador do Azure Dev Spaces associado a esse cluster. Nessa situação, a saída detalhada contém o texto a seguir:

```cmd
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Esse erro ocorre porque a remoção do controlador do Dev Spaces não remove os serviços anteriormente instalados por esse controlador. Recriar o controlador e, em seguida, tentar executar os serviços usando o novo controlador falha porque os serviços antigos ainda estão implantados.

Para corrigir esse problema, use o comando `kubectl delete` para remover manualmente os serviços antigos do cluster e, em seguida, execute novamente os Dev Spaces para instalar os novos serviços.

## <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>A resolução de nomes DNS falha para uma URL pública associada a um serviço do Azure Dev Spaces

Você pode configurar um ponto de extremidade de URL público para seu serviço, especificando a opção `--public` para o comando `azds prep` ou selecionando a caixa de seleção `Publicly Accessible` no Visual Studio. O nome DNS público é registrado automaticamente quando você executa o serviço no Dev Spaces. Se esse nome DNS não estiver registrado, você verá um erro *A página não pode ser exibida* ou *O site não pode ser alcançado* no navegador da Web durante a conexão com a URL pública.

### <a name="try"></a>Experimente:

Usar o seguinte comando para listar todas as URLs associadas aos serviços do Azure Dev Spaces:

```cmd
azds list-uris
```

Se uma URL estiver no estado *Pendente*, isso significa que o Azure Dev Spaces ainda está aguardando a conclusão do registro DNS. Às vezes, leva alguns minutos para que esse registro seja concluído. O Azure Dev Spaces também abre um túnel de localhost para cada serviço, que pode ser usado enquanto aguarda o registro DNS.

Se uma URL permanecer no estado *Pendente* por mais de 5 minutos, isso pode indicar um problema com o pod DNS externo que cria o ponto de extremidade público ou o pod controlador de entrada nginx que adquire o ponto de extremidade público. Você pode usar os comandos a seguir para excluir esses pods. O AKS recria automaticamente os pods excluídos.

```cmd
kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
```

## <a name="error-required-tools-and-configurations-are-missing"></a>Erro "Ferramentas e configurações necessárias estão ausentes"

Este erro pode ocorrer ao iniciar o VS Code: "Ferramentas e configurações do [Azure Dev Spaces] necessárias para compilar e depurar '[nome do projeto]' estão ausentes."
O erro significa que azds.exe não está na variável de ambiente PATH, como visto no VS Code.

### <a name="try"></a>Experimente:

Inicie o VS Code de um prompt de comando em que a variável de ambiente PATH está definida corretamente.

## <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Erro "As ferramentas necessárias para compilar e depurar 'projectname' estão desatualizadas."

Esse erro será exibido no Visual Studio Code se você tiver uma versão mais recente da extensão do VS Code para o Azure Dev Spaces, mas uma versão anterior da CLI do Azure Dev Spaces.

### <a name="try"></a>Experimente

Baixe e instale a última versão da CLI do Azure Dev Spaces:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

## <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>O erro "azds" não é reconhecido como um comando interno ou externo, programa operável ou arquivo em lote
 
Você poderá ver esse erro se azds.exe não estiver instalado ou configurado corretamente.

### <a name="try"></a>Experimente:

1. Verifique o local% ProgramFiles%/Microsoft SDKs\Azure\Azure dev Spaces CLI para azds. exe. Se estiver lá, adicione o local à variável de ambiente PATH.
2. Se azds.exe não estiver instalado, execute o seguinte comando:

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

## <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Aviso 'O Dockerfile não pôde ser gerado devido a um idioma não suportado'
O Azure Dev Spaces fornece suporte nativo para C# e Node.js. Quando você executar *azds prep* em um diretório que contenha código escrito em um desses idiomas, o Azure Dev Spaces criará automaticamente um Dockerfile apropriado para você.

Você ainda pode usar Azure Dev Spaces com código escrito em outros idiomas, mas precisará criar o Dockerfile você mesmo antes de executar o *azds up* pela primeira vez.

### <a name="try"></a>Experimente:
Se seu aplicativo for escrito em um idioma sem suporte nativo do Azure Dev Spaces, você precisará fornecer um Dockerfile apropriado para criar uma imagem de contêiner executando seu código. O Docker fornece uma lista de [melhores práticas para gravar Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/), bem como uma [referência Dockerfile](https://docs.docker.com/engine/reference/builder/) que pode ajudá-lo a gravar um Dockerfile que se ajuste às suas necessidades.

Depois que você tiver um Dockerfile apropriado em vigor, você poderá continuar executando *azds up* para executar seu aplicativo no Azure Dev Spaces.

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Erro 'erro de conexão de upstream ou desconectar/reiniciar antes dos cabeçalhos'
É possível ver esse erro ao tentar acessar o serviço. Por exemplo, ao acessa a URL do serviço em um navegador. 

### <a name="reason"></a>Reason 
A porta do contêiner não está disponível. Esse problema pode ocorrer porque: 
* O contêiner ainda está em processo de ser compilado e implantado. Esse problema poderá ocorrer se você executar `azds up` ou iniciar o depurador e, em seguida, tentar acessar o contêiner antes dele ser implantado com êxito.
* A configuração de porta não é uniforme em todo o _Dockerfile_, Helm Chart e em qualquer código de servidor que abra uma porta.

### <a name="try"></a>Experimente:
1. Se o contêiner estiver sendo compilado/implantado, você poderá aguardar de 2 a 3 segundos e tentar acessar o serviço novamente. 
1. Verifique a configuração de porta. Os números de porta especificados devem ser **idênticos** em todos os seguintes ativos:
    * **Dockerfile:** Especificado pela instrução `EXPOSE`.
    * **[Pacote do Helm](https://docs.helm.sh):** Especificado pelos valores `externalPort` e `internalPort` para um serviço (geralmente localizado em um arquivo `values.yml`),
    * Quaisquer portas sendo abertas no código do aplicativo, por exemplo, no Node.js: `var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>Arquivo de configuração não encontrado
Você executa `azds up` e obtém o erro a seguir: `Config file not found: .../azds.yaml`

### <a name="reason"></a>Reason
É necessário executar `azds up` a partir do diretório raiz do código que deseja executar e inicializar a pasta de código para execução com o Azure Dev Spaces.

### <a name="try"></a>Experimente:
1. Altere o diretório atual para a pasta raiz que contém o código de serviço. 
1. Caso não tenha um arquivo _azds.yaml_ na pasta de código, execute `azds prep` para gerar ativos do Azure Dev Spaces, Docker e Kubernetes.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Erro: 'O programa do pipe 'azds' saiu inesperadamente com o código 126.'
Iniciar o depurador de VS Code pode, às vezes, resultar nesse erro.

### <a name="try"></a>Experimente:
1. Feche e abra novamente o VS Code.
2. Pressione F5 novamente.

## <a name="debugging-error-failed-to-find-debugger-extension-for-typecoreclr"></a>Depurar erro 'Falha ao localizar a extensão do depurador para type:coreclr'
Executar o depurador de VS Code relata o erro: `Failed to find debugger extension for type:coreclr.`

### <a name="reason"></a>Reason
Não é necessário ter a extensão do VS Code para C# instalada no computador de desenvolvimento. A C# extensão inclui suporte de depuração para o CoreCLR (.NET Core).

### <a name="try"></a>Experimente:
Instalar a [extensão do VS Code para C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>Erro de depuração 'não há suporte para tipo de depuração configurado 'coreclr' '
Executar o depurador de VS Code relata o erro: `Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>Reason
Você não tem a extensão do VS Code para Azure Dev Spaces instalados no computador de desenvolvimento.

### <a name="try"></a>Experimente:
Instale a [Extensão do VS Code para Azure Dev Spaces](get-started-netcore.md).

## <a name="debugging-error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Erro de depuração "Invalid 'cwd' value '/src'. O sistema não pode localizar o arquivo especificado." ou "launch: o programa '/src/[caminho para o projeto binário] 'não existe "
Executar o depurador do VS Code relata o erro `Invalid 'cwd' value '/src'. The system cannot find the file specified.` e/ou `launch: program '/src/[path to project executable]' does not exist`

### <a name="reason"></a>Reason
Por padrão, a extensão do VS Code usa `src` como o diretório de trabalho do projeto no contêiner. Se você atualizou `Dockerfile` para especificar um diretório de trabalho diferente, poderá visualizar esse erro.

### <a name="try"></a>Experimente:
Atualize o arquivo `launch.json` no subdiretório `.vscode` da pasta do projeto. Altere a diretiva `configurations->cwd` para apontar para o mesmo diretório que `WORKDIR` definiu no `Dockerfile` do projeto. Além disso, talvez seja necessário atualizar a diretiva `configurations->program`.

## <a name="the-type-or-namespace-name-mylibrary-could-not-be-found"></a>O tipo ou o nome do namespace 'MyLibrary' não foi encontrado

### <a name="reason"></a>Reason 
O contexto de build está no nível de projeto/serviço por padrão, portanto, um projeto de biblioteca que você está usando não pode ser encontrado.

### <a name="try"></a>Experimente:
O que precisa ser feito:
1. Modifique o arquivo _azds.yaml_ para configurar o contexto de compilação para o nível da solução.
2. Modifique os arquivos _Dockerfile_ e _Dockerfile.develop_ para fazer referência aos arquivos ( _.csproj_) do projeto corretamente, relativos ao novo contexto de compilação.
3. Implante um arquivo _.dockerignore_ ao lado do arquivo .sln e modifique conforme necessário.

Você pode encontrar um exemplo em https://github.com/sgreenmsft/buildcontextsample

## <a name="microsoftdevspacesregisteraction-authorization-error"></a>Erro de autorização “Microsoft.DevSpaces/register/action"
Você precisa de acesso de *Proprietário* ou *Colaborador* em sua assinatura do Azure para gerenciar o Azure Dev Spaces. Você pode ver este erro se você está tentando gerenciar espaços de desenvolvimento e você não tem acesso de *Proprietário* ou de *Colaborador* à assinatura do Azure associada.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Reason
A assinatura do Azure selecionada não registrou o namespace `Microsoft.DevSpaces`.

### <a name="try"></a>Experimente:
Alguém com acesso Proprietário ou de Colaborador à assinatura do Azure pode executar o comando da CLI do Azure a seguir para registrar manualmente o namespace `Microsoft.DevSpaces`:

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="dev-spaces-times-out-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>O Dev Spaces atinge o tempo limite na etapa *Aguardando o build de imagem de contêiner...* com os nós virtuais do AKS

### <a name="reason"></a>Reason
Esse tempo limite ocorre quando você tenta usar espaços de desenvolvimento para executar um serviço que está configurado para ser executado em um [nó virtual AKs](https://docs.microsoft.com/azure/aks/virtual-nodes-portal). Atualmente, o Dev Spaces não dá suporte às ações de compilar ou depurar serviços em nós virtuais.

Se você executar `azds up` com a opção `--verbose` ou habilitar o log detalhado no Visual Studio, você verá detalhes adicionais:

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

O comando acima mostra que o Pod do serviço foi atribuído a *virtual-node-ACI-Linux*, que é um nó virtual.

### <a name="try"></a>Experimente:
Atualizar o gráfico do Helm para o serviço remover quaisquer valores de *nodeSelector* e/ou *tolerations* que permitam que o serviço seja executado em um nó virtual. Normalmente, esses valores são definidos no arquivo `values.yaml` do gráfico.

Você ainda pode usar um cluster do AKS que tem o recurso de nós virtuais habilitado, se o serviço que você deseja compilar/depurar por meio de espaços de desenvolvimento é executado em um nó de VM. Essa é a configuração padrão.

## <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>“Erro: não foi possível encontrar um pod tiller pronto” ao abrir o Dev Spaces

### <a name="reason"></a>Reason
Esse erro ocorrerá se o cliente do Helm não puder mais se comunicar com o pod Tiller em execução no cluster.

### <a name="try"></a>Experimente:
Reiniciar os nós de agente em seu cluster geralmente resolve esse problema.

## <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>"Erro: versão azds do\<identificador\>\>-denome\<deNamespaceNamefalhou: ServiceName dos serviços\<\> -\<\>' já existe "ou" acesso de pull negado \<para\>ServiceName, o repositório não existe ou pode exigir ' Docker login "

### <a name="reason"></a>Reason
Esses erros podem ocorrer se você misturar comandos Helm diretos em execução ( `helm install`como `helm upgrade`, ou `helm delete` `azds up` ) com os comandos de espaços de desenvolvimento ( `azds down`como e) dentro do mesmo espaço de desenvolvimento. Elas ocorrem porque os espaços de desenvolvimento têm sua própria instância de gaveta, que está em conflito com a instância de seu próprio gaveta em execução no mesmo espaço de desenvolvimento.

### <a name="try"></a>Experimente:
É bom usar comandos Helm e comandos de espaços de desenvolvimento no mesmo cluster AKS, mas cada namespace habilitado para espaços de desenvolvimento deve usar um ou outro.

Por exemplo, suponha que você use um comando Helm para executar o aplicativo inteiro em um espaço de desenvolvimento pai. Você pode criar espaços de desenvolvimento filho fora desse pai, usar espaços de desenvolvimento para executar serviços individuais dentro dos espaços de desenvolvimento filho e testar os serviços juntos. Quando estiver pronto para fazer check-in das alterações, use um comando Helm para implantar o código atualizado no espaço de desenvolvimento pai. Não use `azds up` para executar o serviço atualizado no espaço de desenvolvimento pai, pois ele entrará em conflito com o serviço executado inicialmente usando Helm.

## <a name="azure-dev-spaces-proxy-can-interfere-with-other-pods-running-in-a-dev-space"></a>o Azure Dev Spaces pode interferir com outros pods em execução em um espaço de desenvolvimento

### <a name="reason"></a>Reason
Quando você habilita espaços de desenvolvimento em um namespace em seu cluster do AKS, um contêiner adicional chamado _mindaro proxy_ é instalado em cada um dos pods em execução dentro desse namespace. Esse contêiner intercepta as chamadas para os serviços no pod, o que é parte integrante das funcionalidades de desenvolvimento de equipe do Dev Spaces; no entanto, isso pode interferir com certos serviços em execução nesses pods. É conhecido por interferir no pods executando o cache do Azure para Redis, causando erros de conexão e falhas na comunicação primária/secundária.

### <a name="try"></a>Experimente:
Você pode mover os pods afetados para um namespace dentro do cluster que _não_ tem o Dev Spaces habilitado. O restante do seu aplicativo pode continuar a executar dentro de um namespace habilitado para o Dev Spaces. Espaços de desenvolvimento não instalará o _mindaro proxy_ namespaces habilitados do contêiner dentro de espaços não - Dev.

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>O Azure Dev Spaces parece não usar o Dockerfile existente para criar um contêiner

### <a name="reason"></a>Reason
O Azure Dev Spaces pode ser configurado para apontar para um _Dockerfile_ específico no projeto. Se aparecer que o Azure Dev Spaces não está usando o _Dockerfile_ que você espera compilar seus contêineres, talvez seja necessário informar explicitamente qual Dockerfile usar ao Azure Dev Spaces. 

### <a name="try"></a>Experimente:
Abra o arquivo _azds.yaml_ que foi gerado pelo Azure Dev Spaces no projeto. Use a diretiva *configurações->desenvolvimento->build->dockerfile* para apontar para o Dockerfile que você deseja usar:

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

## <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Erro "falha na inspeção interna: Watch ENOSPC" ao anexar a depuração a um aplicativo node. js

### <a name="reason"></a>Reason

O nó que executa o Pod com o aplicativo node. js ao qual você está tentando se anexar com um depurador excedeu o valor de *_user_watches FS. INotifyPropertyChanged. Max* . Em alguns casos, [o valor padrão de *FS. INotifyPropertyChanged. Max _user_watches* pode ser muito pequeno para lidar com a anexação de um depurador diretamente a um pod](https://github.com/Azure/AKS/issues/772).

### <a name="try"></a>Experimente
Uma solução alternativa temporária para esse problema é aumentar o valor de *FS. INotifyPropertyChanged. Max _user_watches* em cada nó no cluster e reiniciar esse nó para que as alterações entrem em vigor.

## <a name="new-pods-are-not-starting"></a>Novos pods não estão iniciando

### <a name="reason"></a>Reason

O inicializador kubernetes não pode aplicar o PodSpec para o novo pods devido a alterações de permissão de RBAC para a função de *administrador de cluster* no cluster. O novo pod também pode ter um PodSpec inválido, por exemplo, a conta de serviço associada ao Pod não existe mais. Para ver os pods que estão em um estado *pendente* devido ao problema do inicializador, use `kubectl get pods` o comando:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Esse problema pode afetar os pods em *todos os namespaces* no cluster, incluindo namespaces em que o Azure dev Spaces não está habilitado.

### <a name="try"></a>Experimente

[Atualizando a CLI de espaços de desenvolvimento para a versão mais recente](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) e, em seguida, excluindo o *azds InitializerConfiguration* do controlador de Azure dev Spaces:

```bash
az aks get-credentials --resource-group <resource group name> --name <cluster name>
kubectl delete InitializerConfiguration azds
```

Depois de remover o *azds InitializerConfiguration* do controlador de Azure dev Spaces, use `kubectl delete` para remover qualquer pods em um estado *pendente* . Depois que todos os pods pendentes tiverem sido removidos, reimplante o pods.

Se novos pods ainda estiverem presos em um estado *pendente* após uma reimplantação, use `kubectl delete` para remover todos os pods em um estado *pendente* . Depois que todos os pods pendentes tiverem sido removidos, exclua o controlador do cluster e reinstale-o:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

Depois que o controlador for reinstalado, reimplante o pods.

## <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Permissões RBAC incorretas para chamar controlador e APIs de espaços de desenvolvimento

### <a name="reason"></a>Reason
O usuário que acessa o controlador de Azure Dev Spaces deve ter acesso para ler o *kubeconfig* do administrador no cluster AKs. Por exemplo, essa permissão está disponível na [função de administrador de cluster do serviço kubernetes do Azure interna](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions). O usuário que acessa o controlador de Azure Dev Spaces também deve ter a função de RBAC de *proprietário* ou *colaborador* para o controlador.

### <a name="try"></a>Experimente
Mais detalhes sobre como atualizar as permissões de um usuário para um cluster AKS estão disponíveis [aqui](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group).

Para atualizar a função RBAC do usuário para o controlador:

1. Entre no Portal do Azure em https://portal.azure.com.
1. Navegue até o grupo de recursos que contém o controlador, que geralmente é o mesmo que o cluster AKS.
1. Habilite a caixa de seleção *Mostrar tipos ocultos* .
1. Clique no controlador.
1. Abra o painel *controle de acesso (iam)* .
1. Clique na guia *atribuições de função* .
1. Clique em *Adicionar* e em *Adicionar atribuição de função*.
    * Para *função* , selecione *colaborador* ou *proprietário*.
    * Para *atribuir acesso para* selecionar *usuário, grupo ou entidade de serviço do Azure ad*.
    * Para *selecionar* pesquisa para o usuário que você deseja conceder permissões.
1. Clique em *Salvar*.

## <a name="controller-create-failing-due-to-controller-name-length"></a>Falha na criação do controlador devido a comprimento do nome do controlador

### <a name="reason"></a>Reason
O nome de um controlador de Azure Dev Spaces não pode ter mais de 31 caracteres. Se o nome do controlador exceder 31 caracteres ao habilitar espaços de desenvolvimento em um cluster AKS ou criar um controlador, você receberá um erro como:

*Falha ao criar um controlador de espaços de desenvolvimento para o cluster ' a-Controller-Name-que-is-Way-The-Long-AKs-leste-US ': O nome do controlador de Azure Dev Spaces ' a-Controller-Name-que-is-Way-time-Long-AKs-leste-US ' é inválido. Restrição (ões) violada (s): Azure Dev Spaces nomes de controlador só podem ter no máximo 31 caracteres de comprimento*

### <a name="try"></a>Experimente

Crie um controlador com um nome alternativo:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

## <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>Falha ao habilitar espaços de desenvolvimento quando os pools de nós do Windows são adicionados a um cluster AKS

### <a name="reason"></a>Reason
Atualmente, o Azure Dev Spaces destina-se a ser executado somente em pods e nós do Linux. Quando você tem um cluster AKS com um pool de nós do Windows, deve garantir que Azure Dev Spaces pods sejam agendadas apenas em nós do Linux. Se um pod Azure Dev Spaces estiver agendado para ser executado em um nó do Windows, esse Pod não será iniciado e a habilitação dos espaços de desenvolvimento falhará.

### <a name="try"></a>Experimente
[Adicione um](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) seu cluster AKs para garantir que os pods do Linux não estejam agendados para execução em um nó do Windows.

## <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Erro "não foi encontrado nenhum nó Linux não reconsiderado no estado pronto no cluster. Deve haver pelo menos um nó Linux não comparável no estado pronto para implantar pods no namespace ' azds '. "

### <a name="reason"></a>Reason

Azure Dev Spaces não pôde criar um controlador em seu cluster AKS porque não pôde encontrar um nó não comparável em um estado *pronto* para agendar pods. Azure Dev Spaces requer pelo menos um nó do Linux em um estado *pronto* que permita o agendamento de pods sem especificar Tolerations.

### <a name="try"></a>Experimente
[Atualize sua configuração](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) do seu AKs no cluster para garantir que pelo menos um nó do Linux permita o planejamento de pods sem especificar Tolerations. Além disso, verifique se pelo menos um nó Linux que permite o agendamento de pods sem especificar Tolerations está no estado *pronto* . Se o seu nó estiver demorando muito tempo para chegar ao estado *pronto* , você poderá tentar reiniciar o nó.

## <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Erro "a CLI do Azure Dev Spaces não foi instalada corretamente" durante a execução`az aks use-dev-spaces`

### <a name="reason"></a>Reason
Uma atualização para a CLI do Azure Dev Spaces alterou seu caminho de instalação. Se você estiver usando uma versão do CLI do Azure anterior a 2.0.63, poderá ver esse erro. Para exibir sua versão do CLI do Azure, use `az --version`.

```bash
$ az --version
azure-cli                         2.0.60 *
...
```

Apesar da mensagem de erro ao `az aks use-dev-spaces` ser executada com uma versão do CLI do Azure antes de 2.0.63, a instalação é realizada com sucesso. Você pode continuar a usar `azds` sem problemas.

### <a name="try"></a>Experimente
Atualize a instalação do [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) para o 2.0.63 ou posterior. Isso resolverá a mensagem de erro que você receberá ao executar `az aks use-dev-spaces`. Como alternativa, você pode continuar a usar sua versão atual do CLI do Azure e a CLI do Azure Dev Spaces.


## <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>O dimensionamento automático do pod horizontal não está funcionando em um espaço de desenvolvimento

### <a name="reason"></a>Reason

Quando você executa um serviço em um espaço de desenvolvimento, o Pod do serviço é [injetado com contêineres adicionais para instrumentação](how-dev-spaces-works.md#prepare-your-aks-cluster). Esses contêineres não têm solicitações de recursos ou limites definidos, o que faz com que o autodimensionamento de Pod horizontal seja desabilitado para o pod.

### <a name="try"></a>Experimente
Execute o dimensionador vertical Pod horizontal em um namespace que não tenha espaços de desenvolvimento habilitados.
