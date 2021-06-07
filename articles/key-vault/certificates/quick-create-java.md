---
title: Guia de início rápido para usar a biblioteca de clientes do Certificado do Azure Key Vault para Java
description: Saiba mais sobre a biblioteca de clientes do Certificado do Azure Key Vault para Java seguindo as etapas deste guia de início rápido.
author: msmbaldwin
ms.custom: devx-track-java
ms.author: mbaldwin
ms.date: 12/18/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.openlocfilehash: c064a35ac6136d728f75b65379c543149cc8189d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750024"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-java-certificates"></a>Início Rápido: biblioteca de clientes do Certificado do Azure Key Vault para Java (Certificados)
Introdução à biblioteca de clientes do Certificado do Azure Key Vault para Java. Siga as etapas abaixo para instalar o pacote e testar o código de exemplo para tarefas básicas.

Recursos adicionais:

* [Código-fonte](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates)
* [Documentação de referência da API](https://azure.github.io/azure-sdk-for-java/keyvault.html)
* [Documentação do produto](index.yml)
* [Amostras](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates/src/samples/java/com/azure/security/keyvault/certificates)

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
Em uma janela de console, use o comando `mvn` para criar um novo aplicativo do console do Java com o nome `akv-certificates-java`.

```console
mvn archetype:generate -DgroupId=com.keyvault.certificates.quickstart
                       -DartifactId=akv-certificates-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

O resultado da geração do projeto deve ser algo similar a:

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.certificates.quickstart
[INFO] Parameter: artifactId, Value: akv-certificates-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.certificates.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.certificates.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.certificates.quickstart
[INFO] Parameter: artifactId, Value: akv-certificates-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-certificates-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Altere o diretório para a pasta `akv-certificates-java/` recém-criada.

```console
cd akv-certificates-java
```

### <a name="install-the-package"></a>Instalar o pacote
Abra o arquivo *pom.xml* no seu editor de texto. Adicione os seguintes elementos de dependência ao grupo de dependências.

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-certificates</artifactId>
      <version>4.1.3</version>
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
Crie uma política de acesso para o cofre de chaves que concederá à sua conta de usuário permissões para o certificado.

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --certificate-permissions delete get list create purge
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
A biblioteca de clientes do Certificado do Azure Key Vault para Java permite gerenciar certificados. A seção [Exemplos de código](#code-examples) mostrará de que modo criar um cliente e um certificado, bem como recuperar e excluir um certificado.

Todo o aplicativo de console está [abaixo](#sample-code).

## <a name="code-examples"></a>Exemplos de código
### <a name="add-directives"></a>Adicionar diretivas
Adicione as seguintes diretivas à parte superior de seu código:

```java
import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.certificates.CertificateClient;
import com.azure.security.keyvault.certificates.CertificateClientBuilder;
import com.azure.security.keyvault.certificates.models.CertificateOperation;
import com.azure.security.keyvault.certificates.models.CertificatePolicy;
import com.azure.security.keyvault.certificates.models.DeletedCertificate;
import com.azure.security.keyvault.certificates.models.KeyVaultCertificate;
import com.azure.security.keyvault.certificates.models.KeyVaultCertificateWithPolicy;
```

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente
Neste início rápido, um usuário conectado é usado para autenticação no Key Vault, que é o método preferencial usado para desenvolvimento local. Para aplicativos implantados no Azure, uma Identidade Gerenciada deverá ser atribuída a um Serviço de Aplicativo ou uma Máquina Virtual. Para obter mais informações, confira [Visão geral da Identidade Gerenciada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

No exemplo abaixo, o nome do cofre de chaves é expandido para o URI do cofre de chaves, no formato "https://\<your-key-vault-name\>.vault.azure.net". Esse exemplo usa a classe ['DefaultAzureCredential()'](https://docs.microsoft.com/java/api/com.azure.identity.defaultazurecredential), que permite usar o mesmo código em diferentes ambientes com diferentes opções para fornecer uma identidade. Para obter mais informações, confira [Autenticação de credenciais do Azure padrão](https://docs.microsoft.com/java/api/overview/azure/identity-readme).

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

CertificateClient certificateClient = new CertificateClientBuilder()
    .vaultUrl(keyVaultUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="save-a-secret"></a>Salvar um segredo
Agora que seu aplicativo está autenticado, será possível criar um certificado no cofre de chaves usando o método `certificateClient.beginCreateCertificate`. Essa ação requer um nome para o certificado e uma política de certificação. Nesse exemplo, atribuímos o valor "myCertificate" à variável `certificateName` e usamos uma política padrão.

A criação de certificados é uma operação de execução prolongada, na qual será possível examinar o progresso ou aguardar a conclusão dela.

```java
SyncPoller<CertificateOperation, KeyVaultCertificateWithPolicy> certificatePoller =
    certificateClient.beginCreateCertificate(certificateName, CertificatePolicy.getDefault());
certificatePoller.waitForCompletion();
```

Será possível obter o certificado após a conclusão da criação usando a seguinte chamada:

```java
KeyVaultCertificate createdCertificate = certificatePoller.getFinalResult();
```

### <a name="retrieve-a-certificate"></a>Recuperar um certificado
Agora você pode recuperar o certificado criado anteriormente com o método `certificateClient.getCertificate`.

```java
KeyVaultCertificate retrievedCertificate = certificateClient.getCertificate(certificateName);
 ```

Agora é possível acessar detalhes do certificado recuperado usando operações como `retrievedCertificate.getName`, `retrievedCertificate.getProperties` etc. Além do conteúdo `retrievedCertificate.getCer` do certificado.

### <a name="delete-a-certificate"></a>Excluir um certificado
Por fim, vamos excluir o certificado do cofre de chaves usando o método `certificateClient.beginDeleteCertificate`, que também é uma operação de execução prolongada.

```java
SyncPoller<DeletedCertificate, Void> deletionPoller = certificateClient.beginDeleteCertificate(certificateName);
deletionPoller.waitForCompletion();
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
package com.keyvault.certificates.quickstart;

import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.certificates.CertificateClient;
import com.azure.security.keyvault.certificates.CertificateClientBuilder;
import com.azure.security.keyvault.certificates.models.CertificateOperation;
import com.azure.security.keyvault.certificates.models.CertificatePolicy;
import com.azure.security.keyvault.certificates.models.DeletedCertificate;
import com.azure.security.keyvault.certificates.models.KeyVaultCertificate;
import com.azure.security.keyvault.certificates.models.KeyVaultCertificateWithPolicy;

public class App {
    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {
        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and kv uri = %s \n", keyVaultName, keyVaultUri);

        CertificateClient certificateClient = new CertificateClientBuilder()
                .vaultUrl(keyVaultUri)
                .credential(new DefaultAzureCredentialBuilder().build())
                .buildClient();

        String certificateName = "myCertificate";

        System.out.print("Creating a certificate in " + keyVaultName + " called '" + certificateName + " ... ");

        SyncPoller<CertificateOperation, KeyVaultCertificateWithPolicy> certificatePoller =
                certificateClient.beginCreateCertificate(certificateName, CertificatePolicy.getDefault());
        certificatePoller.waitForCompletion();

        System.out.print("done.");
        System.out.println("Retrieving certificate from " + keyVaultName + ".");

        KeyVaultCertificate retrievedCertificate = certificateClient.getCertificate(certificateName);

        System.out.println("Your certificate's ID is '" + retrievedCertificate.getId() + "'.");
        System.out.println("Deleting your certificate from " + keyVaultName + " ... ");

        SyncPoller<DeletedCertificate, Void> deletionPoller = certificateClient.beginDeleteCertificate(certificateName);
        deletionPoller.waitForCompletion();

        System.out.print("done.");
    }
}
```

## <a name="next-steps"></a>Próximas etapas
Neste guia de início rápido, você criou um cofre de chaves e um certificado, bem como recuperou e excluiu o certificado. Para saber mais sobre o Key Vault e como integrá-lo a seus aplicativos, confira os artigos abaixo.

- Leia uma [Visão geral do Azure Key Vault](../general/overview.md)
- Confira o [Guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- Como [Proteger o acesso a um cofre de chaves](../general/security-overview.md)