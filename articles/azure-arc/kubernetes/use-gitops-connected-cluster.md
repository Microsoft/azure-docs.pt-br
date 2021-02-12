---
title: Implantar configurações usando o GitOps no cluster do Kubernetes habilitado para Arc (versão prévia)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Usar o GitOps para configurar um cluster kubernetes habilitado para arco do Azure (versão prévia)
keywords: GitOps, kubernetes, K8s, Azure, Arc, serviço do Azure kubernetes, AKS, contêineres
ms.openlocfilehash: 72dc42fffb3653de81477fa504c11b9b0328d2eb
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988708"
---
# <a name="deploy-configurations-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Implantar configurações usando o GitOps no cluster do Kubernetes habilitado para Arc (versão prévia)

GitOps, como se refere a kubernetes, é a prática de declarar o estado desejado da configuração de kubernetes (implantações, namespaces, etc.) em um repositório git seguido por uma implantação baseada em pesquisa e recepção dessas configurações no cluster usando um operador. Este documento aborda a configuração de fluxos de trabalho em clusters kubernetes habilitados para Arc do Azure.

A conexão entre o cluster e um repositório Git é criada no Azure Resource Manager como um `Microsoft.KubernetesConfiguration/sourceControlConfigurations` recurso de extensão. As propriedades de recurso de `sourceControlConfiguration` representam onde e como os recursos de Kubernetes devem fluir do Git para o cluster. Os `sourceControlConfiguration` dados são armazenados criptografados em repouso em um banco de dados Azure Cosmos DB para garantir a confidencialidade dos dados.

A `config-agent` execução no seu cluster é responsável por assistir a recursos de extensão novos ou atualizados `sourceControlConfiguration` no recurso de kubernetes habilitado para Arc do Azure, para implantar um operador de fluxo para observar o repositório Git para cada um `sourceControlConfiguration` e aplicar quaisquer atualizações feitas a qualquer um `sourceControlConfiguration` . É possível criar vários `sourceControlConfiguration` recursos no mesmo cluster de kubernetes habilitado para o Azure ARC para obter multilocação. Você pode criar cada `sourceControlConfiguration` um com um `namespace` escopo diferente para limitar as implantações nos respectivos namespaces.

O repositório git pode conter manifestos de formato YAML que descrevem os recursos de kubernetes válidos, incluindo namespaces, ConfigMaps, implantações, DaemonSets, etc.  Ele também pode conter gráficos Helm para implantar aplicativos. Um conjunto comum de cenários inclui a definição de uma configuração de linha de base para sua organização, que pode incluir funções e associações comuns do Azure, agentes de monitoramento ou registro em log ou serviços de todo o cluster.

O mesmo padrão pode ser usado para gerenciar uma coleção maior de clusters, que podem ser implantados em ambientes heterogêneos. Por exemplo, você pode ter um repositório que defina a configuração da linha de base da sua organização e aplicá-lo a dezenas de clusters do Kubernetes de uma só vez. A [política do Azure pode automatizar](use-azure-policy.md) a criação de um `sourceControlConfiguration` com um conjunto específico de parâmetros em todos os recursos de kubernetes habilitados para o Azure ARC em um escopo (assinatura ou grupo de recursos).

Este guia de introdução orientará você pela aplicação de um conjunto de configurações com escopo de administrador de cluster.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tenha um cluster conectado ao Kubernetes e habilitado para Azure Arc. Se você precisar de um cluster conectado, confira o [guia de início rápido sobre conexão a um cluster](./connect-cluster.md).

## <a name="create-a-configuration"></a>Criar uma configuração

O [repositório de exemplo](https://github.com/Azure/arc-k8s-demo) usado neste documento é estruturado em todo o persona de um operador de cluster que gostaria de provisionar alguns namespaces, implantar uma carga de trabalho comum e fornecer uma configuração específica da equipe. O uso desse repositório cria os seguintes recursos no cluster:

**Namespaces:** `cluster-config`, `team-a`, `team-b`
**Implantação:** `cluster-config/azure-vote`
**ConfigMap** `team-a/endpoints`

O `config-agent` pesquisa o Azure para novo ou atualizado a `sourceControlConfiguration` cada 30 segundos, que é o tempo máximo gasto pelo `config-agent` para selecionar uma configuração nova ou atualizada.
Se você estiver associando um repositório privado com o `sourceControlConfiguration` , certifique-se de também concluir as etapas em [aplicar configuração de um repositório git privado](#apply-configuration-from-a-private-git-repository).

### <a name="using-azure-cli"></a>Usando a CLI do Azure

Use a extensão CLI do Azure para `k8sconfiguration` para vincular um cluster conectado ao [repositório git de exemplo](https://github.com/Azure/arc-k8s-demo). Daremos a essa configuração o nome `cluster-config`, instruiremos o agente a implantar o operador no namespace `cluster-config` e concederemos ao operador permissões de `cluster-admin`.

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
| --repositório-URL | http [s]://Server/repo [. git] ou git://Server/repo [. git]

#### <a name="use-a-private-git-repo-with-ssh-and-flux-created-keys"></a>Usar um repositório git privado com SSH e chaves criadas pelo fluxo

| Parâmetro | Formatar | Observações
| ------------- | ------------- | ------------- |
| --repositório-URL | ssh://user@server/repo[. git] ou user@server:repo [. git] | `git@` pode substituir por `user@`

> [!NOTE]
> A chave pública gerada pelo fluxo deve ser adicionada à conta de usuário em seu provedor de serviços git. Se a chave for adicionada ao repositório em vez de > a conta de usuário, use `git@` no lugar de `user@` na URL. [Exibir mais detalhes](#apply-configuration-from-a-private-git-repository)

#### <a name="use-a-private-git-repo-with-ssh-and-user-provided-keys"></a>Usar um repositório git privado com SSH e chaves fornecidas pelo usuário

| Parâmetro | Formatar | Observações |
| ------------- | ------------- | ------------- |
| --repositório-URL  | ssh://user@server/repo[. git] ou user@server:repo [. git] | `git@` pode substituir por `user@` |
| --SSH-privado-chave | chave codificada em base64 no [formato PEM](https://aka.ms/PEMformat) | Fornecer a chave diretamente |
| --SSH-Private-Key-File | caminho completo para o arquivo local | Forneça o caminho completo para o arquivo local que contém a chave de formato PEM

> [!NOTE]
> Forneça sua própria chave privada diretamente ou em um arquivo. A chave deve estar no [formato PEM](https://aka.ms/PEMformat) e terminar com nova linha (\n).  A chave pública associada deve ser adicionada à conta de usuário em seu provedor de serviços git. Se a chave for adicionada ao repositório em vez da conta de usuário, use `git@` no lugar de `user@` . [Exibir mais detalhes](#apply-configuration-from-a-private-git-repository)

#### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>Usar um host git privado com SSH e hosts conhecidos fornecidos pelo usuário

| Parâmetro | Formatar | Observações |
| ------------- | ------------- | ------------- |
| --repositório-URL  | ssh://user@server/repo[. git] ou user@server:repo [. git] | `git@` pode substituir por `user@` |
| --SSH-known-hosts | codificado em base64 | conteúdo de hosts conhecidos fornecido diretamente |
| --SSH-known-hosts-File | caminho completo para o arquivo local | conteúdo de hosts conhecidos fornecido em um arquivo local

> [!NOTE]
> O operador de fluxo mantém uma lista de hosts git comuns em seu arquivo de hosts conhecidos a fim de autenticar o repositório git antes de estabelecer a conexão SSH. Se você estiver usando um repositório git incomum ou seu próprio host git, talvez seja necessário fornecer a chave de host para garantir que o fluxo possa identificar seu repositório. Você pode fornecer o conteúdo de seus hosts conhecidos diretamente ou em um arquivo. [Exibir especificação de formato de conteúdo de hosts conhecidos](https://aka.ms/KnownHostsFormat).
> Você pode usar isso em conjunto com um dos cenários de chave SSH descritos acima.

#### <a name="use-a-private-git-repo-with-https"></a>Usar um repositório git privado com HTTPS

| Parâmetro | Formatar | Observações |
| ------------- | ------------- | ------------- |
| --repositório-URL | https://server/repo[. git] | HTTPS com autenticação básica |
| --https-usuário | brutos ou codificados em base64 | Nome de usuário HTTPS |
| --https-Key | brutos ou codificados em base64 | Token de acesso pessoal HTTPS ou senha

> [!NOTE]
> A autenticação privada de versão Helm HTTPS tem suporte apenas com o gráfico do operador Helm versão >= 1.2.0.  A versão 1.2.0 é usada por padrão.
> A autenticação privada de versão Helm HTTPS não tem suporte atualmente para clusters gerenciados dos serviços Kubernetess do Azure.
> Se precisar de fluxo para acessar o repositório git por meio do proxy, você precisará atualizar os agentes Arc do Azure com as configurações de proxy. [Mais informações](./connect-cluster.md#connect-using-an-outbound-proxy-server)

#### <a name="additional-parameters"></a>Parâmetros Adicionais

Para personalizar a configuração, aqui estão mais parâmetros que você pode usar:

`--enable-helm-operator`: comutador *opcional* para habilitar o suporte para implantações de gráficos Helm.

`--helm-operator-params`: valores de gráfico *opcionais* para o operador Helm (se estiver habilitado).  Por exemplo: '--set helm.versions=v3'.

`--helm-operator-version`: versão de gráfico *opcional* para o operador Helm (se estiver habilitada). Use ' 1.2.0 ' ou superior. Padrão: ' 1.2.0 '.

`--operator-namespace`: nome do namespace *opcional* do operador. Padrão: ' default '. Máximo de 23 caracteres.

`--operator-params`: parâmetros *opcionais* do operador. Deve ser fornecido entre aspas simples. Por exemplo, ```--operator-params='--git-readonly --sync-garbage-collection --git-branch=main' ```

Opções com suporte en -operator-params

| Opção | Descrição |
| ------------- | ------------- |
| --git-branch  | Ramificação do repositório git a ser usada para manifestos kubernetes. O padrão é 'master'. Os repositórios mais recentes têm o Branch raiz chamado ' Main '; nesse caso, você precisa definir--git-Branch = Main. |
| --git-path  | Caminho relativo dentro do repositório Git no Flux para localizar manifestos do Kubernetes. |
| --git-readonly | O repositório Git será considerado somente leitura. O Flux não tentará gravar nele. |
| --manifest-generation  | Se habilitado, o Flux procurará .flux.yaml e executará o Kustomize ou outros geradores de manifesto. |
| --git-poll-interval  | Período durante o qual a pesquisa de novos repositórios Git ocorrerá. O padrão é '5m' (cinco minutos). |
| --sync-garbage-collection  | Se habilitado, o Flux excluirá os recursos que ele criou, mas não estará mais presente no Git. |
| --git-label  | Rótulo para acompanhar o progresso da sincronização, usado para marcar a ramificação Git.  O padrão é 'flux-sync'. |
| --git-user  | Nome de usuário para confirmação de git. |
| --git-email  | Email a ser usado para confirmação de git. |

* Se '--git-user' ou '--git-email' não estiver definido (o que significa que você não deseja que o Flux grave no repositório), então --git-readonly será definido automaticamente (caso ainda não tenha sido definido).

Para obter mais informações, consulte a [documentação do fluxo](https://aka.ms/FluxcdReadme).

> [!TIP]
> É possível criar um sourceControlConfiguration no portal do Azure na guia **GitOps** do recurso kubernetes do Arc do Azure habilitado.

## <a name="validate-the-sourcecontrolconfiguration"></a>Validar o sourceControlConfiguration

Como usar a CLI do Azure para validar a criação do `sourceControlConfiguration` com sucesso.

```azurecli
az k8sconfiguration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

Observe que o recurso `sourceControlConfiguration` é atualizado com status de conformidade, mensagens e informações de depuração.

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

1. O arco do Azure `config-agent` está monitorando Azure Resource Manager para configurações novas ou atualizadas ( `Microsoft.KubernetesConfiguration/sourceControlConfigurations` ) e observa a nova `Pending` configuração.
1. O `config-agent` lê as propriedades de configuração e cria o namespace de destino.
1. O arco do Azure `controller-manager` prepara uma conta de serviço kubernetes com a permissão apropriada ( `cluster` ou `namespace` escopo) e, em seguida, implanta uma instância do `flux` .
1. Se usar a opção de SSH com chaves geradas por fluxo, `flux` o gerará uma chave SSH e registrará a chave pública.
1. O `config-agent` status de relatórios de volta para o `sourceControlConfiguration` recurso no Azure.

Enquanto o processo de provisionamento ocorre, `sourceControlConfiguration` passa por algumas alterações de estado. Monitore o progresso com o comando `az k8sconfiguration show ...` acima:

1. `complianceStatus` -> `Pending`: representa os estados inicial e em andamento
1. `complianceStatus` -> `Installed`: o `config-agent` conseguiu configurar o cluster com êxito e implantar `flux` sem erros
1. `complianceStatus` -> `Failed`: o `config-agent` encontrou um erro ao implantar o `flux`; os detalhes devem estar disponíveis no corpo da resposta `complianceStatus.message`

## <a name="apply-configuration-from-a-private-git-repository"></a>Aplicar a configuração de um repositório git privado

Se você estiver usando um repositório git privado, precisará configurar a chave pública SSH em seu repositório. Você pode configurar a chave pública no repositório git específico ou no usuário do git que tem acesso ao repositório. A chave pública SSH será aquela que você fornecer ou aquela que o fluxo gera.

**Obtenha sua própria chave pública**

Se você gerou suas próprias chaves SSH, você já tem as chaves públicas e privadas.

**Obter a chave pública usando CLI do Azure (útil se o fluxo gerar as chaves)**

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

**Obter a chave pública do portal do Azure (útil se o fluxo gerar as chaves)**

1. No portal do Azure, navegue até o recurso de cluster conectado.
2. Na página de recursos, selecione "GitOps" e veja a lista de configurações para este cluster.
3. Selecione a configuração que usa o repositório git privado.
4. Na janela de contexto exibida, na parte inferior da janela, copie a **Chave pública do repositório**.

Se você estiver usando o GitHub, use uma das duas opções a seguir:

**Opção 1: adicionar a chave pública à sua conta de usuário (aplica-se a todos os repositórios em sua conta)**

1. Abra o GitHub, clique no ícone do seu perfil no canto superior direito da página.
2. Clique em **configurações**
3. Clique nas **chaves SSH e GPG**
4. Clique em **nova chave SSH**
5. Forneça um título
6. Cole a chave pública (sem as aspas)
7. Clique em **Adicionar chave SSH**

**Opção 2: adicionar a chave pública como uma chave de implantação ao repositório git (aplica-se somente a este repositório)**

1. Abra o GitHub, navegue até seu repositório, para **configurações** e, em seguida, para **implantar chaves**
2. Clique em **Adicionar implantar chave**
3. Forneça um título
4. Marque **permitir acesso de gravação**
5. Cole a chave pública (sem as aspas)
6. Clique em **Adicionar chave**

**Se você está usando um repositório DevOps do Azure, adicione a chave às chaves SSH**

1. Em **Configurações de Usuário** no canto superior direito (ao lado da imagem de perfil), clique em **Chaves públicas SSH**
1. Selecione **+ Nova Chave**
1. Forneça um nome
1. Cole a chave pública sem as aspas
1. Clique em **Adicionar**

## <a name="validate-the-kubernetes-configuration"></a>Validar a configuração do Kubernetes

Depois `config-agent` que o tiver instalado a `flux` instância, os recursos mantidos no repositório git devem começar a fluir para o cluster. Verifique se os namespaces, as implantações e os recursos foram criados:

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

Você pode explorar outros recursos implantados como parte do repositório de configuração:

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```

## <a name="delete-a-configuration"></a>Excluir uma configuração

Exclua um `sourceControlConfiguration` usando o CLI do Azure ou portal do Azure.  Depois de iniciar o comando delete, o `sourceControlConfiguration` recurso será excluído imediatamente no Azure e a exclusão completa dos objetos associados do cluster deverá ocorrer dentro de 10 minutos.  Se o `sourceControlConfiguration` estiver em um estado de falha quando for excluído, a exclusão completa de objetos associados poderá levar até uma hora.

> [!NOTE]
> Depois que um sourceControlConfiguration com escopo de namespace é criado, é possível que os usuários com `edit` Associação de função no namespace implantem cargas de trabalho nesse namespace. Quando isso `sourceControlConfiguration` com o escopo do namespace é excluído, o namespace é deixado intacto e não será excluído para evitar a interrupção dessas outras cargas de trabalho.  Se necessário, você pode excluir esse namespace manualmente com kubectl.
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