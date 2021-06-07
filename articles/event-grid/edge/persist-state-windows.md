---
title: Persistir o estado no Windows – grade de eventos do Azure IoT Edge | Microsoft Docs
description: Persistir estado no Windows
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: f38e23a3af1e2c81ee012a4f3c268cbff3fc1bee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96002810"
---
# <a name="persist-state-in-windows"></a>Persistir estado no Windows

Os tópicos e as assinaturas criados no módulo grade de eventos são armazenados no sistema de arquivos de contêiner por padrão. Sem persistência, se o módulo for reimplantado, todos os metadados criados serão perdidos. Para preservar os dados entre implantações e reinicializações, você precisa manter os dados fora do sistema de arquivos do contêiner. 

Por padrão, somente os metadados são persistidos e os eventos ainda são armazenados na memória para melhorar o desempenho. Siga a seção persistir eventos para habilitar a persistência de eventos também.

Este artigo fornece as etapas necessárias para implantar o módulo de grade de eventos com persistência em implantações do Windows.

> [!NOTE]
>O módulo de grade de eventos é executado como um **ContainerUser** de usuário com privilégios Baixos no Windows.

## <a name="persistence-via-volume-mount"></a>Persistência por meio de montagem de volume

Os [volumes do Docker](https://docs.docker.com/storage/volumes/) são usados para preservar dados entre implantações. Para montar um volume, você precisa criá-lo usando comandos do Docker, conceder permissões para que o contêiner possa ler, gravar nele e, em seguida, implantar o módulo.

1. Crie um volume executando o seguinte comando:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    Por exemplo,

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. Obtenha o diretório de host para o qual o volume é mapeado executando o comando abaixo

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    Por exemplo,

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume inspect myeventgridvol
   ```

   Saída de exemplo:-

   ```json
   [
          {
            "CreatedAt": "2019-07-30T21:20:59Z",
            "Driver": "local",
            "Labels": {},
            "Mountpoint": "C:\\ProgramData\\iotedge-moby\u000bolumes\\myeventgridvol\\_data",
            "Name": "myeventgridvol",
            "Options": {},
            "Scope": "local"
          }
   ]
   ```
1. Adicione o grupo **usuários** ao valor apontado pelo **mountpoint** da seguinte maneira:
    1. Inicie o explorador de arquivos.
    1. Navegue até a pasta apontada por **mountpoint**.
    1. Clique com o botão direito do mouse em e selecione **Propriedades**.
    1. Selecione **Segurança**.
    1. Em * nomes de grupo ou de usuário, selecione **Editar**.
    1. Selecione **Adicionar**, insira `Users` , selecione **verificar nomes** e selecione **OK**.
    1. Em *permissões para usuários*, selecione **Modificar** e selecione **OK**.
1. Usar **associações** para montar este volume e reimplantar o módulo de grade de eventos de portal do Azure

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
                  "<your-volume-name-here>:C:\\app\\metadataDb"
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
   >Não altere a segunda parte do valor de ligação. Ele aponta para um local específico no módulo. Para o módulo de grade de eventos no Windows, ele deve ser **C: \\ app \\ metadataDb**.


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
                "myeventgridvol:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
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

## <a name="persistence-via-host-directory-mount"></a>Persistência via montagem de diretório de host

Em vez de montar um volume, você pode criar um diretório no sistema host e montar esse diretório.

1. Crie um diretório no sistema de arquivos de host executando o comando a seguir.

   ```sh
   mkdir <your-directory-name-here>
   ```

   Por exemplo,

   ```sh
   mkdir C:\myhostdir
   ```
1. Use o **bindings** para montar seu diretório e reimplantar o módulo de grade de eventos do portal do Azure.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:C:\\app\\metadataDb"
             ]
         }
    }
    ```

    >[!IMPORTANT]
    >Não altere a segunda parte do valor de ligação. Ele aponta para um local específico no módulo. Para o módulo de grade de eventos no Windows, ele deve ser **C: \\ app \\ metadataDb**.

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
                "C:\\myhostdir:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
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
## <a name="persist-events"></a>Persistir eventos

Para habilitar a persistência de eventos, primeiro você deve habilitar a persistência de eventos por meio da montagem de volume ou da montagem de diretório de host usando as seções acima.

Coisas importantes a serem observadas sobre eventos persistentes:

* A persistência de eventos é habilitada por assinatura de evento e é opcional quando um volume ou diretório é montado.
* A persistência de evento é configurada em uma assinatura de evento no momento da criação e não pode ser modificada depois que a assinatura do evento é criada. Para alternar a persistência de evento, você deve excluir e recriar a assinatura de evento.
* A persistência de eventos é quase sempre mais lenta do que nas operações de memória, no entanto, a diferença de velocidade depende muito das características da unidade. A compensação entre velocidade e confiabilidade é inerente a todos os sistemas de mensagens, mas só se torna perceptível em grande escala.

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