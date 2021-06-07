---
title: Tutorial para o uso das referências do Key Vault da Configuração de Aplicativos do Azure em um aplicativo Java Spring Boot | Microsoft Docs
description: Neste tutorial, você aprenderá a usar as referências do Key Vault da Configuração de Aplicativos do Azure em um aplicativo Java Spring Boot
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: alkemper
ms.custom: mvc, devx-track-java, devx-track-azurecli
ms.openlocfilehash: 7c5534ab836968bc4e72a54db1ddb9667d366558
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768841"
---
# <a name="tutorial-use-key-vault-references-in-a-java-spring-app"></a>Tutorial: Usar as referências do Key Vault em um aplicativo Java Spring

Neste tutorial, você aprenderá a usar o serviço de Configuração de Aplicativos do Azure junto com o Azure Key Vault. A Configuração de Aplicativos e o Key Vault são serviços complementares usados lado a lado na maioria das implantações de aplicativo.

A Configuração de Aplicativos ajuda você a usar os serviços juntos por meio da criação de chaves que fazem referência a valores armazenados no Key Vault. Quando a Configuração de Aplicativos cria essas chaves, ela armazena os URIs de valores do Key Vault em vez dos valores propriamente ditos.

Seu aplicativo usa o provedor do cliente da Configuração de Aplicativos para recuperar referências do Key Vault, assim como faz para quaisquer outras chaves armazenadas na Configuração de Aplicativos. Nesse caso, os valores armazenados na Configuração de Aplicativos são URIs que fazem referência aos valores no Key Vault. Eles não são valores nem credenciais do Key Vault. Já que o provedor do cliente reconhece as chaves como referências do Key Vault, ele usa o Key Vault para recuperar os valores delas.

Seu aplicativo é responsável por autenticar corretamente, tanto na Configuração de Aplicativos quanto no Key Vault. Os dois serviços não se comunicam diretamente.

Este tutorial mostra a você como implementar referências do Key Vault em seu código. Ele se baseia no aplicativo Web introduzido nos Inícios Rápidos. Antes de continuar, primeiro conclua [Criar um aplicativo Java Spring com a Configuração de Aplicativos](./quickstart-java-spring-app.md).

Você pode usar qualquer editor de código para executar as etapas deste tutorial. Por exemplo, o [Visual Studio Code](https://code.visualstudio.com/) é um editor de código multiplataforma disponível para os sistemas operacionais Windows, macOS e Linux.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma chave da Configuração de Aplicativos que referencia um valor armazenado no Key Vault.
> * Acessar o valor dessa chave em um aplicativo Java Spring.

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/)
* Um [JDK (Java Development Kit)](/java/azure/jdk) com suporte na versão 8.
* [Apache Maven](https://maven.apache.org/download.cgi), versão 3.0 ou posterior.

## <a name="create-a-vault"></a>Criar um cofre

1. Selecione a opção **Criar um recurso** no canto superior esquerdo do portal do Azure:

    ![A captura de tela mostra a opção Criar um recurso no portal do Azure.](./media/quickstarts/search-services.png)
1. Digite **Key Vault** na caixa de pesquisa.
1. Na lista de resultados, selecione **Cofres de chaves** à esquerda.
1. Em **Cofres de chaves**, selecione **Adicionar**.
1. À direita, na seção **Criar cofre de chaves**, forneça as seguintes informações:
    * Selecione **Assinatura** para escolher uma assinatura.
    * Em **Grupo de Recursos**, selecione **Criar novo** e digite um nome para o grupo de recursos.
    * Em **Nome do cofre de chaves**, é necessário um nome exclusivo. Para este tutorial, insira **Contoso-vault2**.
    * Na lista suspensa **Região**, escolha uma localização.
1. Deixe as outras opções de **Criar cofre de chaves** com os valores padrão.
1. Selecione **Criar**.

Nesse ponto, sua conta do Azure é a única autorizada a acessar esse novo cofre.

![A captura de tela mostra o cofre de chaves.](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Adicionar um segredo ao Key Vault

Para adicionar um segredo ao cofre, basta executar algumas etapas adicionais. Nesse caso, adicione uma mensagem que você possa usar para testar a recuperação do Key Vault. A mensagem é chamada de **Mensagem** e você armazena nela o valor de "Olá do Key Vault".

1. Na página de propriedades do Key Vault, selecione **Segredos**.
1. Selecione **Gerar/Importar**.
1. No painel **Criar um segredo**, insira os seguintes valores:
    * **Opções de upload**: insira **Manual**.
    * **Name**: insira **Mensagem**.
    * **Valor**: insira **Olá do Key Vault**.
1. Deixe as outras propriedades de **Criar um segredo** com os valores padrão.
1. Selecione **Criar**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Adicionar uma referência do Key Vault à Configuração de Aplicativos

1. Entre no [portal do Azure](https://portal.azure.com). Escolha **Todos os recursos** e depois escolha a instância do repositório de Configurações de Aplicativos que você criou no início rápido.

1. Selecione **Gerenciador de Configurações**.

1. Clique em **+ Criar** > **Referência do Key Vault** e especifique os seguintes valores:
    * **Chave**: Selecione **/application/config.keyvaultmessage**
    * **Rótulo**: deixe esse valor em branco.
    * **Assinatura**, **Grupo de recursos** e **Cofre de chaves**: Insira os valores correspondentes aos valores no cofre de chaves que você criou na seção anterior.
    * **Segredo**: selecione o segredo chamado **Mensagem** criado na seção anterior.

## <a name="connect-to-key-vault"></a>Conectar-se ao Key Vault

1. Neste tutorial, você usará uma entidade de serviço para autenticação no Key Vault. Para criar uma entidade de serviço, use o comando [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) da CLI do Azure:

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Essa operação retorna uma série de pares de chave/valor:

    ```json
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Execute o comando a seguir para permitir que a entidade de serviço acesse o cofre de chaves:

    ```azurecli
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get
    ```

1. Execute o comando a seguir para obter sua object-id e adicioná-la à Configuração de Aplicativos.

    ```azurecli
    az ad sp show --id <clientId-of-your-service-principal>
    az role assignment create --role "App Configuration Data Reader" --assignee-object-id <objectId-of-your-service-principal> --resource-group <your-resource-group>
    ```

1. Crie as variáveis de ambiente **AZURE_CLIENT_ID**, **AZURE_CLIENT_SECRET** e **AZURE_TENANT_ID**. Use os valores da entidade de serviço que foram exibidos nas etapas anteriores. Na linha de comando, execute os seguintes comandos e reinicie o prompt de comando para permitir que a alteração entre em vigor:

    ```cmd
    setx AZURE_CLIENT_ID "clientId"
    setx AZURE_CLIENT_SECRET "clientSecret"
    setx AZURE_TENANT_ID "tenantId"
    ```

    Se você usa o Windows PowerShell, execute o comando a seguir:

    ```azurepowershell
    $Env:AZURE_CLIENT_ID = "clientId"
    $Env:AZURE_CLIENT_SECRET = "clientSecret"
    $Env:AZURE_TENANT_ID = "tenantId"
    ```

    Se você usa macOS ou Linux, execute o comando a seguir:

    ```cmd
    export AZURE_CLIENT_ID ='clientId'
    export AZURE_CLIENT_SECRET ='clientSecret'
    export AZURE_TENANT_ID ='tenantId'
    ```


> [!NOTE]
> Essas credenciais do Key Vault são usadas somente dentro do seu aplicativo.  Seu aplicativo se autentica diretamente com o Key Vault usando essas credenciais sem envolver o serviço de Configuração de Aplicativos.  O Key Vault oferece autenticação para seu aplicativo e o serviço de Configuração de Aplicativos sem compartilhar ou expor chaves.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Atualizar o código para usar uma referência do Key Vault

1. Crie uma variável de ambiente chamada **APP_CONFIGURATION_ENDPOINT**. Defina seu valor como o ponto de extremidade do repositório de Configuração de Aplicativos. Você pode encontrar o ponto de extremidade na folha **Chaves de Acesso** no portal do Azure. Reinicie o prompt de comando para permitir que a alteração entre em vigor. 


1. Abra *bootstrap.properties* na pasta *recursos*. Atualize esse arquivo para usar o valor **APP_CONFIGURATION_ENDPOINT**. Remova todas as referências a uma cadeia de conexão neste arquivo. 

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].endpoint= ${APP_CONFIGURATION_ENDPOINT}
    ```

1. Abra *MessageProperties.java*. Adicione uma nova variável chamada *keyVaultMessage*:

    ```java
    private String keyVaultMessage;

    public String getKeyVaultMessage() {
        return keyVaultMessage;
    }

    public void setKeyVaultMessage(String keyVaultMessage) {
        this.keyVaultMessage = keyVaultMessage;
    }
    ```

1. Abra *HelloController.java*. Atualize o método *getMessage* para incluir a mensagem recuperada do Key Vault.

    ```java
    @GetMapping
    public String getMessage() {
        return "Message: " + properties.getMessage() + "\nKey Vault message: " + properties.getKeyVaultMessage();
    }
    ```

1. Crie um arquivo chamado *AzureCredentials.java* e adicione o código abaixo.

    ```java
    package com.example.demo;

    import com.azure.core.credential.TokenCredential;
    import com.azure.identity.EnvironmentCredentialBuilder;
    import com.microsoft.azure.spring.cloud.config.AppConfigurationCredentialProvider;
    import com.microsoft.azure.spring.cloud.config.KeyVaultCredentialProvider;

    public class AzureCredentials implements AppConfigurationCredentialProvider, KeyVaultCredentialProvider{

        @Override
        public TokenCredential getKeyVaultCredential(String uri) {
            return getCredential();
        }

        @Override
        public TokenCredential getAppConfigCredential(String uri) {
            return getCredential();
        }

        private TokenCredential getCredential() {
            return new EnvironmentCredentialBuilder().build();
        }

    }
    ```

1. Crie um arquivo chamado *AppConfiguration.java*. Além disso, adicione o código abaixo.

    ```java
    package com.example.demo;

    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;

    @Configuration
    public class AppConfiguration {

        @Bean
        public AzureCredentials azureCredentials() {
            return new AzureCredentials();
        }
    }
    ```

1. Crie um arquivo em seu diretório META-INF de recursos chamado *spring.factories* e adicione o código a seguir.

    ```factories
    org.springframework.cloud.bootstrap.BootstrapConfiguration=\
    com.example.demo.AppConfiguration
    ```

1. Compile o aplicativo Spring Boot com Maven e execute-o, por exemplo:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Depois que o aplicativo estiver em execução, use *curl* para testar o aplicativo, por exemplo:

      ```shell
      curl -X GET http://localhost:8080/
      ```

    Você verá a mensagem inserida no repositório de Configuração de Aplicativos. Você também verá a mensagem que inseriu no Key Vault.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou uma chave da Configuração de Aplicativos que referencia um valor armazenado no Key Vault. Para saber como usar os sinalizadores de recurso em seu aplicativo Java Spring, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Integração de identidade gerenciada](./quickstart-feature-flag-spring-boot.md)
