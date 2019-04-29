---
title: solução de problemas
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/11/2018
ms.topic: conceptual
description: Desenvolvimento rápido de Kubernetes com contêineres e microsserviços no Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Serviço de Kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, k8s '
ms.openlocfilehash: 044e997703f5b274215fb05c7152186948b331b4
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63761411"
---
# <a name="troubleshooting-guide"></a>Guia de Solução de Problemas

Este guia contém informações sobre problemas comuns que você pode ter ao usar o Azure Dev Spaces.

Se você tiver um problema ao usar espaços de desenvolvimento do Azure, crie uma [problema no repositório do GitHub do Azure Dev espaços](https://github.com/Azure/dev-spaces/issues).

## <a name="enabling-detailed-logging"></a>Ativando o log detalhado

Para solucionar problemas de maneira mais eficaz, pode ser útil criar logs mais detalhados para revisão.

Para ver a extensão do Visual Studio, defina a variável de ambiente `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` como 1. Certifique-se de reiniciar o Visual Studio para que a variável de ambiente tenha efeito. Uma vez habilitado, os logs detalhados são gravados no seu diretório `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools`.

Na CLI, você pode gerar mais informações durante a execução do comando usando o switch `--verbose`. Também é possível pesquisar logs mais detalhados em `%TEMP%\Azure Dev Spaces`. Em um Mac, o diretório TEMP pode ser encontrado executando `echo $TMPDIR` em uma janela de terminal. Em um computador Linux, o diretório TEMP é geralmente `/tmp`.

## <a name="debugging-services-with-multiple-instances"></a>Serviços de depuração com várias instâncias

Neste momento, o Azure Dev Spaces funciona melhor ao depurar uma única instância ou pod. O arquivo azds.yaml contém uma configuração, *replicaCount*, que indica o número de pods que o Kubernetes executa para seu serviço. Se você alterar o replicaCount para configurar seu aplicativo para executar vários pods para um determinado serviço, o depurador será anexado ao primeiro pod, quando listados em ordem alfabética. O depurador será anexado a um pod diferente quando o pod original for reciclado, provavelmente causando um comportamento inesperado.

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Erro "Falha ao criar o controlador do Azure Dev Spaces"

Você pode ver esse erro quando algo der errado com a criação do controlador. Se esse for um erro transitório, exclua e recrie o controlador para corrigi-lo.

### <a name="try"></a>Experimente:

Para excluir o controlador, use a CLI do Azure Dev Spaces. Não é possível fazê-lo no Visual Studio ou no Cloud Shell. Para instalar a CLI do AZDS, primeiro instale a CLI do Azure e, em seguida, execute este comando:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

E, em seguida, execute este comando para excluir o controlador:

```cmd
azds remove -g <resource group name> -n <cluster name>
```

Recriar o controlador pode ser feito a partir do CLI ou Visual Studio. Siga as instruções nos tutoriais como se estivesse começando pela primeira vez.


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

1. Verifique se azds.exe está no local %ProgramFiles%/Microsoft SDKs\Azure\CLI do Azure Dev Spaces (Versão Prévia). Se estiver lá, adicione o local à variável de ambiente PATH.
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

### <a name="reason"></a>Motivo 
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

### <a name="reason"></a>Motivo
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

### <a name="reason"></a>Motivo
Não é necessário ter a extensão do VS Code para C# instalada no computador de desenvolvimento. O C# extensão inclui suporte para o .NET Core (CoreCLR) de depuração.

### <a name="try"></a>Experimente:
Instalar a [extensão do VS Code para C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>Erro de depuração 'não há suporte para tipo de depuração configurado 'coreclr' '
Executar o depurador de VS Code relata o erro: `Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>Motivo
Você não tem a extensão do VS Code para Azure Dev Spaces instalados no computador de desenvolvimento.

### <a name="try"></a>Experimente:
Instale a [Extensão do VS Code para Azure Dev Spaces](get-started-netcore.md).

## <a name="debugging-error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Erro de depuração "Invalid 'cwd' value '/src'. O sistema não pode localizar o arquivo especificado." ou "launch: o programa '/src/[caminho para o projeto binário] 'não existe "
Executar o depurador do VS Code relata o erro `Invalid 'cwd' value '/src'. The system cannot find the file specified.` e/ou `launch: program '/src/[path to project executable]' does not exist`

### <a name="reason"></a>Motivo
Por padrão, a extensão do VS Code usa `src` como o diretório de trabalho do projeto no contêiner. Se você atualizou `Dockerfile` para especificar um diretório de trabalho diferente, poderá visualizar esse erro.

### <a name="try"></a>Experimente:
Atualize o arquivo `launch.json` no subdiretório `.vscode` da pasta do projeto. Altere a diretiva `configurations->cwd` para apontar para o mesmo diretório que `WORKDIR` definiu no `Dockerfile` do projeto. Além disso, talvez seja necessário atualizar a diretiva `configurations->program`.

## <a name="the-type-or-namespace-name-mylibrary-could-not-be-found"></a>O tipo ou o nome do namespace 'MyLibrary' não foi encontrado

### <a name="reason"></a>Motivo 
O contexto de build está no nível de projeto/serviço por padrão, portanto, um projeto de biblioteca que você está usando não pode ser encontrado.

### <a name="try"></a>Experimente:
O que precisa ser feito:
1. Modifique o arquivo _azds.yaml_ para configurar o contexto de compilação para o nível da solução.
2. Modifique os arquivos _Dockerfile_ e _Dockerfile.develop_ para fazer referência aos arquivos (_.csproj_) do projeto corretamente, relativos ao novo contexto de compilação.
3. Implante um arquivo _.dockerignore_ ao lado do arquivo .sln e modifique conforme necessário.

Você pode encontrar um exemplo em https://github.com/sgreenmsft/buildcontextsample

## <a name="microsoftdevspacesregisteraction-authorization-error"></a>Erro de autorização “Microsoft.DevSpaces/register/action"
Você precisa de acesso de *Proprietário* ou *Colaborador* em sua assinatura do Azure para gerenciar o Azure Dev Spaces. Você pode ver este erro se você está tentando gerenciar espaços de desenvolvimento e você não tem acesso de *Proprietário* ou de *Colaborador* à assinatura do Azure associada.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Motivo
A assinatura do Azure selecionada não registrou o namespace `Microsoft.DevSpaces`.

### <a name="try"></a>Experimente:
Alguém com acesso Proprietário ou de Colaborador à assinatura do Azure pode executar o comando da CLI do Azure a seguir para registrar manualmente o namespace `Microsoft.DevSpaces`:

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="dev-spaces-times-out-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>O Dev Spaces atinge o tempo limite na etapa *Aguardando o build de imagem de contêiner...* com os nós virtuais do AKS

### <a name="reason"></a>Motivo
Esse tempo limite ocorre quando você tenta usar espaços de desenvolvimento para executar um serviço que está configurado para ser executado um [nó do AKS virtual](https://docs.microsoft.com/azure/aks/virtual-nodes-portal). Atualmente, o Dev Spaces não dá suporte às ações de compilar ou depurar serviços em nós virtuais.

Se você executar `azds up` com a opção `--verbose` ou habilitar o log detalhado no Visual Studio, você verá detalhes adicionais:

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

O comando acima mostra que o pod do serviço foi atribuído a *virtual-nó-aci-linux*, que é um nó virtual.

### <a name="try"></a>Experimente:
Atualizar o gráfico do Helm para o serviço remover quaisquer valores de *nodeSelector* e/ou *tolerations* que permitam que o serviço seja executado em um nó virtual. Normalmente, esses valores são definidos no arquivo `values.yaml` do gráfico.

Você ainda pode usar um cluster do AKS que tem o recurso de nós virtuais habilitado, se o serviço que você deseja compilar/depurar por meio de espaços de desenvolvimento é executado em um nó de VM. Essa é a configuração padrão.

## <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>“Erro: não foi possível encontrar um pod tiller pronto” ao abrir o Dev Spaces

### <a name="reason"></a>Motivo
Esse erro ocorrerá se o cliente do Helm não puder mais se comunicar com o pod Tiller em execução no cluster.

### <a name="try"></a>Experimente:
Reiniciar os nós de agente em seu cluster geralmente resolve esse problema.

## <a name="azure-dev-spaces-proxy-can-interfere-with-other-pods-running-in-a-dev-space"></a>o Azure Dev Spaces pode interferir com outros pods em execução em um espaço de desenvolvimento

### <a name="reason"></a>Motivo
Quando você habilita espaços de desenvolvimento em um namespace em seu cluster do AKS, um contêiner adicional chamado _mindaro proxy_ é instalado em cada um dos pods em execução dentro desse namespace. Esse contêiner intercepta as chamadas para os serviços no pod, o que é parte integrante das funcionalidades de desenvolvimento de equipe do Dev Spaces; no entanto, isso pode interferir com certos serviços em execução nesses pods. Ele é conhecido por interferir com pods em execução o Cache do Azure para Redis, causando falhas e erros de conexão na comunicação de primário/secundário.

### <a name="try"></a>Experimente:
Você pode mover os pods afetados para um namespace dentro do cluster que _não_ tem o Dev Spaces habilitado. O restante do seu aplicativo pode continuar a executar dentro de um namespace habilitado para o Dev Spaces. Espaços de desenvolvimento não instalará o _mindaro proxy_ namespaces habilitados do contêiner dentro de espaços não - Dev.

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>O Azure Dev Spaces parece não usar o Dockerfile existente para criar um contêiner

### <a name="reason"></a>Motivo
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

## <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Erro de "watch interno falhou: Assista ENOSPC" ao anexar a depuração para um aplicativo Node. js

### <a name="reason"></a>Motivo

O nó que executa o pod com o aplicativo do Node. js que você está tentando anexar a um depurador foi excedido o *fs.inotify.max_user_watches* valor. Em alguns casos, [o valor padrão de *fs.inotify.max_user_watches* pode ser muito pequeno para manipular a anexar um depurador diretamente a um pod](https://github.com/Azure/AKS/issues/772).

### <a name="try"></a>Experimente
Uma solução alternativa temporária para esse problema é aumentar o valor de *fs.inotify.max_user_watches* em cada nó no cluster e reiniciar o nó para que as alterações entrem em vigor.

## <a name="new-pods-are-not-starting"></a>Novo pods não estão iniciando

### <a name="reason"></a>Motivo

O inicializador de Kubernetes não é possível aplicar o PodSpec para novo pods devido a alterações de permissão RBAC para o *administrador de cluster* função no cluster. O novo pod também pode ter um PodSpec inválido, como a conta de serviço associada com o pod não existe mais. Para ver os pods que estão em um *pendente* estado devido a problema de inicializador, use o `kubectl get pods` comando:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Esse problema pode afetar os pods no *todos os namespaces* no cluster, incluindo namespaces nos espaços de desenvolvimento do Azure não está habilitado.

### <a name="try"></a>Experimente

[Atualizando a CLI de espaços de desenvolvimento para a versão mais recente](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) e, em seguida, excluindo os *azds InitializerConfiguration* do controlador de espaços de desenvolvimento do Azure:

```bash
az aks get-credentials --resource-group <resource group name> --name <cluster name>
kubectl delete InitializerConfiguration azds
```

Depois de remover o *azds InitializerConfiguration* do controlador de espaços de desenvolvimento do Azure, use `kubectl delete` para remover qualquer pods em uma *pendente* estado. Afinal de contas pendente pods foram removidos, reimplantar seus pods.

Se novos pods ainda estão presos em um *pendente* estado após uma reimplantação, use `kubectl delete` para remover qualquer pods em uma *pendente* estado. Depois que todos os pendentes pods foram removidos, exclua o controlador do cluster e reinstalá-lo:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

Depois que o controlador for reinstalado, reimplante seus pods.

## <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Permissões de RBAC incorretas para chamar APIs e o controlador de espaços de desenvolvimento

### <a name="reason"></a>Motivo
O usuário acessar o controlador de espaços de desenvolvimento do Azure deve ter acesso de leitura do administrador *kubeconfig* no cluster do AKS. Por exemplo, essa permissão está disponível na [função de administrador de Cluster de serviço do Azure Kubernetes interno](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions). O usuário acessar o controlador de espaços de desenvolvimento do Azure também deve ter o *Colaborador* ou *proprietário* função RBAC para o controlador.

### <a name="try"></a>Experimente
Mais detalhes sobre como atualizar as permissões do usuário para um cluster do AKS [aqui](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user).

Para atualizar a função RBAC do usuário para o controlador:

1. Entre no Portal do Azure em https://portal.azure.com.
1. Navegue até o grupo de recursos que contém o controlador, que geralmente é o mesmo que o cluster do AKS.
1. Habilitar o *Mostrar tipos ocultos* caixa de seleção.
1. Clique no controlador.
1. Abra o *controle de acesso (IAM)* painel.
1. Clique no *atribuições de função* guia.
1. Clique em *Add* , em seguida, *Adicionar atribuição de função*.
    * Para *função* selecione *Colaborador* ou *proprietário*.
    * Para *atribuir acesso a* selecionar *usuário, grupo ou entidade de serviço do Azure AD*.
    * Para *selecionar* pesquisa para o usuário que você deseja conceder permissões.
1. Clique em *Salvar*.
