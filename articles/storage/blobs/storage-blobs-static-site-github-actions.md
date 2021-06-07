---
title: Usar o GitHub Actions para implantar um site estático no Armazenamento do Azure
description: Hospedagem de sites estáticos do armazenamento do Azure com ações do GitHub
author: juliakm
ms.service: storage
ms.topic: how-to
ms.author: jukullam
ms.reviewer: dineshm
ms.date: 01/11/2021
ms.subservice: blobs
ms.custom: devx-track-javascript, github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 2192cdb3072edba2e5597a697feef99ba4d2070d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102210250"
---
# <a name="set-up-a-github-actions-workflow-to-deploy-your-static-website-in-azure-storage"></a>Configurar um fluxo de trabalho de GitHub Actions para implantar seu site estático no Armazenamento do Azure

Introdução às [ações do GitHub](https://docs.github.com/en/actions) usando um fluxo de trabalho para implantar um site estático em uma conta de armazenamento do Azure. Depois de configurar um fluxo de trabalho de ações do GitHub, você poderá implantar automaticamente seu site no Azure do GitHub quando fizer alterações no código do site.

> [!NOTE]
> Se você estiver usando [aplicativos Web estáticos do Azure](../../static-web-apps/index.yml), não será necessário configurar manualmente um fluxo de trabalho de ações do github.
> Os aplicativos Web estáticos do Azure criam automaticamente um fluxo de trabalho de ações do GitHub para você. 

## <a name="prerequisites"></a>Pré-requisitos

Uma assinatura do Azure e uma conta do GitHub. 

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Um repositório GitHub com seu código de site estático. Se você não tiver uma conta do GitHub, [inscreva-se gratuitamente](https://github.com/join).  
- Um site estático funcional hospedado no armazenamento do Azure. Saiba como [hospedar um site estático no armazenamento do Azure](storage-blob-static-website-how-to.md). Para seguir este exemplo, você também deve implantar a [CDN do Azure](static-website-content-delivery-network.md).

> [!NOTE]
> É comum usar uma CDN (rede de distribuição de conteúdo) para reduzir a latência para seus usuários em todo o mundo e reduzir o número de transações para sua conta de armazenamento. A implantação de conteúdo estático em um serviço de armazenamento baseado em nuvem pode reduzir a necessidade de uma instância de computação potencialmente cara. Para obter mais informações, consulte [padrão de Hospedagem de conteúdo estático](/azure/architecture/patterns/static-content-hosting).

## <a name="generate-deployment-credentials"></a>Gerar as credenciais de implantação

Crie uma [entidade de serviço](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) com o comando [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) na [CLI do Azure](/cli/azure/). Execute esse comando com o [Azure Cloud Shell](https://shell.azure.com/) no portal do Azure ou selecionando o botão **Experimentar**.

Substitua o espaço reservado `myStaticSite` pelo nome do seu site hospedado no armazenamento do Azure. 

```azurecli-interactive
   az ad sp create-for-rbac --name {myStaticSite} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} --sdk-auth
```

No exemplo acima, substitua os espaços reservados pela sua ID da assinatura e o nome do grupo de recursos. A saída é um objeto JSON com as credenciais de atribuição de função que fornecem acesso à sua conta de armazenamento semelhante à mostrada abaixo. Copie esse objeto JSON para uso posterior.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> É sempre uma boa prática permitir acesso mínimo. O escopo no exemplo anterior é limitado ao aplicativo do serviço de aplicativo específico e não ao grupo de recursos inteiro.

## <a name="configure-the-github-secret"></a>Configurar o segredo do GitHub

1. Em [GitHub](https://github.com/), procure seu repositório.

1. Selecione **Configurações > Segredos > Novo segredo**.

1. Cole toda a saída JSON do comando da CLI do Azure no campo valor do segredo. Nomeie o segredo como `AZURE_CREDENTIALS` .

    Ao configurar o arquivo de fluxo de trabalho posteriormente, você usa o segredo para o `creds` de entrada da ação de Logon do Azure. Por exemplo:

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

## <a name="add-your-workflow"></a>Adicionar seu fluxo de trabalho

1. Acesse **Ações** para seu repositório do GitHub. 

    :::image type="content" source="media/storage-blob-static-website/storage-blob-github-actions-header.png" alt-text="Item de menu de ações do GitHub":::

1. Selecione **Configurar seu fluxo de trabalho por conta própria**. 

1. Exclua tudo depois da seção `on:` do seu arquivo de fluxo de trabalho. Por exemplo, o fluxo de trabalho restante pode ter a aparência a seguir. 

    ```yaml
    name: CI

    on:
        push:
            branches: [ master ]
        pull_request:
            branches: [ master ]
    ```

1. Renomeie o fluxo de trabalho `Blob storage website CI` e adicione as ações de check-out e logon. Essas ações farão o check-out do código do site e a autenticação com o Azure usando o segredo do GitHub `AZURE_CREDENTIALS` criado anteriormente. 

    ```yaml
    name: Blob storage website CI

    on:
        push:
            branches: [ master ]
        pull_request:
            branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:            
        - uses: actions/checkout@v2
        - uses: azure/login@v1
          with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. Use a ação CLI do Azure para carregar seu código no armazenamento de BLOBs e para limpar o ponto de extremidade da CDN. Para `az storage blob upload-batch` , substitua o espaço reservado pelo nome da conta de armazenamento. O script será carregado no `$web` contêiner. Para `az cdn endpoint purge` , substitua os espaços reservados pelo nome do perfil CDN, pelo nome do ponto de extremidade da CDN e pelo grupo de recursos.

    ```yaml
        - name: Upload to blob storage
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Purge CDN endpoint
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
               az cdn endpoint purge --content-paths  "/*" --profile-name "CDN_PROFILE_NAME" --name "CDN_ENDPOINT" --resource-group "RESOURCE_GROUP"
    ``` 

1. Conclua o fluxo de trabalho adicionando uma ação para fazer logoff do Azure. Este é o fluxo de trabalho concluído. O arquivo será exibido na pasta `.github/workflows` do seu repositório.

    ```yaml
    name: Blob storage website CI

    on:
        push:
            branches: [ master ]
        pull_request:
            branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:            
        - uses: actions/checkout@v2
        - uses: azure/login@v1
          with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}

        - name: Upload to blob storage
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Purge CDN endpoint
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
               az cdn endpoint purge --content-paths  "/*" --profile-name "CDN_PROFILE_NAME" --name "CDN_ENDPOINT" --resource-group "RESOURCE_GROUP"
      
      # Azure logout 
        - name: logout
          run: |
                az logout
    ```

## <a name="review-your-deployment"></a>Examinar sua implantação

1. Acesse **Ações** para seu repositório do GitHub. 

1. Abra o primeiro resultado para ver os logs detalhados da execução do fluxo de trabalho. 
 
    :::image type="content" source="../media/index/github-actions-run.png" alt-text="Log das GitHub Actions executadas":::

## <a name="clean-up-resources"></a>Limpar os recursos

Quando seu site estático e o repositório do GitHub não forem mais necessários, limpe os recursos implantados excluindo o grupo de recursos e seu repositório do GitHub. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre os aplicativos Web estáticos do Azure](../../static-web-apps/index.yml)
