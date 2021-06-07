---
title: Início Rápido – Biblioteca de clientes de Segredo do Azure Key Vault para Java
description: Fornece um início rápido para usar a biblioteca de clientes de Segredo do Azure Key Vault para Java.
author: msmbaldwin
ms.custom: devx-track-java
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: b45bb353ae32f1037fde7dc5d518472089edf12f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766327"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-java"></a>Início Rápido: Biblioteca de clientes de Segredo do Azure Key Vault para Java
Introdução à biblioteca de clientes de Segredo do Azure Key Vault para Java. Siga as etapas abaixo para instalar o pacote e testar o código de exemplo para tarefas básicas.

Recursos adicionais:

* [Código-fonte](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)
* [Documentação de referência da API](https://azure.github.io/azure-sdk-for-java/keyvault.html)
* [Documentação do produto](index.yml)
* [Amostras](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets/src/samples/java/com/azure/security/keyvault/secrets)

## <a name="prerequisites"></a>Pré-requisitos
- Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](/java/azure/jdk/) versão 8 ou superior
- [Apache Maven](https://maven.apache.org)
- [CLI do Azure](/cli/azure/install-azure-cli)

Este início rápido pressupõe que você está executando a [CLI do Azure](/cli/azure/install-azure-cli) e o [Apache Maven](https://maven.apache.org) em uma janela de terminal do Linux.

## <a name="setting-up"></a>Configurando
Este início rápido usa a biblioteca de Identidades do Azure com a CLI do Azure para autenticar usuários nos Serviços do Azure. Os desenvolvedores também podem usar o Visual Studio ou o Visual Studio Code para autenticar as chamadas. Para saber mais, confira [Autenticar o cliente na biblioteca de clientes do Azure Idendity](/java/api/overview/azure/identity-readme).

### <a name="sign-in-to-azure"></a>Entrar no Azure
1. Execute o comando `login`.

    ```azurecli-interactive
    az login
    ```

   Se a CLI puder abrir o navegador padrão, ela o fará e carregará uma página de entrada do Azure.

   Caso contrário, abra uma página de navegador em [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e insira o código de autorização exibido no terminal.

2. Entre com suas credenciais de conta no navegador.

### <a name="create-a-new-java-console-app"></a>Criar um aplicativo de console Java
Em uma janela de console, use o comando `mvn` para criar um novo aplicativo do console do Java com o nome `akv-secrets-java`.

```console
mvn archetype:generate -DgroupId=com.keyvault.secrets.quickstart
                       -DartifactId=akv-secrets-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

O resultado da geração do projeto deve ser algo similar a:

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.secrets.quickstart
[INFO] Parameter: artifactId, Value: akv-secrets-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.secrets.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.secrets.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.secrets.quickstart
[INFO] Parameter: artifactId, Value: akv-secrets-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-secrets-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Altere o diretório para a pasta `akv-secrets-java/` recém-criada.

```console
cd akv-secrets-java
```

### <a name="install-the-package"></a>Instalar o pacote
Abra o arquivo *pom.xml* no seu editor de texto. Adicione os seguintes elementos de dependência ao grupo de dependências.

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-secrets</artifactId>
      <version>4.2.3</version>
    </dependency>

    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-identity</artifactId>
      <version>1.2.0</version>
    </dependency>
```

### <a name="create-a-resource-group-and-key-vault"></a>Criar um grupo de recursos e um cofre de chaves
[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

#### <a name="grant-access-to-your-key-vault"></a>Permitir acesso ao cofre de chaves
Criar uma política de acesso para o cofre de chaves que concede permissões de segredo à sua conta de usuário.

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --secret-permissions delete get list set purge
```

#### <a name="set-environment-variables"></a>Definir variáveis de ambiente
Este aplicativo usa o nome de seu cofre de chaves como uma variável de ambiente chamada `KEY_VAULT_NAME`.

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS ou Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Modelo de objeto
A biblioteca de clientes de Segredo do Azure Key Vault para Java permite que você gerencie segredos. A seção [Exemplos de código](#code-examples) mostra como criar um cliente, bem como definir, recuperar e excluir um segredo.

Todo o aplicativo de console está [abaixo](#sample-code).

## <a name="code-examples"></a>Exemplos de código
### <a name="add-directives"></a>Adicionar diretivas
Adicione as seguintes diretivas à parte superior de seu código:

```java
import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.DeletedSecret;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret;
```

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente
Neste início rápido, um usuário conectado é usado para autenticação no Key Vault, que é o método preferencial usado para desenvolvimento local. Para aplicativos implantados no Azure, uma Identidade Gerenciada deverá ser atribuída a um Serviço de Aplicativo ou uma Máquina Virtual. Para obter mais informações, confira [Visão geral da Identidade Gerenciada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

No exemplo abaixo, o nome do cofre de chaves é expandido para o URI do cofre de chaves, no formato "https://\<your-key-vault-name\>.vault.azure.net". Esse exemplo usa a classe ['DefaultAzureCredential()'](https://docs.microsoft.com/java/api/com.azure.identity.defaultazurecredential), que permite usar o mesmo código em diferentes ambientes com diferentes opções para fornecer uma identidade. Para obter mais informações, confira [Autenticação de credenciais do Azure padrão](https://docs.microsoft.com/java/api/overview/azure/identity-readme).

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

SecretClient secretClient = new SecretClientBuilder()
    .vaultUrl(keyVaultUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="save-a-secret"></a>Salvar um segredo
Agora que o aplicativo foi autenticado, você pode colocar um segredo no cofre de chaves usando o método `secretClient.setSecret`. Isso requer um nome para o segredo – atribuímos o valor "mySecret" à variável `secretName` neste exemplo.

```java
secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));
```

Você pode verificar se o segredo foi definido usando o comando [az keyvault secret show](/cli/azure/keyvault/secret?#az_keyvault_secret_show):

```azurecli
az keyvault secret show --vault-name <your-unique-key-vault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Recuperar um segredo
Agora, você pode recuperar o segredo definido anteriormente com o método `secretClient.getSecret`.

```java
KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);
 ```

Agora você pode acessar o valor do segredo recuperado com `retrievedSecret.getValue()`.

### <a name="delete-a-secret"></a>Excluir um segredo
Por fim, vamos excluir o segredo do cofre de chaves com o método `secretClient.beginDeleteSecret`.

A exclusão do segredo é uma operação de execução prolongada, em que é possível examinar o progresso da execução ou aguardar sua conclusão.

```java
SyncPoller<DeletedSecret, Void> deletionPoller = secretClient.beginDeleteSecret(secretName);
deletionPoller.waitForCompletion();
```

Você pode verificar se o segredo foi excluído usando o comando [az keyvault secret show](/cli/azure/keyvault/secret?#az_keyvault_secret_show):

```azurecli
az keyvault secret show --vault-name <your-unique-key-vault-name> --name mySecret
```

## <a name="clean-up-resources"></a>Limpar os recursos
Quando o cofre de chaves e o grupo de recursos correspondente não forem mais necessários, use a CLI do Azure ou o Azure PowerShell para removê-los.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Código de exemplo
```java
package com.keyvault.secrets.quickstart;

import java.io.Console;

import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.DeletedSecret;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret

public class App {
    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {
        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and key vault URI = %s \n", keyVaultName, keyVaultUri);

        SecretClient secretClient = new SecretClientBuilder()
            .vaultUrl(keyVaultUri)
            .credential(new DefaultAzureCredentialBuilder().build())
            .buildClient();

        Console con = System.console();  

        String secretName = "mySecret";

        System.out.println("Please provide the value of your secret > ");
        
        String secretValue = con.readLine();

        System.out.print("Creating a secret in " + keyVaultName + " called '" + secretName + "' with value '" + secretValue + "` ... ");

        secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));

        System.out.println("done.");
        System.out.println("Forgetting your secret.");
        
        secretValue = "";
        System.out.println("Your secret's value is '" + secretValue + "'.");

        System.out.println("Retrieving your secret from " + keyVaultName + ".");

        KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);

        System.out.println("Your secret's value is '" + retrievedSecret.getValue() + "'.");
        System.out.print("Deleting your secret from " + keyVaultName + " ... ");

        SyncPoller<DeletedSecret, Void> deletionPoller = secretClient.beginDeleteSecret(secretName);
        deletionPoller.waitForCompletion();

        System.out.println("done.");
    }
}
```

## <a name="next-steps"></a>Próximas etapas
Neste início rápido, você criou um cofre de chaves, armazenou um segredo, o recuperou e o excluiu. Para saber mais sobre o Key Vault e como integrá-lo a seus aplicativos, confira os artigos abaixo.

- Leia uma [Visão geral do Azure Key Vault](../general/overview.md)
- Confira o [Guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- Como [Proteger o acesso a um cofre de chaves](../general/security-overview.md)
