---
title: Armazenar blob de blocos nos dispositivos – Azure IoT Edge | Microsoft Docs
description: Entenda as camadas e os recursos de vida útil, consulte operações de armazenamento de BLOBs com suporte e conecte-se à sua conta de armazenamento de BLOBs.
author: arduppal
manager: mchad
ms.author: arduppal
ms.reviewer: arduppal
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: c5a27a8016202f7f8c9e256eaf6b3077fbef295b
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414523"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Armazenar dados na borda com o Armazenamento de Blobs do Azure no IoT Edge (versão prévia)

O Armazenamento de Blob do Azure no IoT Edge fornece uma solução de armazenamento de [blob de blocos](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) na borda. Um módulo de armazenamento de BLOBs em seu dispositivo IoT Edge se comporta como um serviço blob de blocos do Azure, exceto que os blobs de blocos são armazenados localmente em seu dispositivo IoT Edge. É possível acessar os blobs usando os mesmos métodos do SDK do armazenamento do Azure ou as chamadas à API do blob de blocos que você já está acostumado. Este artigo explica os conceitos relacionados ao armazenamento de BLOBs do Azure no contêiner IoT Edge que executa um serviço blob em seu dispositivo IoT Edge.

Esse módulo é útil em cenários em que os dados precisam ser armazenados localmente até que possam ser processados ou transferidos para a nuvem. Esses dados podem ser vídeos, imagens, dados de finanças, dados de hospital ou quaisquer outros dados não estruturados.

> [!NOTE]
> O Armazenamento de Blobs do Azure no IoT Edge está em [visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Assista ao vídeo para obter uma introdução rápida
> [!VIDEO https://www.youtube.com/embed/QhCYCvu3tiM]

Este módulo vem com os recursos **deviceToCloudUpload** e **deviceAutoDelete** .

**deviceToCloudUpload** é uma funcionalidade configurável. Essa função carrega automaticamente os dados do seu armazenamento de BLOBs local para o Azure com suporte à conectividade intermitente com a Internet. Ele permite que você:

- Ativar/desativar o recurso deviceToCloudUpload.
- Escolha a ordem na qual os dados são copiados para o Azure, como NewestFirst ou OldestFirst.
- Especifique a conta de armazenamento do Azure para a qual você deseja que os dados sejam carregados.
- Especifique os contêineres que você deseja carregar no Azure. Esse módulo permite que você especifique nomes de contêiner de origem e de destino.
- Escolha a capacidade de excluir os BLOBs imediatamente, após a conclusão do carregamento para o armazenamento em nuvem
- Faça upload de blob completo ( `Put Blob` usando operação) e carregamento em nível de `Put Block` bloco `Put Block List` (usando operações e).

Esse módulo usa o carregamento em nível de bloco, quando seu blob é composto por blocos. Aqui estão alguns dos cenários comuns:

- Seu aplicativo atualiza alguns blocos de um blob carregado anteriormente, esse módulo carrega apenas os blocos atualizados e não o blob inteiro.
- O módulo está carregando o blob e a conexão com a Internet desaparece, quando a conectividade está novamente, ele carrega apenas os blocos restantes e não o blob inteiro.

Se um encerramento de processo inesperado (como falha de energia) ocorrer durante um upload de BLOB, todos os blocos que foram devidos ao carregamento serão carregados novamente quando o módulo voltar a ficar online.

**deviceAutoDelete** é uma funcionalidade configurável. Essa função exclui automaticamente os BLOBs do armazenamento local quando a duração especificada (medida em minutos) expira. Ele permite que você:

- Ativar/desativar o recurso deviceAutoDelete.
- Especifique o tempo em minutos (deleteAfterMinutes) após o qual os BLOBs serão excluídos automaticamente.
- Escolha a capacidade de reter o blob enquanto ele estiver sendo carregado se o valor de deleteAfterMinutes expirar.


## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo do Azure IoT Edge:

- Você pode usar seu computador de desenvolvimento ou uma máquina virtual como um dispositivo IoT Edge seguindo as etapas no guia de início rápido para [dispositivos](quickstart.md) [Linux](quickstart-linux.md) ou Windows.

- O Armazenamento de Blobs do Azure no módulo do IoT Edge dá suporte às seguintes configurações de dispositivo:

  | Sistema operacional | AMD64 | ARM32v7 | ARM64 |
  | ---------------- | ----- | ----- | ---- |
  | Raspbian-stretch | Não | Sim | Não |  
  | Ubuntu Server 16.04 | Sim | Não | Sim |
  | Ubuntu Server 18.04 | Sim | Não | Sim |
  | Windows 10 IoT Enterprise, Build 17763 | Sim | Não | Não |
  | Windows Server 2019, Build 17763 | Sim | Não | Não |
  

Recursos de nuvem:

Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na camada padrão no Azure.

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>Propriedades deviceToCloudUpload e deviceAutoDelete

Use as propriedades desejadas do módulo para definir **deviceToCloudUploadProperties** e **deviceAutoDeleteProperties**. As propriedades desejadas podem ser definidas durante a implantação ou alteradas posteriormente editando-se o módulo e sem a necessidade de reimplantar. É recomendável verificar o "módulo `reported configuration` " e `configurationValidation` para garantir que os valores sejam propagados corretamente.

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

O nome dessa configuração é`deviceToCloudUploadProperties`

| Campo | Valores possíveis | Explicação | Variável de ambiente |
| ----- | ----- | ---- | ---- |
| carregar | true, false | Defina como `false` por padrão. Se você quiser ativar o recurso, defina esse campo como `true`. | `deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst, OldestFirst | Permite que você escolha a ordem na qual os dados são copiados para o Azure. Defina como `OldestFirst` por padrão. A ordem é determinada pela hora da última modificação do blob | `deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"`é uma cadeia de conexão que permite especificar a conta de armazenamento do Azure para a qual você deseja que os dados sejam carregados. Especifique `Azure Storage Account Name`, `Azure Storage Account Key`, .`End point suffix` Adicione o EndpointSuffix apropriado do Azure em que os dados serão carregados, varia para o Azure global, o Azure governamental e o Microsoft Azure Stack. | `deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Permite que você especifique os nomes de contêiner que deseja carregar no Azure. Esse módulo permite que você especifique nomes de contêiner de origem e de destino. Se você não especificar o nome do contêiner de destino, ele atribuirá automaticamente o nome `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`do contêiner como. Você pode criar cadeias de caracteres de modelo para o nome do contêiner de destino, confira a coluna de valores possíveis. <br>*% h-> nome do Hub IoT (3-50 caracteres). <br>*% d-> IoT Edge ID do dispositivo (1 a 129 caracteres). <br>*% m-> nome do módulo (1 a 64 caracteres). <br>*% c-> nome do contêiner de origem (3 a 63 caracteres). <br><br>O tamanho máximo do nome do contêiner é de 63 caracteres, enquanto atribui automaticamente o nome do contêiner de destino se o tamanho do contêiner exceder 63 caracteres, ele cortará cada seção (IoTHubName, IotEdgeDeviceID, ModuleName, SourceContainerName) como 15 personagens. | `deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target: <targetName>` |
| deleteAfterUpload | true, false | Defina como `false` por padrão. Quando definido como `true`, ele excluirá automaticamente os dados quando o upload para o armazenamento em nuvem for concluído | `deviceToCloudUploadProperties__deleteAfterUpload={false,true}` |


### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

O nome dessa configuração é`deviceAutoDeleteProperties`

| Campo | Valores possíveis | Explicação | Variável de ambiente |
| ----- | ----- | ---- | ---- |
| deleteOn | true, false | Defina como `false` por padrão. Se você quiser ativar o recurso, defina esse campo como `true`. | `deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | Especifique o tempo em minutos. O módulo excluirá automaticamente seus BLOBs do armazenamento local quando esse valor expirar | `deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | true, false | Por padrão, ele é definido `true`como e manterá o blob enquanto estiver carregando para armazenamento em nuvem se deleteAfterMinutes expirar. Você pode defini-lo `false` como e ele excluirá os dados assim que deleteAfterMinutes expirar. Observação: Para que essa propriedade funcione, o upload deve ser definido como true| `deviceAutoDeleteProperties__retainWhileUploading={false,true}` |

## <a name="configure-log-files"></a>Configurar arquivos de log

Para obter informações sobre como configurar arquivos de log para seu módulo, consulte essas [práticas recomendadas de produção](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics).

## <a name="connect-to-your-blob-storage-module"></a>Conectar ao módulo do armazenamento de blobs

É possível usar o nome da conta e a chave de conta que você configurou para o módulo para acessar o armazenamento de blobs no seu dispositivo do IoT Edge.

Especifique o dispositivo do IoT Edge como ponto de extremidade do blob para quaisquer solicitações de armazenamento que você faz para esse dispositivo. É possível [Criar uma cadeia de conexão para um ponto de extremidade explícito](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) usando as informações do dispositivo do IoT Edge e do nome da conta que você configurou.

- Para módulos que são implantados no mesmo dispositivo em que o armazenamento de BLOBs do Azure no módulo IoT Edge está em execução, `http://<module name>:11002/<account name>`o ponto de extremidade do blob é:.
- Para módulos ou aplicativos em execução em um dispositivo diferente, você precisa escolher o ponto de extremidade certo para sua rede. Dependendo da configuração de sua rede, escolha um formato de ponto de extremidade, de modo que o tráfego de dados do seu módulo ou aplicativo externo possa acessar o dispositivo que executa o armazenamento de BLOBs do Azure no módulo IoT Edge. O ponto de extremidade do blob para este cenário é um de:
  - `http://<device IP >:11002/<account name>`
  - `http://<IoT Edge device hostname>:11002/<account name>`
  - `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Exemplos de início rápido do armazenamento de BLOBs do Azure

A documentação do armazenamento de BLOBs do Azure inclui o código de exemplo de início rápido em vários idiomas. Você pode executar esses exemplos para testar o armazenamento de BLOBs do Azure em IoT Edge alterando o ponto de extremidade de BLOB para se conectar ao seu módulo de armazenamento de BLOBs local.

Os exemplos de início rápido a seguir usam idiomas que também são suportados pelo IoT Edge, portanto, você pode implantá-los como IoT Edge módulos juntamente com o módulo de armazenamento de BLOBs:

- [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Java](../storage/blobs/storage-quickstart-blobs-java.md)
- [Python](../storage/blobs/storage-quickstart-blobs-python.md)
- [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Conecte-se ao seu armazenamento local com Gerenciador de Armazenamento do Azure

Você pode usar [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para se conectar à sua conta de armazenamento local.

1. Baixar e instalar o Gerenciador de Armazenamento do Azure

1. Conectar-se ao armazenamento do Azure usando uma cadeia de conexão

1. Forneça a cadeia de conexão:`DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. Percorra as etapas para se conectar.

1. Criar contêiner dentro de sua conta de armazenamento local

1. Comece a carregar arquivos como BLOBs de blocos.
   > [!NOTE]
   > Este módulo não oferece suporte a blobs de página.

1. Você também pode optar por conectar suas contas de armazenamento do Azure em Gerenciador de Armazenamento. Essa configuração fornece uma exibição única para a conta de armazenamento local e para a conta de armazenamento do Azure

## <a name="supported-storage-operations"></a>Operações de armazenamento com suporte

Os módulos de armazenamento de BLOBs no IoT Edge usam os SDKs de armazenamento do Azure e são consistentes com a versão 2017-04-17 da API de armazenamento do Azure para pontos de extremidade de blob de blocos. 

Como nem todas as operações de armazenamento de BLOBs do Azure têm suporte pelo armazenamento de BLOBs do Azure no IoT Edge, esta seção lista o status de cada um.

### <a name="account"></a>Conta

Com suporte:

- Listar contêineres

Sem suporte:

- Obter e definir propriedades do serviço Blob
- Solicitação de blob de simulação
- Obter estatísticas do serviço Blob
- Obter Informações da conta

### <a name="containers"></a>Contêineres

Com suporte:

- Criar e excluir contêiner
- Obter Propriedades do Contêiner
- Listar blobs
- Obter e definir ACL do contêiner
- Definir Metadados do Contêiner

Sem suporte:

- Concessão de contêiner

### <a name="blobs"></a>Blobs

Com suporte:

- Colocar, obter e excluir blob
- Obter e definir propriedades do blob
- Obter e definir Metadados do Blob

Sem suporte:

- Concessão de blob
- Instantâneo do blob
- Copiar e cancelar cópia do blob
- Cancelar exclusão do blob
- Definir camada do blob

### <a name="block-blobs"></a>Blobs de bloco

Com suporte:

- Colocar bloco
- Colocar e obter lista de blocos

Sem suporte:

- Colocar bloco pela URL

## <a name="release-notes"></a>Notas de versão

Aqui estão as [notas de versão no Hub](https://hub.docker.com/_/microsoft-azure-blob-storage) do Docker para este módulo

## <a name="feedback"></a>Comentários

Seus comentários são importantes para que possamos tornar este módulo e seus recursos úteis e fáceis de usar. Compartilhe seus comentários e informe-nos como podemos melhorar.

Você pode entrar em contato conosco emabsiotfeedback@microsoft.com

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como [implantar o armazenamento de BLOBs do Azure no IOT Edge](how-to-deploy-blob.md)
