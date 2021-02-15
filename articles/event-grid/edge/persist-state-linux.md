---
title: Persistir o estado no Linux-grade de eventos do Azure IoT Edge | Microsoft Docs
description: Manter metadados no Linux
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 92333c2528303a6fa53fa30f47def33c33235d39
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171475"
---
# <a name="persist-state-in-linux"></a>Persistir o estado no Linux

Os tópicos e as assinaturas criados no módulo grade de eventos são armazenados no sistema de arquivos de contêiner por padrão. Sem persistência, se o módulo for reimplantado, todos os metadados criados serão perdidos. Para preservar os dados entre implantações e reinicializações, você precisa manter os dados fora do sistema de arquivos do contêiner.

Por padrão, somente os metadados são persistidos e os eventos ainda são armazenados na memória para melhorar o desempenho. Siga a seção persistir eventos para habilitar a persistência de eventos também.

Este artigo fornece as etapas para implantar o módulo de grade de eventos com persistência em implantações do Linux.

> [!NOTE]
>O módulo de grade de eventos é executado como um usuário com poucos privilégios com UID `2000` e name `eventgriduser` .

## <a name="persistence-via-volume-mount"></a>Persistência por meio de montagem de volume

 Os [volumes do Docker](https://docs.docker.com/storage/volumes/) são usados para preservar os dados entre implantações. Você pode permitir que o Docker crie automaticamente um volume nomeado como parte da implantação do módulo de grade de eventos. Essa opção é a opção mais simples. Você pode especificar o nome do volume a ser criado na seção de **associações** da seguinte maneira:

```json
  {
     "HostConfig": {
          "Binds": [
                 "<your-volume-name-here>:/app/metadataDb"
           ]
      }
   }
```

>[!IMPORTANT]
>Não altere a segunda parte do valor de ligação. Ele aponta para um local específico dentro do módulo. Para o módulo de grade de eventos no Linux, ele deve ser **/app/metadataDb**.

Por exemplo, a configuração a seguir resultará na criação do volume **egmetadataDbVol** em que os metadados serão persistidos.

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge",
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true",
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge",
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ],
  "HostConfig": {
    "Binds": [
      "egmetadataDbVol:/app/metadataDb",
      "egdataDbVol:/app/eventsDb"
    ],
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```

Em vez de montar um volume, você pode criar um diretório no sistema host e montar esse diretório.

## <a name="persistence-via-host-directory-mount"></a>Persistência via montagem de diretório de host

Em vez de um volume do Docker, você também tem a opção de montar uma pasta de host.

1. Primeiro, crie um usuário com o nome **eventgriduser** e a ID **2000** no computador host executando o seguinte comando:

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. Crie um diretório no sistema de arquivos do host executando o comando a seguir.

   ```sh
   md <your-directory-name-here>
   ```

    Por exemplo, a execução do comando a seguir criará um diretório chamado **myhostdir**.

    ```sh
    md /myhostdir
    ```
1. Em seguida, torne o proprietário do **eventgriduser** desta pasta executando o comando a seguir.

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    Por exemplo,

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. Use **bindings** para montar o diretório e reimplantar o módulo de grade de eventos do portal do Azure.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:/app/metadataDb",
                "<your-directory-name-here>:/app/eventsDb",
             ]
         }
    }
    ```

    Por exemplo,

    ```json
    {
          "Env": [
            "inbound__serverAuth__tlsPolicy=strict",
            "inbound__serverAuth__serverCert__source=IoTEdge",
            "inbound__clientAuth__sasKeys__enabled=false",
            "inbound__clientAuth__clientCert__enabled=true",
            "inbound__clientAuth__clientCert__source=IoTEdge",
            "inbound__clientAuth__clientCert__allowUnknownCA=true",
            "outbound__clientAuth__clientCert__enabled=true",
            "outbound__clientAuth__clientCert__source=IoTEdge",
            "outbound__webhook__httpsOnly=true",
            "outbound__webhook__skipServerCertValidation=false",
            "outbound__webhook__allowUnknownCA=true"
          ],
          "HostConfig": {
                "Binds": [
                  "/myhostdir:/app/metadataDb",
                  "/myhostdir2:/app/eventsDb"
                ],
                "PortBindings": {
                      "4438/tcp": [
                        {
                          "HostPort": "4438"
                        }
                      ]
                }
          }
    }
    ```

    >[!IMPORTANT]
    >Não altere a segunda parte do valor de ligação. Ele aponta para um local específico dentro do módulo. Para o módulo de grade de eventos no Linux, ele deve ser **/app/metadataDb** e **/app/eventsDb**


## <a name="persist-events"></a>Persistir eventos

Para habilitar a persistência de evento, primeiro você deve habilitar a persistência de metadados por meio da montagem de volume ou da montagem de diretório de host usando as seções acima.

Coisas importantes a serem observadas sobre eventos persistentes:

* A persistência de eventos é habilitada por assinatura de evento e é opcional quando um volume ou diretório é montado.
* A persistência de evento é configurada em uma assinatura de evento no momento da criação e não pode ser modificada depois que a assinatura do evento é criada. Para alternar a persistência de evento, você deve excluir e recriar a assinatura de evento.
* A persistência de eventos é quase sempre mais lenta do que nas operações de memória, no entanto, a diferença de velocidade depende muito das características da unidade. A compensação entre velocidade e confiabilidade é inerente a todos os sistemas de mensagens, mas geralmente só se torna perceptível em grande escala.

Para habilitar a persistência de evento em uma assinatura de evento, defina `persistencePolicy` como `true` :

 ```json
        {
          "properties": {
            "persistencePolicy": {
              "isPersisted": "true"
            },
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-url>"
              }
            }
          }
        }
 ```
