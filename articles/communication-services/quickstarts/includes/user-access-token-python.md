---
title: incluir arquivo
description: incluir arquivo
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: db6e4a9c6d2829c7980164d5b79bd33e4b3fb6eb
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106113395"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2.7 ou 3.6+.
- Um recurso e uma cadeia de conexão ativos dos Serviços de Comunicação. [Crie um recurso dos Serviços de Comunicação](../create-communication-resource.md).

## <a name="setting-up"></a>Ao configurar o

### <a name="create-a-new-python-application"></a>Criar um novo aplicativo Python

1. Abra o terminal ou a janela de comando para criar um diretório para seu aplicativo e navegue até ele.

   ```console
   mkdir access-tokens-quickstart && cd access-tokens-quickstart
   ```

1. Use um editor de texto para criar um arquivo chamado **issue-access-tokens.py** no diretório raiz do projeto e adicione a estrutura do programa, incluindo a manipulação de exceção básica. Você adicionará todo o código-fonte deste guia de início rápido a esse arquivo nas seções a seguir.

   ```python
   import os
   from azure.communication.identity import CommunicationIdentityClient, CommunicationUserIdentifier

   try:
      print("Azure Communication Services - Access Tokens Quickstart")
      # Quickstart code goes here
   except Exception as ex:
      print("Exception:")
      print(ex)
   ```

### <a name="install-the-package"></a>Instalar o pacote

Ainda no diretório do aplicativo, instale o pacote do SDK de Identidade dos Serviços de Comunicação do Azure para Python usando o comando `pip install`.

```console
pip install azure-communication-identity
```

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie a instância de um `CommunicationIdentityClient` com a sua cadeia de conexão. O código abaixo recupera a cadeia de conexão do recurso de uma variável de ambiente chamada `COMMUNICATION_SERVICES_CONNECTION_STRING`. Saiba como [gerenciar a cadeia de conexão do seu recurso](../create-communication-resource.md#store-your-connection-string).

Adicione esse código ao bloco `try`:

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.environ["COMMUNICATION_SERVICES_CONNECTION_STRING"]

# Instantiate the identity client
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

Como alternativa, se você tiver configurado a identidade gerenciada, consulte [Usar identidades gerenciadas](../managed-identity.md). Também é possível autenticar com a identidade gerenciada.
```python
endpoint = os.environ["COMMUNICATION_SERVICES_ENDPOINT"]
client = CommunicationIdentityClient(endpoint, DefaultAzureCredential())
```

## <a name="create-an-identity"></a>Criar uma identidade

Os Serviços de Comunicação do Azure mantêm um diretório de identidade leve. Use o método `create_user` para criar uma entrada no diretório com um `Id` exclusivo. Armazene a identidade recebida com o mapeamento para os usuários do aplicativo. Por exemplo, armazenando-os no banco de dados do servidor de aplicativos. A identidade é necessária posteriormente para emitir tokens de acesso.

```python
identity = client.create_user()
print("\nCreated an identity with ID: " + identity.identifier)
```

## <a name="issue-access-tokens"></a>Emitir tokens de acesso

Use o método `get_token` a fim de emitir um token de acesso para uma identidade existente dos Serviços de Comunicação. O parâmetro `scopes` define o conjunto de primitivos, que autorizará esse token de acesso. Confira a [lista de ações compatíveis](../../concepts/authentication.md). A nova instância do parâmetro `CommunicationUserIdentifier` pode ser construída com base na representação da cadeia de caracteres da identidade do Serviço de Comunicação do Azure.

```python
# Issue an access token with the "voip" scope for an identity
token_result = client.get_token(identity, ["voip"])
expires_on = token_result.expires_on.strftime("%d/%m/%y %I:%M %S %p")
print("\nIssued an access token with 'voip' scope that expires at " + expires_on + ":")
print(token_result.token)
```

Os tokens de acesso são credenciais de curta duração que precisam ser reemitidas. Deixar de fazer isso pode causar a interrupção da experiência dos usuários do aplicativo. A propriedade de resposta `expires_on` indica o tempo de vida do token de acesso.

## <a name="create-an-identity-and-issue-an-access-token-within-the-same-request"></a>Criar uma identidade e emitir um token de acesso na mesma solicitação

Use o método `create_user_and_token` para criar uma identidade dos Serviços de Comunicação e emitir um token de acesso para ela. O parâmetro `scopes` define o conjunto de primitivos, que autorizará esse token de acesso. Confira a [lista de ações compatíveis](../../concepts/authentication.md).

```python
# Issue an identity and an access token with the "voip" scope for the new identity
identity_token_result = client.create_user_and_token(["voip"])
identity = identity_token_result[0].identifier
token = identity_token_result[1].token
expires_on = identity_token_result[1].expires_on.strftime("%d/%m/%y %I:%M %S %p")
print("\nCreated an identity with ID: " + identity)
print("\nIssued an access token with 'voip' scope that expires at " + expires_on + ":")
print(token)
```

## <a name="refresh-access-tokens"></a>Tokens de acesso de atualização

Para atualizar um token de acesso, use o objeto `CommunicationUserIdentifier` para reemitir:

```python
# Value existingIdentity represents identity of Azure Communication Services stored during identity creation
identity = CommunicationUserIdentifier(existingIdentity)
token_result = client.get_token( identity, ["voip"])
```

## <a name="revoke-access-tokens"></a>Revogar tokens de acesso

Em alguns casos, você pode revogar explicitamente os tokens de acesso. Por exemplo, quando o usuário de um aplicativo altera a senha usada para realizar a autenticação no serviço. O método `revoke_tokens` invalida todos os tokens de acesso ativos que foram emitidos para a identidade.

```python
client.revoke_tokens(identity)
print("\nSuccessfully revoked all access tokens for identity with ID: " + identity.identifier)
```

## <a name="delete-an-identity"></a>Excluir uma identidade

A exclusão de uma identidade revoga todos os tokens de acesso ativos e impede que você emita novos tokens de acesso para a identidade. Isso também remove todo o conteúdo persistente associado à identidade.

```python
client.delete_user(identity)
print("\nDeleted the identity with ID: " + identity.identifier)
```

## <a name="run-the-code"></a>Executar o código

Do prompt de um console, navegue até o diretório que contém o arquivo *issue-access-tokens.py* e efetue o seguinte comando `python` para executar o aplicativo.

```console
python ./issue-access-tokens.py
```
