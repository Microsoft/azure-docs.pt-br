---
title: Início Rápido – Configurar o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure usando a CLI do Azure
description: Início Rápido – Configurar o DPS (Serviço de Provisionamento de Dispositivos) no Hub IoT do Azure usando a CLI do Azure
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 3d52a83c8c0920c4d85aa5b4b6b89fd8d36e5fea
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774919"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Início Rápido: Configurar o Serviço de Provisionamento de Dispositivos no Hub IoT com a CLI do Azure

A CLI do Azure é usada para criar e gerenciar recursos do Azure da linha de comando ou em scripts. Este início rápido detalha o uso da CLI do Azure para criar um Hub IoT e um Serviço de Provisionamento de Dispositivos no Hub IoT e vincular os dois serviços. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> O Hub IoT e o serviço de provisionamento criado neste início rápido serão detectáveis publicamente como pontos de extremidade DNS. Evite usar informações confidenciais se você decidir alterar os nomes usados nesses recursos.
>

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]


## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az_group_create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

O exemplo a seguir cria um grupo de recursos chamado *my-sample-resource-group* na localização *westus*.

```azurecli-interactive 
az group create --name my-sample-resource-group --location westus
```

> [!TIP]
> O exemplo cria o grupo de recursos localizado no Oeste dos EUA. Você pode exibir uma lista dos locais disponíveis executando o comando `az account list-locations -o table`.
>
>

## <a name="create-an-iot-hub"></a>Crie um hub IoT

Crie um hub IoT com o comando [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create).

O exemplo a seguir cria um hub IoT denominado *my-sample-hub* no local *westus*. O nome de um Hub IoT deve ser globalmente exclusivo no Azure, de modo que talvez você queira adicionar um prefixo ou sufixo exclusivo ao nome de exemplo ou escolher um novo nome. Certifique-se de que seu nome siga a convenção de nomenclatura adequada do hub IoT: ele deve ter de 3 a 50 caracteres de comprimento e conter somente caracteres alfanuméricos minúsculos ou hifens ('-'). 

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-device-provisioning-service"></a>Criar um Serviço de Provisionamento de Dispositivos

Crie um Serviço de Provisionamento de Dispositivos com o comando [az iot dps create](/cli/azure/iot/dps#az_iot_dps_create). 

O exemplo a seguir cria um serviço de provisionamento denominado *my-sample-dps* na localização *westus*. Você também precisará escolher um nome globalmente exclusivo para seu próprio serviço de provisionamento. Certifique-se de que ele siga a convenção de nomenclatura adequada para um Serviço de Provisionamento de Dispositivos no Hub IoT: ele deve ter de 3 a 64 caracteres de comprimento e conter somente caracteres alfanuméricos minúsculos ou hifens ('-').

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> O exemplo cria o serviço de provisionamento no local Oeste dos EUA. Você pode exibir uma lista de locais disponíveis executando o comando `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` ou acessando a página [Status do Azure](https://azure.microsoft.com/status/) e pesquisando por "Serviço de Provisionamento de Dispositivos". Nos comandos, os locais podem ser especificados em formato de uma ou várias palavras, por exemplo, westus, West US, WEST US, etc. O valor não diferencia maiúsculas de minúsculas. Se você usar o formato de várias palavras para especificar o local, coloque o valor entre aspas, por exemplo, `--location "West US"`.
>

## <a name="get-the-connection-string-for-the-iot-hub"></a>Obter a cadeia de conexão para o hub IoT

Você precisa da cadeia de conexão do seu hub IoT para vinculá-lo ao serviço de provisionamento de dispositivos. Use o comando [az iot hub show-connection-string](/cli/azure/iot/hub#az_iot_hub_show_connection_string) para obter a cadeia de conexão e use a saída para definir uma variável que será usada quando for vincular os dois recursos. 

O exemplo a seguir define a variável *hubConnectionString* como o valor da cadeia de conexão para a chave primária da política *iothubowner* do hub (o parâmetro `--policy-name` pode ser usado para especificar uma política diferente). Troque *my-sample-hub* pelo nome exclusivo do Hub IoT escolhido anteriormente. O comando usa as opções [consulta](/cli/azure/query-azure-cli) e [saída](/cli/azure/format-output-azure-cli#tsv-output-format) da CLI do Azure para extrair a cadeia de conexão da saída do comando.

```azurecli-interactive 
hubConnectionString=$(az iot hub show-connection-string --name my-sample-hub --key primary --query connectionString -o tsv)
```

Você pode usar o comando `echo` para ver a cadeia de conexão.

```azurecli-interactive 
echo $hubConnectionString
```

> [!NOTE]
> Esses dois comandos são válidos para um host executado em Bash.
> 
> Se você estiver usando um shell CMD/Windows local ou um host do PowerShell, modifique os comandos para usar a sintaxe correta do ambiente.
>
> Se você estiver usando o Azure Cloud Shell, verifique se a lista suspensa ambiente no lado esquerdo da janela do shell diz **Bash**.
>

## <a name="link-the-iot-hub-and-the-provisioning-service"></a>Vincular o Hub IoT ao serviço de provisionamento

Vincule o hub IoT e o serviço de provisionamento ao comando [az iot dps linked-hub create](/cli/azure/iot/dps/linked-hub#az_iot_dps_linked_hub_create). 

O exemplo a seguir vincula um hub IoT denominado *my-sample-hub* na localização *westus* e um serviço de provisionamento de dispositivos denominado *my-sample-dps*. Troque esses nomes pelos nomes exclusivos do hub IoT e do Serviço de Provisionamento de Dispositivos escolhidos anteriormente. O comando usa a cadeia de conexão para seu hub IoT que foi armazenada na variável *hubConnectionString* na etapa anterior.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

O comando pode demorar um pouco para ser concluído.

## <a name="verify-the-provisioning-service"></a>Verificar o serviço de provisionamento

Obtenha os detalhes do seu serviço de provisionamento com o comando [az iot dps show](/cli/azure/iot/dps#az_iot_dps_show).

O exemplo a seguir obtém os detalhes de um serviço de provisionamento denominado *my-sample-dps*. Troque esse nome pelo nome de seu Serviço de Provisionamento de Dispositivos.

```azurecli-interactive
az iot dps show --name my-sample-dps
```
O hub IoT vinculado é mostrado na coleção *properties.iotHubs*.

![Verificar o Serviço de Provisionamento](./media/quick-setup-auto-provision-cli/verify-provisioning-service.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Outros inícios rápidos nessa coleção aproveitam esse início rápido. Se você planeja continuar trabalhando com inícios rápidos subsequentes ou com os tutoriais, não limpe os recursos criados nesse início rápido. Se você não planeja continuar, pode usar os comandos a seguir para excluir o serviço de provisionamento, o hub IoT ou o grupo de recursos e todos os seus recursos. Substitua os nomes dos recursos escritos abaixo pelos nomes dos seus recursos.

Para excluir o serviço de provisionamento, execute o comando [az iot dps delete](/cli/azure/iot/dps#az_iot_dps_delete):

```azurecli-interactive
az iot dps delete --name my-sample-dps --resource-group my-sample-resource-group
```
Para excluir o hub IoT, execute o comando [az iot hub delete](/cli/azure/iot/hub#az_iot_hub_delete):

```azurecli-interactive
az iot hub delete --name my-sample-hub --resource-group my-sample-resource-group
```

Para excluir um grupo de recursos e todos os seus recursos, execute o comando [az group delete](/cli/azure/group#az_group_delete):

```azurecli-interactive
az group delete --name my-sample-resource-group
```

## <a name="next-steps"></a>Próximas etapas

Neste Início Rápido, você implantou um Hub IoT e uma instância do Serviço de Provisionamento de Dispositivos, bem como vinculou os dois recursos. Para aprender a usar essa configuração a fim de provisionar um dispositivo simulado, prossiga para o Início Rápido de criação de dispositivo simulado.

> [!div class="nextstepaction"]
> [Início Rápido para criar um dispositivo simulado](./quick-create-simulated-device.md)
