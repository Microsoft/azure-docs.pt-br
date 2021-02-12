---
title: Autenticação do Azure Spring Cloud com o Key Vault no GitHub Actions
description: Como usar o cofre de chaves com fluxo de trabalho de CI/CD para o Azure Spring Cloud com ações do GitHub
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.custom: devx-track-java
ms.openlocfilehash: 0ea0db1faf8c452958b8d95c193d45506057777c
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98673325"
---
# <a name="authenticate-azure-spring-cloud-with-key-vault-in-github-actions"></a>Autenticação do Azure Spring Cloud com o Key Vault no GitHub Actions

**Este artigo aplica-se a:** ✔️ Java ✔️ C#

O Key Vault é um local seguro para armazenar chaves. Os usuários corporativos precisam armazenar credenciais para ambientes de CI/CD no escopo que controlam. A chave para obter credenciais no cofre de chaves deve ser limitada ao escopo do recurso.  Ele tem acesso apenas ao escopo do cofre de chaves, não ao escopo do Azure inteiro. É como uma chave que só pode abrir uma caixa forte, e não uma chave mestra, que pode abrir todas as portas em um edifício. É uma maneira de obter uma chave com outra chave, que é útil em um fluxo de trabalho CICD. 

## <a name="generate-credential"></a>Gerar credencial
Para gerar uma chave para acessar o cofre de chaves, execute o comando abaixo no computador local:

```azurecli
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.KeyVault/vaults/<KEY_VAULT> --sdk-auth
```
O escopo especificado pelo `--scopes` parâmetro limita o acesso à chave para o recurso.  Ele só pode acessar a caixa forte.

Com resultados:
```output
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Em seguida, salve os resultados em **segredos** do GitHub conforme descrito em [configurar seu repositório do GitHub e autenticar com o Azure](./spring-cloud-howto-github-actions.md#set-up-github-repository-and-authenticate).

## <a name="add-access-policies-for-the-credential"></a>Adicionar políticas de acesso para a credencial
A credencial que você criou acima pode obter apenas informações gerais sobre o Key Vault, não o conteúdo que ele armazena.  Para obter os segredos armazenados no Key Vault, você precisa definir políticas de acesso para a credencial.

Vá para o painel de **Key Vault** em portal do Azure, clique no menu **controle de acesso** e abra a guia **atribuições de função** . Selecione **aplicativos** para o **tipo** e `This resource` para o **escopo**.  Você deve ver a credencial criada na etapa anterior:

 ![Definir política de acesso](./media/github-actions/key-vault1.png)

Copie o nome da credencial, por exemplo, `azure-cli-2020-01-19-04-39-02` . Abra o menu **políticas de acesso** , clique em **+ Adicionar** link de política de acesso.  Selecione `Secret Management` para **modelo** e, em seguida, selecione **entidade de segurança**. Cole o nome da credencial em **principal** / caixa de entrada **selecionar** :

 ![Selecionar](./media/github-actions/key-vault2.png)

 Clique no botão **Adicionar** na caixa de diálogo **Adicionar política de acesso** e, em seguida, clique em **salvar**.

## <a name="generate-full-scope-azure-credential"></a>Gerar credencial do Azure de escopo completo
Essa é a chave mestra para abrir todas as portas na construção. O procedimento é semelhante à etapa anterior, mas aqui alteramos o escopo para gerar a chave mestra:

```azurecli
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

Novamente, os resultados:
```output
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Copie a cadeia de caracteres JSON inteira.  Volte para **Key Vault** painel. Abra o menu **segredos** e clique no botão **gerar/importar** . Insira o nome do segredo, como `AZURE-CREDENTIALS-FOR-SPRING` . Cole a cadeia de caracteres de credencial JSON na caixa de entrada **valor** . Você pode notar que a caixa de entrada valor é um campo de texto de uma linha, em vez de uma área de texto de várias linhas.  Você pode colar a cadeia de caracteres JSON completa lá.

 ![Credencial de escopo completo](./media/github-actions/key-vault3.png)

## <a name="combine-credentials-in-github-actions"></a>Combinar credenciais em ações do GitHub
Defina as credenciais usadas quando o pipeline CICD for executado:

```console
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}           # Strong box key you generated in the first step
    - uses: Azure/get-keyvault-secrets@v1.0
      with:
        keyvault: "<Your Key Vault Name>"
        secrets: "AZURE-CREDENTIALS-FOR-SPRING"           # Master key to open all doors in the building
      id: keyvaultaction
    - uses: azure/login@v1
      with:
        creds: ${{ steps.keyvaultaction.outputs.AZURE-CREDENTIALS-FOR-SPRING }}
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud             # Spring CLI commands from here
          az spring-cloud list

```

## <a name="next-steps"></a>Próximas etapas
* [Ações do GitHub do Spring Cloud](./spring-cloud-howto-github-actions.md)
