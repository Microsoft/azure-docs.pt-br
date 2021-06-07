---
title: 'Tutorial: Identidade gerenciada para conectar o Key Vault'
description: Configure a identidade gerenciada para conectar o Key Vault a um aplicativo do Azure Spring Cloud
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/08/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 870a04af244d18826e1041316895f746e27870eb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786577"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-spring-cloud-app"></a>Tutorial: Usar uma identidade gerenciada para conectar o Key Vault a um aplicativo do Azure Spring Cloud

**Este artigo aplica-se a:** ✔️ Java

Este artigo mostra como criar uma identidade gerenciada para um aplicativo do Azure Spring Cloud e usá-la para acessar o Azure Key Vault.

O Azure Key Vault pode ser usado para armazenar com segurança e controlar firmemente o acesso a tokens, senhas, certificados, chaves de API e outros segredos para o aplicativo. É possível criar uma identidade gerenciada no AAD (Azure Active Directory) e autenticar-se em qualquer serviço que dá suporte à autenticação do AAD, incluindo o Key Vault, sem precisar exibir as credenciais no seu código.

## <a name="prerequisites"></a>Pré-requisitos

* [Inscrever-se em uma assinatura do Azure](https://azure.microsoft.com/free/)
* [Instalar a CLI do Azure versão 2.0.67 ou posterior](/cli/azure/install-azure-cli)
* [Instalar o Maven 3.0 ou posterior](https://maven.apache.org/download.cgi)

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Crie um grupo de recursos para conter o Key Vault e o Spring Cloud usando o comando [az group create](/cli/azure/group#az_group_create):

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="set-up-your-key-vault"></a>Configurar o Key Vault
Para criar um Key Vault, use o comando [az keyvault create](/cli/azure/keyvault#az_keyvault_create):

> [!Important]
> Cada Key Vault deve ter um nome exclusivo. Substitua <nome-do-seu-key-vault> pelo nome do seu Key Vault nos exemplos a seguir.

```azurecli-interactive
az keyvault create --name "<your-keyvault-name>" -g "myResourceGroup"
```

Anote o `vaultUri` retornado, que estará no formato "https://<nome-do-seu-key-vault>.vault.azure.net". Ele será usado na próxima etapa.

Agora você pode inserir um segredo em seu Key Vault com o comando [az keyvault secret set](/cli/azure/keyvault/secret#az_keyvault_secret_set):

```azurecli-interactive
az keyvault secret set --vault-name "<your-keyvault-name>" \
    --name "connectionString" \
    --value "jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;"
```

## <a name="create-azure-spring-cloud-service-and-app"></a>Criar aplicativo e serviço do Azure Spring Cloud
Depois de instalar a extensão correspondente, crie uma instância do Azure Spring Cloud com o comando `az spring-cloud create` da CLI do Azure. 

```azurecli-interactive
az extension add --name spring-cloud
az spring-cloud create -n "myspringcloud" -g "myResourceGroup"
```
O exemplo a seguir cria um aplicativo nomeado `springapp` com uma identidade gerenciada atribuída ao sistema, conforme solicitado pelo parâmetro `--assign-identity`.

```azurecli
az spring-cloud app create -n "springapp" -s "myspringcloud" -g "myResourceGroup" --assign-endpoint true --assign-identity
export SERVICE_IDENTITY=$(az spring-cloud app show --name "springapp" -s "myspringcloud" -g "myResourceGroup" | jq -r '.identity.principalId')
```

Anote o `url` retornado, que estará no formato `https://<your-app-name>.azuremicroservices.io`. Ele será usado na próxima etapa.


## <a name="grant-your-app-access-to-key-vault"></a>Permitir ao aplicativo acesso ao Key Vault
Use `az keyvault set-policy` para permitir ao aplicativo acesso apropriado ao Key Vault.
```azurecli
az keyvault set-policy --name "<your-keyvault-name>" --object-id ${SERVICE_IDENTITY} --secret-permissions set get list
```
> [!NOTE]
> Use `az keyvault delete-policy --name "<your-keyvault-name>" --object-id ${SERVICE_IDENTITY}` para remover o acesso ao seu aplicativo após a desabilitação da identidade gerenciada atribuída ao sistema.

## <a name="build-a-sample-spring-boot-app-with-spring-boot-starter"></a>Criar um aplicativo Spring Boot de exemplo com o inicializador do Spring Boot
Este aplicativo terá acesso para obter segredos do Azure Key Vault. Usar o aplicativo inicializador: [Inicializador do Spring Boot para segredos do Azure Key Vault](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-keyvault-secrets).  O Azure Key Vault é adicionado como uma instância do **PropertySource** do Spring.  Os segredos armazenados no Azure Key Vault podem ser acessados e usados de maneira conveniente como qualquer propriedade de configuração externa, como propriedades em arquivos. 

1. Gere um projeto de exemplo de start.spring.io com o Spring Starter do Azure Key Vault. 
    ```azurecli
    curl https://start.spring.io/starter.tgz -d dependencies=web,azure-keyvault-secrets -d baseDir=springapp -d bootVersion=2.3.1.RELEASE -d javaVersion=1.8 | tar -xzvf -
    ```

2. Especifique o Key Vault no aplicativo. 

    ```azurecli
    cd springapp
    vim src/main/resources/application.properties
    ```

    Para usar a identidade gerenciada para aplicativos do Azure Spring Cloud, adicione propriedades com o conteúdo abaixo para src/main/resources/application.properties.

    ```
    azure.keyvault.enabled=true
    azure.keyvault.uri=https://<your-keyvault-name>.vault.azure.net
    ```
    > [!Note] 
    > É necessário adicionar a URL do cofre de chaves em `application.properties`, conforme mostrado acima. Caso contrário, a URL do Key Vault pode não ser capturada durante o runtime.

3. Adicione o exemplo de código a src/main/java/com/example/demo/DemoApplication.java. Ele recupera a cadeia de conexão do Key Vault. 

    ```Java
    package com.example.demo;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.boot.CommandLineRunner;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;

    @SpringBootApplication
    @RestController
    public class DemoApplication implements CommandLineRunner {

        @Value("${connectionString}")
        private String connectionString;

        public static void main(String[] args) {
          SpringApplication.run(DemoApplication.class, args);
        }

        @GetMapping("get")
        public String get() {
          return connectionString;
        }

        public void run(String... varl) throws Exception {
          System.out.println(String.format("\nConnection String stored in Azure Key Vault:\n%s\n",connectionString));
        }
      }
    ```

    Se você abrir pom.xml, verá a dependência de `azure-keyvault-secrets-spring-boot-starter`. Adicione essa dependência ao seu projeto em pom.xml. 

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-keyvault-secrets-spring-boot-starter</artifactId>
    </dependency>
    ```

4. Empacote o aplicativo de exemplo. 

    ```azurecli
    mvn clean package
    ```

5. Agora você pode implantar o aplicativo no Azure com o comando `az spring-cloud app deploy` da CLI do Azure. 

    ```azurecli
    az spring-cloud app deploy -n "springapp" -s "myspringcloud" -g "myResourceGroup" --jar-path target/demo-0.0.1-SNAPSHOT.jar
    ```

6.  Para testar o aplicativo, acesse o ponto de extremidade público ou o ponto de extremidade de teste.

    ```azurecli
    curl https://myspringcloud-springapp.azuremicroservices.io/get
    ```

    Você verá a mensagem "O valor da connectionString do segredo do Key Vault https://<nome-do-seu-key-vault>.vault.azure.net/: jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE; foi obtido com êxito".

## <a name="build-sample-spring-boot-app-with-java-sdk"></a>Criar aplicativo Spring Boot de exemplo com o SDK do Java

Este exemplo pode definir e obter segredos do Azure Key Vault. A [biblioteca de clientes de Segredo do Azure Key Vault para Java](/java/api/overview/azure/security-keyvault-secrets-readme) dá suporte à autenticação de token de Azure Active Directory no SDK do Azure. Ela fornece um conjunto de implementações de **TokenCredential** que podem ser usadas para construir clientes do SDK do Azure para dar suporte à autenticação de token do AAD.

A biblioteca de clientes de segredo do Azure Key Vault permite armazenar e controlar com segurança o acesso a tokens, senhas, chaves de API e outros segredos. A biblioteca oferece operações para criar, recuperar, atualizar, excluir, limpar, fazer backup, restaurar e listar os segredos e as respectivas versões.

1. Clonar um projeto de exemplo. 

    ```azurecli
    git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
    ```

2. Especifique o Key Vault no aplicativo. 

    ```azurecli
    cd Azure-Spring-Cloud-Samples/managed-identity-keyvault
    vim src/main/resources/application.properties
    ```

    Para usar a identidade gerenciada para aplicativos do Azure Spring Cloud, adicione propriedades com o conteúdo a seguir para *src/main/resources/application.properties*.

    ```
    azure.keyvault.enabled=true
    azure.keyvault.uri=https://<your-keyvault-name>.vault.azure.net
    ```

3. Inclua [ManagedIdentityCredentialBuilder](/java/api/com.azure.identity.managedidentitycredentialbuilder) para obter o token do Azure Active Directory e [SecretClientBuilder](/java/api/com.azure.security.keyvault.secrets.secretclientbuilder) para definir ou obter segredos do Key Vault em seu código.

    Obtenha o exemplo de [MainController.java](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/managed-identity-keyvault/src/main/java/com/microsoft/azure/MainController.java#L28) do projeto de exemplo clonado.

    Além disso, inclua `azure-identity` e `azure-security-keyvault-secrets` como dependência em seu pom.xml. Obtenha o exemplo de [pom.xml](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/managed-identity-keyvault/pom.xml#L21) do projeto de exemplo clonado. 

4. Empacote o aplicativo de exemplo. 

    ```azurecli
    mvn clean package
    ```

5. Agora, implante o aplicativo no Azure com o comando `az spring-cloud app deploy` da CLI do Azure. 

    ```azurecli
    az spring-cloud app deploy -n "springapp" -s "myspringcloud" -g "myResourceGroup" --jar-path target/asc-managed-identity-keyvault-sample-0.1.0.jar
    ```

6. Para testar o aplicativo, acesse o ponto de extremidade público ou o ponto de extremidade de teste. 

    Primeiro, obtenha o valor do segredo que você definiu no Azure Key Vault. 
    ```azurecli
    curl https://myspringcloud-springapp.azuremicroservices.io/secrets/connectionString
    ```

    Você verá a mensagem "O valor da connectionString do segredo do Key Vault https://<nome-do-seu-key-vault>.vault.azure.net/: jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE; foi obtido com êxito".

    Agora, crie um segredo e recupere-o usando o SDK do Java. 
    ```azurecli
    curl -X PUT https://myspringcloud-springapp.azuremicroservices.io/secrets/test?value=success

    curl https://myspringcloud-springapp.azuremicroservices.io/secrets/test
    ```

    Você verá a mensagem "O valor do teste do segredo foi obtido com êxito do Key Vault https://<nome-do-seu-key-vault>.vault.azure.net/: êxito". 

## <a name="next-steps"></a>Próximas etapas

* [Como acessar o blob de armazenamento com uma identidade gerenciada no Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [Como habilitar uma identidade gerenciada atribuída pelo sistema para o aplicativo Azure Spring Cloud](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [Saiba mais sobre identidades gerenciadas para recursos do Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Autenticação do Azure Spring Cloud com o Key Vault no GitHub Actions](./spring-cloud-github-actions-key-vault.md)
