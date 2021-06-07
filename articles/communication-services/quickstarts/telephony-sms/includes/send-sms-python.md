---
title: incluir arquivo
description: incluir arquivo
services: azure-communication-services
author: lakshmans
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/11/2021
ms.topic: include
ms.custom: include file
ms.author: lakshmans
ms.openlocfilehash: 2b96d62fb2be27de03964212557446d2e792beb8
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106113136"
---
Comece a usar os Serviços de Comunicação do Azure usando o SDK de SMS do Python dos Serviços de Comunicação para enviar mensagens SMS.

A realização deste início rápido gera um pequeno custo de alguns centavos de dólar ou menos em sua conta do Azure.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](#todo-sdk-repo) | [Package (PiPy)](#todo-nuget) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2.7 ou 3.6+.
- Um recurso e uma cadeia de conexão ativos dos Serviços de Comunicação. [Crie um recurso dos Serviços de Comunicação](../../create-communication-resource.md).
- Um número de telefone habilitado para SMS. [Obter um número de telefone](../get-phone-number.md).

### <a name="prerequisite-check"></a>Verificação de pré-requisitos

- Em um terminal ou em uma janela de comando, execute o comando `python --version` para verificar se o Python está instalado.
- Para exibir os números de telefone associados ao seu recurso dos Serviços de Comunicação, entre no [portal do Azure](https://portal.azure.com/), localize o recurso em questão e abra a guia **números de telefone** no painel de navegação esquerdo.

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-python-application"></a>Criar um novo aplicativo Python

Abra a janela de comando ou do terminal para criar um diretório para seu aplicativo e navegue até ele.

```console
mkdir sms-quickstart && cd sms-quickstart
```

Use um editor de texto para criar um arquivo chamado **send-sms.py** no diretório raiz do projeto e adicione a estrutura do programa, incluindo o tratamento básico de exceções. Você adicionará todo o código-fonte deste guia de início rápido a esse arquivo nas seções a seguir.

```python
import os
from azure.communication.sms import SmsClient

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-the-package"></a>Instalar o pacote

Ainda no diretório do aplicativo, instale o pacote do SDK de SMS dos Serviços de Comunicação do Azure para Python usando o comando `pip install`.

```console
pip install azure-communication-sms
```

## <a name="object-model"></a>Modelo de objeto

As classes e interfaces a seguir lidam com alguns dos principais recursos do SDK de SMS dos Serviços de Comunicação do Azure para Python.

| Nome                                  | Descrição                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Essa classe é necessária para toda a funcionalidade de SMS. Crie uma instância dela com suas informações de assinatura e use-a para enviar mensagens SMS.                                                                                                                 |
| SmsSendResult               | Essa classe contém o resultado do serviço de SMS.                                          |

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie uma instância de um **SmsClient** com sua cadeia de conexão. Saiba como [gerenciar a cadeia de conexão do seu recurso](../../create-communication-resource.md#store-your-connection-string).

```python
# Create the SmsClient object which will be used to send SMS messages
sms_client = SmsClient.from_connection_string(<connection_string>)
```
Para simplificar, estamos usando cadeias de conexão neste guia de início rápido, mas em ambientes de produção, recomendamos o uso de [identidades gerenciadas](../../../quickstarts/managed-identity.md), pois elas são mais seguras e gerenciáveis em escala.


## <a name="send-a-11-sms-message"></a>Enviar uma mensagem SMS 1:1

Para enviar uma mensagem SMS para um só destinatário, chame o método ```send``` no **SmsClient** com o número de telefone de um destinatário. Você também pode transmitir parâmetros opcionais para especificar se o relatório de entrega deve ser habilitado e para definir marcas personalizadas. Adicione esse código ao final do bloco `try` em **send-sms.py**:

```python

# calling send() with sms values
sms_responses = sms_client.send(
    from_="<from-phone-number>",
    to="<to-phone-number>",
    message="Hello World via SMS",
    enable_delivery_report=True, # optional property
    tag="custom-tag") # optional property

```

Substitua `<from-phone-number>` por um número de telefone habilitado para SMS associado ao seu serviço de comunicação e `<to-phone-number>` pelo número de telefone para o qual deseja enviar uma mensagem.

> [!WARNING]
> Observe que os números de telefone devem ser fornecidos no formato padrão internacional E.164. (por exemplo: +14255550123).

## <a name="send-a-1n-sms-message"></a>Enviar uma mensagem SMS 1:N

Para enviar uma mensagem SMS para uma lista de destinatários, chame o método ```send``` no **SmsClient** com uma lista de números de telefone de destinatários. Você também pode transmitir parâmetros opcionais para especificar se o relatório de entrega deve ser habilitado e para definir marcas personalizadas. Adicione esse código ao final do bloco `try` em **send-sms.py**:

```python

# calling send() with sms values
sms_responses = sms_client.send(
    from_="<from-phone-number>",
    to=["<to-phone-number-1>", "<to-phone-number-2>"],
    message="Hello World via SMS",
    enable_delivery_report=True, # optional property
    tag="custom-tag") # optional property

```

Substitua `<from-phone-number>` por um número de telefone habilitado para SMS associado ao seu serviço de comunicação e `<to-phone-number-1>` `<to-phone-number-2>` pelos números de telefone para os quais deseja enviar uma mensagem.

> [!WARNING]
> Observe que os números de telefone devem ser fornecidos no formato padrão internacional E.164. (por exemplo: +14255550123).

## <a name="optional-parameters"></a>Parâmetros Opcionais

O parâmetro `enable_delivery_report` é um parâmetro opcional que você pode usar para configurar o Relatório de Entrega. Isso é útil para cenários em que você deseja emitir eventos quando mensagens SMS são entregues. Confira o guia de início rápido [Manipular eventos de SMS](../handle-sms-events.md) para configurar o Relatório de Entrega das suas mensagens SMS.

O parâmetro `tag` é um parâmetro opcional que pode ser usado para aplicar uma marca ao relatório de entrega.

## <a name="run-the-code"></a>Executar o código
Execute o aplicativo do seu diretório de aplicativo com o comando `python`.

```console
python send-sms.py
```

O script completo do Python deve ser semelhante a:

```python

import os
from azure.communication.sms import SmsClient

try:
    # Create the SmsClient object which will be used to send SMS messages
    sms_client = SmsClient.from_connection_string("<connection string>")
    # calling send() with sms values
    sms_responses = sms_client.send(
       from_="<from-phone-number>",
       to="<to-phone-number>",
       message="Hello World via SMS",
       enable_delivery_report=True, # optional property
       tag="custom-tag") # optional property

except Exception as ex:
    print('Exception:')
    print(ex)
```
