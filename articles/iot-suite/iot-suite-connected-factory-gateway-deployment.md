---
title: "Implantar o gateway de fábrica conectada do Azure IoT Suite | Microsoft Docs"
description: "Como implantar um gateway no Windows ou no Linux para permitir conectividade com a solução pré-configurada de fábrica conectada."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: dobett
ms.openlocfilehash: b0e6ae705911d7c18643c77b7fe08fdffffa5eb1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-a-gateway-on-windows-or-linux-for-the-connected-factory-preconfigured-solution"></a>Implantar um gateway no Windows ou Linux para a solução pré-configurada de fábrica conectada

O software necessário para implantar um gateway para a solução pré-configurada de fábrica conectada tem dois componentes:

* O *Proxy OPC* estabelece uma conexão ao Hub IoT. O *Proxy OPC* aguarda então as mensagens de comando e controle do navegador de OPC integrado que é executado no portal da solução de fábrica conectada.
* O *Publicador OPC* conecta-se a servidores OPC UA locais existentes e encaminha mensagens de telemetria deles para o Hub IoT.

Ambos os componentes são de código livre e estão disponíveis como origem no GitHub e nos contêineres do Docker:

| GitHub | DockerHub |
| ------ | --------- |
| [Publicador OPC][lnk-publisher-github] | [Publicador OPC][lnk-publisher-docker] |
| [Proxy OPC][lnk-proxy-github] | [Proxy OPC][lnk-proxy-docker] |

Nenhum endereço IP voltado ao público nem falhas no firewall do gateway são necessários para nenhum desses componentes. O Proxy OPC e o Publicador OPC usam somente as portas de saída 443, 5671 e 8883.

As etapas neste artigo mostram como implantar um gateway usando o Docker no [Windows](#windows-deployment) ou [Linux](#linux-deployment). O gateway permite conectividade com a solução pré-configurada de fábrica conectada.

> [!NOTE]
> O software do gateway que é executado no contêiner do Docker é o [Azure IoT Edge].

## <a name="windows-deployment"></a>Implantação no Windows

> [!NOTE]
> Se você ainda não tem um dispositivo de gateway, a Microsoft aconselha a comprar um gateway comercial de um de nossos parceiros. Veja o [catálogo de dispositivos do Azure IoT] para obter uma lista de dispositivos de gateway compatíveis com a solução de fábrica conectada. Siga as instruções que acompanham o dispositivo para configurar o gateway. Se preferir, use as instruções a seguir para configurar manualmente um dos seus gateways existentes.

### <a name="install-docker"></a>Instalar o Docker

Instale o [Docker para Windows] no seu dispositivo de gateway baseado no Windows. Durante a configuração do Docker no Windows, selecione uma unidade no seu computador host para compartilhar com o Docker. A seguinte captura de tela mostra o compartilhamento da unidade D no sistema Windows:

![Instalar o Docker][img-install-docker]

Em seguida, crie uma pasta chamada **docker** na raiz da unidade compartilhada.
Você também pode executar essa etapa após a instalação do docker usando o menu **Configurações**.

### <a name="configure-the-gateway"></a>Configurar o gateway

1. Você precisa da cadeia de conexão **iothubowner** da sua implantação de fábrica conectada do Azure IoT Suite para concluir a implantação do gateway. No [portal do Azure], navegue até o Hub IoT no grupo de recursos criado quando você implantou a solução de fábrica conectada. Clique em **Políticas de acesso compartilhado** para acessar a cadeia de conexão **iothubowner**:

    ![Localizar a cadeia de conexão Hub IoT][img-hub-connection]

    Copie o valor de **Cadeia de conexão – chave primária**.

1. Configure o gateway para o Hub IoT executando os dois módulos de gateway **uma vez** em um prompt de comando com:

    `docker run -it --rm -h <ApplicationName> -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v //D/docker:/root/.dotnet/corefx/cryptography/x509stores microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName> "<IoTHubOwnerConnectionString>"`

    `docker run -it --rm -v //D/docker:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db`

    * **&lt;ApplicationName&gt;** é o nome a dar ao Publicador OPC UA no formato **publisher.&lt;seu nome de domínio totalmente qualificado&gt;**. Por exemplo, se sua rede de fábrica é chamada **myfactorynetwork.com**, o valor de **ApplicationName** é **publisher.myfactorynetwork.com**.
    * **&lt;IoTHubOwnerConnectionString&gt;** é a cadeia de conexão **iothubowner** que você copiou na etapa anterior. Essa cadeia de conexão é usada apenas nessa etapa e você não precisará dela nas próximas etapas:

    Você usa a pasta D:\\docker mapeada (o argumento `-v`) posteriormente para persistir os dois certificados X.509 usados pelos módulos de gateway.

### <a name="run-the-gateway"></a>Executar o gateway

1. Reinicie o gateway usando os seguintes comandos:

    `docker run -it --rm -h <ApplicationName> --expose 62222 -p 62222:62222 -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/Logs -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v //D/docker:/shared -v //D/docker:/root/.dotnet/corefx/cryptography/x509stores -e _GW_PNFP="/shared/publishednodes.JSON" microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName>`

    `docker run -it --rm -v //D/docker:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -D /mapped/cs.db`

1. Por motivos de segurança, os dois certificados X.509 persistidos na pasta D:\\docker contêm a chave privada. Limite o acesso a essa pasta às credenciais (normalmente **Administradores**) usadas para executar o contêiner do Docker. Clique com o botão direito do mouse na pasta D:\\docker, escolha **Propriedades**, **Segurança** e, por fim, **Editar**. Dê aos **Administradores** controle total e remova todas as outras pessoas:

    ![Conceder permissões ao compartilhamento do Docker][img-docker-share]

1. Verifique a conectividade de rede. Em um prompt de comando, digite o comando `ping publisher.<your fully qualified domain name>` para efetuar ping no gateway. Se o destino não estiver acessível, adicione o endereço IP e o nome do seu gateway ao arquivo de hosts no gateway. O arquivo de hosts está localizado na pasta **Windows\\System32\\drivers\\etc**.

1. Em seguida, tente se conectar ao publicador usando um cliente OPC UA local em execução no gateway. A URL do ponto de extremidade OPC UA é `opc.tcp://publisher.<your fully qualified domain name>:62222`. Caso não tenha um cliente OPC UA, você poderá baixar e usar um [cliente OPC UA de software livre].

1. Quando tiver concluído com êxito esses testes locais, navegue até a página **Conectar seu próprio Servidor OPC UA** no portal de solução de fábrica conectada. Insira a URL de ponto de extremidade do publicador (`tcp://publisher.<your fully qualified domain name>:62222`) e clique em **Conectar**. Você recebe um aviso de certificado. Clique em **Continuar.** Em seguida, você verá um erro informando que o publicador não confia no UA Web Client. Para resolver esse erro, copie o certificado **UA Web Client** da pasta **D:\\docker\\Rejected Certificates\\certs** para a pasta **D:\\docker\\UA Applications\\certs** no gateway. Não é preciso reiniciar o gateway. Repita esta etapa. Agora, é possível se conectar ao gateway da nuvem e você está pronto para adicionar servidores OPC UA à solução.

### <a name="add-your-opc-ua-servers"></a>Adicionar os servidores OPC UA

1. Navegue até a página **Conectar seu próprio Servidor OPC UA** no portal de solução de fábrica conectada. Siga as mesmas etapas da seção anterior para estabelecer uma relação de confiança entre o portal da fábrica conectada e o servidor OPC UA. Esta etapa estabelece uma relação de confiança mútua dos certificados do portal da fábrica conectada e o servidor OPC UA e cria uma conexão.

1. Procure a árvore de nós OPC UA do servidor OPC UA, clique com o botão direito do mouse nos nós OPC e selecione **publicar**. Para que a publicação funcione dessa maneira, o servidor OPC UA e o publicador devem estar na mesma rede. Em outras palavras, se o nome de domínio totalmente qualificado do publicador for **publicador.meudominio.com**, o nome de domínio totalmente qualificado do servidor OPC UA deverá ser, por exemplo, **meuservidoropcua.meudominio.com**. Se sua configuração for diferente, você poderá adicionar nós manualmente ao arquivo publishesnodes.json encontrado na pasta **D:\\docker**. O arquivo publishesnodes.json é gerado automaticamente na primeira publicação bem-sucedida de um nó OPC.

1. Agora, a telemetria flui do dispositivo de gateway. Você pode ver a telemetria na exibição **Locais de Fábrica** do portal de fábrica conectada em **Nova Fábrica**.

## <a name="linux-deployment"></a>Implantação no Linux

> [!NOTE]
> Se você ainda não tem um dispositivo de gateway, a Microsoft aconselha a comprar um gateway comercial de um de nossos parceiros. Veja o [catálogo de dispositivos do Azure IoT] para obter uma lista de dispositivos de gateway compatíveis com a solução de fábrica conectada. Siga as instruções que acompanham o dispositivo para configurar o gateway. Se preferir, use as instruções a seguir para configurar manualmente um dos seus gateways existentes.

[Instale o Docker] em seu dispositivo de gateway Linux.

### <a name="configure-the-gateway"></a>Configurar o gateway

1. Você precisa da cadeia de conexão **iothubowner** da sua implantação de fábrica conectada do Azure IoT Suite para concluir a implantação do gateway. No [portal do Azure], navegue até o Hub IoT no grupo de recursos criado quando você implantou a solução de fábrica conectada. Clique em **Políticas de acesso compartilhado** para acessar a cadeia de conexão **iothubowner**:

    ![Localizar a cadeia de conexão Hub IoT][img-hub-connection]

    Copie o valor de **Cadeia de conexão – chave primária**.

1. Configure o gateway para o Hub IoT executando os dois módulos de gateway **uma vez** em um shell com:

    `sudo docker run -it --rm -h <ApplicationName> -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/ -v /shared:/root/.dotnet/corefx/cryptography/x509stores microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName> "<IoTHubOwnerConnectionString>"`

    `sudo docker run --rm -it -v /shared:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db`

    * **&lt;ApplicationName&gt;** é o nome do aplicativo OPC UA que o gateway cria no formato **publicador.&lt;seu nome de domínio totalmente qualificado&gt;**. Por exemplo, **publicador.microsoft.com**.
    * **&lt;IoTHubOwnerConnectionString&gt;** é a cadeia de conexão **iothubowner** que você copiou na etapa anterior. Essa cadeia de conexão é usada apenas nessa etapa e você não precisará dela nas próximas etapas:

    Você usa a pasta **/shared** mapeada (o argumento `-v`) posteriormente para persistir os dois certificados X.509 usados pelos módulos de gateway.

### <a name="run-the-gateway"></a>Executar o gateway

1. Reinicie o gateway usando os seguintes comandos:

    `sudo docker run -it -h <ApplicationName> --expose 62222 -p 62222:62222 –-rm -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/Logs -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v /shared:/shared -v /shared:/root/.dotnet/corefx/cryptography/x509stores -e _GW_PNFP="/shared/publishednodes.JSON" microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName>`

    `sudo docker run -it -v /shared:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -D /mapped/cs.db`

1. Por motivos de segurança, os dois certificados X.509 persistidos na pasta **/shared** contêm a chave privada. Limite o acesso a essa pasta às credenciais usadas para executar o contêiner do Docker. Para definir as permissões apenas para **raiz**, use o comando do shell `chmod` na pasta.

1. Verifique a conectividade de rede. De um shell, digite o comando `ping publisher.<your fully qualified domain name>` para efetuar ping no gateway. Se o destino não estiver acessível, adicione o endereço IP e o nome do seu gateway ao arquivo de hosts no gateway. O arquivo de hosts está localizado na pasta **/etc**.

1. Em seguida, tente se conectar ao publicador usando um cliente OPC UA local em execução no gateway. A URL do ponto de extremidade OPC UA é `opc.tcp://publisher.<your fully qualified domain name>:62222`. Caso não tenha um cliente OPC UA, você poderá baixar e usar um [cliente OPC UA de software livre].

1. Quando tiver concluído com êxito esses testes locais, navegue até a página **Conectar seu próprio Servidor OPC UA** no portal de solução de fábrica conectada. Insira a URL de ponto de extremidade do publicador (`tcp://publisher.<your fully qualified domain name>:62222`) e clique em **Conectar**. Você recebe um aviso de certificado. Clique em **Continuar.** Em seguida, você verá um erro informando que o publicador não confia no UA Web Client. Para resolver esse erro, copie o certificado **UA Web Client** da pasta **/shared/Rejected Certificates/certs** para a pasta **/shared/UA Applications/certs** no gateway. Não é preciso reiniciar o gateway. Repita esta etapa. Agora, é possível se conectar ao gateway da nuvem e você está pronto para adicionar servidores OPC UA à solução.

### <a name="add-your-opc-ua-servers"></a>Adicionar os servidores OPC UA

1. Navegue até a página **Conectar seu próprio Servidor OPC UA** no portal de solução de fábrica conectada. Siga as mesmas etapas da seção anterior para estabelecer uma relação de confiança entre o portal da fábrica conectada e o servidor OPC UA. Esta etapa estabelece uma relação de confiança mútua dos certificados do portal da fábrica conectada e o servidor OPC UA e cria uma conexão.

1. Procure a árvore de nós OPC UA do servidor OPC UA, clique com o botão direito do mouse nos nós OPC e selecione **publicar**. Para que a publicação funcione dessa maneira, o servidor OPC UA e o publicador devem estar na mesma rede. Em outras palavras, se o nome de domínio totalmente qualificado do publicador for **publicador.meudominio.com**, o nome de domínio totalmente qualificado do servidor OPC UA deverá ser, por exemplo, **meuservidoropcua.meudominio.com**. Se sua configuração for diferente, você poderá adicionar nós manualmente ao arquivo publishesnodes.json encontrado na pasta **/shared**. O publishesnodes.json é gerado automaticamente na primeira publicação bem-sucedida de um nó OPC.

1. Agora, a telemetria flui do dispositivo de gateway. Você pode ver a telemetria na exibição **Locais de Fábrica** do portal de fábrica conectada em **Nova Fábrica**.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a arquitetura da solução pré-configurada de fábrica conectada, confira [Passo a passo da solução pré-configurada de fábrica conectada][lnk-walkthrough].

[img-install-docker]: ./media/iot-suite-connected-factory-gateway-deployment/image1.png
[img-hub-connection]: ./media/iot-suite-connected-factory-gateway-deployment/image2.png
[img-docker-share]: ./media/iot-suite-connected-factory-gateway-deployment/image3.png

[Docker para Windows]: https://www.docker.com/docker-windows
[catálogo de dispositivos do Azure IoT]: https://catalog.azureiotsuite.com/?q=opc
[portal do Azure]: http://portal.azure.com/
[cliente OPC UA de software livre]: https://github.com/OPCFoundation/UA-.NETStandardLibrary/tree/master/SampleApplications/Samples/Client.Net4
[Instale o Docker]: https://www.docker.com/community-edition#/download
[lnk-walkthrough]: iot-suite-connected-factory-sample-walkthrough.md
[Azure IoT Edge]: https://github.com/Azure/iot-edge

[lnk-publisher-github]: https://github.com/Azure/iot-edge-opc-publisher
[lnk-publisher-docker]: https://hub.docker.com/r/microsoft/iot-gateway-opc-ua
[lnk-proxy-github]: https://github.com/Azure/iot-edge-opc-proxy
[lnk-proxy-docker]: https://hub.docker.com/r/microsoft/iot-gateway-opc-ua-proxy