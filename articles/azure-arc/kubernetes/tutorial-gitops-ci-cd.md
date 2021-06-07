---
title: 'Tutorial: implementar CI/CD com GitOps usando clusters do Kubernetes habilitados para Azure Arc'
description: Este tutorial orienta a configuração de uma solução de CI/CD usando o GitOps com clusters do Kubernetes habilitados para Azure Arc. Para obter uma abordagem conceitual sobre esse fluxo de trabalho, confira o artigo Fluxo de trabalho de CI/CD usando GitOps – Kubernetes habilitado para Azure Arc.
author: tcare
ms.author: tcare
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/03/2021
ms.custom: template-tutorial
ms.openlocfilehash: a94784f2f3fc622e0232033d63bc957279a7d34c
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076285"
---
# <a name="tutorial-implement-cicd-with-gitops-using-azure-arc-enabled-kubernetes-clusters"></a>Tutorial: implementar CI/CD com GitOps usando clusters do Kubernetes habilitados para Azure Arc


Neste tutorial, você configurará uma solução de CI/CD usando o GitOps com clusters do Kubernetes habilitados para Azure Arc. Com o aplicativo de amostra Azure Vote, você pode:

> [!div class="checklist"]
> * Criar um cluster do Kubernetes habilitado para Azure Arc
> * Conectar seu aplicativo e repositórios GitOps ao Azure Repos.
> * Importar pipelines de CI/CD.
> * Conectar seu ACR (Registro de Contêiner do Azure) ao Azure DevOps e Kubernetes.
> * Criar grupos de variáveis de ambiente.
> * Implantar os ambientes `dev` e `stage`.
> * Testar os ambientes de aplicativo.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="before-you-begin"></a>Antes de começar

Este tutorial pressupõe familiaridade com o Azure DevOps, Azure Repos, Pipelines e a CLI do Azure.

* Entre no [Azure DevOps Services](https://dev.azure.com/).
* Conclua o [tutorial anterior](./tutorial-use-gitops-connected-cluster.md) para aprender a implantar o GitOps no seu ambiente de CI/CD.
* Entenda os [benefícios e a arquitetura](./conceptual-configurations.md) desse recurso.
* Verifique se você tem:
  * Um [cluster do Kubernetes habilitado para Azure Arc conectado](./quickstart-connect-cluster.md#connect-an-existing-kubernetes-cluster) chamado **arc-cicd-cluster**.
  * Um ACR (Registro de Contêiner do Azure) conectado com a [integração do AKS](../../aks/cluster-container-registry-integration.md) ou a [autenticação de cluster não AKS](../../container-registry/container-registry-auth-kubernetes.md).
  * Permissões de "Administrador de Build" e "Administrador de Projeto" para [Azure Repos](/azure/devops/repos/get-started/what-is-repos) e [Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started).
* Instale as seguintes extensões da CLI do Kubernetes habilitado para Azure Arc de versões iguais ou superiores a 1.0.0:

  ```azurecli
  az extension add --name connectedk8s
  az extension add --name k8s-configuration
  ```
  * Para atualizar essas extensões para a última versão, execute os seguintes comandos:

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-configuration
    ```

## <a name="import-application-and-gitops-repos-into-azure-repos"></a>Importar repositório de aplicativos e do GitOps no Azure Repos

Importe um [repositório de aplicativos](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-ci-cd#application-repo) e um [repositório do GitOps](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-ci-cd#gitops-repo) no Azure Repos. Para este tutorial, use o seguinte repositório de amostra:

* Repositório de aplicativos **arc-cicd-demo-src**
   * URL: https://github.com/Azure/arc-cicd-demo-src
   * Contém o aplicativo de amostra Azure Vote que você implantará usando o GitOps.
* Repositório do GitOps **arc-cicd-demo-gitops**
   * URL: https://github.com/Azure/arc-cicd-demo-gitops
   * Funciona como uma base para os recursos de cluster que hospedam o aplicativo Azure Vote.

Saiba mais sobre [a importação do repositório Git](/azure/devops/repos/git/import-git-repository).

>[!NOTE]
> Importar e usar dois repositórios separados para repositórios de aplicativos e do GitOps pode aumentar a segurança e a simplicidade. As permissões e a visibilidade dos repositórios de aplicativos e do GitOps podem ser ajustadas individualmente.
> Por exemplo, o administrador de cluster pode não encontrar as alterações no código do aplicativo relevantes para o estado desejado do cluster. Por outro lado, um desenvolvedor de aplicativos não precisa saber os parâmetros específicos de cada ambiente – um conjunto de valores de teste que fornecem cobertura para os parâmetros pode ser suficiente.

## <a name="connect-the-gitops-repo"></a>Conectar o repositório do GitOps

Para implantar continuamente seu aplicativo, conecte o repositório de aplicativos ao cluster usando o GitOps. O repositório do GitOps **arc-cicd-demo-gitops** contém os recursos básicos para colocar o aplicativo em funcionamento no cluster **arc-cicd-cluster**.

O repositório do GitOps inicial contém apenas um [manifesto](https://github.com/Azure/arc-cicd-demo-gitops/blob/master/arc-cicd-cluster/manifests/namespaces.yml) que cria os namespaces **dev** e **stage** correspondentes aos ambientes de implantação.

A conexão do GitOps que você criar fará o seguinte automaticamente:
* Sincronizará os manifestos no diretório de manifesto.
* Atualizará o estado do cluster.

O fluxo de trabalho de CI/CD preencherá o diretório de manifesto com manifestos adicionais para implantar o aplicativo.


1. [Crie uma conexão do GitOps](./tutorial-use-gitops-connected-cluster.md) com seu repositório **arc-cicd-demo-gitops** importado recentemente no Azure Repos.

   ```azurecli
   az k8sconfiguration create \
      --name cluster-config \
      --cluster-name arc-cicd-cluster \
      --resource-group myResourceGroup \
      --operator-instance-name cluster-config \
      --operator-namespace cluster-config \
      --repository-url https://dev.azure.com/<Your organization>/arc-cicd-demo-gitops \
      --https-user <Azure Repos username> \
      --https-key <Azure Repos PAT token> \
      --scope cluster \
      --cluster-type connectedClusters \
      --operator-params='--git-readonly --git-path=arc-cicd-cluster/manifests'
   ```

1. Verifique se o Flux usa *apenas* o diretório `arc-cicd-cluster/manifests` como o caminho base. Defina o caminho usando o seguinte parâmetro do operador:

   `--git-path=arc-cicd-cluster/manifests`

   > [!NOTE]
   > Se você estiver usando uma cadeia de conexão HTTPS e estiver com problemas de conexão, omita o prefixo do nome de usuário na URL. Por exemplo, `https://alice@dev.azure.com/contoso/arc-cicd-demo-gitops` deve ter `alice@` removido. Em vez disso, o `--https-user` especifica o usuário, por exemplo, `--https-user alice`.

1. Verifique o estado da implantação no portal do Azure.
   * Se ela for bem-sucedida, você verá os namespaces `dev` e `stage` criados no cluster.

## <a name="import-the-cicd-pipelines"></a>Importar os pipelines de CI/CD

Agora que você sincronizou uma conexão do GitOps, precisará importar os pipelines de CI/CD que criam os manifestos.

O repositório de aplicativos contém uma pasta `.pipeline` com os pipelines que você usará para PRs, CI e CD. Importe e renomeie os três pipelines fornecidos no repositório de amostra:

| Nome do arquivo de pipeline | Descrição |
| ------------- | ------------- |
| [`.pipelines/az-vote-pr-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-pr-pipeline.yaml)  | O pipeline de PR do aplicativo, chamado **arc-cicd-demo-src PR** |
| [`.pipelines/az-vote-ci-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-ci-pipeline.yaml) | O pipeline de CI do aplicativo, chamado **arc-cicd-demo-src CI** |
| [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml) | O pipeline de CD do aplicativo, chamado **arc-cicd-demo-src CD** |



## <a name="connect-your-acr"></a>Conectar o ACR
Os pipelines e o cluster usarão o ACR para armazenar e recuperar imagens do Docker.

### <a name="connect-acr-to-azure-devops"></a>Conectar o ACR ao Azure DevOps
Durante o processo de CI, você implantará os contêineres de aplicativo em um registro. Comece criando uma conexão de serviço do Azure:

1. No Azure DevOps, abra a página **Conexões de serviço** na página de configurações do projeto. No TFS, abra a página **Serviços** no ícone de **configurações** na barra de menus superior.
2. Escolha **+ Nova conexão de serviço** e selecione o tipo de conexão de serviço de que você precisa.
3. Preencha os parâmetros para a conexão de serviço. Neste tutorial:
   * Nomeie a conexão de serviço como **arc-demo-acr**. 
   * Selecione **myResourceGroup** como o grupo de recursos.
4. Selecione **Conceder permissão de acesso a todos os pipelines**. 
   * Essa opção autoriza arquivos de pipeline do YAML para conexões de serviço. 
5. Escolha **OK** para criar a conexão.

### <a name="connect-acr-to-kubernetes"></a>Conectar ACR ao Kubernetes
Habilite o cluster do Kubernetes para efetuar pull de imagens de seu ACR. Se ele for particular, a autenticação será necessária.

#### <a name="connect-acr-to-existing-aks-clusters"></a>Conectar ACR a clusters do AKS existentes

Integre um ACR existente a clusters do AKS usando o seguinte comando:

```azurecli
az aks update -n arc-cicd-cluster -g myResourceGroup --attach-acr arc-demo-acr
```

#### <a name="create-an-image-pull-secret"></a>Criar um segredo de pull de imagem

Para conectar clusters não-AKS e locais ao seu ACR, crie um segredo de pull de imagem. O Kubernetes usa segredos de pull de imagem para armazenar as informações necessárias para autenticar o registro.

Crie um segredo de pull de imagem com o comando `kubectl` a seguir. Repita para os namespaces `dev` e `stage`.
```console
kubectl create secret docker-registry <secret-name> \
    --namespace <namespace> \
    --docker-server=<container-registry-name>.azurecr.io \
    --docker-username=<service-principal-ID> \
    --docker-password=<service-principal-password>
```

> [!TIP]
> Para evitar a definição de um imagePullSecret para cada Pod, considere adicionar o imagePullSecret à conta de serviço nos namespaces `dev` e `stage`. Confira o [Tutorial do Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#add-imagepullsecrets-to-a-service-account) para obter mais informações.

## <a name="create-environment-variable-groups"></a>Criar grupos de variáveis de ambiente

### <a name="app-repo-variable-group"></a>Grupo de variáveis do repositório de aplicativos
[Crie um grupo de variáveis](/azure/devops/pipelines/library/variable-groups) chamado **az-vote-app-dev**. Defina os seguintes valores:

| Variável | Valor |
| -------- | ----- |
| AZ_ACR_NAME | (por exemplo, sua instância de ACR azurearctest.azurecr.io) |
| AZURE_SUBSCRIPTION | (sua Conexão de Serviço do Azure, que deve ser a **arc-demo-acr** estabelecida antes no tutorial) |
| AZURE_VOTE_IMAGE_REPO | O caminho completo para o repositório de aplicativos do Azure Vote, por exemplo, azurearctest.azurecr.io/azvote |
| ENVIRONMENT_NAME | Desenvolvimento |
| MANIFESTS_BRANCH | `master` |
| MANIFESTS_REPO | A cadeia de conexão Git para seu repositório GitOps |
| PAT | Um [token PAT criado](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate#create-a-pat) com permissões de leitura/gravação da origem. Salve-o para uso posterior ao criar o grupo de variáveis `stage`. |
| SRC_FOLDER | `azure-vote` | 
| TARGET_CLUSTER | `arc-cicd-cluster` |
| TARGET_NAMESPACE | `dev` |

> [!IMPORTANT]
> Marque seu PAT como um tipo secreto. Em seus aplicativos, considere a vinculação de segredos de um [Azure Key Vault](/azure/devops/pipelines/library/variable-groups#link-secrets-from-an-azure-key-vault).
>
### <a name="stage-environment-variable-group"></a>Preparar grupos de variáveis de ambiente

1. Clone o grupo de variáveis **az-vote-app-dev**.
1. Altere o nome para **az-vote-app-stage**.
1. Garanta os seguintes valores para as variáveis correspondentes:

| Variável | Valor |
| -------- | ----- |
| ENVIRONMENT_NAME | Estágio |
| TARGET_NAMESPACE | `stage` |

Agora você está pronto para implantar nos ambientes `dev` e `stage`.

## <a name="deploy-the-dev-environment-for-the-first-time"></a>Implantar o ambiente de desenvolvimento pela primeira vez
Com os pipelines de CI e CD criados, execute o pipeline de CI para implantar o aplicativo pela primeira vez.

### <a name="ci-pipeline"></a>Pipeline de CI

Durante a execução inicial do pipeline de CI, você poderá obter um erro de autorização de recurso ao ler o nome da conexão de serviço.
1. Verifique se a variável que está sendo acessada é AZURE_SUBSCRIPTION.
1. Autorize o uso.
1. Executar novamente o pipeline.

O pipeline de CI:
* Garante que a alteração do aplicativo seja aprovada por todas as verificações de qualidade automatizadas para a implantação.
* Faz qualquer validação adicional que não pôde ser concluída no pipeline de PR.
    * Específico do GitOps, o pipeline também publica os artefatos para o commit que será implantado pelo pipeline de CD.
* Verifica se a imagem do Docker foi alterada e a nova imagem foi enviada por push.

### <a name="cd-pipeline"></a>Pipeline de CD
A execução bem-sucedida do pipeline de CI dispara o pipeline de CD para concluir o processo de implantação. Você implantará em cada ambiente incrementalmente.

> [!TIP]
> Se o pipeline de CD não for disparado de forma automática:
> 1. Verifique se o nome corresponde ao gatilho de branch em [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml)
>    * Ela deverá ser `arc-cicd-demo-src CI`.
> 1. Execute novamente o pipeline de CI.

Depois que as alterações de modelo e manifesto para o repositório do GitOps tiverem sido geradas, o pipeline de CD criará um commit, enviará por push e criará uma PR para aprovação.
1. Abra o link de PR fornecido na saída da tarefa `Create PR`.
1. Verifique as alterações no repositório do GitOps. Você deverá ver:
   * Alterações de modelo Helm de alto nível.
   * Manifestos do Kubernetes de nível baixo que mostram as alterações subjacentes ao estado desejado. O Flux implanta esses manifestos.
1. Se tudo estiver correto, aprove e conclua a PR.

1. Após alguns minutos, o Flux pega a alteração e inicia a implantação.
1. Encaminhe a porta localmente usando `kubectl` e verifique se o aplicativo funciona corretamente usando:

   `kubectl port-forward -n dev svc/azure-vote-front 8080:80`

1. Exiba o aplicativo Azure Vote em seu navegador em `http://localhost:8080/`.

1. Vote em seus favoritos e prepare-se para fazer algumas alterações no aplicativo.

## <a name="set-up-environment-approvals"></a>Configurar aprovações de ambiente
Após a implantação do aplicativo, você não só poderá fazer alterações no código ou nos modelos, como também poderá colocar o cluster involuntariamente em um estado inválido.

Se o ambiente de desenvolvimento revelar uma interrupção após a implantação, evite que ela vá para ambientes posteriores usando aprovações de ambiente.

1. No seu projeto do Azure DevOps, vá para o ambiente que precisa ser protegido.
1. Navegue para **Aprovações e Verificações** do recurso.
1. Selecione **Criar**.
1. Forneça os aprovadores e uma mensagem opcional.
1. Selecione **Criar** novamente para concluir a adição da verificação de aprovação manual.

Para obter mais detalhes, confira o tutorial [Definir aprovação e verificações](/azure/devops/pipelines/process/approvals).

Na próxima vez que o pipeline de CD for executado, ele será pausado após a criação da PR do GitOps. Verifique se a alteração foi sincronizada corretamente e transmite a funcionalidade básica. Aprove a verificação do pipeline para permitir que a alteração vá para o próximo ambiente.

## <a name="make-an-application-change"></a>Alterar um aplicativo

Com esse conjunto de linhas de base de modelos e manifestos que representam o estado no cluster, você fará uma pequena alteração no aplicativo.

1. No repositório **arc-cicd-demo-src**, edite o arquivo [`azure-vote/src/azure-vote-front/config_file.cfg`](https://github.com/Azure/arc-cicd-demo-src/blob/master/azure-vote/src/azure-vote-front/config_file.cfg).

2. Como "Gatos vs Cachorros" não está recebendo votos suficientes, altere para "Guias vs Espaços" para aumentar a contagem de votos.

3. Confirme a alteração em um novo branch, envie por push e crie uma solicitação de pull.
   * Esse é o fluxo típico do desenvolvedor que iniciará o ciclo de vida de CI/CD.

## <a name="pr-validation-pipeline"></a>Pipeline de validação de PR

O pipeline de PR é a primeira linha de defesa contra uma alteração com falha. As verificações de qualidade do código do aplicativo usuais incluem análise estática e lint. De uma perspectiva do GitOps, você também precisa garantir a mesma qualidade para que a infraestrutura resultante seja implantada.

Os gráficos Dockerfile e Helm do aplicativo podem usar o lint de forma semelhante ao aplicativo.

Os erros encontrados durante o lint variam de:
* Arquivos YAML formatados incorretamente até
* Sugestões de melhores práticas, como a definição de limites de CPU e de memória para seu aplicativo.

> [!NOTE]
> Para obter a melhor cobertura de lint do Helm em um aplicativo real, você precisará substituir valores razoavelmente semelhantes aos usados em um ambiente real.

Erros encontrados durante a execução do pipeline aparecem na seção de resultados de teste da execução. A partir daqui, você pode:
* Acompanhar as estatísticas úteis sobre os tipos de erro.
* Localizar o primeiro commit em que eles foram detectados.
* Fazer o rastreamento de pilha dos links de estilo para as seções de código que causaram o erro.

Depois que a execução do pipeline for concluída, você terá garantido a qualidade do código do aplicativo e do modelo que o implantará. Agora você pode aprovar e concluir a PR. A CI será executada novamente, regenerando os modelos e manifestos antes de disparar o pipeline de CD.

> [!TIP]
> Em um ambiente real, não se esqueça de definir as políticas de branch para garantir que a PR transmita suas verificações de qualidade. Para obter mais informações, confira o artigo [Definir políticas de branch](/azure/devops/repos/git/branch-policies).

## <a name="cd-process-approvals"></a>Aprovações do processo de CD

Uma execução bem-sucedida do pipeline de CI dispara o pipeline de CD para concluir o processo de implantação. Semelhante à primeira vez que você pôde usar o pipeline de CD, você implantará em cada ambiente de forma incremental. Desta vez, o pipeline exige que você aprove cada ambiente de implantação.

1. Aprove a implantação para o ambiente `dev`.
1. Depois que as alterações de modelo e manifesto para o repositório do GitOps tiverem sido geradas, o pipeline de CD criará um commit, enviará por push e criará uma PR para aprovação.
1. Abra o link de PR fornecido na tarefa.
1. Verifique as alterações no repositório do GitOps. Você deverá ver:
   * Alterações de modelo Helm de alto nível.
   * Manifestos do Kubernetes de nível baixo que mostram as alterações subjacentes ao estado desejado.
1. Se tudo estiver correto, aprove e conclua a PR.
1. Aguarde até que a implantação seja concluída.
1. Como um smoke test básico, navegue até a página do aplicativo e verifique se o aplicativo de votação agora exibe Guias vs Espaços.
   * Encaminhe a porta localmente usando `kubectl` e verifique se o aplicativo funciona corretamente usando: `kubectl port-forward -n dev svc/azure-vote-front 8080:80`
   * Exiba o aplicativo Azure Vote no navegador em http://localhost:8080/ e verifique se as opções de votação foram alteradas para Guias vs Espaços. 
1. Repita as etapas de 1 a 7 para o ambiente `stage`.

Sua implantação foi concluída. Isso encerra o fluxo de trabalho de CI/CD.

## <a name="clean-up-resources"></a>Limpar recursos

Se você não quiser continuar usando esse aplicativo, exclua qualquer recurso seguindo estas etapas:

1. Exclua a conexão de configuração do GitOps do Azure Arc:
   ```azurecli
   az k8sconfiguration delete \
   --name cluster-config \
   --cluster-name arc-cicd-cluster \
   --resource-group myResourceGroup \
   --cluster-type connectedClusters
   ```

2. Remova o namespace `dev`:
   * `kubectl delete namespace dev`

3. Remova o namespace `stage`:
   * `kubectl delete namespace stage`

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou um fluxo de trabalho completo de CI/CD que implementa o DevOps do desenvolvimento de aplicativos por meio da implantação. As alterações no aplicativo disparam automaticamente a validação e a implantação, restringidas por aprovações manuais.

Prossiga para nosso artigo conceitual para saber mais sobre GitOps e configurações com Kubernetes habilitado para o Azure Arc.

> [!div class="nextstepaction"]
> [Fluxo de trabalho de CI/CD usando GitOps – Kubernetes habilitado para Azure Arc](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-ci-cd)
