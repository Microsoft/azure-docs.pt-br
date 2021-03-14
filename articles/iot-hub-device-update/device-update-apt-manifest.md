---
title: Entender a atualização do dispositivo para o manifesto APT do Hub IoT do Azure | Microsoft Docs
description: Entenda como a atualização de dispositivo para o Hub IoT usa o manifesto de apt para uma atualização baseada em pacote.
author: vimeht
ms.author: vimeht
ms.date: 2/17/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 0b68b78499aa3bf0d84d8bd0fa5ab55d1f969113
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101678987"
---
# <a name="device-update-apt-manifest"></a>Manifesto de APT de atualização de dispositivo

O manifesto APT é um arquivo JSON que descreve os detalhes de atualização exigidos pelo manipulador de atualização APT. Esse arquivo pode ser importado para a atualização do dispositivo para o Hub IoT, assim como qualquer outra atualização.

[Saiba mais](import-update.md) sobre como importar atualizações para a atualização de dispositivo.

## <a name="overview"></a>Visão geral

Quando um manifesto APT é entregue a um agente de atualização de dispositivo como uma atualização, o agente processará o manifesto e realizará as operações necessárias. Essas operações incluem baixar e instalar os pacotes especificados no arquivo de manifesto APT e suas dependências.

A atualização de dispositivo dá suporte ao manipulador de atualização do APT e do Gerenciador de atualizações da APT. Esse suporte permite que o agente de atualização de dispositivo avalie os pacotes Debian instalados e atualize os pacotes necessários. 

## <a name="schema"></a>Esquema

Um arquivo de manifesto APT é um arquivo JSON com um esquema com versão.

```json
{
    "name": "<name>",
    "version": "<version>",
    "packages": [
        {
            "name": "<package name>",
            "version": "<version specifier>"
        }
    ]
}
```

Exemplo:

```json
{
    "name": "contoso-iot-edge",
    "version": "1.0.0.0",
    "packages": [
        {
            "name" : "thermocontrol",
            "version" : "1.0.1"
        },
        {
            "name" : "tempreport",
            "version" : "2.0.0"
        }
    ]
}
```

### <a name="name"></a>name

O nome deste manifesto APT. Pode ser qualquer nome ou ID significativo para seus cenários. Por exemplo, `contoso-iot-edge`.

### <a name="version"></a>version

Um número de versão para este manifesto APT. Por exemplo, `1.0.0.0`.


### <a name="packages"></a>pacotes

Uma lista de objetos que contêm propriedades específicas do pacote.

#### <a name="name"></a>name

O nome ou a ID do pacote. Por exemplo, `iotedge`.

#### <a name="version"></a>version

Os critérios de versão desejados para o pacote. Por exemplo, `1.0.8-2`.

Atualmente, apenas o número de versão exato é suportado. O número de versão é a versão de pacote Debian desejada no formato [época:] upstream_version [-debian_revision].

**época** é um int não assinado.

**upstream_version** pode incluir alfanuméricos e caracteres como ".", "+", "-" e "~". Ele deve começar com um dígito.
> [!NOTE]
> ' 1.0.8 ' é igual a ' 1.0.8-0 '

Por exemplo, **`"name":"iotedge" and "version":"1.0.8-2"`** é equivalente a instalar um pacote usando o comando `apt-get install iotedge=1.0.8-2`

> [!NOTE]
> O valor da versão não contém um sinal de igual

Se a versão for omitida, a versão mais recente disponível do pacote especificado será instalada.

[Saiba mais](https://www.debian.org/doc/debian-policy/ch-controlfields.html#s-f-version) sobre como os pacotes Debian têm controle de versão.

> [!NOTE]
> O Gerenciador de pacotes APT ignora os requisitos de controle de versão fornecidos por um pacote quando os pacotes dependentes a serem instalados estão sendo resolvidos automaticamente. A menos que sejam dadas as versões explícitas dos pacotes dependentes, elas usarão o mais recente, mesmo que o próprio pacote possa especificar um requisito estrito (=) em uma determinada versão. Essa resolução automática pode levar a erros relacionados a uma dependência não atendida. [Saiba mais](https://unix.stackexchange.com/questions/350192/apt-get-not-properly-resolving-a-dependency-on-a-fixed-version-in-a-debian-ubunt)

Se você estiver atualizando uma versão específica do daemon de segurança do Azure IoT Edge, deverá incluir a versão desejada do `iotedge` pacote e seu pacote dependente `libiothsm-std` em seu manifesto apt.
[Saiba mais](https://docs.microsoft.com/azure/iot-edge/how-to-update-iot-edge#update-the-security-daemon)

> [!NOTE]
> Um manifesto apt pode ser usado para atualizar o agente de atualização de dispositivo e suas dependências. Liste o nome do agente de atualização de dispositivo e a versão desejada no manifesto apt, como você faria para qualquer outro pacote. Esse manifesto apt pode ser importado e implantado por meio da atualização do dispositivo para o pipeline do Hub IoT. 

## <a name="removing-packages"></a>Removendo pacotes

Você também pode usar um manifesto apt para remover pacotes instalados do seu dispositivo. Um único manifesto de apt pode ser usado para remover, adicionar e atualizar vários pacotes. Para remover um pacote, adicione um sinal de menos "-" após o nome do pacote. Você não deve incluir um número de versão para os pacotes que você está removendo. A remoção de pacotes por meio de um manifesto apt não remove suas dependências e configurações.

Exemplo:

```json
{
    "name": "contoso-video",
    "version": "2.0.0.1",
    "packages": [
        {
            "name" : "foo-"
        }
    ]
}
```
Esse manifesto de apt removerá o pacote "foo" do (s) dispositivo (es) no qual ele está implantado. 

## <a name="recommended-value-for-installed-criteria"></a>Valor recomendado para critérios instalados

Os critérios instalados para um manifesto APT é `<name>-<version>` onde `<name>` é o nome do manifesto apt e `<version>` é a versão do manifesto apt. Por exemplo, `contoso-iot-edge-1.0.0.0`. 

## <a name="guidelines-on-creating-an-apt-manifest"></a>Diretrizes sobre como criar um manifesto APT

Ao criar o manifesto APT, há algumas diretrizes a serem consideradas:

- Sempre verifique se o manifesto APT é um arquivo JSON bem formado
- Cada manifesto APT deve ter uma versão exclusiva. Tente criar uma metodologia padronizada para incrementar a versão do manifesto APT, de modo que faça sentido para seus cenários e possa ser facilmente seguido
- Quando se trata do estado desejado de cada pacote individual, especifique o nome exato e a versão do pacote que você deseja instalar em seu dispositivo. Sempre valide os valores em relação ao repositório de pacotes que você pretende usar como a origem do pacote
- Verifique se os pacotes no manifesto APT estão listados na ordem em que eles devem ser instalados/removidos
- Sempre valide a instalação de pacotes em um dispositivo de teste para garantir que o resultado seja desejado
- Ao instalar uma versão específica de um pacote (por exemplo, `iotedge 1.0.9-1` ), é recomendável também ter no manifesto de apt as versões explícitas dos pacotes dependentes a serem instalados (por exemplo, `libiothsm 1.0.9-1` )
- Embora não seja obrigatório, sempre garanta que seu manifesto APT seja cumulativo para evitar que seu dispositivo fique em um estado desconhecido. Uma atualização cumulativa garantirá que seus dispositivos tenham a versão desejada de cada pacote que você se importa mesmo se o dispositivo tiver ignorado uma implantação de atualização de APT devido a uma falha na instalação ou ficar offline

Por exemplo: 

**Manifesto base APT**

```JSON
{
    "name": "contoso-iot-edge",
    "version": "1.0",
    "packages": [
        {
            "name": "foo",
            "version": "1.0.1"
        }
    ]
}
```

**ATUALIZAÇÃO INADEQUADA**

Essa atualização inclui o pacote de barras, mas não o pacote foo.

```JSON
{
    "name": "contoso-iot-edge",
    "version": "2.0",
    "packages": [
        {
            "name": "bar",
            "version": "3.0.2"
        }
    ]
}
```

**BOA ATUALIZAÇÃO**

Essa atualização inclui o pacote foo e também inclui o pacote de barras.

```JSON
{
    "name": "contoso-iot-edge",
    "version": "2.0",
    "packages": [
        {
            "name": "foo",
            "version": "1.0.1"
        },
        {
            "name": "bar",
            "version": "3.0.2"
        }
    ]
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Importar nova atualização](import-update.md)

