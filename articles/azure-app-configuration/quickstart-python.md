---
title: Início rápido para usar a Configuração de Aplicativos do Azure com aplicativos Python | Microsoft Docs
description: Neste início rápido, crie um aplicativo Python com a Configuração de Aplicativos do Azure para centralizar o armazenamento e o gerenciamento de configurações de aplicativos separadas do código.
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: devx-track-python
ms.date: 9/17/2020
ms.author: drewbat
ms.openlocfilehash: 1be74ceeb1f4808a8df8f56b71b66aa870cdd91d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104800866"
---
# <a name="quickstart-create-a-python-app-with-azure-app-configuration"></a>Início Rápido: Criar um aplicativo Python com a Configuração de Aplicativos do Azure

Neste início rápido, você usará a Configuração de Aplicativos do Azure para centralizar o armazenamento e o gerenciamento de configurações de aplicativos usando a [Biblioteca de clientes da Configuração de Aplicativos do Azure para Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration).

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/)
- O Python 2.7, 3.6 ou posterior – Para obter informações sobre como configurar o Python no Windows, confira a [Documentação do Python no Windows]( https://docs.microsoft.com/windows/python/)

## <a name="create-an-app-configuration-store"></a>Criar um repositório de Configuração de Aplicativos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Selecione **Gerenciador de Configurações** > **Criar** > **Chave-valor** para adicionar os seguintes pares chave-valor:

    | Chave | Valor |
    |---|---|
    | TestApp:Settings:Message | Dados da Configuração de Aplicativo do Azure |

    Deixe **Rótulo** e **Tipo de Conteúdo** vazios por enquanto.

8. Escolha **Aplicar**.

## <a name="setting-up-the-python-app"></a>Como configurar um aplicativo Python

1. Neste tutorial, você criará um diretório para o projeto chamado *app-configuration-quickstart*.

    ```console
    mkdir app-configuration-quickstart
    ```

1. Mude para o diretório *app-configuration-quickstart* que foi recém-criado.

    ```console
    cd app-configuration-quickstart
    ```

1. Instale a biblioteca de clientes da Configuração de Aplicativos do Azure usando o comando `pip install`.

    ```console
    pip install azure-appconfiguration
    ```

1. Crie um arquivo chamado *app-configuration-quickstart.py* no diretório *app-configuration-quickstart* e adicione o seguinte código:

    ```python
    import os
    from azure.appconfiguration import AzureAppConfigurationClient, ConfigurationSetting
    
    try:
        print("Azure App Configuration - Python Quickstart")
        # Quickstart code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)
    ```

> [!NOTE]
> Os snippets de código deste início rápido ajudarão você a começar a usar a biblioteca de clientes da Configuração de Aplicativos para Python. Você também deverá considerar usar um tratamento de exceções para o aplicativo de acordo com suas necessidades. Para saber mais sobre o tratamento de exceções, confira a [documentação do SDK do Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration).

## <a name="configure-your-app-configuration-connection-string"></a>Configurar uma cadeia de conexão da Configuração de Aplicativos

1. Configure uma variável de ambiente chamada **AZURE_APP_CONFIG_CONNECTION_STRING** e defina-a como a chave de acesso ao repositório da Configuração de Aplicativos. Na linha de comando, execute o seguinte comando:

    ```cmd
    setx AZURE_APP_CONFIG_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    Se você usa o Windows PowerShell, execute o comando a seguir:

    ```azurepowershell
    $Env:AZURE_APP_CONFIG_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    Se você usa macOS ou Linux, execute o comando a seguir:

    ```console
    export AZURE_APP_CONFIG_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

2. Reinicie o prompt de comando para permitir que a alteração entre em vigor. Imprima o valor da variável de ambiente para confirmar que ele está definido corretamente.

## <a name="code-samples"></a>Exemplos de código

Os snippets de código de exemplo desta seção mostrarão como executar operações comuns com a biblioteca de clientes da Configuração de Aplicativos para Python. Adicione esses snippets de código ao bloco `try` no arquivo *app-configuration-quickstart.py* criado anteriormente.

> [!NOTE]
> A biblioteca de clientes da Configuração de Aplicativos se refere a um objeto chave-valor como `ConfigurationSetting`. Portanto, neste artigo, os **pares chave-valor** no repositório da Configuração de Aplicativos serão chamadas de **Parâmetros de configuração**.

* [Conectar um repositório de Configuração de Aplicativos](#connect-to-an-app-configuration-store)
* [Obter um parâmetro de configuração](#get-a-configuration-setting)
* [Adicionar um parâmetro de configuração](#add-a-configuration-setting)
* [Obter uma lista de parâmetros de configuração](#get-a-list-of-configuration-settings)
* [Bloquear um parâmetro de configuração](#lock-a-configuration-setting)
* [Desbloquear um parâmetro de configuração](#unlock-a-configuration-setting)
* [Atualizar um parâmetro de configuração](#update-a-configuration-setting)
* [Excluir um parâmetro de configuração](#delete-a-configuration-setting)

### <a name="connect-to-an-app-configuration-store"></a>Conectar um repositório de Configuração de Aplicativos

O snippet de código a seguir criará uma instância do **AzureAppConfigurationClient** usando a cadeia de conexão armazenada nas variáveis de ambiente.

```python
    connection_string = os.getenv('AZURE_APP_CONFIG_CONNECTION_STRING')
    app_config_client = AzureAppConfigurationClient.from_connection_string(connection_string)
```

### <a name="get-a-configuration-setting"></a>Obter um parâmetro de configuração

O snippet de código a seguir recuperará um parâmetro de configuração por nome de `key`.

```python
    retrieved_config_setting = app_config_client.get_configuration_setting(key='TestApp:Settings:Message')
    print("\nRetrieved configuration setting:")
    print("Key: " + retrieved_config_setting.key + ", Value: " + retrieved_config_setting.value)
```

### <a name="add-a-configuration-setting"></a>Adicionar um parâmetro de configuração

O snippet de código a seguir criará um objeto `ConfigurationSetting` com os campos `key` e `value`, bem como invocará o método `add_configuration_setting`. Esse método gerará uma exceção caso tente adicionar um parâmetro de configuração que já exista em seu repositório. Caso queira evitar essa exceção, o método [set_configuration_setting](#update-a-configuration-setting) poderá ser usado como alternativa.

```python
    config_setting = ConfigurationSetting(
        key='TestApp:Settings:NewSetting',
        value='New setting value'
    )
    added_config_setting = app_config_client.add_configuration_setting(config_setting)
    print("\nAdded configuration setting:")
    print("Key: " + added_config_setting.key + ", Value: " + added_config_setting.value)
```

### <a name="get-a-list-of-configuration-settings"></a>Obter uma lista de parâmetros de configuração

O snippet de código a seguir recuperará uma lista de parâmetros de configuração. Os argumentos `key_filter` e `label_filter` poderão ser fornecidos para filtrar pares chave-valor com base na `key` e no `label`, respectivamente. Para obter mais informações sobre filtragem, confira como [consultar parâmetros de configuração](./concept-key-value.md#query-key-values).

```python
    filtered_settings_list = app_config_client.list_configuration_settings(key_filter="TestApp*")
    print("\nRetrieved list of configuration settings:")
    for item in filtered_settings_list:
        print("Key: " + item.key + ", Value: " + item.value)
```

### <a name="lock-a-configuration-setting"></a>Bloquear um parâmetro de configuração

O status do bloqueio de uma chave-valor na Configuração de Aplicativos será indicado pelo atributo `read_only` do objeto `ConfigurationSetting`. Caso `read_only` seja `True`, a configuração será bloqueada. O método `set_read_only` poderá ser invocado com o argumento `read_only=True` para bloquear o parâmetro de configuração.

```python
    locked_config_setting = app_config_client.set_read_only(added_config_setting, read_only=True)
    print("\nRead-only status for " + locked_config_setting.key + ": " + str(locked_config_setting.read_only))
```

### <a name="unlock-a-configuration-setting"></a>Desbloquear um parâmetro de configuração

Caso o atributo `read_only` do `ConfigurationSetting` seja `False`, a configuração será desbloqueada. O método `set_read_only` poderá ser invocado com o argumento `read_only=False` para desbloquear o parâmetro de configuração.

```python
    unlocked_config_setting = app_config_client.set_read_only(locked_config_setting, read_only=False)
    print("\nRead-only status for " + unlocked_config_setting.key + ": " + str(unlocked_config_setting.read_only))
```

### <a name="update-a-configuration-setting"></a>Atualizar um parâmetro de configuração

O método `set_configuration_setting` poderá ser usado para atualizar uma configuração existente ou para criar um configuração. O snippet de código a seguir alterará o valor de um parâmetro de configuração existente.

```python
    added_config_setting.value = "Value has been updated!"
    updated_config_setting = app_config_client.set_configuration_setting(added_config_setting)
    print("\nUpdated configuration setting:")
    print("Key: " + updated_config_setting.key + ", Value: " + updated_config_setting.value)
```

### <a name="delete-a-configuration-setting"></a>Excluir um parâmetro de configuração

O snippet de código a seguir excluirá um parâmetro de configuração por nome de `key`.

```python
    deleted_config_setting = app_config_client.delete_configuration_setting(key="TestApp:Settings:NewSetting")
    print("\nDeleted configuration setting:")
    print("Key: " + deleted_config_setting.key + ", Value: " + deleted_config_setting.value)
```

## <a name="run-the-app"></a>Executar o aplicativo

Neste início rápido, você criou um aplicativo Python que usa uma biblioteca de clientes da Configuração de Aplicativos do Azure para recuperar um parâmetro de configuração criado por meio do portal do Azure, além de adicionar um novo parâmetro, recuperar uma lista de parâmetros existentes, bloquear e desbloquear um parâmetro, atualizar um parâmetro e, por fim, excluir um parâmetro.

Nesse momento, o arquivo *app-configuration-quickstart.py* deverá ter o seguinte código:

```python
import os
from azure.appconfiguration import AzureAppConfigurationClient, ConfigurationSetting

try:
    print("Azure App Configuration - Python Quickstart")
    # Quickstart code goes here

    connection_string = os.getenv('AZURE_APP_CONFIG_CONNECTION_STRING')
    app_config_client = AzureAppConfigurationClient.from_connection_string(connection_string)

    retrieved_config_setting = app_config_client.get_configuration_setting(key='TestApp:Settings:Message')
    print("\nRetrieved configuration setting:")
    print("Key: " + retrieved_config_setting.key + ", Value: " + retrieved_config_setting.value)

    config_setting = ConfigurationSetting(
        key='TestApp:Settings:NewSetting',
        value='New setting value'
    )
    added_config_setting = app_config_client.add_configuration_setting(config_setting)
    print("\nAdded configuration setting:")
    print("Key: " + added_config_setting.key + ", Value: " + added_config_setting.value)

    filtered_settings_list = app_config_client.list_configuration_settings(key_filter="TestApp*")
    print("\nRetrieved list of configuration settings:")
    for item in filtered_settings_list:
        print("Key: " + item.key + ", Value: " + item.value)

    locked_config_setting = app_config_client.set_read_only(added_config_setting, read_only=True)
    print("\nRead-only status for " + locked_config_setting.key + ": " + str(locked_config_setting.read_only))

    unlocked_config_setting = app_config_client.set_read_only(locked_config_setting, read_only=False)
    print("\nRead-only status for " + unlocked_config_setting.key + ": " + str(unlocked_config_setting.read_only))

    added_config_setting.value = "Value has been updated!"
    updated_config_setting = app_config_client.set_configuration_setting(added_config_setting)
    print("\nUpdated configuration setting:")
    print("Key: " + updated_config_setting.key + ", Value: " + updated_config_setting.value)

    deleted_config_setting = app_config_client.delete_configuration_setting(key="TestApp:Settings:NewSetting")
    print("\nDeleted configuration setting:")
    print("Key: " + deleted_config_setting.key + ", Value: " + deleted_config_setting.value)

except Exception as ex:
    print('Exception:')
    print(ex)
```

Na janela do console, acesse o diretório que contém o arquivo *app-configuration-quickstart.py* e execute o seguinte comando Python para executar o aplicativo:

```console
python app-configuration-quickstart.py
```

Você deve ver o seguinte resultado:

```output
Azure App Configuration - Python Quickstart

Retrieved configuration setting:
Key: TestApp:Settings:Message, Value: Data from Azure App Configuration

Added configuration setting:
Key: TestApp:Settings:NewSetting, Value: New setting value

Retrieved list of configuration settings:
Key: TestApp:Settings:Message, Value: Data from Azure App Configuration
Key: TestApp:Settings:NewSetting, Value: New setting value

Read-only status for TestApp:Settings:NewSetting: True

Read-only status for TestApp:Settings:NewSetting: False

Updated configuration setting:
Key: TestApp:Settings:NewSetting, Value: Value has been updated!

Deleted configuration setting:
Key: TestApp:Settings:NewSetting, Value: Value has been updated!
```

## <a name="clean-up-resources"></a>Limpar os recursos


[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um repositório da Configuração de Aplicativos e aprendeu a acessar pares chave-valor de um aplicativo Python.

Para obter códigos de exemplo adicionais, visite:

> [!div class="nextstepaction"]
> [Exemplos de biblioteca de clientes da Configuração de Aplicativos do Azure](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration/samples)
