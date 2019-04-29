---
title: Requisitos de sistema do Microsoft Azure Data Box Gateway | Microsoft Docs
description: Saiba mais sobre o software e os requisitos de rede do Azure Data Box Gateway
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 04/02/2019
ms.author: alkohli
ms.openlocfilehash: cac451634bfa357784f9fd3d3a24e06ef3a4ee19
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60754549"
---
# <a name="azure-data-box-gateway-system-requirements"></a>Requisitos de sistema de Gateway da caixa de dados do Azure

Este artigo descreve os requisitos de sistema importantes para a solução Gateway do Microsoft Azure Data Box e para os clientes que se conectam ao Azure Data Box Gateway. Recomendamos que você examine as informações com atenção antes de implantar o Data Box Gateway e consulte-as, quando necessário, durante a implantação e a subsequente operação.

Os requisitos de sistema para o dispositivo virtual Data Box Gateway incluem:

- **Requisitos de software para hosts** – descreve as plataformas com suporte, os navegadores da interface do usuário de configuração local, os clientes SMB e requisitos adicionais para os hosts que se conectam ao dispositivo.
- **Requisitos de rede para o dispositivo** – fornece informações sobre os requisitos de rede da operação do dispositivo virtual.


## <a name="specifications-for-the-virtual-device"></a>Especificações para o dispositivo virtual

O sistema host subjacente para o Data Box Gateway é capaz de dedicar os seguintes recursos para provisionar seu dispositivo virtual:

| Especificações                                          | DESCRIÇÃO              |
|---------------------------------------------------------|--------------------------|
| Processadores virtuais (núcleos)   | Mínimo de 4 |
| Memória  | Mínimo de 8 GB|
| Disponibilidade|Nó único|
| Discos| Disco do sistema operacional: 250 GB <br> Disco de dados: Mínimo de 2 TB, com provisionamento dinâmico e deve ser ativado por SSDs|
| Interfaces de rede|1 ou mais adaptadores de rede virtual|


## <a name="supported-os-for-clients-connected-to-device"></a>Sistema operacional com suporte para clientes conectados ao dispositivo

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Protocolos com suporte para clientes que acessam o dispositivo

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-virtualization-platforms-for-device"></a>Plataformas de virtualização com suporte para o dispositivo

| **Sistema operacional/plataforma**  |**Versões**   |**Observações**  |
|---------|---------|---------|
|Hyper-V  |  2012 R2 <br> 2016  |         |
|VMware ESXi     | 6,0 <br> 6.5 <br> 6.7       |Não há suporte para as ferramentas do VMware.         |


## <a name="supported-storage-accounts"></a>Contas de armazenamento com suporte

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]


## <a name="supported-storage-types"></a>Tipos de armazenamento com suporte

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Navegadores com suporte para a interface do usuário da Web local

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Requisitos de porta de rede

A tabela a seguir lista as portas que devem ser abertas no firewall para permitir o tráfego SMB, de nuvem ou de gerenciamento. Nesta tabela, *entrada* ou *de entrada* refere-se à direção da qual as solicitações do cliente acessam o dispositivo. *Saída* ou *de saída* refere-se à direção para a qual seu dispositivo Data Box Gateway envia dados externamente, além da implantação: por exemplo, saída para a Internet.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

## <a name="url-patterns-for-firewall-rules"></a>Padrões de URL para regras de firewall

Os administradores de rede geralmente podem configurar regras avançadas de firewall com base nos padrões de URL para filtrar o tráfego de entrada e de saída. Seu dispositivo Data Box Gateway e o serviço Data Box Gateway dependem de outros aplicativos da Microsoft, como o Barramento de Serviço do Azure, o Controle de Acesso do Azure Active Directory, contas de armazenamento e servidores do Microsoft Update. Os padrões de URL associados a esses aplicativos podem ser usados para configurar regras de firewall. É importante entender que os padrões de URL associados a esses aplicativos podem ser alterados. Isso, por sua vez, exigirá que o administrador de rede monitore e atualize as regras de firewall do Data Box Gateway conforme e quando necessário.

É recomendado que você defina suas regras de firewall para tráfego de saída, com base nos endereços IP fixos do Data Box Gateway e, na maioria dos casos, de modo flexível. No entanto, você pode usar as informações a seguir para definir regras avançadas de firewall que são necessárias para criar ambientes seguros.

> [!NOTE]
> - Os IPs do dispositivo (de origem) sempre devem estar configurados para todas as interfaces de rede habilitadas para a nuvem.
> - Os IPs de destino devem ser configurados como [Intervalos de IP do datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-azure-government"></a>Padrões de URL para o Azure governamental

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

## <a name="internet-bandwidth"></a>Largura de banda de Internet

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>Próxima etapa

* [Implantar o Azure Data Box Gateway](data-box-gateway-deploy-prep.md)

