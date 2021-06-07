---
title: 'Tutorial: Implantar configurações usando o GitOps em um cluster do Kubernetes habilitado para Azure Arc'
description: Este tutorial demonstra como aplicar configurações em um cluster do Kubernetes habilitado para Azure Arc. Para obter uma abordagem conceitual sobre esse processo, confira o artigo Configurações e GitOps – Kubernetes habilitado para Azure Arc.
author: shashankbarsin
ms.author: shasb
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial , devx-track-azurecli
ms.openlocfilehash: 0f1172ffa0d29734e7ec005bf2812eeca215aa0d
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484159"
---
# <a name="tutorial-deploy-configurations-using-gitops-on-an-azure-arc-enabled-kubernetes-cluster"></a>Tutorial: Implantar configurações usando o GitOps em um cluster do Kubernetes habilitado para Azure Arc 

Neste tutorial, você aplicará configurações usando o GitOps em um cluster do Kubernetes habilitado para Azure Arc. Você aprenderá a:

> [!div class="checklist"]
> * Criar uma configuração em um cluster do Kubernetes habilitado para Azure Arc usando um repositório Git de exemplo.
> * Confirme se a configuração foi criada com êxito.
> * Aplicar a configuração de um repositório Git privado.
> * Valide a configuração do Kubernetes.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Um cluster conectado do Kubernetes habilitado para Azure Arc.
    - Se você ainda não conectou um cluster, siga nosso [guia de início rápido Conectar um cluster do Kubernetes habilitado para Azure Arc](quickstart-connect-cluster.md).
- Uma compreensão dos benefícios e da arquitetura desse recurso. Leia mais no [artigo Configurações e GitOps – Kubernetes habilitado para Azure Arc](conceptual-configurations.md).
- Instale a extensão `k8s-configuration` da CLI do Azure, com versão >= 1.0.0:
  
  ```azurecli
  az extension add --name k8s-configuration
  ```

    >[!TIP]
    > Se a extensão `k8s-configuration` já estiver instalada, você poderá atualizá-la para a versão mais recente usando o comando a seguir – `az extension update --name k8s-configuration`

## <a name="create-a-configuration"></a>Criar uma configuração

O [repositório de exemplo](https://github.com/Azure/arc-k8s-demo) usado neste artigo é estruturado em torno da persona de um operador de cluster. Os manifestos desse repositório provisionam alguns namespaces, implantam cargas de trabalho e fornecem uma configuração específica da equipe. O uso desse repositório com o GitOps cria os seguintes recursos no cluster:

* Namespaces: `cluster-config`, `team-a`, `team-b`
* Implantação: `cluster-config/azure-vote`
* ConfigMap: `team-a/endpoints`

O `config-agent` pesquisa no Azure configurações novas ou atualizadas. Essa tarefa levará até 30 segundos.

Se você estiver associando um repositório privado à configuração, conclua as etapas abaixo em [Aplicar a configuração de um repositório Git privado](#apply-configuration-from-a-private-git-repository).

## <a name="use-azure-cli"></a>Usar a CLI do Azure
Use a extensão da CLI do Azure para `k8s-configuration` a fim de vincular um cluster conectado ao [repositório Git de exemplo](https://github.com/Azure/arc-k8s-demo). 
1. Nomeie essa configuração `cluster-config`.
1. Instrua o agente a implantar o operador no namespace `cluster-config`.
1. Forneça ao operador as permissões de `cluster-admin`.

    ```azurecli
    az k8s-configuration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
    ```

    ```output
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

### <a name="use-a-public-git-repository"></a>Usar um repositório Git público

| Parâmetro | Formatar |
| ------------- | ------------- |
| `--repository-url` | http[s]://server/repo[.git] ou git://server/repo[.git]

### <a name="use-a-private-git-repository-with-ssh-and-flux-created-keys"></a>Usar um repositório Git privado com o SSH e as chaves criadas pelo Flux

Adicione a chave pública gerada pelo Flux à conta de usuário no provedor de serviços Git. Se a chave for adicionada ao repositório em vez da conta de usuário, use `git@` no lugar de `user@` na URL. 

Vá para a seção [Aplicar a configuração de um repositório Git privado](#apply-configuration-from-a-private-git-repository) para obter mais detalhes.


| Parâmetro | Formatar | Observações
| ------------- | ------------- | ------------- |
| `--repository-url` | ssh://user@server/repo[.git] ou user@server:repo[.git] | `git@` poderá substituir `user@`

### <a name="use-a-private-git-repository-with-ssh-and-user-provided-keys"></a>Usar um repositório Git privado com o SSH e as chaves fornecidas pelo usuário

Forneça uma chave privada própria diretamente ou em um arquivo. A chave precisa estar no [formato PEM](https://aka.ms/PEMformat) e terminar com uma nova linha (\n). 

Adicione a chave pública associada à conta de usuário no seu provedor de serviços Git. Se a chave for adicionada ao repositório em vez da conta de usuário, use `git@` no lugar de `user@`. 

Vá para a seção [Aplicar a configuração de um repositório Git privado](#apply-configuration-from-a-private-git-repository) para obter mais detalhes.  

| Parâmetro | Formatar | Observações |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[.git] ou user@server:repo[.git] | `git@` poderá substituir `user@` |
| `--ssh-private-key` | chave codificada em Base64 no [formato PEM](https://aka.ms/PEMformat) | Fornecer a chave diretamente |
| `--ssh-private-key-file` | caminho completo para o arquivo local | Forneça o caminho completo para o arquivo local que contém a chave de formato PEM

### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>Usar um host do Git privado com o SSH e hosts conhecidos fornecidos pelo usuário

O operador do Flux mantém uma lista de hosts do Git comuns no arquivo de hosts conhecidos para autenticar o repositório Git antes de estabelecer a conexão SSH. Se você estiver usando um repositório Git *incomum* ou um host do Git próprio, forneça a chave de host para que o Flux possa identificar seu repositório. 

Assim como as chaves privadas, você pode fornecer o conteúdo de known_hosts diretamente ou em um arquivo. Ao fornecer um conteúdo próprio, use as [especificações de formato de conteúdo known_hosts](https://aka.ms/KnownHostsFormat), juntamente com um dos cenários de chave SSH descritos acima.

| Parâmetro | Formatar | Observações |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[.git] ou user@server:repo[.git] | `git@` poderá substituir `user@` |
| `--ssh-known-hosts` | codificado em Base64 | Fornecer o conteúdo de hosts conhecidos diretamente |
| `--ssh-known-hosts-file` | caminho completo para o arquivo local | Fornecer o conteúdo de hosts conhecidos em um arquivo local |

### <a name="use-a-private-git-repository-with-https"></a>Usar um repositório Git privado com HTTPS

| Parâmetro | Formatar | Observações |
| ------------- | ------------- | ------------- |
| `--repository-url` | https://server/repo [.git] | HTTPS com autenticação básica |
| `--https-user` | bruto ou codificado em Base64 | Nome de usuário HTTPS |
| `--https-key` | bruto ou codificado em Base64 | Token de acesso pessoal HTTPS ou senha

>[!NOTE]
>* O gráfico do operador Helm versão 1.2.0 e posterior dá suporte à autenticação privada da versão HTTPS do Helm.
>* A versão HTTPS do Helm não é compatível com clusters gerenciados do AKS.
>* Caso você precise do Flux para acessar o repositório Git por meio do proxy, atualize os agentes do Azure Arc com as configurações de proxy. Para obter mais informações, confira [Conectar-se usando um servidor proxy de saída](./quickstart-connect-cluster.md#connect-using-an-outbound-proxy-server).


## <a name="additional-parameters"></a>Parâmetros Adicionais

Personalize a configuração com os seguintes parâmetros opcionais:

| Parâmetro | Descrição |
| ------------- | ------------- |
| `--enable-helm-operator`| Alterne para habilitar o suporte para implantações de gráficos do Helm. |
| `--helm-operator-params` | Valores de gráfico do operador Helm (se estiver habilitado). Por exemplo, `--set helm.versions=v3`. |
| `--helm-operator-chart-version` | Versão de gráfico do operador Helm (se estiver habilitado). Use a versão 1.2.0 e posterior. Padrão: '1.2.0'. |
| `--operator-namespace` | Nome do namespace do operador. Padrão: 'default'. Máximo: 23 caracteres. |
| `--operator-params` | Parâmetros do operador. Deve ser fornecido entre aspas simples. Por exemplo, ```--operator-params='--git-readonly --sync-garbage-collection --git-branch=main'``` 

### <a name="options-supported-in----operator-params"></a>Opções compatíveis com `--operator-params`:

| Opção | Descrição |
| ------------- | ------------- |
| `--git-branch`  | Branch do repositório Git a ser usado para manifestos do Kubernetes. O padrão é 'master'. Os repositórios mais recentes têm o branch raiz chamado `main`. Nesse caso, você precisará definir `--git-branch=main`. |
| `--git-path`  | Caminho relativo no repositório Git para o Flux para localizar manifestos do Kubernetes. |
| `--git-readonly` | O repositório Git será considerado somente leitura. O Flux não tentará fazer gravações nele. |
| `--manifest-generation`  | Se habilitado, o Flux procurará .flux.yaml e executará o Kustomize ou outros geradores de manifesto. |
| `--git-poll-interval`  | Período durante o qual será feita a pesquisa de novos commits no repositório Git. O padrão é `5m` (5 minutos). |
| `--sync-garbage-collection`  | Se habilitado, o Flux excluirá os recursos que ele criou, mas não estará mais presente no Git. |
| `--git-label`  | Rótulo usado para acompanhar o progresso da sincronização. Usado para marcar o GIT branch.  O padrão é `flux-sync`. |
| `--git-user`  | Nome de usuário para commit no Git. |
| `--git-email`  | Email a ser usado para commit no Git. 

Caso não deseje que o Flux faça gravações no repositório e `--git-user` ou `--git-email` não seja definido, `--git-readonly` será definido automaticamente.

Para obter mais informações, confira a [documentação do Flux](https://aka.ms/FluxcdReadme).

> [!TIP]
> Crie uma configuração no portal do Azure na guia **GitOps** do recurso do Kubernetes habilitado para Azure Arc.

## <a name="validate-the-configuration"></a>Validar a configuração

Use a CLI do Azure para confirmar se a configuração foi criada com êxito.

```azurecli
az k8s-configuration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

O recurso de configuração será atualizado com status de conformidade, mensagens e informações de depuração.

```output
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

Quando uma configuração é criada ou atualizada, algumas coisas acontecem:

1. O `config-agent` do Azure Arc monitora o Azure Resource Manager quanto a configurações novas ou atualizadas (`Microsoft.KubernetesConfiguration/sourceControlConfigurations`) e detecta a nova configuração `Pending`.
1. O `config-agent` lê as propriedades de configuração e cria o namespace de destino.
1. O `controller-manager` do Azure Arc cria uma conta de serviço do Kubernetes e a mapeia para [ClusterRoleBinding ou RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/) para as permissões apropriadas (escopo `cluster` ou `namespace`). Depois, ele implanta uma instância do `flux`.
1. Se a opção do SSH com chaves geradas pelo Flux for usada, o `flux` vai gerar uma chave SSH e registrar a chave pública em log.
1. O `config-agent` relata o status novamente para o recurso de configuração no Azure.

Enquanto o processo de provisionamento ocorre, o recurso de configuração passa por algumas alterações de estado. Monitore o progresso com o comando `az k8s-configuration show ...` acima:

| Alteração de fase | Descrição |
| ------------- | ------------- |
| `complianceStatus`-> `Pending` | Representa os estados inicial e em andamento. |
| `complianceStatus` -> `Installed`  | `config-agent` configurou o cluster com êxito e implantou o `flux` sem erros. |
| `complianceStatus` -> `Failed` | Ocorreu um erro no `config-agent` ao implantar o `flux`. Os detalhes são fornecidos no corpo da resposta de `complianceStatus.message`. |

## <a name="apply-configuration-from-a-private-git-repository"></a>Aplicar a configuração de um repositório Git privado

Se você estiver usando um repositório Git privado, configure a chave pública SSH no seu repositório. Forneça a chave pública SSH ou permita que o Flux gere uma. Você pode configurar a chave pública no repositório Git específico ou no usuário do Git que tem acesso ao repositório. 

### <a name="get-your-own-public-key"></a>Obter uma chave pública própria

Se você gerou chaves SSH próprias, já tem as chaves públicas e privadas.

#### <a name="get-the-public-key-using-azure-cli"></a>Obter a chave pública usando a CLI do Azure 

Use o comando a seguir na CLI do Azure se o Flux estiver gerando as chaves.

```console
$ az k8s-configuration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

#### <a name="get-the-public-key-from-the-azure-portal"></a>Obter a chave pública por meio do portal do Azure

Execute as etapas a seguir no portal do Azure se o Flux estiver gerando as chaves.

1. No portal do Azure, navegue até o recurso de cluster conectado.
2. Na página de recursos, selecione "GitOps" e confira a lista de configurações desse cluster.
3. Selecione a configuração que usa o repositório git privado.
4. Na janela de contexto exibida, na parte inferior da janela, copie a **Chave pública do repositório**.

#### <a name="add-public-key-using-github"></a>Adicionar uma chave pública usando o GitHub

Use uma das seguintes opções:

* Opção 1: adicionar a chave pública à sua conta de usuário (aplica-se a todos os repositórios na sua conta):  
    1. Abra o GitHub e clique no ícone do seu perfil no canto superior direito da página.
    2. Clique em **Configurações**.
    3. Clique nas **Chaves SSH e GPG**.
    4. Clique em **Nova chave SSH**.
    5. Forneça um título.
    6. Cole a chave pública sem as aspas.
    7. Clique em **Adicionar chave SSH**.

* Opção 2: adicionar a chave pública como uma chave de implantação ao repositório Git (aplica-se somente a esse repositório):  
    1. Abra o GitHub e procure o repositório.
    1. Clique em **Configurações**.
    1. Clique em **Chaves de implantação**.
    1. Clique em **Adicionar chave de implantação**.
    1. Forneça um título.
    1. Marque **Permitir acesso de gravação**.
    1. Cole a chave pública sem as aspas.
    1. Clique em **Adicionar chave**.

#### <a name="add-public-key-using-an-azure-devops-repository"></a>Adicionar uma chave pública usando um repositório do Azure DevOps

Use as seguintes etapas para adicionar a chave às chaves SSH:

1. Em **Configurações de Usuário** no canto superior direito (ao lado da imagem de perfil), clique em **Chaves públicas SSH**.
1. Selecione **+ Nova Chave**.
1. Forneça um nome.
1. Cole a chave pública sem as aspas.
1. Clique em **Adicionar**.

## <a name="validate-the-kubernetes-configuration"></a>Validar a configuração do Kubernetes

Depois que o `config-agent` instalar a instância do `flux`, os recursos mantidos no repositório Git deverão começar a fluir para o cluster. Verifique se os namespaces, as implantações e os recursos foram criados com o seguinte comando:

```console
kubectl get ns --show-labels
```

```output
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

O operador `flux` foi implantado no namespace `cluster-config`, conforme indicado pelo recurso de configuração:

```console
kubectl -n cluster-config get deploy  -o wide
```

```output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES                         SELECTOR
cluster-config   1/1     1            1           3h    flux         docker.io/fluxcd/flux:1.16.0   instanceName=cluster-config,name=flux
memcached        1/1     1            1           3h    memcached    memcached:1.5.15               name=memcached
```

## <a name="further-exploration"></a>Mais explorações

Explore outros recursos implantados como parte do repositório de configuração usando:

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```
## <a name="clean-up-resources"></a>Limpar os recursos

Exclua uma configuração usando a CLI do Azure ou o portal do Azure. Depois que você executar o comando delete, o recurso de configuração será excluído imediatamente do Azure. A exclusão completa dos objetos associados do cluster deverá ocorrer em até 10 minutos. Se a configuração estiver em um estado de falha quando for removida, a exclusão completa dos objetos associados poderá levar até uma hora.

Quando uma configuração com o escopo `namespace` é excluída, o namespace não é excluído pelo Azure Arc para evitar a interrupção de cargas de trabalho existentes. Se necessário, exclua esse namespace manualmente usando `kubectl`.

```azurecli
az k8s-configuration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

> [!NOTE]
> Todas as alterações no cluster que foram o resultado de implantações do repositório Git rastreado não são excluídas quando a configuração é excluída.

## <a name="next-steps"></a>Próximas etapas

Avance para o próximo tutorial para saber como implementar CI/CD com GitOps.
> [!div class="nextstepaction"]
> [Implementar CI/CD com GitOps](./tutorial-gitops-ci-cd.md)
