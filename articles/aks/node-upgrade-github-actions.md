---
title: Manipular atualizações de nó AKS com ações do GitHub
titleSuffix: Azure Kubernetes Service
description: Saiba como atualizar nós do AKS usando ações do GitHub
services: container-service
ms.topic: article
ms.date: 11/27/2020
ms.openlocfilehash: 6876cf1e5044246492e249d8a61060cbeac46f96
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98217950"
---
# <a name="apply-security-updates-to-azure-kubernetes-service-aks-nodes-automatically-using-github-actions"></a>Aplicar atualizações de segurança aos nós do AKS (serviço kubernetes do Azure) automaticamente usando ações do GitHub

As atualizações de segurança são uma parte fundamental da manutenção da segurança e da conformidade do seu cluster AKS com as correções mais recentes para o sistema operacional subjacente. Essas atualizações incluem correções de segurança do SO ou atualizações de kernel. Algumas atualizações exigem uma reinicialização do nó para concluir o processo.

`az aks upgrade`A execução oferece uma maneira sem tempo de inatividade para aplicar atualizações. O comando trata da aplicação das atualizações mais recentes a todos os nós do cluster, cordoning e drenando o tráfego para os nós e reiniciando os nós e, em seguida, permitindo o tráfego para os nós atualizados. Se você atualizar seus nós usando um método diferente, o AKS não reiniciará os nós automaticamente.

> [!NOTE]
> A principal diferença entre `az aks upgrade` quando usada com o `--node-image-only` sinalizador é que, quando ela é usada, somente as imagens de nó serão atualizadas. Se omitido, as imagens de nó e a versão do plano de controle kubernetes serão atualizadas. Você pode verificar [os documentos para atualizações gerenciadas em nós][managed-node-upgrades-article] e [os documentos para atualizações de cluster][cluster-upgrades-article] para obter informações mais detalhadas.

Todos os nós do kubernetes são executados em uma VM (máquina virtual) do Azure padrão. Essas VMs podem ser baseadas no Windows ou no Linux. As VMs baseadas em Linux usam uma imagem do Ubuntu, com o sistema operacional configurado para verificar automaticamente se há atualizações todas as noites.

Quando você usa o `az aks upgrade` comando, CLI do Azure cria um surto de novos nós com as atualizações de segurança e kernel mais recentes, esses nós são inicialmente isoladosdos para impedir que todos os aplicativos sejam agendados para eles até que a atualização seja concluída. Após a conclusão, o Azure cordons (torna o nó indisponível para agendamento de novas cargas de trabalho) e drena (move as cargas de trabalho existentes para outro nó) os nós mais antigos e desenvolvem os novos, transferindo efetivamente todos os aplicativos agendados para os novos nós.

Esse processo é melhor do que atualizar os kernels baseados em Linux manualmente, pois o Linux requer uma reinicialização quando uma nova atualização do kernel é instalada. Se você atualizar o sistema operacional manualmente, também precisará reinicializar a VM, cordoning-la manualmente e drenar todos os aplicativos.

Este artigo mostra como você pode automatizar o processo de atualização de nós AKS. Você usará ações do GitHub e CLI do Azure para criar uma tarefa de atualização baseada em `cron` que é executada automaticamente.

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se precisar de um cluster do AKS, veja o guia de início rápido do AKS [usando a CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

A CLI do Azure versão 2.0.59 ou posterior também precisa estar instalada e configurada. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][install-azure-cli].

Este artigo também pressupõe que você tenha uma conta do [GitHub][github] para criar suas ações no.

## <a name="create-a-timed-github-action"></a>Criar uma ação do GitHub cronometrado

`cron` é um utilitário que permite executar um conjunto de comandos, ou trabalho, em um agendamento automatizado. Para criar um trabalho para atualizar seus nós do AKS em um agendamento automatizado, você precisará de um repositório para hospedar suas ações. Normalmente, as ações do GitHub são configuradas no mesmo repositório que o seu aplicativo, mas você pode usar qualquer repositório. Para este artigo, vamos usar seu [repositório de perfis][profile-repository]. Se você não tiver um, crie um novo repositório com o mesmo nome do seu nome de usuário do GitHub.

1. Navegue até o repositório no GitHub
1. Clique na guia **ações** na parte superior da página.
1. Se você já configurou um fluxo de trabalho nesse repositório, será direcionado para a lista de execuções concluídas, nesse caso, clique no botão **novo fluxo de trabalho** . Se esse for seu primeiro fluxo de trabalho no repositório, o GitHub apresentará alguns modelos de projeto e clicará no link **configurar um fluxo de trabalho por conta própria** abaixo do texto de descrição.
1. Altere o fluxo de trabalho `name` e as `on` marcas semelhantes à mostrada abaixo. As ações do GitHub usam a mesma [sintaxe de POSIX cron][cron-syntax] que qualquer sistema baseado em Linux. Neste cronograma, estamos dizendo ao fluxo de trabalho para ser executado a cada 15 dias em 3am.

    ```yml
    name: Upgrade cluster node images
    on:
      schedule:
        - cron: '0 3 */15 * *'
    ```

1. Crie um novo trabalho usando o abaixo. Esse trabalho é nomeado `upgrade-node` , é executado em um agente do Ubuntu e se conectará à sua conta do CLI do Azure para executar as etapas necessárias para atualizar os nós.

    ```yml
    name: Upgrade cluster node images

    on:
      schedule:
        - cron: '0 3 */15 * *'

    jobs:
      upgrade-node:
        runs-on: ubuntu-latest
    ```

## <a name="set-up-the-azure-cli-in-the-workflow"></a>Configurar o CLI do Azure no fluxo de trabalho

Na `steps` chave, você definirá todos os trabalhos que o fluxo de trabalho executará para atualizar os nós.

Baixe e entre no CLI do Azure.

1. No lado direito da tela ações do GitHub, localize a *barra de pesquisa do Marketplace* e digite **"logon do Azure"**.
1. Você obterá como resultado, uma ação chamada **logon do Azure** publicada **pelo Azure**:

      :::image type="content" source="media/node-upgrade-github-actions/azure-login-search.png" alt-text="Resultados da pesquisa mostrando duas linhas, a primeira ação é chamada de &quot;Azure login e o segundo&quot; registro de contêiner do Azure login":::

1. Clique em **logon do Azure**. Na próxima tela, clique no **ícone de cópia** no canto superior direito do exemplo de código.

    :::image type="content" source="media/node-upgrade-github-actions/azure-login.png" alt-text="Painel de resultados de ação de logon do Azure com exemplo de código abaixo, quadrado vermelho em torno de um ícone de cópia realça o ponto de clique":::

1. Cole o seguinte na `steps` chave:

      ```yml
      name: Upgrade cluster node images

      on:
        schedule:
          - cron: '0 3 */15 * *'

      jobs:
        upgrade-node:
          runs-on: ubuntu-latest

          steps:
            - name: Azure Login
              uses: Azure/login@v1.1
              with:
                creds: ${{ secrets.AZURE_CREDENTIALS }}
      ```

1. No CLI do Azure, execute o comando a seguir para gerar um novo nome de usuário e senha.

    ```azurecli-interactive
    az ad sp create-for-rbac -o json
    ```

    A saída deve ser semelhante ao JSON a seguir:

    ```output
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-xxxx-xx-xx-xx-xx-xx",
      "name": "http://azure-cli-xxxx-xx-xx-xx-xx-xx",
      "password": "xXxXxXxXx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. **Em uma nova janela do navegador** , navegue até o repositório GitHub e abra a guia **configurações** do repositório. Clique em **segredos** e, em seguida, clique em **novo repositório segredo**.
1. Para o *Nome*, use `AZURE_CREDENTIALS`.
1.  Para *valor*, adicione todo o conteúdo da saída da etapa anterior em que você criou um novo nome de usuário e senha.

    :::image type="content" source="media/node-upgrade-github-actions/azure-credential-secret.png" alt-text="Formulário mostrando AZURE_CREDENTIALS como título secreto e a saída do comando executado colado como JSON":::

1. Clique em **Adicionar segredo**.

A CLI usada por sua ação será registrada em sua conta do Azure e pronta para executar comandos.

Para criar as etapas para executar comandos de CLI do Azure.

1. Navegue até a **página de pesquisa** no *Marketplace do GitHub* no lado direito da tela e pesquise *CLI do Azure ação*. Escolha *CLI do Azure ação pelo Azure*.

    :::image type="content" source="media/node-upgrade-github-actions/azure-cli-action.png" alt-text="Resultado da pesquisa para ' CLI do Azure Action ' com o primeiro resultado mostrado pelo Azure":::

1. Clique no botão Copiar no *resultado do Marketplace do GitHub* e cole o conteúdo da ação no editor principal, abaixo da etapa de *logon do Azure* , semelhante ao seguinte:

    ```yml
    name: Upgrade cluster node images

    on:
      schedule:
        - cron: '0 3 */15 * *'

    jobs:
      upgrade-node:
        runs-on: ubuntu-latest

        steps:
          - name: Azure Login
            uses: Azure/login@v1.1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
          - name: Upgrade node images
            uses: Azure/cli@v1.0.0
            with:
              inlineScript: az aks upgrade -g {resourceGroupName} -n {aksClusterName} --node-image-only --yes
    ```

    > [!TIP]
    > Você pode desassociar os `-g` `-n` parâmetros e do comando adicionando-os aos segredos semelhantes às etapas anteriores. Substitua os `{resourceGroupName}` `{aksClusterName}` espaços reservados e por suas contrapartes secretas, por exemplo, `${{secrets.RESOURCE_GROUP_NAME}}` e `${{secrets.AKS_CLUSTER_NAME}}`

1. Renomear o arquivo para `upgrade-node-images`.
1. Clique em **Iniciar confirmação**, adicione um título de mensagem e salve o fluxo de trabalho.

Depois de criar a confirmação, o fluxo de trabalho será salvo e estará pronto para execução.

> [!NOTE]
> Para atualizar um único pool de nós em vez de todos os pools de nós no cluster, adicione o `--name` parâmetro ao `az aks nodepool upgrade` comando para especificar o nome do pool de nós. Por exemplo:
> ```azurecli-interactive
> az aks nodepool upgrade -g {resourceGroupName} --cluster-name {aksClusterName} --name {{nodePoolName}} --node-image-only
> ```

## <a name="run-the-github-action-manually"></a>Executar a ação do GitHub manualmente

Você pode executar o fluxo de trabalho manualmente, além da execução agendada, adicionando um novo `on` gatilho chamado `workflow_dispatch` . O arquivo concluído deve ser semelhante ao YAML abaixo:

```yml
name: Upgrade cluster node images

on:
  schedule:
    - cron: '0 3 */15 * *'
  workflow_dispatch:

jobs:
  upgrade-node:
    runs-on: ubuntu-latest

    steps:
      - name: Azure Login
        uses: Azure/login@v1.1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      # Code for upgrading one or more node pools
```

## <a name="next-steps"></a>Próximas etapas

- Consulte as [notas de versão do AKS](https://github.com/Azure/AKS/releases) para obter informações sobre as imagens de nó mais recentes.
- Saiba como atualizar a versão do kubernetes com a [atualização de um cluster do AKS][cluster-upgrades-article].
- Saiba mais sobre vários pools de nós e como atualizar pools de nós com [criar e gerenciar vários pools de nós][use-multiple-node-pools].
- Saiba mais sobre [pools de nós do sistema][system-pools]
- Para saber como economizar custos usando instâncias de spot, consulte [Adicionar um pool de nós de spot a AKs][spot-pools]

<!-- LINKS - external -->
[github]: https://github.com
[profile-repository]: https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-github-profile/about-your-profile
[cron-syntax]: https://pubs.opengroup.org/onlinepubs/9699919799/utilities/crontab.html#tag_20_25_07

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[managed-node-upgrades-article]: node-image-upgrade.md
[cluster-upgrades-article]: upgrade-cluster.md
[system-pools]: use-system-pools.md
[spot-pools]: spot-node-pool.md
[use-multiple-node-pools]: use-multiple-node-pools.md
