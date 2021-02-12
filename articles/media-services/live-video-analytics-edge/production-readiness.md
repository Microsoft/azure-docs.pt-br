---
title: Preparação para produção e práticas recomendadas – Azure
description: Este artigo fornece orientação sobre como configurar e implantar a análise de vídeo ao vivo em IoT Edge módulo em ambientes de produção.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 56982d84b7ffac718072683076657d56a2691d6c
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400549"
---
# <a name="production-readiness-and-best-practices"></a>Preparação para produção e práticas recomendadas

Este artigo fornece orientação sobre como configurar e implantar a análise de vídeo ao vivo em IoT Edge módulo em ambientes de produção. Você também deve examinar a [preparação para implantar seu IOT Edge solução no artigo de produção](../../iot-edge/production-checklist.md) sobre como preparar sua solução de IOT Edge. 

> [!NOTE]
> Você deve consultar os departamentos de ti de suas organizações sobre aspectos relacionados à segurança.

## <a name="running-the-module-as-a-local-user"></a>Execução do módulo como usuário local

Quando você implanta a análise de vídeo ao vivo no módulo IoT Edge em um dispositivo de borda, por padrão ele é executado com privilégios elevados. Ao fazer isso, se você verificar os logs no módulo ( `sudo iotedge logs {name-of-module}` ), verá o seguinte:

```
!! production readiness: user accounts – Warning
       LOCAL_USER_ID and LOCAL_GROUP_ID environment variables are not set. The program will run as root!
       For optimum security, make sure to set LOCAL_USER_ID and LOCAL_GROUP_ID environment variables to a non-root user and group.
       See https://aka.ms/lva-iot-edge-prod-checklists-user-accounts for more information.
```

As seções a seguir discutem como você pode resolver o aviso acima.

### <a name="creating-and-using-a-local-user-account"></a>Criando e usando uma conta de usuário local

Você pode e deve executar a análise de vídeo ao vivo no módulo IoT Edge em produção usando uma conta com o mínimo de privilégios possível. Os comandos a seguir, por exemplo, mostram como você pode criar uma conta de usuário local em uma VM do Linux:

```
sudo groupadd -g 1010 localuser
sudo adduser --home /home/edgeuser --uid 1010 -gid 1010 edgeuser
```

Em seguida, no manifesto de implantação, você pode definir o LOCAL_USER_ID e LOCAL_GROUP_ID variáveis de ambiente para esse usuário e grupo não raiz:

```
"lvaEdge": {
"version": "1.0",
…
"env": {
    "LOCAL_USER_ID": 
    {
        "value": "1010"
    },
    "LOCAL_GROUP_ID": {
        "value": "1010"
    }
}
},
…
```

### <a name="granting-permissions-to-device-storage"></a>Concedendo permissões ao armazenamento do dispositivo

O módulo análise de vídeo ao vivo no IoT Edge requer a capacidade de gravar arquivos no sistema de arquivos local quando:

* Usando uma propriedade MyModule [`applicationDataDirectory`](module-twin-configuration-schema.md#module-twin-properties) , em que você deve especificar um diretório no sistema de arquivos local para armazenar dados de configuração.
* Usando um grafo de mídia para gravar vídeo na nuvem, o módulo requer o uso de um diretório no dispositivo de borda como um cache (consulte o artigo [gravação de vídeo contínua](continuous-video-recording-concept.md) para obter mais informações).
* [Registro em um arquivo local](event-based-video-recording-concept.md#video-recording-based-on-events-from-other-sources), onde você deve especificar um caminho de arquivo para o vídeo gravado.

Se você pretende fazer uso de qualquer um dos itens acima, certifique-se de que a conta de usuário acima tenha acesso ao diretório relevante. Considere applicationDataDirectory por exemplo. Você pode criar um diretório no dispositivo de borda e vincular o armazenamento do dispositivo ao armazenamento de módulo. 

```
sudo mkdir /var/local/mediaservices
sudo chown -R edgeuser /var/local/mediaservices
```

Em seguida, no criar opções para o módulo de borda no manifesto de implantação, você pode adicionar uma configuração de associações mapeando o diretório ("var/local/mediaservices/") acima para um diretório no módulo (como "/var/lib/azuremediaservices/"). E você usaria o último diretório como o valor para o applicationDataDirectory.

```
"lvaEdge": {
    "version": "1.0",
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
        "image": "mcr.microsoft.com/media/live-video-analytics:1.0",
        "createOptions": "{\"HostConfig\":{\"LogConfig\":{\"Type\":\"\",\"Config\":{\"max-size\":\"10m\",\"max-file\":\"10\"}},\"Binds\":[\"/var/local/mediaservices/:/var/lib/azuremediaservices/\"]}}"
    },
    "env": {
        "LOCAL_USER_ID": 
        {
            "value": "1010"
        },
        "LOCAL_GROUP_ID": {
            "value": "1010"
        }
    }
    },
    …
    
    "lvaEdge": {
    "properties.desired": {
    "applicationDataDirectory": "/var/lib/azuremediaservices",
    …
    }
}
```

Se você examinar os gráficos de mídia de exemplo para o início rápido e os tutoriais, como a [gravação contínua de vídeo](continuous-video-recording-tutorial.md), observará que o diretório de cache de mídia (localMediaCachePath) usa um subdiretório em applicationDataDirectory. Essa é a abordagem recomendada, já que o cache contém dados transitórios.

### <a name="naming-video-assets-or-files"></a>Nomenclatura dos ativos de vídeo ou arquivos

Os grafos de mídia permitem a criação de ativos na nuvem ou em arquivos MP4 na borda. Os ativos de mídia podem ser gerados pela [gravação contínua de vídeo](continuous-video-recording-tutorial.md) ou pela [gravação de vídeo baseada em evento](event-based-video-recording-tutorial.md). Embora esses ativos e arquivos possam ser nomeados como você desejar, a estrutura de nomenclatura recomendada para o ativo de mídia com base na gravação de vídeo contínua é " &lt; anytext &gt; -$ {System. GraphTopologyName}-$ {System. GraphInstanceName}".   

O padrão de substituição é definido pelo sinal $, seguido por chaves: **$ {VariableName}**.  

Por exemplo, você pode definir o assetNamePattern no coletor de ativos da seguinte maneira:

```
"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}
```

Para os ativos gerados pelo registro de vídeo baseado em evento, o padrão de nomenclatura recomendado é " &lt; anytext &gt; -$ {System. DateTime}". A variável do sistema garante que os ativos não sejam substituídos se os eventos ocorrerem ao mesmo tempo. Por exemplo, você pode definir o assetNamePattern no coletor de ativos da seguinte maneira:

```
"assetNamePattern": "sampleAssetFromEVR-LVAEdge-${System.DateTime}"
```

Se você estiver executando várias instâncias do mesmo grafo, poderá usar o nome da topologia do grafo e o nome da instância para diferenciar. Por exemplo, você pode definir o assetNamePattern no coletor de ativos da seguinte maneira:

```
"assetNamePattern": "sampleAssetFromEVR-${System.GraphTopologyName}-${System.GraphInstanceName}-${System.DateTime}"
```

Para a gravação de vídeo baseada em evento-os clipes de vídeo MP4 gerados na borda, o padrão de nomenclatura recomendado deve incluir DateTime e várias instâncias do mesmo grafo recomendadas usando as variáveis de sistema GraphTopologyName e GraphInstanceName. Como exemplo, você pode definir filePathPattern no coletor de arquivos da seguinte maneira: 

```
"fileNamePattern": "/var/media/sampleFilesFromEVR-${fileSinkOutputName}-${System.DateTime}"
```

Ou 

```
"fileNamePattern": "/var/media/sampleFilesFromEVR-${fileSinkOutputName}--${System.GraphTopologyName}-${System.GraphInstanceName} ${System.DateTime}"
```
>[!NOTE]
> No exemplo acima, a variável **fileSinkOutputName** é um nome de variável de exemplo que você define na topologia do grafo. Essa **não** é uma variável do sistema. 

#### <a name="system-variables"></a>Variáveis do sistema
Algumas variáveis definidas pelo sistema que você pode usar são:

|Variável do sistema|Descrição|Exemplo|
|-----------|-----------|-----------|
|System.DateTime|Data e hora UTC no formato compatível com arquivo ISO8601 (representação básica AAAAMMDDThhmmss).|20200222T173200Z|
|System. PreciseDateTime|Data e hora UTC no formato compatível com o arquivo ISO8601 com milissegundos (representação básica AAAAMMDDThhmmss. SSS).|20200222T 173200.123 Z|
|System. GraphTopologyName|Nome fornecido pelo usuário da topologia do grafo em execução.|IngestAndRecord|
|System. GraphInstanceName|Nome fornecido pelo usuário da instância do grafo em execução.|camera001|

>[!TIP]
> System. PreciseDateTime não pode ser usado ao nomear ativos devido a "." no nome
### <a name="keeping-your-vm-clean"></a>Mantendo sua VM limpa

A VM Linux que você está usando como um dispositivo de borda pode deixar de responder se não for gerenciada periodicamente. É essencial manter os caches limpos, eliminar pacotes desnecessários e remover contêineres não utilizados da VM também. Para fazer isso aqui está um conjunto de comandos recomendados, você pode usar em sua VM de borda.

1. `sudo apt-get clean`

    O comando apt-get limpo limpa o repositório local de arquivos de pacote recuperados que são deixados no/var/cache. Os diretórios que limpam são/var/cache/apt/archives/e/var/cache/apt/archives/partial/. Os únicos arquivos que ele deixa em/var/cache/apt/archives são o arquivo de bloqueio e o subdiretório parcial. O comando apt-get limpo geralmente é usado para limpar o espaço em disco conforme necessário, geralmente como parte da manutenção agendada regularmente. Para obter mais informações, consulte [limpando com apt-get](https://www.networkworld.com/article/3453032/cleaning-up-with-apt-get.html).
1. `sudo apt-get autoclean`

    A opção apt-get de autolimpeza, como apt-get limpo, limpa o repositório local dos arquivos de pacote recuperados, mas remove apenas os arquivos que não podem mais ser baixados e não são úteis. Isso ajuda a manter o crescimento de seu cache muito grande.
1. `sudo apt-get autoremove1`

    A opção de remoção automática remove pacotes que foram instalados automaticamente porque algum outro pacote os exigiu, mas com esses outros pacotes removidos, eles não são mais necessários
1. `sudo docker image ls` – Fornece uma lista de imagens do Docker no sistema de borda
1. `sudo docker system prune`

    O Docker usa uma abordagem conservadora para limpar objetos não utilizados (geralmente chamados de "coleta de lixo"), como imagens, contêineres, volumes e redes: esses objetos geralmente não são removidos, a menos que você solicite explicitamente o Docker para fazer isso. Isso pode fazer com que o Docker use espaço em disco extra. Para cada tipo de objeto, o Docker fornece um comando de remoção. Além disso, você pode usar a remoção do sistema Docker para limpar vários tipos de objetos ao mesmo tempo. Para obter mais informações, consulte [remover objetos do Docker não utilizados](https://docs.docker.com/config/pruning/).
1. `sudo docker rmi REPOSITORY:TAG`

    À medida que as atualizações acontecem no módulo do Edge, o Docker pode ter versões mais antigas do módulo do Edge ainda presentes. Nesse caso, é aconselhável usar o comando Docker RMI para remover imagens específicas identificadas pela marca de versão da imagem.

## <a name="next-steps"></a>Próximas etapas

[Início Rápido: Introdução – Análise de Vídeo ao vivo no IoT Edge](get-started-detect-motion-emit-events-quickstart.md)
