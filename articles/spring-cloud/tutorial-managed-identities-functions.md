---
title: 'Tutorial: Identidade gerenciada para invocar o Azure Functions'
description: Usar identidade gerenciada para invocar o Azure Functions de um aplicativo do Azure Spring Cloud
author: MarkGardner
ms.author: margard
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/10/2020
ms.openlocfilehash: b737ea751d3b3d2132691e04a1a2cd853748db65
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792499"
---
# <a name="tutorial-use-a-managed-identity-to-invoke-azure-functions-from-an-azure-spring-cloud-app"></a>Tutorial: Usar uma identidade gerenciada para invocar o Azure Functions em um aplicativo do Azure Spring Cloud

Este artigo mostra como criar uma identidade gerenciada para um aplicativo do Azure Spring Cloud e usá-la para invocar funções disparadas por HTTP.

Tanto o Azure Functions quanto os Serviços de Aplicativos têm suporte interno para autenticação do Azure AD (Azure Active Directory). Ao aproveitar essa funcionalidade interna de autenticação junto com as Identidades gerenciadas do Azure Spring Cloud, podemos invocar serviços RESTful usando semântica OAuth moderna. Esse método não exige o armazenamento de segredos no código e fornece controles mais granulares para controlar o acesso a recursos externos. 


## <a name="prerequisites"></a>Pré-requisitos

* [Inscrever-se em uma assinatura do Azure](https://azure.microsoft.com/free/)
* [Instalar a CLI do Azure versão 2.0.67 ou posterior](/cli/azure/install-azure-cli)
* [Instalar o Maven 3.0 ou posterior](https://maven.apache.org/download.cgi)
* [Instalar o Azure Functions Core Tools versão 3.0.2009 ou superior](../azure-functions/functions-run-local.md#install-the-azure-functions-core-tools)


## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Crie um grupo de recursos para conter o aplicativo de funções e o Spring Cloud usando o comando [az group create](/cli/azure/group#az_group_create):

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```


## <a name="create-a-function-app"></a>Criar um aplicativo de funções
Para criar um aplicativo de funções, você deve primeiro criar uma conta de armazenamento de backup, usar o comando [az storage account create](/cli/azure/storage/account#az_storage_account_create):

> [!Important]
> O aplicativo de funções e a conta de armazenamento devem ter um nome exclusivo. Substitua <your-functionapp-name> pelo nome do seu aplicativo de funções e <your-storageaccount-name> pelo nome da sua conta de armazenamento nos exemplos a seguir.

```azurecli-interactive
az storage account create --name <your-storageaccount-name> --resource-group myResourceGroup --location eastus --sku Standard_LRS
```

Depois de criar a conta de armazenamento, crie o aplicativo de funções.

```azurecli-interactive
az functionapp create --name <your-functionapp-name> --resource-group myResourceGroup --consumption-plan-location eastus --os-type windows --runtime node --storage-account <your-storageaccount-name> --functions-version 3
```

Anote os **hostNames** retornados, que estarão no formato "https://<your-functionapp-name>.azurewebsites.net". Eles serão usados na próxima etapa.


## <a name="enable-azure-active-directory-authentication"></a>Habilitar a Autenticação do Azure Active Directory

Acesse o aplicativo de funções recém-criado no [portal do Azure](https://portal.azure.com) e selecione "Autenticação/Autorização" no menu de configurações. Habilite a Autenticação do Serviço de Aplicativo e configure a "Ação a ser tomada quando a solicitação não for autenticada" para "Logon com o Azure Active Directory". Essa configuração garantirá que todas as solicitações não autenticadas sejam negadas (resposta 401).

![Configurações de autenticação mostrando o Azure Active Directory como provedor padrão](media/spring-cloud-tutorial-managed-identities-functions/function-auth-config-1.jpg)

Em Provedores de Autenticação, selecione Azure Active Directory para configurar o registro de aplicativo. A seleção do Modo de Gerenciamento Expresso criará automaticamente um registro de aplicativo no locatário do Azure AD com a configuração correta.

![Provedor do Azure Active Directory definido como Modo de Gerenciamento Expresso](media/spring-cloud-tutorial-managed-identities-functions/function-auth-config-2.jpg)

Ao salvar as configurações, o aplicativo de funções será reiniciado e todas as solicitações subsequentes deverão fazer logon por meio do Azure AD. Para testar se as solicitações não autenticadas estão sendo rejeitadas, navegue até a URL raiz dos aplicativos de funções (retornada na saída **hostNames** na etapa acima). Você deve ser redirecionado para a tela de logon do Azure AD da sua organização.


## <a name="create-an-http-triggered-function"></a>Criar uma função disparada por HTTP

Em um diretório local vazio, crie um aplicativo de funções e adicione uma função disparada por HTTP.

```console
func init --worker-runtime node
func new --template HttpTrigger --name HttpTrigger
```

Por padrão, o Functions usa a autenticação baseada em chave para proteger pontos de extremidade HTTP. Nós habilitaremos a autenticação do Azure AD para proteger o acesso às funções, portanto queremos [definir o nível de autenticação da função como anônimo](../azure-functions/functions-bindings-http-webhook-trigger.md#secure-an-http-endpoint-in-production).

```json function.json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      ...
    }
  ]
}
```

Agora, o aplicativo pode ser publicado na instância do [Aplicativo de funções](#create-a-function-app) criada na etapa anterior.

```console
func azure functionapp publish <your-functionapp-name>
```

A saída do comando de publicar deve listar a URL da função recém-criada.

```output
Deployment completed successfully.
Syncing triggers...
Functions in <your-functionapp-name>:
    HttpTrigger - [httpTrigger]
        Invoke url: https://<your-functionapp-name>.azurewebsites.net/api/httptrigger
```


## <a name="create-azure-spring-cloud-service-and-app"></a>Criar aplicativo e serviço do Azure Spring Cloud
Depois de instalar a extensão spring-cloud, crie uma instância do Azure Spring Cloud com o comando `az spring-cloud create` da CLI do Azure. 

```azurecli-interactive
az extension add --name spring-cloud
az spring-cloud create --name mymsispringcloud --resource-group myResourceGroup --location eastus
```

O exemplo a seguir cria um aplicativo nomeado `msiapp` com uma identidade gerenciada atribuída ao sistema, conforme solicitado pelo parâmetro `--assign-identity`.

```azurecli
az spring-cloud app create --name "msiapp" --service "mymsispringcloud" --resource-group "myResourceGroup" --assign-endpoint true --assign-identity
```

## <a name="build-sample-spring-boot-app-to-invoke-the-function"></a>Criar o aplicativo Spring Boot de exemplo para invocar a função

Este exemplo invocará a função disparada por HTTP solicitando primeiro um token de acesso do [ponto de extremidade MSI](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md#get-a-token-using-http) e usará esse token para autenticar a solicitação HTTP da função.

1. Clone o projeto de exemplo. 

    ```console
    git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
    ```

2. Especifique o URI da sua função e o nome do gatilho nas propriedades do aplicativo. 

    ```bash
    cd Azure-Spring-Cloud-Samples/managed-identity-function
    vim src/main/resources/application.properties
    ```

    Para usar a identidade gerenciada para aplicativos do Azure Spring Cloud, adicione propriedades com o conteúdo a seguir para *src/main/resources/application.properties*.

    ```
    azure.function.uri=https://<your-functionapp-name>.azurewebsites.net
    azure.function.triggerPath=httptrigger
    ```

3. Empacote o aplicativo de exemplo. 

    ```console
    mvn clean package
    ```

4. Agora, implante o aplicativo no Azure com o comando `az spring-cloud app deploy` da CLI do Azure. 

    ```azurecli
    az spring-cloud app deploy  --name "msiapp" --service "mymsispringcloud" --resource-group "myResourceGroup" --jar-path target/sc-managed-identity-function-sample-0.1.0.jar
    ```

5. Para testar o aplicativo, acesse o ponto de extremidade público ou o ponto de extremidade de teste. 

    ```console
    curl https://mymsispringcloud-msiapp.azuremicroservices.io/func/springcloud
    ```

    Você verá a mensagem a seguir retornada no corpo da resposta.
    ```output
    Function Response: Hello, springcloud. This HTTP triggered function executed successfully.
    ```
    
    Tente passar valores diferentes para a função alterando o parâmetro do caminho.

## <a name="next-steps"></a>Próximas etapas

* [Como habilitar uma identidade gerenciada atribuída pelo sistema para o aplicativo Azure Spring Cloud](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [Saiba mais sobre identidades gerenciadas para recursos do Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Configurar aplicativos do cliente para acessar o Serviço de Aplicativo](../app-service/configure-authentication-provider-aad.md#configure-client-apps-to-access-your-app-service)
