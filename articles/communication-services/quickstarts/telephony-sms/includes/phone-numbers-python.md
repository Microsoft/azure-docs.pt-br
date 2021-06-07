---
ms.openlocfilehash: e62aed02a0ad5f26ec8fd0a79de5e91269386095
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450274"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2.7, 3.5 ou superior.
- Um recurso e uma cadeia de conexão dos Serviços de Comunicação implantados. [Crie um recurso dos Serviços de Comunicação](../../create-communication-resource.md).

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-python-application"></a>Criar um novo aplicativo Python

Abra a janela de comando ou do terminal a fim de criar um diretório para o aplicativo e navegue até ele.

```console
mkdir phone-numbers-quickstart && cd phone-numbers-quickstart
```

Use um editor de texto para criar um arquivo chamado phone_numbers_sample.py no diretório raiz do projeto e adicione o código a seguir. Vamos adicionar o código de início rápido restante nas seções a seguir.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient

try:
   print('Azure Communication Services - Phone Numbers Quickstart')
   # Quickstart code goes here
except Exception as ex:
   print('Exception:')
   print(ex)
```

### <a name="install-the-package"></a>Instalar o pacote

Ainda no diretório do aplicativo, instale o pacote da biblioteca de clientes da Administração dos Serviços de Comunicação do Azure para Python usando o comando `pip install`.

```console
pip install azure-communication-phonenumbers
```

## <a name="authenticate-the-phone-numbers-client"></a>Autenticar o cliente de números de telefone

O `PhoneNumbersClient` está habilitado para usar a Autenticação do Azure Active Directory. Usar o objeto `DefaultAzureCredential` é a maneira mais fácil de começar a usar o Azure Active Directory e você pode instalá-lo usando o comando `pip install`.

```console
pip install azure-identity
```

A criação de um objeto `DefaultAzureCredential` exige que você tenha `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET` e `AZURE_TENANT_ID` já definidos como variáveis de ambiente com seus valores correspondentes do aplicativo registrado do Azure AD.

Depois de instalar a biblioteca `azure-identity`, podemos continuar autenticando o cliente.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
except Exception as ex:
    print('Exception:')
    print(ex)
```

Como alternativa, o uso do ponto de extremidade e da chave de acesso do recurso de comunicação para autenticar também é possível.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient

# You can find your connection string from your resource in the Azure portal
connection_string = 'https://<RESOURCE_NAME>.communication.azure.com/;accesskey=<YOUR_ACCESS_KEY>'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    phone_numbers_client = PhoneNumbersClient.from_connection_string(connection_string)
except Exception as ex:
    print('Exception:')
    print(ex)
```

## <a name="functions"></a>Funções

Depois que `PhoneNumbersClient` tiver sido autenticado, podemos começar a trabalhar nas diferentes funções que ele pode fazer.

### <a name="search-for-available-phone-numbers"></a>Pesquisar números de telefone disponíveis

Para comprar números de telefone, você deve primeiro procurar por números de telefone disponíveis. Para procurar por números de telefone, forneça o código de área, o tipo de atribuição, as [funcionalidades do número de telefone](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services), o [tipo do número de telefone](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services) e a quantidade (a quantidade padrão é definida como 1). Observe que para números de telefone do tipo chamada gratuita, fornecer o código de área é opcional.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient, PhoneNumberCapabilityType, PhoneNumberAssignmentType, PhoneNumberType, PhoneNumberCapabilities
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
    capabilities = PhoneNumberCapabilities(
        calling = PhoneNumberCapabilityType.INBOUND,
        sms = PhoneNumberCapabilityType.INBOUND_OUTBOUND
    )
    search_poller = self.phone_number_client.begin_search_available_phone_numbers(
        "US",
        PhoneNumberType.TOLL_FREE,
        PhoneNumberAssignmentType.APPLICATION,
        capabilities,
        polling = True
    )
    search_result = search_poller.result()
    print ('Search id: ' + search_result.search_id)
    phone_number_list = search_result.phone_numbers
    print('Reserved phone numbers:')
    for phone_number in phone_number_list:
        print(phone_number)

except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="purchase-phone-numbers"></a>Comprar números de telefone

O resultado da pesquisa por números de telefone é um `PhoneNumberSearchResult`. Ela contém um `searchId` que pode ser passado para a API de comprar números para adquirir os números na pesquisa. Observe que chamar a API de comprar números de telefone resultará em um encargo para sua conta do Azure.

```python
import os
from azure.communication.phonenumbers import (
    PhoneNumbersClient,
    PhoneNumberCapabilityType,
    PhoneNumberAssignmentType,
    PhoneNumberType,
    PhoneNumberCapabilities
)
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
    capabilities = PhoneNumberCapabilities(
        calling = PhoneNumberCapabilityType.INBOUND,
        sms = PhoneNumberCapabilityType.INBOUND_OUTBOUND
    )
    search_poller = phone_numbers_client.begin_search_available_phone_numbers(
        "US",
        PhoneNumberType.TOLL_FREE,
        PhoneNumberAssignmentType.APPLICATION,
        capabilities,
        area_code="833",
        polling = True
    )
    search_result = poller.result()
    print ('Search id: ' + search_result.search_id)
    phone_number_list = search_result.phone_numbers
    print('Reserved phone numbers:')
    for phone_number in phone_number_list:
        print(phone_number)

    purchase_poller = phone_numbers_client.begin_purchase_phone_numbers(search_result.search_id, polling = True)
    purchase_poller.result()
    print("The status of the purchase operation was: " + purchase_poller.status())
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="get-purchased-phone-numbers"></a>Obter números de telefone comprados

Depois de comprar um número, você poderá recuperá-lo do cliente.

```python
purchased_phone_number_information = phone_numbers_client.get_purchased_phone_number("+18001234567")
print('Phone number: ' + purchased_phone_number_information.phone_number)
print('Country code: ' + purchased_phone_number_information.country_code)
```

Você também pode recuperar todos os números de telefone comprados.

```python
purchased_phone_numbers = phone_numbers_client.list_purchased_phone_numbers()
print('Purchased phone numbers:')
for purchased_phone_number in purchased_phone_numbers:
    print(purchased_phone_number.phone_number)
```

### <a name="update-phone-number-capabilities"></a>Atualizar as funcionalidades do número de telefone

Você pode atualizar as funcionalidades de um número de telefone adquirido anteriormente.
```python
update_poller = phone_numbers_client.begin_update_phone_number_capabilities(
    "+18001234567",
    PhoneNumberCapabilityType.OUTBOUND,
    PhoneNumberCapabilityType.OUTBOUND,
    polling = True
)
update_poller.result()
print('Status of the operation: ' + update_poller.status())
```

### <a name="release-phone-number"></a>Liberar número de telefone

Você pode liberar um número de telefone comprado.

```python
release_poller = phone_numbers_client.begin_release_phone_number("+18001234567")
release_poller.result()
print('Status of the operation: ' + release_poller.status())
```

## <a name="run-the-code"></a>Executar o código

Do prompt do console, navegue até o diretório que contém o arquivo phone_numbers_sample.py e execute o comando Python a seguir para executar o aplicativo.

```console
./phone_numbers_sample.py
```
