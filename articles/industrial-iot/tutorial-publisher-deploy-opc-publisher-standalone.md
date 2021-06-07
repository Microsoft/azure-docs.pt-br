---
title: Implantar o Microsoft OPC Publisher
description: Neste tutorial, você aprenderá a implantar o OPC Publisher no modo autônomo.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: c82d15541459b5b482e427fc707b92755aa02c6c
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787167"
---
# <a name="tutorial-deploy-the-opc-publisher"></a>Tutorial: Implantar o OPC Publisher

O OPC Publisher é um produto da Microsoft com suporte completo, desenvolvido como software livre, que preenche a lacuna entre os ativos industriais e a nuvem do Microsoft Azure. Ele faz isso conectando-se a ativos habilitados para OPC UA ou a programas de software de conectividade industrial e publica os dados telemétricos no [Hub IoT do Azure](https://azure.microsoft.com/services/iot-hub/) em vários formatos, incluindo o formato padrão IEC62541 OPC UA PubSub (da versão 2.6 em diante).

Ele é executado no [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) como um módulo ou em um Docker simples como um contêiner. Como ele aproveita o [runtime multiplataforma do .NET](https://docs.microsoft.com/dotnet/core/introduction), também é executado nativamente no Linux e no Windows 10.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Implantar o OPC Publisher
> * Executar a última versão de lançamento do OPC Publisher como um contêiner
> * Especificar opções de criação de contêiner no portal do Azure

Caso você não tenha uma assinatura do Azure, crie uma conta de avaliação gratuita

## <a name="prerequisites"></a>Pré-requisitos

- Um Hub IoT precisa ser criado
- Um dispositivo IoT Edge precisa ser criado

## <a name="deploy-the-opc-publisher-from-the-azure-marketplace"></a>Implantar o OPC Publisher do Azure Marketplace

1. Escolha a assinatura do Azure a ser usada. Se nenhuma assinatura do Azure estiver disponível, será necessário criá-la.
2. Escolha o Hub IoT ao qual o OPC Publisher deve enviar dados. Se não houver um Hub IoT disponível, será necessário criá-lo.
3. Escolha o dispositivo IoT Edge no qual o OPC Publisher deve ser executado (ou insira um nome para o dispositivo IoT Edge a ser criado).
4. Clique em Criar. A página "Definir módulos no Dispositivo" do dispositivo IoT Edge selecionado será aberta.
5. Clique em "OPCPublisher" para abrir a página "Atualizar Módulo do IoT Edge" do OPC Publisher e selecione "Opções de Criação de Contêiner".
6. Para especificar opções adicionais de criação de contêiner com base no uso do OPC Publisher, confira a próxima seção abaixo.


### <a name="accessing-the-microsoft-container-registry-docker-containers-for-opc-publisher-manually"></a>Como acessar os contêineres do Docker no Microsoft Container Registry para o OPC Publisher manualmente

A última versão de lançamento do OPC Publisher pode ser executada manualmente por meio do seguinte:

```
docker run mcr.microsoft.com/iotedge/opc-publisher:latest <name>
```

Em que "name" é o nome do contêiner.

## <a name="specifying-container-create-options-in-the-azure-portal"></a>Como especificar opções de criação de contêiner no portal do Azure
Ao implantar o OPC Publisher pelo portal do Azure, as opções de criação de contêiner podem ser especificadas na página Atualizar Módulo do IoT Edge do OPC Publisher. Essas opções de criação precisam estar no formato JSON. Os argumentos de linha de comando do OPC Publisher podem ser especificados por meio da tecla Cmd, por exemplo:
```
"Cmd": [
    "--pf=./pn.json",
    "--aa"
],
```

Um conjunto típico de opções de criação de contêiner do módulo do IoT Edge para o OPC Publisher é:
```
{
    "Hostname": "opcpublisher",
    "Cmd": [
        "--pf=./pn.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "/iiotedge:/appdata"
        ]
    }
}
```

Com essas opções especificadas, o OPC Publisher lerá o arquivo de configuração `./pn.json`. O diretório de trabalho do OPC Publisher é definido como `/appdata` na inicialização e, portanto, o OPC Publisher lerá o arquivo `/appdata/pn.json` dentro do contêiner do Docker. O arquivo de log do OPC Publisher será gravado em `/appdata`, e o diretório `CertificateStores` (usado para certificados OPC UA) também será criado nesse diretório. Para disponibilizar esses arquivos no sistema de arquivos de host do IoT Edge, a configuração do contêiner exige um volume de montagem de associação. A associação `/iiotedge:/appdata` mapeará o diretório `/appdata` para o diretório de host `/iiotedge` (que será criado pelo runtime do IoT Edge se ele não existir).
**Sem esse volume de montagem de associação, todos os arquivos de configuração do OPC Publisher serão perdidos quando o contêiner for reiniciado.**

Uma conexão com um servidor OPC UA que usa o nome de host sem um servidor DNS configurado na rede pode ser obtida pela adição de uma entrada `ExtraHosts` à seção `HostConfig`:

```
"HostConfig": {
    "ExtraHosts": [
        "opctestsvr:192.168.178.26"
    ]
}
```

## <a name="next-steps"></a>Próximas etapas 
Agora que você implantou o módulo do Edge no OPC Publisher, a próxima etapa será configurá-lo:

> [!div class="nextstepaction"]
> [Configurar o OPC Publisher](tutorial-publisher-configure-opc-publisher.md)