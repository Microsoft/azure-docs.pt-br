---
title: Requisitos de sistema do Microsoft Azure Stack Edge pro R | Microsoft Docs
description: Saiba mais sobre os requisitos de software e rede para seu Azure Stack Edge pro R
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: alkohli
ms.openlocfilehash: ef81494b6c93f2941cc41b367e566332d6bc7cc9
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831324"
---
# <a name="azure-stack-edge-pro-r-system-requirements"></a>Requisitos de sistema do Azure Stack Edge pro R

Este artigo descreve os requisitos de sistema importantes para sua solução Azure Stack Edge pro R e para os clientes que se conectam ao Azure Stack Edge pro R. Recomendamos que você revise as informações cuidadosamente antes de implantar seu Azure Stack Edge pro R. Você pode consultar essas informações conforme necessário durante a implantação e a operação subsequente.

Os requisitos de sistema para o Azure Stack Edge pro R incluem:

- **Requisitos de software para hosts** – descreve as plataformas com suporte, os navegadores da interface do usuário de configuração local, os clientes SMB e requisitos adicionais para os clientes que acessam o dispositivo.
- **Requisitos de rede para o dispositivo** – fornece informações sobre os requisitos de rede da operação do dispositivo físico.

## <a name="supported-os-for-clients-connected-to-device"></a>Sistema operacional com suporte para clientes conectados ao dispositivo

[!INCLUDE [Supported OS for clients connected to device](../../includes/azure-stack-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Protocolos com suporte para clientes que acessam o dispositivo

[!INCLUDE [Supported protocols for clients accessing device](../../includes/azure-stack-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Contas de armazenamento com suporte

[!INCLUDE [Supported storage accounts](../../includes/azure-stack-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-tiered-storage-accounts"></a>Contas de armazenamento em camadas com suporte

Quando gerenciados do Azure Stack, as seguintes contas de armazenamento em camadas têm suporte com interfaces SMB/NFS/REST.

|Tipo  |Conta de armazenamento  |Comentários  |
|---------|---------|---------|
|Standard     |GPv1: BLOB de blocos         |         |
|    |  Armazenamento de BLOBs: BLOB de blocos       | Com suporte apenas para NAS     |

* Atualmente, não há suporte para BLOBs de páginas e arquivos do Azure no Azure Stack.
* * Camada quente e frio não existem no Azure Stack. Use o Azure PowerShell para mover os dados para a camada de arquivo depois que os dados forem carregados. Para obter instruções passo a passo, acesse [usar Azure PowerShell para definir a camada de blob]()

## <a name="supported-storage-types"></a>Tipos de armazenamento com suporte

[!INCLUDE [Supported storage types](../../includes/azure-stack-edge-gateway-supported-storage-types.md)]


## <a name="supported-browsers-for-local-web-ui"></a>Navegadores com suporte para a interface do usuário da Web local

[!INCLUDE [Supported browsers for local web UI](../../includes/azure-stack-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Requisitos de porta de rede

### <a name="port-requirements-for-azure-stack-edge-pro-r"></a>Requisitos de porta para Azure Stack o Edge pro R

A tabela a seguir lista as portas que devem ser abertas no firewall para permitir o tráfego SMB, de nuvem ou de gerenciamento. Nesta tabela, *entrada* ou *de entrada* refere-se à direção da qual as solicitações do cliente acessam o dispositivo. *Out* ou *Outbound* refere-se à direção em que seu dispositivo R Azure Stack Edge pro envia dados externamente, além da implantação, por exemplo, de saída para a Internet.

[!INCLUDE [Port configuration for device](../../includes/azure-stack-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>Requisitos de porta para IoT Edge

O Azure IoT Edge permite a comunicação de saíde de um dispositivo do Edge local com a nuvem do Azure usando os protocolos do Hub IoT com suporte. A comunicação de entrada só é necessária para cenários específicos em que o Hub IoT do Azure precisa enviar mensagens por push para o dispositivo do Azure IoT Edge (por exemplo, mensagens de nuvem a dispositivo).

Use a tabela a seguir para a configuração de porta dos servidores que hospedam o runtime do Azure IoT Edge:

| Nº da porta | Entrada ou saída | Escopo da porta | Obrigatório | Diretrizes |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| Saída       | WAN        | Sim      | Saída aberta para provisionamento de IoT Edge. Essa configuração é necessária ao usar scripts manuais ou o DPS (serviço de provisionamento de dispositivos) do Azure IoT.|

Para todas as informações, acesse [Regras de configuração de firewall e de porta para implantação do IoT Edge](../iot-edge/troubleshoot.md).

## <a name="url-patterns-for-firewall-rules"></a>Padrões de URL para regras de firewall

Os administradores de rede geralmente podem configurar regras avançadas de firewall com base nos padrões de URL para filtrar o tráfego de entrada e de saída. O dispositivo pro R do Azure Stack Edge e o serviço dependem de outros aplicativos da Microsoft, como o barramento de serviço do Azure, o controle de acesso Azure Active Directory, as contas de armazenamento e os servidores Microsoft Update. Os padrões de URL associados a esses aplicativos podem ser usados para configurar regras de firewall. É importante entender que os padrões de URL associados a esses aplicativos podem ser alterados. Essas alterações exigem que o administrador de rede monitore e atualize regras de firewall para seu Azure Stack Edge pro R como e quando necessário.

Recomendamos que você defina suas regras de firewall para o tráfego de saída, com base em endereços IP fixos do Azure Stack Edge pro R, livremente na maioria dos casos. No entanto, você pode usar as informações a seguir para definir regras avançadas de firewall que são necessárias para criar ambientes seguros.

> [!NOTE]
> - Os IPs do dispositivo (de origem) sempre devem estar configurados para todas as interfaces de rede habilitadas para a nuvem.
> - Os IPs de destino devem ser definidos para [intervalos de IP do datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).

### <a name="url-patterns-for-gateway-feature"></a>Padrões de URL para o recurso de gateway

[!INCLUDE [URL patterns for firewall](../../includes/azure-stack-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>Padrões de URL para o recurso de computação

| Padrão de URL                      | Componente ou funcionalidade                     |   
|----------------------------------|---------------------------------------------|
| https: \/ /MCR.Microsoft.com<br></br>https://\*.cdn.mscr.io | Registro de Contêiner da Microsoft (obrigatório)               |
| https://\*.azurecr.io                     | Registros de contêiner pessoal e de terceiros (opcional) | 
| https://\*.azure-devices.net              | Acesso do Hub IoT (obrigatório)                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>Padrões de URL para gateway para o Azure governamental

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/azure-stack-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>Padrões de URL para computação para o Azure governamental

| Padrão de URL                      | Componente ou funcionalidade                     |  
|----------------------------------|---------------------------------------------|
| https: \/ /MCR.Microsoft.com<br></br>https:// \* . cdn.mscr.com | Registro de Contêiner da Microsoft (obrigatório)               |
| https:// \* . Azure-Devices.us              | Acesso do Hub IoT (obrigatório)           |
| https:// \* . azurecr.us                    | Registros de contêiner pessoal e de terceiros (opcional) | 

## <a name="internet-bandwidth"></a>Largura de banda de Internet

[!INCLUDE [Internet bandwidth](../../includes/azure-stack-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>Considerações sobre o dimensionamento de computação

Use sua experiência ao desenvolver e testar sua solução para garantir que haja capacidade suficiente em seu dispositivo Azure Stack Edge pro R e você obtenha o desempenho ideal do seu dispositivo.

Entre os fatores que você deve considerar estão:

- **Especificações do contêiner** -pense no seguinte.

    - Quantos contêineres estão em sua carga de trabalho? Você pode ter muitos contêineres leves versus poucos recursos.
    - Quais são os recursos alocados para esses contêineres versus quais são os recursos que estão consumindo?
    - Quantas camadas seus contêineres compartilham?
    - Há contêineres não utilizados? Um contêiner interrompido ainda ocupa espaço em disco.
    - Em que idioma os contêineres são gravados?
- **Tamanho dos dados processados** -a quantidade de dados que seus contêineres serão processadas? Esses dados consumirão espaço em disco ou os dados serão processados na memória?
- **Desempenho esperado** -quais são as características de desempenho desejadas da sua solução? 

Para entender e refinar o desempenho de sua solução, você pode usar:

- As métricas de computação disponíveis no portal do Azure. Vá para o recurso Azure Stack Edge pro R e vá para **monitoramento > métricas**. Examine a **computação de borda – uso de memória** e **porcentagem de CPU de computação** para entender os recursos disponíveis e como os recursos estão sendo consumidos.
- Os [comandos de monitoramento disponíveis por meio da interface do PowerShell do dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge).

Por fim, certifique-se de validar sua solução em seu conjunto de e quantificar o desempenho no Azure Stack Edge pro R antes de implantar em produção.

## <a name="next-step"></a>Próxima etapa

- [Implantar seu Azure Stack Edge pro R](azure-stack-edge-pro-r-deploy-prep.md)