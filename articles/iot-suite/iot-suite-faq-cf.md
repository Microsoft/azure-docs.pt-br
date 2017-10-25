---
title: "Perguntas frequentes sobre a fábrica conectada do Azure IoT Suite | Microsoft Docs"
description: "Perguntas frequentes sobre a fábrica conectada do IoT Suite"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: corywink
ms.openlocfilehash: babd39d5e2f290d9e3f7fc3a232937c6ccfc85c6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="frequently-asked-questions-for-iot-suite-connected-factory-preconfigured-solution"></a>Perguntas frequentes sobre a solução pré-configurada de fábrica conectada do IoT Suite

Consulte também as [Perguntas frequentes](iot-suite-faq.md) gerais sobre o IoT Suite.

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solution"></a>Onde encontrar o código-fonte da solução pré-configurada?

O código-fonte é armazenado no seguinte repositório GitHub:

* [Solução pré-configurada de fábrica conectada](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>O que é OPC UA?

O OPC UA (Unified Architecture), lançado em 2008, é um padrão de interoperabilidade orientado a serviços e independente de plataforma. O OPC UA é usado por vários sistemas e dispositivos industriais, como computadores, PLCs e sensores do setor. O OPC UA integra a funcionalidade das especificações do OPC Clássico a uma estrutura extensível com segurança interna. É um padrão controlado pela OPC Foundation. A [OPC Foundation](http://opcfoundation.org/) é uma organização sem fins lucrativos com mais de 440 membros. A meta da organização é usar as especificações do OPC para facilitar a interoperabilidade segura e confiável entre fornecedores e plataformas por meio de:

* Infraestrutura
* Especificações
* Tecnologia
* Processos

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-preconfigured-solution"></a>Por que a Microsoft escolheu o OPC UA para a solução pré-configurada de fábrica conectada?

A Microsoft escolheu OPC UA porque ele é um padrão aberto, comprovado, não proprietário, independente de plataforma e reconhecido no setor. É um requisito para soluções de arquitetura de referência Industrie 4.0 (RAMI4.0) garantindo a interoperabilidade entre um amplo conjunto de processos de fabricação e equipamento. A Microsoft considera a demanda de nossos clientes para criar soluções do Industrie 4.0. O suporte para OPC UA ajuda a reduzir a barreira para os clientes atingirem suas metas e fornece valor comercial imediato para eles.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>Como fazer para adicionar um endereço IP público à VM de simulação?

Você tem duas opções para adicionar o endereço IP:

* Usar o script `Simulation/Factory/Add-SimulationPublicIp.ps1` do PowerShell no [repositório](https://github.com/Azure/azure-iot-connected-factory). Passar o nome da implantação como um parâmetro. Para uma implantação local, use `<your username>ConnFactoryLocal`. O script imprime o endereço IP da VM.

* No portal do Azure, localize o grupo de recursos de sua implantação. Com exceção de uma implantação local, o grupo de recursos tem o nome especificado como o nome da solução ou da implantação. Para uma implantação local que usa o script de build, o nome do grupo de recursos é `<your username>ConnFactoryLocal`. Agora, adicione um novo recurso **Endereço IP público** ao grupo de recursos.

> [!NOTE]
> Em qualquer caso, instale os últimos patches seguindo as instruções deste [site do Ubuntu](https://wiki.ubuntu.com/Security/Upgrades). Mantenha a instalação atualizada pelo período em que a VM estiver acessível por meio de um endereço IP público.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>Como fazer para remover o endereço IP público da VM de simulação?

Você tem duas opções para remover o endereço IP:

* Usar o script Simulation/Factory/Remove-SimulationPublicIp.ps1 do PowerShell do [repositório](https://github.com/Azure/azure-iot-connected-factory). Passar o nome da implantação como um parâmetro. Para uma implantação local, use `<your username>ConnFactoryLocal`. O script imprime o endereço IP da VM.

* No portal do Azure, localize o grupo de recursos de sua implantação. Com exceção de uma implantação local, o grupo de recursos tem o nome especificado como o nome da solução ou da implantação. Para uma implantação local que usa o script de build, o nome do grupo de recursos é `<your username>ConnFactoryLocal`. Agora, remova o recurso **Endereço IP público** do grupo de recursos.

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>Como fazer para entrar na VM de simulação?

Há suporte para a entrada na VM de simulação apenas se você implantou a solução usando o script `build.ps1` do PowerShell no [repositório](https://github.com/Azure/azure-iot-connected-factory).

Se você implantou a solução por meio de www.azureiotsuite.com, não poderá entrar na VM. Você não poderá entrar porque a senha é gerada aleatoriamente e não é possível redefini-la.

1. Adicione um endereço IP público à VM. Consulte [Como fazer para adicionar um endereço IP público à VM de simulação?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. Crie uma sessão do SSH para a VM usando o endereço IP da VM.
1. O nome de usuário a ser usado é `docker`.
1. A senha a ser usada depende da versão usada para implantação:
    * Para soluções implantadas com o script build.ps1 antes de 1º de junho de 2017, a senha é `Passw0rd`.
    * Para soluções implantadas com o script build.ps1 após 1º de junho de 2017, encontre a senha no arquivo `<name of your deployment>.config.user`. A senha é armazenada na configuração **VmAdminPassword**. A senha é gerada aleatoriamente no momento da implantação, a menos que você a especifique usando o parâmetro `-VmAdminPassword` do script `build.ps1`

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>Como fazer para interromper e iniciar todos os processos do Docker na VM de simulação?

1. Entre na VM de simulação. Consulte [Como fazer para entrar na VM de simulação?](#how-do-i-sign-in-to-the-simulation-vm)
1. Para verificar quais contêineres estão ativos, execute `docker ps`.
1. Para interromper todos os contêineres de simulação, execute `./stopsimulation`.
1. Para iniciar todos os contêineres de simulação:
    * Exporte uma variável do shell com o nome **IOTHUB_CONNECTIONSTRING**. Use o valor da configuração **IotHubOwnerConnectionString** no arquivo `<name of your deployment>.config.user`. Por exemplo:

        ```
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * Execute `./startsimulation`.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>Como fazer para atualizar a simulação na VM?

Se você fez alterações na simulação, use o script `build.ps1` do PowerShell no [repositório](https://github.com/Azure/azure-iot-connected-factory) usando o comando `updatedimulation`. Esse script compila todos os componentes da simulação, interrompe a simulação na VM, carrega-os, instala-os e inicia-os.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>Como fazer para descobrir a cadeia de conexão do hub IoT usada por minha solução?

Se você implantou sua solução com o script `build.ps1` no [repositório](https://github.com/Azure/azure-iot-connected-factory), a cadeia de conexão é o valor de **IotHubOwnerConnectionString** no arquivo `<name of your deployment>.config.user`.

Encontre também a cadeia de conexão usando o portal do Azure. No recurso Hub IoT no grupo de recursos da implantação, localize as configurações da cadeia de conexão.

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>Quais dispositivos Hub IoT são usados pela simulação de Fábrica conectada?

A simulação registra automaticamente os seguintes dispositivos:

* proxy.beijing.corp.contoso
* proxy.capetown.corp.contoso
* proxy.mumbai.corp.contoso
* proxy.munich0.corp.contoso
* proxy.rio.corp.contoso
* proxy.seattle.corp.contoso
* publisher.beijing.corp.contoso
* publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

Usando o [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) ou a ferramenta [iothub-explorer](https://github.com/azure/iothub-explorer), verifique quais dispositivos estão registrados no hub IoT usado pela solução. Para usar essas ferramentas, você precisa da cadeia de conexão do hub IoT na implantação.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Como obter dados de log dos componentes da simulação?

Todos os componentes da simulação registram informações em arquivos de log. Esses arquivos podem ser encontrados na VM, na pasta `home/docker/Logs`. Para recuperar os logs, use o script `Simulation/Factory/Get-SimulationLogs.ps1` do PowerShell no [repositório](https://github.com/Azure/azure-iot-connected-factory).

Esse script precisa entrar na VM. Talvez seja necessário fornecer credenciais para a entrada. Consulte [Como fazer para entrar na VM de simulação?](#how-do-i-sign-in-to-the-simulation-vm) para encontrar as credenciais.

O script adiciona/remove um endereço IP público da VM, caso ela ainda não tenha um e o remove. O script coloca todos os arquivos de log em um arquivo morto e baixa o arquivo morto na estação de trabalho de desenvolvimento.

Como alternativa, faça logon na VM por meio do SSH e inspecione os arquivos de log em tempo de execução.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Como verificar se a simulação está enviando dados para a nuvem?

Com o [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) ou a ferramenta [iothub-explorer](https://github.com/azure/iothub-explorer), inspecione os dados enviados para o Hub IoT de determinados dispositivos. Para usar essas ferramentas, você precisa saber a cadeia de conexão do hub IoT na implantação. Consulte [Como fazer para descobrir a cadeia de conexão do hub IoT usada por minha solução?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

Inspecione os dados enviados por um dos dispositivos fornecedores:

* publisher.beijing.corp.contoso
* publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

Caso você não veja nenhum dado enviado para o Hub IoT, há um problema com a simulação. Como uma primeira etapa de análise, você deve analisar os arquivos de log dos componentes da simulação. Consulte [Como obter dados de log dos componentes da simulação?](#how-can-i-get-log-data-from-the-simulation-components) Em seguida, tente interromper e iniciar a simulação e, se ainda nenhum dado estiver sendo enviado, atualize a simulação por completo. Consulte [Como fazer para atualizar a simulação na VM?](#how-do-i-update-the-simulation-in-the-vm)

### <a name="next-steps"></a>Próximas etapas

Você também pode explorar alguns dos outros recursos das soluções pré-configuradas do IoT Suite:

* [Visão geral da solução pré-configurada de manutenção preditiva](iot-suite-predictive-overview.md)
* [Visão geral da solução pré-configurada de fábrica conectada](iot-suite-connected-factory-overview.md)
* [Segurança da IoT desde o início](securing-iot-ground-up.md)