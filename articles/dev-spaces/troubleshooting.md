---
title: Solução de problemas
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: troubleshooting
description: Aprenda a solucionar problemas comuns ao habilitar e usar o Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Serviço de Kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, k8s '
ms.openlocfilehash: 14ced0c66b42b6f18c946d0c75091be1af5598f5
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102197785"
---
# <a name="azure-dev-spaces-troubleshooting"></a>Solução de problemas do Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Este guia contém informações sobre problemas comuns que você pode ter ao usar o Azure Dev Spaces.

Se você tiver um problema ao usar o Azure Dev Spaces, crie um [problema no repositório do GitHub do Azure Dev Spaces](https://github.com/Azure/dev-spaces/issues).

## <a name="before-you-begin"></a>Antes de começar

Para solucionar problemas de maneira mais eficaz, pode ser útil criar logs mais detalhados para revisão.

Para o Visual Studio, defina a variável de ambiente `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` como 1. Certifique-se de reiniciar o Visual Studio para que a variável de ambiente tenha efeito. Uma vez habilitado, os logs detalhados são gravados no seu diretório `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools`.

Na CLI, você pode gerar mais informações durante a execução do comando usando o switch `--verbose`. Também é possível pesquisar logs mais detalhados em `%TEMP%\Azure Dev Spaces`. Em um Mac, o diretório *TEMP* pode ser encontrado executando `echo $TMPDIR` em uma janela de terminal. Em um computador Linux, o diretório *TEMP* é geralmente `/tmp`. Além disso, verifique se o registro em log está ativado no seu [arquivo de configuração da CLI do Azure](/cli/azure/azure-cli-configuration#cli-configuration-values-and-environment-variables).

O Azure Dev Spaces também funciona melhor ao depurar uma única instância ou pod. O arquivo `azds.yaml` contém uma configuração, *replicaCount*, que indica o número de pods que o Kubernetes executa para seu serviço. Se você alterar o *replicaCount* para configurar seu aplicativo para executar vários pods para um determinado serviço, o depurador será anexado ao primeiro pod, quando listados em ordem alfabética. O depurador será anexado a um pod diferente quando o pod original for reciclado, provavelmente causando um comportamento inesperado.

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>Problemas comuns ao habilitar o Azure Dev Spaces

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Erro "Falha ao criar o controlador do Azure Dev Spaces"

Você pode ver esse erro quando algo der errado com a criação do controlador. Se esse for um erro transitório, exclua e recrie o controlador para corrigi-lo.

Você também pode tentar excluir o controlador:

```bash
azds remove -g <resource group name> -n <cluster name>
```

Use a CLI do Azure Dev Spaces para excluir um controlador. Não é possível excluir um controlador do Visual Studio. Você também não pode instalar a CLI do Azure Dev Spaces no Azure Cloud Shell; portanto, não é possível excluir um controlador do Azure Cloud Shell.

Se você não tiver a CLI do Azure Dev Spaces instalada, primeiro instale-a usando o seguinte comando e, em seguida, exclua seu controlador:

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

### <a name="controller-create-failing-because-of-controller-name-length"></a>Falha na criação do controlador devido ao tamanho do nome

O nome de um controlador do Azure Dev Spaces não pode ter mais de 31 caracteres. Se o nome do seu controlador exceder 31 caracteres quando você ativar os Dev Spaces em um cluster AKS ou criar um controlador, você receberá um erro. Por exemplo:

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

Para corrigir esse problema, crie um controlador com um nome alternativo. Por exemplo:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>Habilitar o Dev Spaces com falha quando os pools de nós do Windows são adicionados a um cluster AKS

Atualmente, o Azure Dev Spaces deve ser executado apenas em pods e nós do Linux. Quando você tem um cluster AKS com um pool de nós do Windows, deve garantir que os pods do Azure Dev Spaces sejam agendados apenas nos nós do Linux. Se um pod do Azure Dev Spaces estiver agendado para execução em um nó do Windows, esse pod não será iniciado e a habilitação do Dev Spaces falhará.

Para corrigir esse problema, [adicione um taint](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) ao cluster AKS para garantir que os pods do Linux não estejam agendados para execução em um nó do Windows.

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Erro "Não foi encontrado nenhum nó Linux sem taints no estado Pronto no cluster. Deve haver pelo menos um nó Linux sem taints no estado Pronto para implantar pods no namespace 'azds'".

O Azure Dev Spaces não conseguiu criar um controlador no cluster AKS porque ele não encontrou um nó sem taints em estado *Pronto* no qual agendar pods. O Azure Dev Spaces exige pelo menos um nó do Linux em estado *Pronto* que permita o agendamento de pods sem especificar tolerâncias.

Para corrigir esse problema, [atualize sua configuração de taints](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) no cluster AKS para garantir que pelo menos um nó do Linux permita o agendamento de pods sem especificar tolerâncias. Além disso, certifique-se de que pelo menos um nó do Linux que permita o agendamento de pods sem especificar tolerâncias esteja em estado *Pronto*. Se o seu nó estiver demorando muito tempo para atingir o estado *Pronto*, tente reiniciá-lo.

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Erro "A CLI do Azure Dev Spaces não foi instalada corretamente" ao executar az aks use-dev-spaces

Uma atualização da CLI do Azure Dev Spaces alterou seu caminho de instalação. Se você estiver usando uma versão da CLI do Azure anterior a 2.0.63, poderá ver esse erro. Para exibir sua versão da CLI do Azure, use `az --version`.

```azurecli
az --version
```

```output
azure-cli                         2.0.60 *
...
```

Apesar da mensagem de erro ao executar `az aks use-dev-spaces` com uma versão da CLI do Azure anterior à 2.0.63, a instalação é realizada com sucesso. Você pode continuar a usar `azds` sem problemas.

Para corrigir esse problema, atualize sua instalação da [CLI do Azure](/cli/azure/install-azure-cli) para 2.0.63 ou posterior. Esta atualização resolverá a mensagem de erro que você recebe ao executar `az aks use-dev-spaces`. Como alternativa, você pode continuar a usar sua versão atual da CLI do Azure e da CLI do Azure Dev Spaces.

### <a name="error-unable-to-reach-kube-apiserver"></a>Erro "Não é possível alcançar kube-apiserver"

Você poderá ver esse erro quando o Azure Dev Spaces não puder se conectar ao servidor da API do cluster AKS.

Se o acesso ao servidor de API do cluster do AKS estiver bloqueado ou se você tiver [intervalos de endereços IP autorizados do servidor de API](../aks/api-server-authorized-ip-ranges.md) habilitados para o cluster do AKs, você também deverá [criar](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled) ou [Atualizar](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges) seu cluster para [permitir intervalos adicionais com base na sua região](configure-networking.md#aks-cluster-network-requirements)

Verifique se o servidor da API está disponível executando comandos kubectl. Se o servidor da API não estiver disponível, entre em contato com o suporte do AKS e tente novamente quando o servidor da API estiver funcionando.

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>Problemas comuns ao preparar seu projeto para Azure Dev Spaces

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Aviso “O Dockerfile não pôde ser gerado devido a uma linguagem sem suporte”
O Azure Dev Spaces fornece suporte nativo para C# e Node.js. Quando você executar `azds prep` em um diretório que contenha código escrito em um dessas linguagens, o Azure Dev Spaces criará automaticamente um Dockerfile apropriado para você.

Você ainda pode usar Azure Dev Spaces com código escrito em outras linguagens, mas precisará criar o Dockerfile você mesmo antes de executar `azds up` pela primeira vez.

Se seu aplicativo for escrito em uma linguagem sem suporte nativo do Azure Dev Spaces, você precisará fornecer um Dockerfile apropriado para criar uma imagem de contêiner executando seu código. O Docker fornece uma lista de [melhores práticas para gravar Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/), bem como uma [referência Dockerfile](https://docs.docker.com/engine/reference/builder/) que pode ajudá-lo a gravar um Dockerfile que se ajuste às suas necessidades.

Depois que você tiver um Dockerfile apropriado em vigor, execute `azds up` para executar seu aplicativo no Azure Dev Spaces.

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>Problemas comuns ao iniciar ou interromper serviços com o Azure Dev Spaces

### <a name="error-config-file-not-found"></a>Erro "Arquivo de configuração não encontrado:"

Ao executar `azds up`, você poderá ver esse erro. Tanto `azds up` quanto `azds prep` devem ser executados no diretório raiz do projeto que você deseja executar no espaço de desenvolvimento.

Para corrigir esse problema:
1. Altere o diretório atual para a pasta raiz que contém o código de serviço. 
1. Caso não tenha um arquivo _azds.yaml_ na pasta de código, execute `azds prep` para gerar ativos do Azure Dev Spaces, Docker e Kubernetes.

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Tempo limite na etapa "Aguardando o build de imagem de contêiner..." com os nós virtuais do AKS

Esse tempo limite ocorre quando você tentar usar o Dev Spaces para executar um serviço que está configurado para ser executado em um [nó virtual do AKS](../aks/virtual-nodes-portal.md). Atualmente, o Dev Spaces não dá suporte às ações de compilar ou depurar serviços em nós virtuais.

Se você executar `azds up` com a opção `--verbose` ou habilitar o log detalhado no Visual Studio, você verá detalhes adicionais:

```cmd
azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

O comando acima mostra que o pod do serviço foi atribuído a *virtual-node-aci-linux*, que é um nó virtual.

Para corrigir esse problema, atualize o gráfico do Helm para o serviço remover quaisquer valores de *nodeSelector* ou *tolerations* que permitam que o serviço seja executado em um nó virtual. Normalmente, esses valores são definidos no arquivo `values.yaml` do gráfico.

Você ainda pode usar um cluster do AKS que tem o recurso de nós virtuais habilitado, se o serviço que você deseja compilar ou depurar por meio do Dev Spaces é executado em um nó de VM. A execução de um serviço com Dev Spaces em um nó de VM é a configuração padrão.

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Erro "Não foi possível encontrar um pod tiller pronto" ao abrir o Dev Spaces

Esse erro ocorrerá se o cliente do Helm não puder mais se comunicar com o pod Tiller em execução no cluster.

Para corrigir esse problema, reinicie os nós de agente no cluster.

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>Erro "Release azds- \<identifier\> - \<spacename\> - \<servicename\> Failed: os serviços ' \<servicename\> ' já existe" ou "acesso pull negado para \<servicename\> , o repositório não existe ou pode exigir ' Docker login"

Esses erros podem ocorrer se você misturar comandos diretos do Helm em execução (como `helm install`, `helm upgrade`ou `helm delete`) com comandos do Dev Spaces (como `azds up` e `azds down`) dentro do mesmo espaço de desenvolvimento. Eles ocorrem porque o Dev Spaces tem sua própria instância de Tiller, que está em conflito com a sua instância de Tiller em execução no mesmo espaço de desenvolvimento.

É possível usar comandos do Helm e comandos do Dev Spaces no mesmo cluster AKS, mas cada namespace habilitado para o Dev Spaces deve usar um ou o outro.

Por exemplo, suponha que você use um comando do Helm para executar o aplicativo inteiro em um espaço de desenvolvimento pai. Você pode criar espaços de desenvolvimento filho com base nesse pai, usar o Dev Spaces para executar serviços individuais dentro dos espaços de desenvolvimento filho e testar os serviços juntos. Quando estiver pronto para verificar as alterações, use um comando do Helm para implantar o código atualizado no espaço de desenvolvimento pai. Não use `azds up` para executar o serviço atualizado no espaço de desenvolvimento pai, pois ele entrará em conflito com o serviço executado inicialmente usando Helm.

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>Dockerfile existente não usado para criar um contêiner

O Azure Dev Spaces pode ser configurado para apontar para um _Dockerfile_ específico no projeto. Se aparecer que o Azure Dev Spaces não está usando o _Dockerfile_ que você espera compilar seus contêineres, talvez seja necessário informar explicitamente qual Dockerfile usar ao Azure Dev Spaces. 

Para corrigir o problema, abra o arquivo _azds.yaml_ que foi gerado pelo Azure Dev Spaces no projeto. Atualize *configurations: develop: build: dockerfile* para apontar para o Dockerfile que você deseja usar. Por exemplo:

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>Erro "não autorizado: autenticação necessária" ao tentar usar uma imagem Docker de um registro privado

Você está usando uma imagem Docker de um registro privado que requer autenticação.

Para corrigir esse problema, você pode permitir que o Dev Spaces autentique e receba imagens desse registro privado usando [imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets). Para usar imagePullSecrets, [crie um segredo Kubernetes](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) no namespace em que você está usando a imagem. Em seguida, forneça o segredo como um imagePullSecret no `azds.yaml`.

Abaixo está um exemplo de uma especificação de imagePullSecrets no `azds.yaml`.

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: $BUILD_CONTEXT$
  dockerfile: Dockerfile
install:
  chart: $CHART_DIR$
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    # Optional, specify an array of imagePullSecrets. These secrets must be manually created in the namespace.
    # This will override the imagePullSecrets array in values.yaml file.
    # If the dockerfile specifies any private registry, the imagePullSecret for the registry must be added here.
    # ref: https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod
    #
    # This uses credentials from secret "myRegistryKeySecretName".
    imagePullSecrets:
      - name: myRegistryKeySecretName
```

> [!IMPORTANT]
> A definição de imagePullSecrets em `azds.yaml` substituirá imagePullSecrets especificado em `values.yaml`.

### <a name="error-service-cannot-be-started"></a>Erro "O serviço não pode ser iniciado".

Você pode ver esse erro quando seu código de serviço não pode ser iniciado. Frequentemente, a causa está no código do usuário. Para saber mais de diagnóstico, habilite o registro em log mais detalhado ao iniciar o serviço.

Na linha de comando, use `--verbose` para habilitar o registro em log mais detalhado. Você também pode especificar um formato de saída usando `--output`. Por exemplo:

```cmd
azds up --verbose --output json
```

No Visual Studio:

1. Abra **Ferramentas > Opções** e, em **Projetos e Soluções**, escolha **Compilar e executar**.
2. Altere as configurações de **Detalhamento da saída de compilação do projeto no MSBuild** para **Detalhado** ou **Diagnóstico**.

    ![Captura de tela da caixa de diálogo Opções de ferramentas](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>Executar novamente um serviço após a recriação do controlador

Você recebe um erro *O serviço não pode ser iniciado* ao tentar executar novamente um serviço após ter removido e, em seguida, recriado o controlador do Azure Dev Spaces associado a esse cluster. Nessa situação, a saída detalhada contém o texto a seguir:

```output
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Esse erro ocorre porque a remoção do controlador do Dev Spaces não remove os serviços anteriormente instalados por esse controlador. Recriar o controlador e, em seguida, tentar executar os serviços usando o novo controlador falha porque os serviços antigos ainda estão implantados.

Para corrigir esse problema, use o comando `kubectl delete` para remover manualmente os serviços antigos do cluster e, em seguida, execute novamente os Dev Spaces para instalar os novos serviços.

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>Erro "O serviço não pode ser iniciado". ao usar Dockerfiles de vários estágios

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

Esse erro ocorre porque o Azure Dev Spaces atualmente não dá suporte a compilações de vários estágios. Reescreva seu Dockerfile para evitar builds de vários estágios.

### <a name="network-traffic-is-not-forwarded-to-your-aks-cluster-when-connecting-your-development-machine"></a>O tráfego de rede não é encaminhado para o cluster AKS ao conectar seu computador de desenvolvimento

Ao usar o [Azure Dev Spaces para conectar o cluster AKS ao seu computador de desenvolvimento](https://code.visualstudio.com/docs/containers/bridge-to-kubernetes), você pode encontrar um problema em que o tráfego de rede não é encaminhado entre o computador de desenvolvimento e o cluster AKS.

Ao conectar seu computador de desenvolvimento ao cluster AKS, o Azure Dev Spaces encaminha o tráfego de rede entre o cluster AKS e o computador de desenvolvimento modificando o arquivo `hosts` do seu computador de desenvolvimento. O Azure Dev Spaces cria uma entrada no `hosts` com o endereço do serviço Kubernetes que você está substituindo como um nome de host. Essa entrada é usada com o encaminhamento de porta para direcionar o tráfego de rede entre o computador de desenvolvimento e o cluster AKS. Se um serviço em seu computador de desenvolvimento estiver em conflito com a porta do serviço kubernetes que você está substituindo, o Azure Dev Spaces não poderá encaminhar o tráfego de rede para o serviço Kubernetes. Por exemplo, o serviço *Windows BranchCache* geralmente está associado a *0.0.0.0:80*, cujos conflitos causarão um conflito para a porta 80 em todos os IPs locais.

Para corrigir esse problema, você precisa interromper todos os serviços ou processos que entram em conflito com a porta do serviço Kubernetes que você está tentando substituir. Você pode usar ferramentas, como *netstat*, para inspecionar quais serviços ou processos em seu computador de desenvolvimento estão em conflito.

Por exemplo, para interromper e desabilitar o serviço *Windows BranchCache*:
* Execute `services.msc` no prompt de comando.
* Clique com o botão direito do mouse em *BranchCache* e selecione *Propriedades*.
* Clique em *Parar*.
* Opcionalmente, você pode desabilitá-lo definindo *Tipo de inicialização* como *Desabilitado*.
* Clique em *OK*.

### <a name="error-no-azureassignedidentity-found-for-podazdsazds-webhook-deployment-id-in-assigned-state"></a>Erro "nenhum AzureAssignedIdentity encontrado para Pod: azds/azds-webhook-implantação- \<id\> no estado atribuído"

Ao executar um serviço com Azure Dev Spaces em um cluster AKS com [identidade](../aks/use-managed-identity.md) gerenciada e [identidades gerenciadas Pod](../aks/developer-best-practices-pod-security.md#use-pod-managed-identities) instaladas, o processo pode parar de responder após a etapa de *instalação do gráfico* . Se você inspecionar o *azds-injector-webhook* no namespace *azds*, poderá ver esse erro.

Os serviços Azure Dev Spaces executados no cluster utilizam a identidade gerenciada do cluster para se comunicar com os serviços de back-end do Azure Dev Spaces fora do cluster. Quando a identidade gerenciada por pod é instalada, as regras de rede são configuradas nos nós do cluster para redirecionar todas as chamadas para credenciais de identidade gerenciadas para um [DaemonSet NMI (Identidade Gerenciada do Nó) instalado no cluster](https://github.com/Azure/aad-pod-identity#node-managed-identity). Esse DaemonSet NMI identifica o pod de chamada e garante que o pod tenha sido rotulado adequadamente para acessar a identidade gerenciada solicitada. O Azure Dev Spaces não pode detectar se um cluster tem identidade gerenciada por pod instalada e não pode executar a configuração necessária para permitir que os serviços do Azure Dev Spaces acessem a identidade gerenciada do cluster. Como os serviços de Azure Dev Spaces não foram configurados para acessar a identidade gerenciada do cluster, o Daemonset NMI não permitirá que eles obtenham um token do Azure AD para a identidade gerenciada e não se comuniquem com os serviços de back-end Azure Dev Spaces.

Para corrigir esse problema, aplique um [AzurePodIdentityException](https://azure.github.io/aad-pod-identity/docs/configure/application_exception) para o *azds-injector-webhook* e atualize o pods instrumentados pelo Azure Dev Spaces para acessar a identidade gerenciada.

Crie um arquivo chamado *webhookexception.yaml* e copie a seguinte definição de YAML:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: azds-infrastructure-exception
  namespace: azds
spec:
  PodLabels:
    azds.io/uses-cluster-identity: "true"
```

O arquivo acima cria um objeto *AzurePodIdentityException* para o *azds-injector-webhook*. Para implantar esse objeto, use `kubectl`:

```cmd
kubectl apply -f webhookException.yaml
```

Para atualizar o pods instrumentados pelo Azure Dev Spaces para acessar a identidade gerenciada, atualize o *namespace* na definição de YAML abaixo e use `kubectl` para aplicá-lo para cada espaço de desenvolvimento.

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: azds-infrastructure-exception
  namespace: myNamespace
spec:
  PodLabels:
    azds.io/instrumented: "true"
```

Como alternativa, você pode criar objetos *AzureIdentity* e *AzureIdentityBinding* e atualizar os rótulos de pod para cargas de trabalho em execução em espaços instrumentados pelo Azure Dev Spaces para acessar a identidade gerenciada criada pelo cluster AKS.

Para listar os detalhes da identidade gerenciada, execute o seguinte comando para o cluster AKS:

```azurecli
az aks show -g <resourcegroup> -n <cluster> -o json --query "{clientId: identityProfile.kubeletidentity.clientId, resourceId: identityProfile.kubeletidentity.resourceId}"
```

O comando acima gera o *clientId* e *ResourceId* para a identidade gerenciada. Por exemplo:

```json
{
  "clientId": "<clientId>",
  "resourceId": "/subscriptions/<subid>/resourcegroups/<resourcegroup>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<name>"
}
```

Para criar um objeto *AzureIdentity*, crie um arquivo chamado *clusteridentity.yaml* e use a seguinte definição de YAML atualizada com os detalhes da sua identidade gerenciada do comando anterior:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentity
metadata:
  name: my-cluster-mi
spec:
  type: 0
  ResourceID: /subscriptions/<subid>/resourcegroups/<resourcegroup>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<name>
  ClientID: <clientId>
```

Para criar um objeto *AzureIdentityBinding*, crie um arquivo chamado *clusteridentitybinding.yaml* e use a seguinte definição de YAML:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: my-cluster-mi-binding
spec:
  AzureIdentity: my-cluster-mi
  Selector: my-label-value
```

Para implantar objetos *AzureIdentity* e *AzureIdentityBinding*, use `kubectl`:

```cmd
kubectl apply -f clusteridentity.yaml
kubectl apply -f clusteridentitybinding.yaml
```

Depois de implantar os objetos *AzureIdentity* e *AzureIdentityBinding*, qualquer carga de trabalho com o rótulo *aadpodidbinding: my-label-value* pode acessar a identidade gerenciada do cluster. Adicione esse rótulo e reimplante todas as cargas de trabalho em execução em qualquer espaço de desenvolvimento. Por exemplo:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: sample
        aadpodidbinding: my-label-value
    spec:
      [...]
```

### <a name="error-cannot-get-connection-details-for-azure-dev-spaces-controller-abc-because-it-is-in-the-failed-state-something-wrong-might-have-happened-with-your-controller"></a>Erro "não é possível obter detalhes de conexão para o controlador de Azure Dev Spaces ' ABC ' porque ele está no estado ' Failed '. Algo errado pode ter ocorrido com o controlador. "

Para resolver esse problema, tente excluir o controlador de Azure Dev Spaces do cluster e reinstalá-lo:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

Além disso, à medida que Azure Dev Spaces estiver sendo desativada, considere [migrar para o kubernetes](migrate-to-bridge-to-kubernetes.md) , que fornece uma experiência melhor.

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>Problemas comuns ao usar o Visual Studio e o Visual Studio Code com Azure Dev Spaces

### <a name="error-required-tools-and-configurations-are-missing"></a>Erro "Ferramentas e configurações necessárias estão ausentes"

Este erro pode ocorrer ao iniciar o VS Code: "Ferramentas e configurações do [Azure Dev Spaces] necessárias para compilar e depurar '[nome do projeto]' estão ausentes."
O erro significa que azds.exe não está na variável de ambiente PATH, como visto no VS Code.

Tente iniciar o VS Code de um prompt de comando em que a variável de ambiente PATH está definida corretamente.

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Erro "As ferramentas necessárias para compilar e depurar 'projectname' estão desatualizadas."

Esse erro será exibido no Visual Studio Code se você tiver uma versão mais recente da extensão do VS Code para o Azure Dev Spaces, mas uma versão anterior da CLI do Azure Dev Spaces.

Tente baixar e instalar a última versão da CLI do Azure Dev Spaces:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>Erro: “Falha ao localizar a extensão do depurador para type:coreclr”

Você pode ver esse erro ao executar o depurador do Visual Studio Code. Não é necessário ter a extensão do VS Code para C# instalada no computador de desenvolvimento. A extensão do C# inclui o suporte à depuração para .NET Core (CoreCLR).

Para corrigir esse problema, instale a [extensão do VS Code para C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>Erro “não há suporte para tipo de depuração configurado 'coreclr'”

Você pode ver esse erro ao executar o depurador do Visual Studio Code. Você pode não ter a extensão do VS Code para Azure Dev Spaces instalados no computador de desenvolvimento.

Para corrigir esse problema, instale a extensão do VS Code para Azure Dev Spaces.

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Erro "Valor 'cwd' inválido '/src'. O sistema não pode localizar o arquivo especificado." ou "launch: o programa '/src/[caminho para o projeto binário] 'não existe "

Você pode ver esse erro ao executar o depurador do Visual Studio Code. Por padrão, a extensão do VS Code usa `src` como o diretório de trabalho do projeto no contêiner. Se você atualizou `Dockerfile` para especificar um diretório de trabalho diferente, poderá visualizar esse erro.

Para corrigir esse problema, atualize o arquivo `launch.json` no subdiretório `.vscode` da pasta do projeto. Altere a diretiva `configurations->cwd` para apontar para o mesmo diretório que `WORKDIR` definiu no `Dockerfile` do projeto. Além disso, talvez seja necessário atualizar a diretiva `configurations->program`.

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Erro: “O programa do pipe 'azds' foi encerrado inesperadamente com o código de 126.”

Você pode ver esse erro ao executar o depurador do Visual Studio Code.

Para corrigir esse problema, feche e reabra o Visual Studio Code. Reinicie o depurador.

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Erro "Falha na inspeção interna: watch ENOSPC" ao anexar a depuração a um aplicativo Node.js

Esse erro ocorre quando o nó que executa o pod com o aplicativo Node.js ao qual você está tentando anexar com um depurador excedeu o valor *fs.inotify.max_user_watches*. Em alguns casos, [o valor padrão de *fs.inotify.max_user_watches* pode ser muito pequeno para lidar com a anexação de um depurador diretamente a um pod](https://github.com/Azure/AKS/issues/772).

Uma solução alternativa temporária para esse problema é aumentar o valor de *fs.inotify.max_user_watches* em cada nó no cluster e reiniciar esse nó para que as alterações entrem em vigor.

## <a name="other-common-issues"></a>Outros problemas comuns

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>O erro "azds" não é reconhecido como um comando interno ou externo, programa operável ou arquivo em lote

Esse erro pode ocorrer se `azds.exe` não estiver instalado ou configurado corretamente.

Para corrigir esse problema:

1. Verifique o local %ProgramFiles%/Microsoft SDKs\Azure\Azure Dev Spaces CLI for `azds.exe`. Se estiver lá, adicione o local à variável de ambiente PATH.
2. Se `azds.exe` não estiver instalado, execute o seguinte comando:

    ```azurecli
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>Erro de autorização “Microsoft.DevSpaces/register/action"

Você precisa de acesso de *Proprietário* ou *Colaborador* em sua assinatura do Azure para gerenciar o Azure Dev Spaces. Você pode ver este erro se você está tentando gerenciar espaços de desenvolvimento e você não tem acesso de *Proprietário* ou de *Colaborador* à assinatura do Azure associada. Por exemplo:

```output
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

Para corrigir esse problema, usando uma conta com acesso de *Proprietário* ou *Colaborador* à assinatura do Azure, registre manualmente o namespace `Microsoft.DevSpaces`:

```azurecli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>Novos pods não estão sendo iniciados

O inicializador kubernetes não pode aplicar o PodSpec para novos pods devido às alterações de permissão do RBAC kubernetes para a função de *administrador de cluster* no cluster. O novo pod também pode ter um PodSpec inválido, por exemplo, a conta de serviço associada ao pod não existe mais. Para ver os pods que estão em um estado *Pendente* devido ao problema do inicializador, use o comando `kubectl get pods`:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Esse problema pode afetar os pods em *todos os namespaces* no cluster, incluindo namespaces em que o Azure Dev Spaces não está habilitado.

Para corrigir esse problema, [atualize a CLI do Dev Spaces para a versão mais recente](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) e, em seguida, exclua o *azds InitializerConfiguration* do controlador do Azure Dev Spaces:

```azurecli
az aks get-credentials --resource-group <resource group name> --name <cluster name>
```

```bash
kubectl delete InitializerConfiguration azds
```

Depois de remover o *azds InitializerConfiguration* do controlador de Azure Dev Spaces, use `kubectl delete` para remover os pods com o estado *Pendente*. Depois que todos os pods pendentes tiverem sido removidos, reimplante o pods.

Se novos pods ainda estiverem presos em um estado *Pendente* após uma reimplantação, use `kubectl delete` para remover os pods com estado *Pendente*. Depois que todos os pods pendentes tiverem sido removidos, exclua o controlador do cluster e reinstale-o:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

Depois que o controlador for reinstalado, reimplante o pods.

### <a name="incorrect-azure-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Permissões do RBAC do Azure incorretas para chamar controlador e APIs de espaços de desenvolvimento

O usuário que acessa o controlador do Azure Dev Spaces deve ter acesso para ler o *kubeconfig* de administrador no cluster AKS. Por exemplo, essa permissão está disponível na [Função de Administrador de Cluster do Serviço de Kubernetes do Azure](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions). O usuário que acessa o controlador de Azure Dev Spaces também deve ter a função de *colaborador* ou *proprietário* do Azure para o controlador. Mais detalhes sobre como atualizar as permissões de um usuário para um cluster AKS estão disponíveis [aqui](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group).

Para atualizar a função do Azure do usuário para o controlador:

1. Entre no Portal do Azure em https://portal.azure.com.
1. Navegue até o Grupo de Recursos que contém o controlador, que geralmente é o mesmo que o cluster AKS.
1. Habilite a caixa de seleção *Mostrar tipos ocultos*.
1. Clique no controlador.
1. Abra o painel *Controle de Acesso (IAM)* .
1. Clique na guia *Atribuições de Função*.
1. Clique em *Adicionar* > *Adicionar atribuição de função*.
    * Para *Função*, selecione *Colaborador* ou *Proprietário*.
    * Para *Atribuir acesso a*, selecione *Usuário, grupo ou entidade de serviço do Azure AD*.
    * Para *Selecionar*, procure o usuário ao qual deseja conceder permissões.
1. Clique em *Save* (Salvar).

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>A resolução de nomes DNS falha para uma URL pública associada a um serviço do Azure Dev Spaces

Você pode configurar um ponto de extremidade de URL público para seu serviço, especificando a opção `--enable-ingress` para o comando `azds prep` ou selecionando a caixa de seleção `Publicly Accessible` no Visual Studio. O nome DNS público é registrado automaticamente quando você executa o serviço no Dev Spaces. Se esse nome DNS não estiver registrado, você verá um erro *A página não pode ser exibida* ou *O site não pode ser alcançado* no navegador da Web durante a conexão com a URL pública.

Para corrigir esse problema:

* Verifique o status de todas as URLs associadas aos seus serviços do Dev Spaces:

  ```console
  azds list-uris
  ```

* Se uma URL estiver no estado *Pendente*, o Dev Spaces ainda está aguardando a conclusão do registro DNS. Às vezes, leva alguns minutos para que esse registro seja concluído. O Azure Dev Spaces também abre um túnel de localhost para cada serviço, que pode ser usado enquanto aguarda o registro DNS.
* Se uma URL permanecer no estado *Pendente* por mais de 5 minutos, isso pode indicar um problema com o pod DNS externo que cria o ponto de extremidade público ou o pod controlador de entrada nginx que adquire o ponto de extremidade público. Use os comandos a seguir para excluir esses pods e permitir que o AKS os recrie automaticamente:
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Erro “erro de conexão de upstream ou desconectar/reiniciar antes dos cabeçalhos'”

É possível ver esse erro ao tentar acessar o serviço. Por exemplo, ao acessa a URL do serviço em um navegador. Esse erro significa que a porta do contêiner não está disponível. Isso pode ocorrer pelos seguintes motivos:

* O contêiner ainda está em processo de ser compilado e implantado. Esse problema poderá ocorrer se você executar `azds up` ou iniciar o depurador e, em seguida, tentar acessar o contêiner antes dele ser implantado com êxito.
* A configuração de porta não é uniforme em todo o _Dockerfile_, Helm Chart e em qualquer código de servidor que abra uma porta.

Para corrigir esse problema:

1. Se o contêiner estiver sendo compilado/implantado, você poderá aguardar de 2 a 3 segundos e tentar acessar o serviço novamente. 
1. Verifique a configuração das suas portas nos seguintes ativos:
    * **[Pacote do Helm](https://docs.helm.sh):** Especificado por `service.port` e `deployment.containerPort` em values.yaml com scaffold pelo comando `azds prep`.
    * Quaisquer portas sendo abertas no código do aplicativo, por exemplo, no Node.js: `var server = app.listen(80, function () {...}`

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>O tipo ou o nome do namespace “MyLibrary” não foi encontrado

Um projeto de biblioteca que você está usando não pode ser encontrado. Com o Dev Spaces, o contexto de compilação está no nível de projeto/serviço por padrão.  

Para corrigir esse problema:

1. Modifique o arquivo `azds.yaml` para configurar o contexto de compilação para o nível da solução.
2. Modifique os arquivos `Dockerfile` e `Dockerfile.develop` para referir-se aos arquivos do projeto, como `.csproj`, corretamente em relação ao novo contexto de compilação.
3. Adicione um `.dockerignore` ao mesmo diretório do que o arquivo `.sln`.
4. Atualize o `.dockerignore` com entradas adicionais, conforme necessário.

Você pode encontrar um exemplo [aqui](https://github.com/sgreenmsft/buildcontextsample).

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>O dimensionamento automático do pod horizontal não está funcionando em um espaço de desenvolvimento

Quando você executa um serviço em um espaço de desenvolvimento, o pod desse serviço é [injetado com contêineres adicionais para instrumentação](how-dev-spaces-works-cluster-setup.md#prepare-your-aks-cluster) e todos os contêineres em um pod precisam ter limites de recursos e solicitações definidos para o dimensionamento automático de pod horizontal.

Para corrigir esse problema, aplique uma solicitação de recurso e limite-a aos contêineres injetados do Dev Spaces. As solicitações de recursos e os limites podem ser aplicados ao contêiner injetado (devspaces-proxy) adicionando a anotação `azds.io/proxy-resources` à sua especificação de pod. O valor deve ser definido como um objeto JSON que representa a seção de recursos da especificação do contêiner para o proxy.

Abaixo está um exemplo de uma anotação de recursos de proxy que deve ser aplicada à sua especificação de pod.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>Habilite o Azure Dev Spaces em um namespace existente com pods em execução

Você pode ter um cluster AKS existente e um namespace com pods em execução onde deseja habilitar o Azure Dev Spaces.

Para habilitar o Azure Dev Spaces em um namespace existente em um cluster AKS, execute `use-dev-spaces` e use `kubectl` para reiniciar todos os pods nesse namespace.

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
```

```console
kubectl -n my-namespace delete pod --all
```

Depois que os pods forem reiniciados, você poderá começar a usar seu namespace existente com o Azure Dev Spaces.

### <a name="enable-azure-dev-spaces-on-aks-cluster-with-restricted-egress-traffic-for-cluster-nodes"></a>Habilite o Azure Dev Spaces no cluster AKS com tráfego de saída restrito para nós de cluster

Para habilitar o Azure Dev Spaces em um cluster AKS para o qual o tráfego de saída de nós de cluster é restrito, você precisará permitir os seguintes FQDNs:

| FQDN                                    | Porta      | Use      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | Para efetuar o pull de imagens do Linux Alpine e de outras imagens do Azure Dev Spaces |
| gcr.io | HTTP:443 | Para efetuar o pull de imagens do Helm/tiller|
| storage.googleapis.com | HTTP:443 | Para efetuar o pull de imagens do Helm/tiller|

Atualize seu firewall ou configuração de segurança para permitir o tráfego de rede de e para todos os FQDNs e [serviços de infraestrutura de Azure dev Spaces](../dev-spaces/configure-networking.md#virtual-network-or-subnet-configurations)acima.

### <a name="error-could-not-find-the-cluster-cluster-in-subscription-subscriptionid"></a>Erro "não foi possível localizar o cluster \<cluster\> na assinatura \<subscriptionId\> "

Você poderá ver esse erro se o arquivo kubeconfig estiver direcionado a um cluster ou uma assinatura diferente do que você está tentando usar com as ferramentas do lado do cliente do Azure Dev Spaces. As ferramentas do lado do cliente do Azure Dev Spaces replicam o comportamento de *kubectl*, que usa [um ou mais arquivos kubeconfig](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/) para selecionar e se comunicar com o cluster.

Para corrigir esse problema:

* Use `az aks use-dev-spaces -g <resource group name> -n <cluster name>` para atualizar o contexto atual. Esse comando também habilita o Azure Dev Spaces no cluster AKS se ele ainda não estiver habilitado. Como alternativa, você pode usar `kubectl config use-context <cluster name>` para atualizar o contexto atual.
* Use `az account show` para mostrar a assinatura atual do Azure que você está direcionando e verifique se isso está correto. Você pode alterar a assinatura que está direcionando usando `az account set`.

### <a name="error-using-dev-spaces-after-rotating-aks-certificates"></a>Erro ao usar Dev Spaces após a rotação de certificados AKS

Depois da [rotação dos certificados em seu cluster AKS](../aks/certificate-rotation.md), determinadas operações, como `azds space list` e `azds up`, falharão. Você também precisa atualizar os certificados no controlador do Azure Dev Spaces depois da rotação dos certificados no cluster.

Para corrigir esse problema, verifique se o *kubeconfig* tem os certificados atualizados usando `az aks get-credentials` e execute o comando `azds controller refresh-credentials`. Por exemplo:

```azurecli
az aks get-credentials -g <resource group name> -n <cluster name>
```

```console
azds controller refresh-credentials -g <resource group name> -n <cluster name>
```
