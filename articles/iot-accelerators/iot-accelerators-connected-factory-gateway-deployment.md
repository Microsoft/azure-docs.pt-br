---
title: Implantar o gateway da Fábrica Conectada - Azure | Microsoft Docs
description: Como implantar um gateway no Windows ou no Linux para permitir conectividade com o acelerador de solução de Fábrica Conectada.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 99953b486fbd1daa9800aa850684447465eead9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450138"
---
# <a name="deploy-an-edge-gateway-for-the-connected-factory-solution-accelerator-on-windows-or-linux"></a>Implantar um gateway edge para o acelerador de Fábrica conectada no Windows ou Linux

Você precisa de dois componentes de software para implantar um gateway edge para o acelerador de solução da *Fábrica Conectada*:

- O *Proxy de OPC* estabelece uma conexão para a fábrica conectada. O Proxy OPC aguarda as mensagens de comando e controle de navegador de OPC integrado que é executado no portal de solução de fábrica conectada.

- O *publicador OPC* conecta-se para os servidores OPC UA locais existentes e encaminha mensagens de telemetria para fábrica conectada. Você pode conectar um dispositivo clássico de OPC usando o [adaptador clássico de OPC para o agente do usuário de OPC](https://github.com/OPCFoundation/UA-.NETStandard/blob/master/ComIOP/README.md).

Ambos os componentes são de código livre e estão disponíveis como origem no GitHub e nos contêineres do Docker no DockerHub:

| GitHub | DockerHub |
| ------ | --------- |
| [Publicador OPC](https://github.com/Azure/iot-edge-opc-publisher) | [Publicador OPC](https://hub.docker.com/r/microsoft/iot-edge-opc-publisher/)   |
| [Proxy OPC](https://github.com/Azure/iot-edge-opc-proxy)         | [Proxy OPC](https://hub.docker.com/r/microsoft/iot-edge-opc-proxy/) |

Você não precisa de um endereço IP voltado para o público nem abrir portas de entrada no firewall do gateway para nenhum dos componentes. Os componentes do Proxy OPC e do Publicador OPC só usam a porta de saída 443.

As etapas neste artigo mostram como implantar um gateway de borda usando o Docker no Windows ou no Linux. O gateway permite conectividade com o acelerador de solução de Fábrica Conectada. Você também pode usar os componentes sem a Fábrica Conectada.

> [!NOTE]
> Ambos os componentes podem ser usados como módulos no [do Azure IoT Edge](https://github.com/Azure/iot-edge).

## <a name="choose-a-gateway-device"></a>Escolher um dispositivo de gateway

Se você ainda não tem um dispositivo de gateway, a Microsoft te aconselha a comprar um gateway comercial de um de nossos parceiros. Para obter uma lista de dispositivos de gateway compatíveis com a solução de fábrica conectada, visite o [catálogo de dispositivos do IoT do Azure](https://catalog.azureiotsolutions.com/?q=opc). Siga as instruções que acompanham o dispositivo para configurar o gateway.

Se você precisar configurar manualmente um dispositivo de gateway existente, use as instruções a seguir.

## <a name="install-and-configure-docker"></a>Instalar e configurar o Docker

Instale o  [Docker para Windows](https://www.docker.com/docker-windows)  em seu dispositivo de gateway baseado em Windows ou use um gerenciador de pacotes para instalar a janela de encaixe em seu dispositivo de gateway baseado em Linux.

Durante a configuração do Docker para Windows, selecione uma unidade no seu computador host para compartilhar com o Docker. Captura de tela a seguir mostra o compartilhamento de **1!d** unidade em seu sistema Windows. Uma unidade de compartilhamento permite o acesso à unidade de host de dentro de um contêiner do docker:

![Instalar o Docker para Windows](./media/iot-accelerators-connected-factory-gateway-deployment/image1.png)

> [!NOTE]
> Você também pode executar essa etapa após a instalação do docker usando o **as configurações** caixa de diálogo. Clique com botão direito no ícone do **Docker** na bandeja do sistema do Windows e escolha **Configurações**. Se as principais atualizações do Windows tiverem sido implantadas no sistema, como a Windows Fall Creators Update, descompartilhe as unidades e compartilhe-as novamente para atualizar os direitos de acesso.

Se você estiver usando o Linux, nenhuma configuração adicional será necessária para ativar o acesso ao sistema de arquivos.

No Windows, crie uma pasta na unidade compartilhada com o Docker; no Linux, crie uma pasta no sistema de arquivos raiz. Este passo a passo é referente a essa pasta como `<SharedFolder>`.

Quando você referencia o `<SharedFolder>` em um comando do Docker, certifique-se de usar a sintaxe correta para o seu sistema operacional. Aqui estão dois exemplos, um para Windows e outro para Linux:

- Se você estiver usando a pasta `D:\shared` no Windows como o seu `<SharedFolder>`, a sintaxe de comando do Docker é `d:/shared`.

- Se você estiver usando a pasta `/shared` no Linux como o seu `<SharedFolder>`, a sintaxe de comando do Docker é `/shared`.

Para obter mais informações, consulte a referência do mecanismo de encaixe [ Use volumes ](https://docs.docker.com/engine/admin/volumes/volumes/).

## <a name="configure-the-opc-components"></a>Configurar os componentes OPC

Antes de instalar os componentes OPC, conclua as etapas a seguir para preparar o seu ambiente:

1. Para concluir a implementação do gateway, você precisa da sequência de conexão  **do proprietário**  do Hub IoT na sua implantação do Connected Factory. No  [Portal do Azure](https://portal.azure.com/), navegue para o Hub IoT no grupo de recursos criado quando você implantou a solução Connected Factory. Clique em  **Políticas de acesso compartilhado**  para acessar a sequência de conexão  **do proprietário** :

    ![Localizar a cadeia de conexão Hub IoT](./media/iot-accelerators-connected-factory-gateway-deployment/image2.png)

    Cópia de **chave primária da cadeia de Conexão** valor.

1. Para permitir a comunicação entre contêineres do docker, você precisa de uma rede de ponte definida pelo usuário. Para criar uma rede de ponte para os seus contêineres, execute os seguintes comandos no prompt de comando:

    ```cmd/sh
    docker network create -d bridge iot_edge
    ```

    Para verificar se a rede de ponte **iot_edge** foi criada, execute o comando a seguir:

    ```cmd/sh
    docker network ls
    ```

    A sua rede de ponte **iot_edge** está incluída na lista de redes.

Para executar o Publicador OPC, execute o comando a seguir no prompt de comando:

```cmd/sh
docker run --rm -it -v <SharedFolder>:/docker -v x509certstores:/root/.dotnet/corefx/cryptography/x509stores --network iot_edge --name publisher -h publisher -p 62222:62222 --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-publisher:2.1.4 publisher "<IoTHubOwnerConnectionString>" --lf /docker/publisher.log.txt --as true --si 1 --ms 0 --tm true --vc true --di 30
```

- O [GitHub de Publicador OPC](https://github.com/Azure/iot-edge-opc-publisher) e a [referência de execução do docker](https://docs.docker.com/engine/reference/run/) fornecem mais informações sobre:

  - As opções da linha de comandos do Docker especificadas antes do nome do contêiner (`microsoft/iot-edge-opc-publisher:2.1.4`).
  - O significado dos parâmetros da linha de comandos do OPC Publisher especificados após o nome do contêiner (`microsoft/iot-edge-opc-publisher:2.1.4`).

- O `<IoTHubOwnerConnectionString>` é a cadeia de conexão da política de acesso compartilhado do **iot hubowner** do portal do Azure. Você copiou essa cadeia de conexão em uma etapa anterior. Você só precisa dessa cadeia de conexão para a primeira execução do Publicador OPC. Em execuções subsequentes, você deve omiti-lo porque representa um risco de segurança.

- O `<SharedFolder>` que você usa e sua sintaxe são descritos na seção [Instalar e configurar o Docker](#install-and-configure-docker). OPC Publisher uses the `<SharedFolder>` to:

    - Leia e escreva no arquivo de configuração do OPC Publisher.
    - Escreva no arquivo de log.
    - Torne esses arquivos disponíveis fora do contêiner.

- O Publicador OPC lê sua configuração a partir do arquivo **publishednodes.json**, que é lida e gravada na pasta `<SharedFolder>/docker`. Esse arquivo de configuração define quais dados do nó de agente do usuário de OPC em um determinado servidor de agente do usuário de OPC o Publicador OPC deve assinar. A sintaxe completa do arquivo **publishednodes.json** é descrita na página do [Publicador OPC](https://github.com/Azure/iot-edge-opc-publisher) no GitHub. Quando você adicionar um gateway, coloque um **publishednodes.json** vazio na pasta:

    ```json
    [
    ]
    ```

- Sempre que o servidor de agente do usuário de OPC notificar o Publicador OPC quanto a uma alteração de dados, o novo valor é enviado ao Hub IoT. Dependendo das configurações de envio em lote, o Publicador OPC pode, primeiro, acumular os dados antes de enviá-los ao Hub IoT em um lote.

- O Docker não oferece suporte à resolução de nomes NetBIOS, somente à resolução de nomes DNS. Se você não tiver um servidor DNS na rede, poderá usar a solução alternativa mostrada no exemplo de linha de comando anterior. O exemplo de linha de comando anterior usa o parâmetro `--add-host` para incluir uma entrada no arquivo de hosts de contêineres. Essa entrada permite que o nome de host pesquise por `<OpcServerHostname>`, resolvendo para o endereço IP fornecido `<IpAddressOfOpcServerHostname>`.

- O agente do usuário do OPC usa certificados X.509 para autenticação e criptografia. Coloque o certificado do OPC Publisher no servidor OPC UA ao qual você está se conectando, para garantir que ele confie no OPC Publisher. O repositório de certificados do Publicador OPC está localizado na pasta `<SharedFolder>/CertificateStores`. Você pode encontrar o certificado do Publicador OPC na pasta `trusted/certs` na pasta `CertificateStores`.

  As etapas para configurar o servidor OPC UA dependem do dispositivo que você está usando. Normalmente, essas etapas ficam documentadas no manual do usuário do servidor de agente do usuário de OPC.

Para instalar o Proxy OPC, execute o comando a seguir no prompt de comando:

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.4 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db
```

Você precisa executar a instalação de uma vez em um sistema.

Use o comando a seguir para executar o Proxy OPC:

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.4 -D /mapped/cs.db
```

O Proxy OPC salva a cadeia de conexão durante a instalação. Em execuções subsequentes, você deve omiti-la, porque ela representa um risco de segurança.

## <a name="enable-your-gateway"></a>Habilitar o seu gateway

Conclua as etapas a seguir para habilitar o seu gateway no acelerador de solução da Fábrica Conectada:

1. Quando ambos os componentes estão em execução, navegue até a **conectar seu próprio servidor OPC UA** página no portal de solução de fábrica conectada. Esta página só está disponível para os administradores na solução. Insira o URL do endpoint do editor (opc.tcp: // publisher: 62222) e clique em  **Connect**.

1. Estabeleça uma relação de confiança entre o portal da Fábrica Conectada e o Publicador OPC. Quando você vir um aviso de certificado, clique em  **Prossiga**. Em seguida, você verá um erro informando que o publicador OPC não confia no cliente Web do agente do usuário. Para resolver esse erro, copie o **UA Web Client** de certificado do `<SharedFolder>/CertificateStores/rejected/certs` pasta para o `<SharedFolder>/CertificateStores/trusted/certs` pasta no gateway. Você não precisa reiniciar o gateway.

Agora você pode conectar ao gateway da nuvem, e você está pronto para adicionar servidores OPC UA à solução.

## <a name="add-your-own-opc-ua-servers"></a>Adicionar seus próprios servidores OPC de agente do usuário

Para adicionar seus próprios servidores OPC de agente do usuário ao acelerador de solução da Fábrica Conectada:

1. Navegue até a página  ** Conecte seu próprio servidor OPC UA **   no portal da solução Connected Factory.

    1. Inicie o servidor OPC de agente do usuário ao qual você deseja se conectar. Verifique se o seu servidor OPC UA pode ser alcançado pelo OPC Publisher e pelo OPC Proxy em execução no contêiner. Veja os comentários anteriores sobre a resolução de nomes.
    1. Insira a URL do ponto de extremidade do servidor OPC de agente do usuário (`opc.tcp://<host>:<port>`) e clique em **Conectar**.
    1. O processo de configuração da conexão estabelece uma relação de confiança entre o portal Connected Factory (cliente OPC UA) e o servidor OPC UA que você está tentando conectar. No painel do Connected Factory, você recebe um aviso **Certificado do servidor que deseja conectar não pode ser verificado**. Quando você encontrar um aviso de certificado, clique em **Continuar**.
    1. Mais difícil de configurar é a configuração do certificado do servidor OPC UA que você está tentando se conectar. Para servidores OPC UA baseados em PC, você pode apenas obter uma caixa de diálogo de aviso no painel que você pode confirmar. Para sistemas de servidor OPC de agente do usuário inseridos, consulte a documentação do seu servidor OPC de agente do usuário para pesquisar como essa tarefa é feita. Para concluir essa tarefa, talvez seja necessário ter o certificado do cliente OPC de agente do usuário do portal Fábrica Conectada. Um administrador pode fazer o download esse certificado na página **Conectar seu próprio servidor OPC de agente do usuário**:

        ![Portal de solução](./media/iot-accelerators-connected-factory-gateway-deployment/image4.png)

1. Navegue pela árvore de nós OPC UA do seu servidor OPC UA, clique com o botão direito do mouse nos nós OPC para os quais você deseja enviar valores ao Connected Factory e selecione  **publish**.

1. Agora, a telemetria flui do dispositivo de gateway. Você pode visualizar a telemetria na visualização  **Fábrica Locais** do portal Connected Factory em **Nova Fábrica**.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a arquitetura do acelerador de solução Connected Factory, consulte  [Passo a passo do acelerador de solução Connected Factory](iot-accelerators-connected-factory-sample-walkthrough.md).

Saiba mais sobre o [implementação de referência do publicador OPC](https://docs.microsoft.com/azure/iot-suite/iot-suite-connected-factory-publisher).