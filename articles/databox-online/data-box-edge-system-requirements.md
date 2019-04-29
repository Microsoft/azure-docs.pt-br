---
title: Requisitos de sistema do Microsoft Azure Data Box Edge | Microsoft Docs
description: Aprender sobre os requisitos de software e de rede para seu Azure Data Box Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/03/2019
ms.author: alkohli
ms.openlocfilehash: 90c60d586d505ca0c9bd787c37e137f7a38ee1f7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60756538"
---
# <a name="azure-data-box-edge-system-requirements"></a>Requisitos de sistema de borda da caixa de dados do Azure

Este artigo descreve os requisitos de sistema importantes para a solução do Microsoft Azure Data Box Edge e para os clientes que se conectam ao Azure Data Box Edge. É recomendável que você examine as informações com atenção antes de implantar o Data Box Edge. Você pode consultar novamente a essas informações conforme necessário durante a implantação e operação subsequente.

São requisitos de sistema do Data Box Edge:

- **Requisitos de software para hosts** – descreve as plataformas com suporte, os navegadores da interface do usuário de configuração local, os clientes SMB e requisitos adicionais para os clientes que acessam o dispositivo.
- **Requisitos de rede para o dispositivo** – fornece informações sobre os requisitos de rede da operação do dispositivo físico.

## <a name="supported-os-for-clients-connected-to-device"></a>Sistema operacional com suporte para clientes conectados ao dispositivo

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Protocolos com suporte para clientes que acessam o dispositivo

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Contas de armazenamento com suporte

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>Tipos de armazenamento com suporte

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Navegadores com suporte para a interface do usuário da Web local

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Requisitos de porta de rede

### <a name="port-requirements-for-data-box-edge"></a>Requisitos de porta para Data Box Edge

A tabela a seguir lista as portas que devem ser abertas no firewall para permitir o tráfego SMB, de nuvem ou de gerenciamento. Nesta tabela, *entrada* ou *de entrada* refere-se à direção da qual as solicitações do cliente acessam o dispositivo. *Saída* ou *de saída* referem-se à direção para a qual seu dispositivo do Data Box Edge envia dados externamente, além da implantação, por exemplo, saída para a internet.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>Requisitos de porta para IoT Edge

O Azure IoT Edge permite a comunicação de saíde de um dispositivo do Edge local com a nuvem do Azure usando os protocolos do Hub IoT com suporte. A comunicação de entrada só é necessária para cenários específicos em que o Hub IoT do Azure precisa enviar mensagens por push para o dispositivo do Azure IoT Edge (por exemplo, mensagens de nuvem a dispositivo).

Use a tabela a seguir para a configuração de porta dos servidores que hospedam o tempo de execução do IoT Edge:

| Nº da porta | Entrada ou saída | Escopo da porta | Obrigatório | Diretrizes |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| Saída       | WAN        | Sim      | Saída aberta para o provisionamento do IoT Edge. Essa configuração é necessária ao usar scripts manuais ou o DPS (serviço de provisionamento de dispositivos) do Azure IoT.|

Para todas as informações, acesse [Regras de configuração de firewall e de porta para implantação do IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot).

## <a name="url-patterns-for-firewall-rules"></a>Padrões de URL para regras de firewall

Os administradores de rede geralmente podem configurar regras avançadas de firewall com base nos padrões de URL para filtrar o tráfego de entrada e de saída. Seu dispositivo do Data Box Edge e o serviço dependem de outros aplicativos da Microsoft, como o Barramento de Serviço do Azure, o Controle de Acesso do Azure Active Directory, contas de armazenamento e servidores do Microsoft Update. Os padrões de URL associados a esses aplicativos podem ser usados para configurar regras de firewall. É importante entender que os padrões de URL associados a esses aplicativos podem ser alterados. Essas mudanças exigem que o administrador de rede monitore e atualize as regras de firewall do Data Box Edge conforme e quando necessário.

É recomendado que você defina suas regras de firewall para tráfego de saída, com base nos endereços IP fixos do Data Box Edge e, na maioria dos casos, de modo flexível. No entanto, você pode usar as informações a seguir para definir regras avançadas de firewall que são necessárias para criar ambientes seguros.

> [!NOTE]
> - Os IPs do dispositivo (de origem) sempre devem estar configurados para todas as interfaces de rede habilitadas para a nuvem.
> - Os IPs de destino devem ser configurados como [Intervalos de IP do datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).

### <a name="url-patterns-for-gateway-feature"></a>Padrões de URL para o recurso de gateway

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>Padrões de URL para o recurso de computação

| Padrão de URL                      | Componente ou funcionalidade                     |   
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*.cdn.mscr.io | Registro de Contêiner da Microsoft (obrigatório)               |
| https://\*.azurecr.io                     | Registros de contêiner pessoal e de terceiros (opcional) | 
| https://\*.azure-devices.net              | Acesso do Hub IoT (obrigatório)                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>Padrões de URL para o gateway para o Azure governamental

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>Padrões de URL para a computação para o Azure governamental

| Padrão de URL                      | Componente ou funcionalidade                     |  
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*.cdn.mscr.com | Registro de Contêiner da Microsoft (obrigatório)               |
| https://\*.azure-devices.us              | Acesso do Hub IoT (obrigatório)           |
| https://\*.azurecr.us                    | Registros de contêiner pessoal e de terceiros (opcional) | 

## <a name="internet-bandwidth"></a>Largura de banda de Internet

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>Considerações sobre dimensionamento de computação

Use sua experiência ao desenvolver e testar sua solução para garantir que há capacidade suficiente em seu dispositivo de borda da caixa de dados e obter o desempenho ideal do seu dispositivo.

Você deve considerar os fatores incluem:

- **Informações específicas do contêiner** -pensar sobre o seguinte.

    - Quantos contêineres estão em sua carga de trabalho? Você poderia ter muitos contêineres leves em comparação com aqueles alguns de muitos recursos.
    - Quais são os recursos alocados para esses contêineres versus o que são os recursos que estão sendo consumidos?
    - Quantas camadas de seus contêineres de compartilhamento?
    - Há contêineres não utilizados? Um contêiner de parada ainda ocupa espaço em disco.
    - Em qual idioma seus contêineres são gravados?
- **Tamanho dos dados processados** -a quantidade de dados serão seus contêineres de processamento? Esses dados consumirão espaço em disco ou os dados serão processados na memória?
- **Desempenho esperado** -quais são as características de desempenho desejado da sua solução? 

Para compreender e refinar o desempenho de sua solução, você pode usar:

- As métricas de computação disponíveis no portal do Azure. Vá para o recurso de borda da caixa de dados e, em seguida, vá para **monitoramento > métricas**. Examine os **computação de borda - o uso de memória** e **computação de borda – porcentagem de CPU** para entender os recursos disponíveis e como os recursos estão obtendo consumidos.
- Os comandos de monitoramento disponíveis por meio da interface do PowerShell do dispositivo, como:

    - `dkr` estatísticas para obter estatísticas de uso de recursos de uma transmissão ao vivo de contêineres. O comando dá suporte ao uso de memória, CPU, limite de memória e métricas de e/s de rede.
    - `dkr system df` Para obter informações sobre a quantidade de espaço em disco usado. 
    - `dkr image [prune]` a limpeza de imagens não usadas e liberar espaço.
    - `dkr ps --size` Para exibir o tamanho aproximado de um contêiner em execução. 

    Para obter mais informações sobre os comandos disponíveis, acesse [monitorar e solucionar problemas de computação módulos](data-box-edge-connect-powershell-interface.md#monitor-and-troubleshoot-compute-modules).

Por fim, certifique-se de que você valide sua solução em seu conjunto de dados e quantificar o desempenho na borda da caixa de dados antes de implantar na produção.


## <a name="next-step"></a>Próxima etapa

- [Implante seu Azure Data Box Edge](data-box-edge-deploy-prep.md)
