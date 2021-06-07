---
title: Proxy REST do Apache Kafka – Azure HDInsight
description: Saiba como executar operações no Apache Kafka usando o proxy REST do Kafka no Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: has-adal-ref, devx-track-python
ms.date: 04/03/2020
ms.openlocfilehash: bc3cbe5d0d7cf5e5a78112ae5df63ebb88a97f5a
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864832"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interagir com os clusters do Apache Kafka no Azure HDInsight por meio de um proxy REST

O proxy REST do Kafka permite que você interaja com o cluster do Kafka por meio de uma API REST por HTTPS. Dessa maneira, os clientes do Kafka podem ficar fora da rede virtual. Os clientes podem fazer chamadas HTTPS simples e seguras para o cluster Kafka, em vez de depender de bibliotecas Kafka. Neste artigo, você verá como criar um proxy REST habilitado para o cluster do Kafka. Além disso, verá um exemplo de código que mostra como fazer chamadas ao proxy REST.

## <a name="rest-api-reference"></a>Referência da API REST

Para ver as operações compatíveis com a API REST do Kafka, confira [Referência de API do Proxy REST do Kafka no HDInsight](/rest/api/hdinsight-kafka-rest-proxy).

## <a name="background"></a>Segundo plano

:::image type="content" source="./media/rest-proxy/rest-proxy-architecture.png" alt-text="Design do proxy REST do Kafka" border="false":::

Para ver a especificação completa das operações compatíveis com a API, confira [API do Proxy REST do Apache Kafka](/rest/api/hdinsight-kafka-rest-proxy).

### <a name="rest-proxy-endpoint"></a>Ponto de extremidade de proxy REST

Quando você cria um cluster do Kafka no HDInsight com o proxy REST, um novo ponto de extremidade público é gerado para o cluster. É possível encontrá-lo no cluster **Propriedades** do HDInsight no portal do Azure.

### <a name="security"></a>Segurança

O acesso ao proxy REST do Kafka é gerenciado com os grupos de segurança do Azure Active Directory. Ao criar o cluster do Kafka, forneça o grupo de segurança do Azure AD com o acesso de ponto de extremidade REST. Os clientes do Kafka que precisam de acesso ao proxy REST devem ser registrados neste grupo pelo proprietário do grupo. O proprietário do grupo pode registrar por meio do Portal ou do PowerShell.

Para solicitações de ponto de extremidade de proxy REST, os aplicativos clientes precisam obter um token OAuth. O token é usado para verificar a associação ao grupo de segurança. Encontre um [exemplo de aplicativo cliente](#client-application-sample) abaixo que mostre como obter um token OAuth. O aplicativo cliente passa o token OAuth na solicitação HTTPS para o proxy REST.

> [!NOTE]
> Para saber mais sobre os grupos de segurança do AAD, confira [Gerenciar o acesso a aplicativos e recursos por meio dos grupos do Azure Active Directory](../../active-directory/fundamentals/active-directory-manage-groups.md). Para saber mais sobre como os tokens OAuth funcionam, confira [Autorizar o acesso aos aplicativos Web do Azure Active Directory por meio do fluxo de concessão de código do OAuth 2.0](../../active-directory/azuread-dev/v1-protocols-oauth-code.md).

## <a name="kafka-rest-proxy-with-network-security-groups"></a>Proxy REST do Kafka com os Grupos de Segurança de Rede
Se você colocar a VNet e controlar o tráfego de rede nos grupos de segurança de rede, permita o tráfego de **entrada** na porta **9400** além de na porta 443. Dessa maneira, o servidor proxy REST do Kafka fica acessível.

## <a name="prerequisites"></a>Pré-requisitos

1. Registrar um aplicativo com o Azure AD. Os aplicativos cliente que você escreve para interagir com o proxy REST do Kafka usarão a ID e o segredo do aplicativo para autenticação no Azure.

1. Crie um grupo de segurança do Azure AD. Adicione o aplicativo registrado no Azure AD ao grupo de segurança como um **membro** do grupo. Esse grupo de segurança será usado para controlar quais aplicativos têm permissão para interagir com o proxy REST. Para obter mais informações sobre como criar grupos do Azure AD, confira [Criar um grupo básico e adicionar membros usando o Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    Valide se o grupo é do tipo **Segurança**.
    :::image type="content" source="./media/rest-proxy/rest-proxy-group.png" alt-text="Grupo de Segurança" border="true":::

    Valide se o aplicativo é membro do grupo.
    :::image type="content" source="./media/rest-proxy/rest-proxy-membergroup.png" alt-text="Verificar Associação" border="true":::

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Criar um cluster do Kafka com o proxy REST habilitado

Use o portal do Azure nas etapas a seguir. Para ver um exemplo de como usar a CLI do Azure, confira [Criar cluster do proxy REST do Apache Kafka com a CLI do Azure](tutorial-cli-rest-proxy.md).

1. Durante o fluxo de trabalho de criação do cluster do Kafka, na guia **Segurança + rede**, verifique a opção **Habilitar proxy REST do Kafka**.

     :::image type="content" source="./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png" alt-text="Captura de tela mostra a página criar cluster do H D Insight com segurança + rede selecionada." border="true":::

1. Clique em **Selecionar Grupo de Segurança**. Na lista de grupos de segurança, selecione aqueles que você quer que tenham acesso ao proxy REST. Use a caixa de pesquisa para encontrar o grupo de segurança apropriado. Clique no botão **Selecionar** na parte inferior.

     :::image type="content" source="./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png" alt-text="Captura de tela mostra a página criar cluster do H D Insight com a opção de selecionar um grupo de segurança." border="true":::

1. Conclua as etapas restantes para criar o cluster, conforme descrito em [Criar um cluster do Apache Kafka no Azure HDInsight usando o portal do Azure](./apache-kafka-get-started.md).

1. Após a criação do cluster, acesse as propriedades dele para registrar a URL do proxy REST do Kafka.

     :::image type="content" source="./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png" alt-text="Exibir a URL do proxy REST" border="true":::

## <a name="client-application-sample"></a>Exemplo de aplicativo cliente

Use o código Python a seguir para interagir com o proxy REST no cluster do Kafka. Para usar o exemplo de código, siga estas etapas:

1. Salve o exemplo de código em um computador com o Python instalado.
1. Instale as dependências necessárias do Python executando `pip3 install msal`.
1. Modifique a seção de código **Configurar estas propriedades** e atualize as seguintes propriedades do seu ambiente:

    |Propriedade |Descrição |
    |---|---|
    |ID do locatário|O locatário do Azure em que sua assinatura está.|
    |ID do Cliente|A ID do aplicativo que você registrou no grupo de segurança.|
    |Segredo do cliente|O segredo para o aplicativo que você registrou no grupo de segurança.|
    |Kafkarest_endpoint|Obtenha esse valor na guia **Propriedades** da visão geral do cluster, conforme descrito na [seção de implantação](#create-a-kafka-cluster-with-rest-proxy-enabled). Ele deve estar no seguinte formato: `https://<clustername>-kafkarest.azurehdinsight.net`|

1. Na linha de comando, execute o arquivo Python com `sudo python3 <filename.py>`

Esse código faz o seguinte:

1. Busca um token OAuth no Azure AD.
1. Mostra como fazer uma solicitação para o proxy REST do Kafka.

Para obter mais informações sobre como obter tokens OAuth no Python, consulte [Python AuthenticationContext Class](/python/api/adal/adal.authentication_context.authenticationcontext). Talvez haja um atraso enquanto `topics` que não são criados ou excluídos por meio do proxy REST do Kafka são refletidos. Esse atraso ocorre por causa da atualização do cache. O campo de **valor** da API do produtor foi aprimorado. Agora, ele aceita objetos JSON e qualquer forma serializada.

```python
#Required python packages
#pip3 install msal

import json
import msal
import random
import requests
import string
import sys
import time

def get_random_string():
    letters = string.ascii_letters
    random_string = ''.join(random.choice(letters) for i in range(7))

    return random_string


#--------------------------Configure these properties-------------------------------#
# Tenant ID for your Azure Subscription
tenant_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Application Id
client_id = 'XYZABCDE-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Credentials
client_secret = 'password'
# kafka rest proxy -endpoint
kafkarest_endpoint = "https://<clustername>-kafkarest.azurehdinsight.net"
#--------------------------Configure these properties-------------------------------#

# Get access token
# Scope
scope = 'https://hib.azurehdinsight.net/.default'
#Authority
authority = 'https://login.microsoftonline.com/' + tenant_id

app = msal.ConfidentialClientApplication(
    client_id , client_secret, authority,
    #cache - For details on how look at this example: https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py
)

# The pattern to acquire a token looks like this.
result = None
result = app.acquire_token_for_client(scopes=[scope])
accessToken = result['access_token']
verify_https = True
request_timeout = 10

# Print access token
print("Access token: " + accessToken)

# API format
api_version = 'v1'
api_format = kafkarest_endpoint + '/{api_version}/{rest_api}'
get_topic_api = 'metadata/topics'
topic_api_format = 'topics/{topic_name}'
producer_api_format = 'producer/topics/{topic_name}'
consumer_api_format = 'consumer/topics/{topic_name}/partitions/{partition_id}/offsets/{offset}?count={count}'  # by default count = 1

# Request header
headers = {
    'Authorization': 'Bearer ' + accessToken,
    'Content-type': 'application/json'          # set Content-type to 'application/json'
}

# New topic
new_topic = 'hello_topic_' + get_random_string()
print("Topic " + new_topic + " is going to be used for demo.")

topics = []

# Create a  new topic
# Example of topic config
topic_config = {
    "partition_count": 1,
    "replication_factor": 1,
    "topic_properties": {
        "retention.ms": 604800000,
        "min.insync.replicas": "1"
    }
}

create_topic_url = api_format.format(api_version=api_version, rest_api=topic_api_format.format(topic_name=new_topic))
response = requests.put(create_topic_url, headers=headers, json=topic_config, timeout=request_timeout, verify=verify_https)
print(response.content)

if response.ok:
    while new_topic not in topics:
        print("The new topic " + new_topic + " is not visible yet. sleep 30 seconds...")
        time.sleep(30)
        # List Topic
        get_topic_url = api_format.format(api_version=api_version, rest_api=get_topic_api)

        response = requests.get(get_topic_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
        topic_list = response.json()
        topics = topic_list.get("topics", [])
else:
    print("Topic " + new_topic + " was created. Exit.")
    sys.exit(1)

# Produce messages to new_topic
# Example payload of Producer REST API
payload_json = {
    "records": [
        {
            "key": "key1",
            "value": "**********"         # A string                              
        },
        {
            "partition": 0,
            "value": 5                    # An integer
        },
        {
            "value": 3.14                 # A floating number
        },
        {
            "value": {                    # A JSON object
                "id": 1,
                "name": "HDInsight Kafka REST proxy"
            }
        },
        {
            "value": [                    # A list of JSON objects
                {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1"
                },
                {
                    "id": 2,
                    "name": "HDInsight Kafka REST proxy 2"
                },
                {
                    "id": 3,
                    "name": "HDInsight Kafka REST proxy 3"
                }
            ]
        },
        {
            "value": {                  # A nested JSON object
                "group id": 1,
                "HDI Kafka REST": {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1"
                },
                "HDI Kafka REST server info": {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1",
                    "servers": [
                        {
                            "server id": 1,
                            "server name": "HDInsight Kafka REST proxy server 1"
                        },
                        {
                            "server id": 2,
                            "server name": "HDInsight Kafka REST proxy server 2"
                        },
                        {
                            "server id": 3,
                            "server name": "HDInsight Kafka REST proxy server 3"
                        }
                    ]
                }
            }
        }
    ]
}

print("Payloads in a Producer request: \n", payload_json)
producer_url = api_format.format(api_version=api_version, rest_api=producer_api_format.format(topic_name=new_topic))
response = requests.post(producer_url, headers=headers, json=payload_json, timeout=request_timeout, verify=verify_https)
print(response.content)

# Consume messages from the topic
partition_id = 0
offset = 0
count = 2

while True:
    consumer_url = api_format.format(api_version=api_version, rest_api=consumer_api_format.format(topic_name=new_topic, partition_id=partition_id, offset=offset, count=count))
    print("Consuming " + str(count) + " messages from offset " + str(offset))

    response = requests.get(consumer_url, headers=headers, timeout=request_timeout, verify=verify_https)

    if response.ok:
        messages = response.json()
        print("Consumed messages: \n" + json.dumps(messages, indent=2))
        next_offset = response.headers.get("NextOffset")
        if offset == next_offset or not messages.get("records", []):
            print("Consumer caught up with producer. Exit for now...")
            break

        offset = next_offset

    else:
        print("Error " + str(response.status_code))
        break
        
# List partitions
get_partitions_url = api_format.format(api_version=api_version, rest_api=partitions_api_format.format(topic_name=new_topic))
print("Fetching partitions from  " + get_partitions_url)

response = requests.get(get_partitions_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
partition_list = response.json()
print("Partition list: \n" + json.dumps(partition_list, indent=2))

# List a partition
get_partition_url = api_format.format(api_version=api_version, rest_api=partition_api_format.format(topic_name=new_topic, partition_id=partition_id))
print("Fetching metadata of a partition from  " + get_partition_url)

response = requests.get(get_partition_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
partition = response.json()
print("Partition metadata: \n" + json.dumps(partition, indent=2))

```

Veja abaixo outro exemplo de como obter um token do Azure para o proxy REST usando um comando curl. **Observe que o `scope=https://hib.azurehdinsight.net/.default` especificado é necessário para obter o token.**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>Próximas etapas

* [Documentos de referência da API do proxy REST do Kafka](/rest/api/hdinsight-kafka-rest-proxy/)
