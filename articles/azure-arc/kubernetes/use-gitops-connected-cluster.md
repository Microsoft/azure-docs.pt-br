---
title: Implantar configurações usando o GitOps no cluster do Kubernetes habilitado para Arc (versão prévia)
services: azure-arc
ms.service: azure-arc
ms.date: 02/09/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Usar o GitOps para configurar um cluster kubernetes habilitado para Arc do Azure (versão prévia)
keywords: GitOps, kubernetes, K8s, Azure, Arc, serviço do Azure kubernetes, AKS, contêineres
ms.openlocfilehash: 072bfc8c243eb9b69e06366961019b88b67e0941
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392231"
---
# <a name="deploy-configurations-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Implantar configurações usando o GitOps no cluster do Kubernetes habilitado para Arc (versão prévia)

Em relação a kubernetes, o GitOps é a prática de declarar o estado desejado de configurações de cluster kubernetes (implantações, namespaces, etc.) em um repositório git. Essa declaração é seguida por uma implantação baseada em sondagem e pull dessas configurações de cluster usando um operador. 

Este artigo aborda a configuração de fluxos de trabalho do GitOps em clusters kubernetes habilitados para Arc do Azure.

A conexão entre o cluster e um repositório Git é criada como um `Microsoft.KubernetesConfiguration/sourceControlConfigurations` recurso de extensão no Azure Resource Manager. As propriedades de recurso de `sourceControlConfiguration` representam onde e como os recursos de Kubernetes devem fluir do Git para o cluster. Os `sourceControlConfiguration` dados são armazenados criptografados, em repouso, em um Azure Cosmos DB banco de dados, para garantir a confidencialidade do dado.

A `config-agent` execução no seu cluster é responsável por:
* Acompanhando recursos de extensão novos ou atualizados `sourceControlConfiguration` no recurso kubernetes habilitado para Arc do Azure.
* Implantando um operador de fluxo para observar o repositório Git para cada um `sourceControlConfiguration` .
* Aplicando qualquer atualização feita a qualquer `sourceControlConfiguration` . 

Você pode criar vários `sourceControlConfiguration` recursos no mesmo cluster de kubernetes habilitado para Arc do Azure para obter multilocação. Limite as implantações para dentro dos respectivos namespaces criando cada `sourceControlConfiguration` uma com um `namespace` escopo diferente.

O repositório git pode conter:
* Manifestos de formato YAML que descrevem quaisquer recursos de kubernetes válidos, incluindo namespaces, ConfigMaps, implantações, DaemonSets, etc. 
* Gráficos do Helm para implantação de aplicativos. 

Um conjunto comum de cenários inclui a definição de uma configuração de linha de base para sua organização, como funções e associações comuns do Azure, agentes de monitoramento ou registro em log ou serviços de todo o cluster.

O mesmo padrão pode ser usado para gerenciar uma coleção maior de clusters, que podem ser implantados em ambientes heterogêneos. Por exemplo, você tem um repositório que define a configuração de linha de base para sua organização, que se aplica a vários clusters kubernetes de uma só vez. [Azure Policy pode automatizar](use-azure-policy.md) a criação de um `sourceControlConfiguration` com um conjunto específico de parâmetros em todos os recursos de kubernetes habilitados para o Azure Arc dentro de um escopo (assinatura ou grupo de recursos).

Percorra as etapas a seguir para saber como aplicar um conjunto de configurações com `cluster-admin` escopo.

## <a name="before-you-begin"></a>Antes de começar

Verifique se você tem um cluster conectado do Azure kubernetes habilitado para Arc existente. Se você precisar de um cluster conectado, consulte o guia de [início rápido conectar um cluster do kubernetes habilitado para o Azure Arc](./connect-cluster.md).

## <a name="create-a-configuration"></a>Criar uma configuração

O [repositório de exemplo](https://github.com/Azure/arc-k8s-demo) usado neste artigo é estruturado em todo o persona de um operador de cluster que gostaria de provisionar alguns namespaces, implantar uma carga de trabalho comum e fornecer uma configuração específica da equipe. O uso desse repositório cria os seguintes recursos no cluster:


* **Namespaces:** `cluster-config` , `team-a` , `team-b`
* **Implantação:**`cluster-config/azure-vote`
* **ConfigMap:**`team-a/endpoints`

As `config-agent` pesquisas do Azure para novas ou atualizadas `sourceControlConfiguration` . Essa tarefa levará até 30 segundos.

Se você estiver associando um repositório privado ao `sourceControlConfiguration` , conclua as etapas em [aplicar configuração de um repositório git privado](#apply-configuration-from-a-private-git-repository).

### <a name="using-azure-cli"></a>Usando a CLI do Azure

Use a extensão CLI do Azure para `k8sconfiguration` para vincular um cluster conectado ao [repositório git de exemplo](https://github.com/Azure/arc-k8s-demo). 
1. Nomeie esta configuração `cluster-config` .
1. Instrua o agente a implantar o operador no `cluster-config` namespace.
1. Forneça as permissões de operador `cluster-admin` .

```azurecli
az k8sconfiguration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
```

**Saída:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Pending",
    "lastConfigApplied": "0001-01-01T00:00:00",
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
    "messageLevel": "3"
  },
  "configurationProtectedSettings": {},
  "enableHelmOperator": false,
  "helmOperatorProperties": null,
  "id": "/subscriptions/<sub id>/resourceGroups/<group name>/providers/Microsoft.Kubernetes/connectedClusters/<cluster name>/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "cluster-config",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "cluster-config",
  "operatorParams": "--git-readonly",
  "operatorScope": "cluster",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "",
  "repositoryUrl": "https://github.com/Azure/arc-k8s-demo",
  "resourceGroup": "MyRG",
  "sshKnownHostsContents": "",
  "systemData": {
    "createdAt": "2020-11-24T21:22:01.542801+00:00",
    "createdBy": null,
    "createdByType": null,
    "lastModifiedAt": "2020-11-24T21:22:01.542801+00:00",
    "lastModifiedBy": null,
    "lastModifiedByType": null
  },
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
  ```

#### <a name="use-a-public-git-repo"></a>Usar um repositório git público

| Parâmetro | Formatar |
| ------------- | ------------- |
| `--repository-url` | http [s]://Server/repo [. git] ou git://Server/repo [. git]

#### <a name="use-a-private-git-repo-with-ssh-and-flux-created-keys"></a>Usar um repositório git privado com SSH e chaves criadas pelo fluxo

| Parâmetro | Formatar | Anotações
| ------------- | ------------- | ------------- |
| `--repository-url` | ssh://user@server/repo[. git] ou user@server:repo [. git] | `git@` pode substituir `user@`

> [!NOTE]
> A chave pública gerada pelo fluxo deve ser adicionada à conta de usuário em seu provedor de serviços git. Se a chave for adicionada ao repositório em vez da conta de usuário, use `git@` no lugar de `user@` na URL. Vá para a seção [aplicar a configuração de um repositório git privado](#apply-configuration-from-a-private-git-repository) para obter mais detalhes.

#### <a name="use-a-private-git-repo-with-ssh-and-user-provided-keys"></a>Usar um repositório git privado com SSH e chaves fornecidas pelo usuário

| Parâmetro | Formatar | Anotações |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[. git] ou user@server:repo [. git] | `git@` pode substituir `user@` |
| `--ssh-private-key` | chave codificada em base64 no [formato PEM](https://aka.ms/PEMformat) | Fornecer a chave diretamente |
| `--ssh-private-key-file` | caminho completo para o arquivo local | Forneça o caminho completo para o arquivo local que contém a chave de formato PEM

> [!NOTE]
> Forneça sua própria chave privada diretamente ou em um arquivo. A chave deve estar no [formato PEM](https://aka.ms/PEMformat) e terminar com nova linha (\n).  A chave pública associada deve ser adicionada à conta de usuário em seu provedor de serviços git. Se a chave for adicionada ao repositório em vez da conta de usuário, use `git@` no lugar de `user@` . Vá para a seção [aplicar a configuração de um repositório git privado](#apply-configuration-from-a-private-git-repository) para obter mais detalhes.

#### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>Usar um host git privado com SSH e hosts conhecidos fornecidos pelo usuário

| Parâmetro | Formatar | Anotações |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[. git] ou user@server:repo [. git] | `git@` pode substituir `user@` |
| `--ssh-known-hosts` | codificado em base64 | Fornecer conteúdo de hosts conhecidos diretamente |
| `--ssh-known-hosts-file` | caminho completo para o arquivo local | Fornecer conteúdo de hosts conhecidos em um arquivo local |

> [!NOTE]
> Para autenticar o repositório git antes de estabelecer a conexão SSH, o operador de fluxo mantém uma lista de hosts git comuns em seu arquivo de hosts conhecidos. Se você estiver usando um repositório git incomum ou seu próprio host git, talvez seja necessário fornecer a chave de host para garantir que o fluxo possa identificar seu repositório. Você pode fornecer seu conteúdo de known_hosts diretamente ou em um arquivo. Use as [especificações de formato de conteúdo known_hosts](https://aka.ms/KnownHostsFormat) em conjunto com um dos cenários de chave SSH descritos acima ao fornecer seu próprio conteúdo.

#### <a name="use-a-private-git-repo-with-https"></a>Usar um repositório git privado com HTTPS

| Parâmetro | Formatar | Anotações |
| ------------- | ------------- | ------------- |
| `--repository-url` | https://server/repo[. git] | HTTPS com autenticação básica |
| `--https-user` | brutos ou codificados em base64 | Nome de usuário HTTPS |
| `--https-key` | brutos ou codificados em base64 | Token de acesso pessoal HTTPS ou senha

> [!NOTE]
> HTTPS Helm versão de autenticação privada tem suporte somente com o Helm Operator Chart versão 1.2.0 + (padrão).
> A autenticação privada de versão Helm HTTPS não tem suporte atualmente para clusters gerenciados pelos serviços Kubernetess do Azure.
> Se precisar de fluxo para acessar o repositório git por meio do proxy, você precisará atualizar os agentes Arc do Azure com as configurações de proxy. Para obter mais informações, consulte [conectar-se usando um servidor proxy de saída](./connect-cluster.md#connect-using-an-outbound-proxy-server).

#### <a name="additional-parameters"></a>Parâmetros Adicionais

Personalize a configuração com os seguintes parâmetros opcionais:

| Parâmetro | Descrição |
| ------------- | ------------- |
| `--enable-helm-operator`| Alterne para habilitar o suporte para implantações de gráfico Helm. |
| `--helm-operator-params` | Valores de gráfico para o operador Helm (se estiver habilitado). Por exemplo, `--set helm.versions=v3`. |
| `--helm-operator-version` | Versão do gráfico para o operador Helm (se estiver habilitado). Use a versão 1.2.0 +. Padrão: ' 1.2.0 '. |
| `--operator-namespace` | Nome para o namespace do operador. Padrão: ' default '. Máx.: 23 caracteres. |
| `--operator-params` | Parâmetros para operador. Deve ser fornecido entre aspas simples. Por exemplo, ```--operator-params='--git-readonly --sync-garbage-collection --git-branch=main' ``` 

##### <a name="options-supported-in----operator-params"></a>Opções com suporte no  `--operator-params` :

| Opção | Descrição |
| ------------- | ------------- |
| `--git-branch`  | Ramificação do repositório git a ser usada para manifestos kubernetes. O padrão é 'master'. Os repositórios mais recentes têm o Branch raiz chamado `main` , caso em que você precisa definir `--git-branch=main` . |
| `--git-path`  | Caminho relativo dentro do repositório Git no Flux para localizar manifestos do Kubernetes. |
| `--git-readonly` | O repositório Git será considerado somente leitura. O Flux não tentará gravar nele. |
| `--manifest-generation`  | Se habilitado, o Flux procurará .flux.yaml e executará o Kustomize ou outros geradores de manifesto. |
| `--git-poll-interval`  | Período durante o qual a pesquisa de novos repositórios Git ocorrerá. O padrão é `5m` (5 minutos). |
| `--sync-garbage-collection`  | Se habilitado, o Flux excluirá os recursos que ele criou, mas não estará mais presente no Git. |
| `--git-label`  | Rótulo para acompanhar o progresso da sincronização. Usado para marcar a ramificação git.  O padrão é `flux-sync`. |
| `--git-user`  | Nome de usuário para confirmação de git. |
| `--git-email`  | Email a ser usado para confirmação de git. 

Se você não quiser que o fluxo grave no repositório e `--git-user` ou `--git-email` não esteja definido, `--git-readonly` ele será automaticamente definido.

Para obter mais informações, consulte a [documentação do fluxo](https://aka.ms/FluxcdReadme).

> [!TIP]
> Você pode criar um `sourceControlConfiguration` no portal do Azure na guia **GitOps** do recurso kubernetes habilitado para Arc do Azure.

## <a name="validate-the-sourcecontrolconfiguration"></a>Validar o sourceControlConfiguration

Use o CLI do Azure para validar que o `sourceControlConfiguration` foi criado com êxito.

```azurecli
az k8sconfiguration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

O `sourceControlConfiguration` recurso será atualizado com o status de conformidade, mensagens e informações de depuração.

**Saída:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2020-12-10T18:26:52.801000+00:00",
    "message": "...",
    "messageLevel": "Information"
  },
  "configurationProtectedSettings": {},
  "enableHelmOperator": false,
  "helmOperatorProperties": {
    "chartValues": "",
    "chartVersion": ""
  },
  "id": "/subscriptions/<sub id>/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "cluster-config",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "cluster-config",
  "operatorParams": "--git-readonly",
  "operatorScope": "cluster",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git",
  "resourceGroup": "AzureArcTest",
  "sshKnownHostsContents": null,
  "systemData": {
    "createdAt": "2020-12-01T03:58:56.175674+00:00",
    "createdBy": null,
    "createdByType": null,
    "lastModifiedAt": "2020-12-10T18:30:56.881219+00:00",
    "lastModifiedBy": null,
    "lastModifiedByType": null
},
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

Quando um `sourceControlConfiguration` é criado ou atualizado, algumas coisas acontecem nos bastidores:

1. O Arc do Azure `config-agent` monitora Azure Resource Manager para configurações novas ou atualizadas ( `Microsoft.KubernetesConfiguration/sourceControlConfigurations` ) e observa a nova `Pending` configuração.
1. O `config-agent` lê as propriedades de configuração e cria o namespace de destino.
1. O arco do Azure `controller-manager` prepara uma conta de serviço kubernetes com a permissão apropriada ( `cluster` ou `namespace` escopo) e, em seguida, implanta uma instância do `flux` .
1. Se usar a opção de SSH com chaves geradas por fluxo, `flux` o gerará uma chave SSH e registrará a chave pública.
1. O `config-agent` status de relatórios de volta para o `sourceControlConfiguration` recurso no Azure.

Enquanto o processo de provisionamento ocorre, `sourceControlConfiguration` passa por algumas alterações de estado. Monitore o progresso com o comando `az k8sconfiguration show ...` acima:

| Alteração de estágio | Descrição |
| ------------- | ------------- |
| `complianceStatus`-> `Pending` | Representa os Estados inicial e em andamento. |
| `complianceStatus` -> `Installed`  | `config-agent` o foi capaz de configurar com êxito o cluster e implantá-lo `flux` sem erros. |
| `complianceStatus` -> `Failed` | `config-agent` encontrado um erro ao implantar `flux` , os detalhes devem estar disponíveis no `complianceStatus.message` corpo da resposta. |

## <a name="apply-configuration-from-a-private-git-repository"></a>Aplicar a configuração de um repositório git privado

Se você estiver usando um repositório git privado, precisará configurar a chave pública SSH em seu repositório. A chave pública SSH será aquela que o fluxo gera ou aquela que você fornecer. Você pode configurar a chave pública no repositório git específico ou no usuário do git que tem acesso ao repositório. 

### <a name="get-your-own-public-key"></a>Obtenha sua própria chave pública

Se você gerou suas próprias chaves SSH, você já tem as chaves públicas e privadas.

#### <a name="get-the-public-key-using-azure-cli"></a>Obter a chave pública usando a CLI do Azure 

O seguinte será útil se o fluxo gerar as chaves.

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

#### <a name="get-the-public-key-from-the-azure-portal"></a>Obter a chave pública por meio do portal do Azure

O seguinte será útil se o fluxo gerar as chaves.

1. No portal do Azure, navegue até o recurso de cluster conectado.
2. Na página de recursos, selecione "GitOps" e veja a lista de configurações para este cluster.
3. Selecione a configuração que usa o repositório git privado.
4. Na janela de contexto exibida, na parte inferior da janela, copie a **Chave pública do repositório**.

#### <a name="add-public-key-using-github"></a>Adicionar chave pública usando o GitHub

Use uma das seguintes opções:

* Opção 1: adicionar a chave pública à sua conta de usuário (aplica-se a todos os repositórios em sua conta):  
    1. Abra o GitHub e clique no ícone do seu perfil no canto superior direito da página.
    2. Clique em **Configurações**.
    3. Clique nas **chaves SSH e GPG**.
    4. Clique em **nova chave SSH**.
    5. Forneça um título.
    6. Cole a chave pública sem aspas ao redor.
    7. Clique em **Adicionar chave SSH**.

* Opção 2: Adicione a chave pública como uma chave de implantação ao repositório git (aplica-se somente a este repositório):  
    1. Abra o GitHub e navegue até seu repositório.
    1. Clique em **Configurações**.
    1. Clique em **implantar chaves**.
    1. Clique em **Adicionar implantar chave**.
    1. Forneça um título.
    1. Marque **permitir acesso de gravação**.
    1. Cole a chave pública sem aspas ao redor.
    1. Clique em **Adicionar chave**.

#### <a name="add-public-key-using-an-azure-devops-repository"></a>Adicionar chave pública usando um repositório DevOps do Azure

Use as etapas a seguir para adicionar a chave às suas chaves SSH:

1. Em **configurações do usuário** no canto superior direito (ao lado da imagem do perfil), clique em **chaves públicas SSH**.
1. Selecione  **+ nova chave**.
1. Forneça um nome.
1. Cole a chave pública sem aspas ao redor.
1. Clique em **Adicionar**.

## <a name="validate-the-kubernetes-configuration"></a>Validar a configuração do Kubernetes

Depois `config-agent` que o tiver instalado a `flux` instância, os recursos mantidos no repositório git devem começar a fluir para o cluster. Verifique se os namespaces, as implantações e os recursos foram criados com o seguinte comando:

```console
kubectl get ns --show-labels
```

**Saída:**

```console
NAME              STATUS   AGE    LABELS
azure-arc         Active   24h    <none>
cluster-config    Active   177m   <none>
default           Active   29h    <none>
itops             Active   177m   fluxcd.io/sync-gc-mark=sha256.9oYk8yEsRwWkR09n8eJCRNafckASgghAsUWgXWEQ9es,name=itops
kube-node-lease   Active   29h    <none>
kube-public       Active   29h    <none>
kube-system       Active   29h    <none>
team-a            Active   177m   fluxcd.io/sync-gc-mark=sha256.CS5boSi8kg_vyxfAeu7Das5harSy1i0gc2fodD7YDqA,name=team-a
team-b            Active   177m   fluxcd.io/sync-gc-mark=sha256.vF36thDIFnDDI2VEttBp5jgdxvEuaLmm7yT_cuA2UEw,name=team-b
```

Podemos ver que os namespaces `team-a`, `team-b`, `itops` e `cluster-config` foram criados.

O operador `flux` foi implantado no namespace `cluster-config`, conforme indicado por nosso `sourceControlConfig`:

```console
kubectl -n cluster-config get deploy  -o wide
```

**Saída:**

```console
NAME             READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES                         SELECTOR
cluster-config   1/1     1            1           3h    flux         docker.io/fluxcd/flux:1.16.0   instanceName=cluster-config,name=flux
memcached        1/1     1            1           3h    memcached    memcached:1.5.15               name=memcached
```

## <a name="further-exploration"></a>Exploração adicional

Você pode explorar os outros recursos implantados como parte do repositório de configuração usando:

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```

## <a name="delete-a-configuration"></a>Excluir uma configuração

Exclua um `sourceControlConfiguration` usando o CLI do Azure ou portal do Azure.  Depois de iniciar o comando delete, o `sourceControlConfiguration` recurso será excluído imediatamente no Azure. A exclusão completa dos objetos associados do cluster deve ocorrer em 10 minutos. Se o `sourceControlConfiguration` estiver em um estado de falha quando removido, a exclusão completa de objetos associados poderá levar até uma hora.

> [!NOTE]
> Depois que `sourceControlConfiguration` um `namespace` escopo with é criado, os usuários com `edit` Associação de função no namespace podem implantar cargas de trabalho nesse namespace. Quando isso `sourceControlConfiguration` com o escopo do namespace é excluído, o namespace é deixado intacto e não será excluído para evitar a interrupção dessas outras cargas de trabalho. Se necessário, você pode excluir esse namespace manualmente com `kubectl` .  
> As alterações no cluster que foram o resultado de implantações do repositório git rastreado não são excluídas quando o `sourceControlConfiguration` é excluído.

```azurecli
az k8sconfiguration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

**Saída:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
```

## <a name="next-steps"></a>Próximas etapas

- [Usar o Helm com a configuração de controle do código-fonte](./use-gitops-with-helm.md)
- [Usar Azure Policy para controlar a configuração do cluster](./use-azure-policy.md)