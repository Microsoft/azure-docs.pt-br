---
title: Operar dispositivos offline – Azure IoT Edge | Microsoft Docs
description: Saiba como módulos e dispositivos IoT Edge podem operar sem conexão à Internet por longos períodos e como o IoT Edge pode permitir que dispositivos IoT regulares operem offline também.
author: kgremban
ms.author: kgremban
ms.date: 11/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c9412e2adeb9b43b4c61437fb41e68bc96b86afd
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481817"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>Entender os recursos offline estendidos para dispositivos, módulos e dispositivos filho do IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

O Azure IoT Edge é compatível com operações offline estendidas em seus dispositivos do IoT Edge e também permite operações offline em dispositivos filho que não são do IoT Edge. Contanto que um dispositivo do IoT Edge tenha tido uma oportunidade de se conectar ao Hub IoT, esse dispositivo e todos os dispositivos filho podem continuar a funcionar com uma conexão com a Internet intermitente ou sem nenhuma conexão.

## <a name="how-it-works"></a>Como ele funciona

Quando um dispositivo do IoT Edge entra em modo offline, o hub do IoT Edge assume três funções. Primeiro, ele armazena todas as mensagens que estão no sentido upstream e as salva até que o dispositivo se reconecte. Em segundo lugar, ele atua em nome do Hub IoT para autenticar dispositivos filho e módulos para que eles possam continuar a operar. Em terceiro lugar, ele permite a comunicação entre os dispositivos filho que normalmente passariam pelo Hub IoT.

O exemplo a seguir mostra como um cenário de IoT Edge opera no modo offline:

1. **Configurar dispositivos**

   Dispositivos do IoT Edge automaticamente têm recursos offline habilitados. Para estender essa funcionalidade para outros dispositivos de IoT, você precisa declarar uma relação de pai-filho entre os dispositivos no Hub IoT. Em seguida, configure os dispositivos filho para confiar no dispositivo pai atribuído e encaminhe as comunicações do dispositivo para a nuvem por meio do pai como gateway.

2. **Sincronizar com o Hub IoT**

   Pelo menos uma vez após a instalação do runtime do IoT Edge, o dispositivo do IoT Edge precisa estar online para sincronizar com o Hub IoT. Nessa sincronização, o dispositivo do IoT Edge obtém detalhes sobre todos os dispositivos filho atribuídos a ele. O dispositivo do IoT Edge também atualiza seu cache local com segurança para permitir operações offline e recupera as configurações para o armazenamento local de mensagens de telemetria.

3. **Ficar offline**

   Enquanto estiver desconectado do Hub IoT, o dispositivo do IoT Edge, seus módulos implantados e quaisquer dispositivos de IoT filho poderão operar indefinidamente. Os módulos e dispositivos filho podem ser iniciados e reiniciados ao autenticarem com o hub do IoT Edge enquanto estiverem offline. A telemetria upstream associada para o Hub IoT é armazenada localmente. A comunicação entre os módulos ou entre dispositivos de IoT filho é mantida por meio de métodos diretos ou mensagens.

4. **Reconectar e sincronizar novamente com o Hub IoT**

   Quando a conexão com o Hub IoT for restaurada, o dispositivo do IoT Edge será sincronizado novamente. As mensagens armazenadas localmente são entregues ao Hub IoT imediatamente, mas dependem da velocidade da conexão, da latência do Hub IoT e dos fatores relacionados. Elas são entregues na mesma ordem em que foram armazenadas.

   Todas as diferenças entre as propriedades desejadas e relatadas dos módulos e dispositivos são reconciliadas. O dispositivo do IoT Edge atualiza qualquer alteração de seu conjunto de dispositivos de IoT filho atribuídos.

## <a name="restrictions-and-limits"></a>Restrições e limites

Os recursos offline estendidos descritos neste artigo estão disponíveis no [IoT Edge versão 1.0.7 ou superior](https://github.com/Azure/azure-iotedge/releases). As versões anteriores têm um subconjunto de recursos offline. Os dispositivos do IoT Edge existentes que não têm recursos offline estendidos não podem ser atualizados alterando a versão de runtime, mas devem ser reconfigurados com uma nova identidade do dispositivo do IoT Edge para obter esses recursos.

Somente dispositivos que não são do IoT Edge podem ser adicionados como dispositivos filho.

Dispositivos do IoT Edge e seus dispositivos filho atribuídos podem funcionar indefinidamente offline após a sincronização inicial única. No entanto, o armazenamento de mensagens depende da configuração de TTL (vida útil) e do espaço em disco disponível para armazenar as mensagens.

## <a name="set-up-parent-and-child-devices"></a>Configurar dispositivos pai e filho

Para um dispositivo do IoT Edge estender suas funcionalidades offline estendidas para dispositivos de IoT filho, você precisa concluir duas etapas. Em primeiro lugar, declare as relações pai-filho no portal do Azure. Em segundo lugar, crie uma relação de confiança entre o dispositivo pai e todos os dispositivos filho e configure as comunicações do dispositivo para a nuvem para passar pelo pai como gateway.

### <a name="assign-child-devices"></a>Atribuir dispositivos filho

Dispositivos filho podem ser qualquer dispositivo que não é do IoT Edge registrado no mesmo Hub IoT. Dispositivos pai podem ter vários dispositivos de filho, mas um dispositivo filho tem apenas um pai. Há três opções para definir os dispositivos filho para um dispositivo de borda: por meio do portal do Azure, usando a CLI do Azure ou usando o SDK do serviço de Hub IoT.

As seções a seguir fornecem exemplos de como você pode declarar a relação pai/filho no Hub IoT para dispositivos IoT existentes. Se você estiver criando novas identidades de dispositivo para os dispositivos filho, confira [Autenticar um dispositivo downstream no Hub IoT do Azure](how-to-authenticate-downstream-device.md) para obter mais informações.

#### <a name="option-1-iot-hub-portal"></a>Opção 1: Portal do Hub IoT

Você pode declarar a relação pai-filho ao criar um novo dispositivo. Ou para dispositivos existentes, você pode declarar a relação na página de detalhes do dispositivo do dispositivo IoT Edge pai ou filho.

   ![Gerenciar dispositivos filho da página de detalhes do dispositivo do IoT Edge](./media/offline-capabilities/manage-child-devices.png)

#### <a name="option-2-use-the-az-command-line-tool"></a>Opção 2: Usar a ferramenta de linha de comando `az`

Usando a [interface da linha de comando do Azure](/cli/azure/) com a [extensão de IoT](https://github.com/azure/azure-iot-cli-extension) (v0.7.0 ou mais recente), você pode gerenciar relações pai-filho com os subcomandos de [identidade do dispositivo](/cli/azure/iot/hub/device-identity/). O exemplo a seguir usa uma consulta para atribuir todos os dispositivos que não são do IoT Edge no hub para serem dispositivos filho de um dispositivo IoT Edge.

```azurecli
# Set IoT Edge parent device
egde_device="edge-device1"

# Get All IoT Devices
device_list=$(az iot hub query \
        --hub-name replace-with-hub-name \
        --subscription replace-with-sub-name \
        --resource-group replace-with-rg-name \
        -q "SELECT * FROM devices WHERE capabilities.iotEdge = false" \
        --query 'join(`, `, [].deviceId)' -o tsv)

# Add all IoT devices to IoT Edge (as child)
az iot hub device-identity add-children \
  --device-id $egde_device \
  --child-list $device_list \
  --hub-name replace-with-hub-name \
  --resource-group replace-with-rg-name \
  --subscription replace-with-sub-name
```

Você pode modificar a [consulta](../iot-hub/iot-hub-devguide-query-language.md) para selecionar um subconjunto diferente de dispositivos. O comando poderá levar vários segundos se você especificar um grande conjunto de dispositivos.

#### <a name="option-3-use-iot-hub-service-sdk"></a>Opção 3: Usar o SDK de serviço do Hub IoT

Por fim, você pode gerenciar as relações filho-pai programaticamente usando o SDK do serviço de Hub IoT do C#, Java ou Node.js. Este é um [exemplo de atribuição de um dispositivo filho](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/e2e/test/iothub/service/RegistryManagerE2ETests.cs) usando o SDK do C#.

### <a name="set-up-the-parent-device-as-a-gateway"></a>Configurar o dispositivo pai como gateway

Você pode considerar uma relação pai/filho como gateway transparente, em que o dispositivo filho tem sua própria identidade no Hub IoT, mas se comunica através da nuvem por meio do pai. Para uma comunicação segura, o dispositivo filho precisa verificar se o dispositivo pai vem de uma fonte confiável. Caso contrário, terceiros poderiam configurar dispositivos mal-intencionados para representar os pais e interceptar as comunicações.

Uma maneira de criar essa relação de confiança é descrita detalhadamente nos artigos a seguir:

* [Configure um dispositivo IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md)
* [Conectar um dispositivo downstream (filho) para um gateway do Azure IoT Edge](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>Especificar servidores de DNS

Para melhorar a robustez, é altamente recomendável que você especifique os endereços de servidor DNS usados em seu ambiente. Para definir o servidor DNS para IoT Edge, confira a resolução para [O módulo Edge Agent relata continuamente 'arquivo de configuração vazio' e nenhum módulo é iniciado no dispositivo](troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device) no artigo de solução de problemas.

## <a name="optional-offline-settings"></a>Configurações offline opcionais

Se os dispositivos ficarem offline, o dispositivo pai IoT Edge armazenará todas as mensagens do dispositivo para a nuvem até que a conexão seja restabelecida. O módulo hub do IoT Edge gerencia o armazenamento e o encaminhamento das mensagens offline. Para dispositivos que podem ficar offline por longos períodos, otimize o desempenho definindo duas configurações de hub do IoT Edge.

Primeiro, aumente a configuração do tempo de vida útil para que o hub do IoT Edge mantenha as mensagens por tempo suficiente para que o dispositivo se reconecte. Em seguida, adicione mais espaço em disco para o armazenamento de mensagens.

### <a name="time-to-live"></a>Vida útil

A configuração de vida útil é a quantidade de tempo (em segundos) que uma mensagem pode esperar para ser entregue antes de expirar. O padrão é 7200 segundos (duas horas). O valor máximo é limitado apenas pelo valor máximo de uma variável de inteiro, que é cerca de 2 bilhões.

Essa configuração é uma propriedade desejada do hub do IoT Edge, que é armazenada no gêmeo do módulo. Você pode configurá-lo no portal do Azure ou diretamente no manifesto de implantação.

```json
"$edgeHub": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
        "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
        }
    }
}
```

### <a name="host-storage-for-system-modules"></a>Armazenamento de host para módulos do sistema

As mensagens e as informações de estado do módulo são armazenadas no sistema de arquivos do contêiner local do hub do IoT Edge por padrão. Para maior confiabilidade, especialmente ao operar offline, você também pode dedicar o armazenamento no dispositivo IoT Edge de host. Para obter mais informações, confira [Fornecer acesso de módulos ao armazenamento local de um dispositivo](how-to-access-host-storage-from-module.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como configurar um gateway transparente para suas conexões de dispositivo pai/filho:

* [Configure um dispositivo IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md)
* [Autenticar um dispositivo downstream no Hub IoT do Azure](how-to-authenticate-downstream-device.md)
* [Conectar um dispositivo downstream para um gateway do Azure IoT Edge](how-to-connect-downstream-device.md)
