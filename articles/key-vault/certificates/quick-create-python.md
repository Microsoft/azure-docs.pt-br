---
title: Início Rápido – Biblioteca de clientes Python do Azure Key Vault – gerenciar certificados
description: Saiba como criar, recuperar e excluir certificados de um Azure Key Vault usando a biblioteca de clientes do Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: 62249f579909c3f8bfa9bcdf4e77e45453fcb68b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792013"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-python"></a>Início Rápido: Biblioteca de clientes do certificado do Azure Key Vault para Python

Comece a usar a biblioteca de clientes do certificado do Azure Key Vault para Python. Siga as etapas abaixo para instalar o pacote e testar o código de exemplo para tarefas básicas. Usando o Key Vault para armazenar certificados, você evita armazenar certificados no código, o que aumenta a segurança do aplicativo.

[Documentação de referência da API](/python/api/overview/azure/keyvault-certificates-readme) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates) | [Pacote (Índice de Pacote do Python)](https://pypi.org/project/azure-keyvault-certificates)

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- O [Python 2.7+ ou 3.6+](/azure/developer/python/configure-local-development-environment)
- [CLI do Azure](/cli/azure/install-azure-cli)

Este início rápido pressupõe que você está executando a [CLI do Azure](/cli/azure/install-azure-cli) em uma janela de terminal do Linux.

## <a name="set-up-your-local-environment"></a>Configurar o ambiente local

Este guia de início rápido usa a biblioteca de identidades do Azure com a CLI do Azure para autenticar o usuário nos serviços do Azure. Os desenvolvedores também podem usar o Visual Studio ou o Visual Studio Code para autenticar as chamadas. Para obter mais informações, confira [Autenticar o cliente na biblioteca de clientes de identidades do Azure](/python/api/overview/azure/identity-readme)

### <a name="sign-in-to-azure"></a>Entrar no Azure

1. Execute o comando `login`.

    ```azurecli-interactive
    az login
    ```

    Se a CLI puder abrir o navegador padrão, ela o fará e carregará uma página de entrada do Azure.

    Caso contrário, abra uma página de navegador em [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e insira o código de autorização exibido no terminal.

2. Entre com suas credenciais de conta no navegador.

### <a name="install-the-packages"></a>Instalar os pacotes

1. Em um terminal ou prompt de comando, crie uma pasta de projeto adequada e depois crie e ative um ambiente virtual do Python conforme descrito em [Usar ambientes virtuais do Python](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments)

1. Instale a biblioteca de identidades do Azure Active Directory:

    ```terminal
    pip install azure.identity
    ```


1. Instale a biblioteca de clientes do certificado do Key Vault:

    ```terminal
    pip install azure-keyvault-certificates
    ```

### <a name="create-a-resource-group-and-key-vault"></a>Criar um grupo de recursos e um cofre de chaves

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

### <a name="grant-access-to-your-key-vault"></a>Permitir acesso ao cofre de chaves

Criar uma política de acesso para o cofre de chaves que conceda a permissão de certificados à sua conta de usuário

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --certificate-permissions delete get list create
```

#### <a name="set-environment-variables"></a>Definir variáveis de ambiente

Este aplicativo usa o nome do cofre de chaves como uma variável de ambiente chamada `KEY_VAULT_NAME`.

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

## <a name="create-the-sample-code"></a>Criar o código de exemplo

A biblioteca de clientes do certificado do Azure Key Vault para Python permite gerenciar certificados. O exemplo de código a seguir demonstrará como criar um cliente, além de definir, recuperar e excluir um certificado.

Crie um arquivo chamado *kv_certificates.py* que contenha esse código.

```python
import os
from azure.keyvault.certificates import CertificateClient, CertificatePolicy,CertificateContentType, WellKnownIssuerNames 
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)

certificateName = input("Input a name for your certificate > ")

print(f"Creating a certificate in {keyVaultName} called '{certificateName}' ...")

policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()

print(" done.")

print(f"Retrieving your certificate from {keyVaultName}.")

retrieved_certificate = client.get_certificate(certificateName)

print(f"Certificate with name '{retrieved_certificate.name}' was found'.")
print(f"Deleting your certificate from {keyVaultName} ...")

poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>Executar o código

Verifique se o código na seção anterior está em um arquivo chamado *kv_certificates.py*. Execute o código com o seguinte comando:

```terminal
python kv_certificates.py
```

- Se você encontrar erros de permissões, verifique se executou o [comando `az keyvault set-policy`](#grant-access-to-your-key-vault).
- Executar novamente o código com o mesmo nome de chave pode produzir este erro: "O Certificado (Conflito) <name> está em um estado excluído, mas recuperável". Use um nome de chave diferente.

## <a name="code-details"></a>Detalhes do código

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente

Neste guia de início rápido, o usuário conectado é usado para se autenticar no cofre de chaves, que é o método preferencial para o desenvolvimento local. Para os aplicativos implantados no Azure, a identidade gerenciada deve ser atribuída ao Serviço de Aplicativo ou à Máquina Virtual. Para obter mais informações, confira [Visão geral da identidade gerenciada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

No exemplo abaixo, o nome do cofre de chaves é expandido para o URI do cofre de chaves, no formato "https://\<your-key-vault-name\>.vault.azure.net". Este exemplo usa a classe ['DefaultAzureCredential()'](/python/api/azure-identity/azure.identity.defaultazurecredential), que permite usar o mesmo código em diferentes ambientes com outras opções para fornecer a identidade. Para obter mais informações, confira [Autenticação de credenciais do Azure padrão](https://docs.microsoft.com/python/api/overview/azure/identity-readme). 

```python
credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>Salvar um certificado

Depois de obter o objeto de cliente para o cofre de chaves, você pode criar um certificado usando o método [begin_create_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#begin-create-certificate-certificate-name--policy----kwargs-): 

```python
policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()
```

Aqui, o certificado requer uma política obtida com o método [CertificatePolicy.get_default](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificatepolicy?#get-default--).

Chamar um método `begin_create_certificate` gera uma chamada assíncrona à API REST do Azure para o cofre de chaves. A chamada assíncrona retorna um objeto do instrumento de sondagem. Para aguardar o resultado da operação, chame o método `result` do instrumentos de sondagem.

Ao processar a solicitação, o Azure autentica a identidade do chamador (a entidade de serviço) usando o objeto de credencial fornecido ao cliente.


### <a name="retrieve-a-certificate"></a>Recuperar um certificado

Para ler um certificado do Key Vault, use o método [get_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#get-certificate-certificate-name----kwargs-):

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

Verifique também se o certificado foi definido com o comando [az keyvault certificate show](/cli/azure/keyvault/certificate?#az_keyvault_certificate_show) da CLI do Azure.

### <a name="delete-a-certificate"></a>Excluir um certificado

Para excluir um certificado, use o método [begin_delete_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#begin-delete-certificate-certificate-name----kwargs-):

```python
poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()
```

O método `begin_delete_certificate` é assíncrono e retorna um objeto do instrumento de sondagem. Chamar o método `result` do instrumento de sondagem aguarda sua conclusão.

Você pode verificar se o certificado foi excluído com o comando da CLI do Azure [az keyvault certificate show](/cli/azure/keyvault/certificate?#az_keyvault_certificate_show).

Depois de excluído, um certificado permanece em um estado excluído, mas recuperável, por algum tempo. Se você executar o código novamente, use outro nome de certificado.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você também quiser experimentar [segredos](../secrets/quick-create-python.md) e [chaves](../keys/quick-create-python.md), poderá reutilizar o Key Vault criado neste artigo.

Caso contrário, quando tiver concluído os recursos criados neste artigo, use o seguinte comando para excluir o grupo de recursos e todos os recursos que ele contém:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da o Cofre da Chave do Azure](../general/overview.md)
- [Proteger o acesso a um cofre de chaves](../general/security-overview.md)
- [Guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- [Visão geral da segurança do Key Vault](../general/security-overview.md)
- [Autenticar com o Key Vault](../general/authentication.md)
