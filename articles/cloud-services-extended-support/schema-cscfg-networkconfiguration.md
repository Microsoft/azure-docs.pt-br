---
title: Esquema NetworkConfiguration de serviços de nuvem do Azure (suporte estendido) | Microsoft Docs
description: Informações relacionadas ao esquema de configuração de rede para serviços de nuvem (suporte estendido)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2650da2579f13ec1588af7a25e5b28908209bc82
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700177"
---
# <a name="azure-cloud-services-extended-support-config-networkconfiguration-schema"></a>Esquema networkConfiguration de configuração de serviços de nuvem do Azure (suporte estendido)

O elemento `NetworkConfiguration` do arquivo de configuração de serviço especifica os valores DNS e Rede Virtual. Essas configurações são opcionais para serviços de nuvem.

É possível usar o recurso a seguir para saber mais sobre redes virtuais e os esquemas associados:

- [Esquema de configuração do serviço de nuvem (suporte estendido)](schema-cscfg-file.md).
- [Esquema de definição do serviço de nuvem (suporte estendido)](schema-csdef-file.md).
- [Criar uma rede virtual](../virtual-network/manage-virtual-network.md).

## <a name="networkconfiguration-element"></a>Elemento NetworkConfiguration
O exemplo a seguir mostra o elemento `NetworkConfiguration` e seus elementos filho.

```xml
<ServiceConfiguration>
  <NetworkConfiguration>
    <AccessControls>
      <AccessControl name="aclName1">
        <Rule order="<rule-order>" action="<rule-action>" remoteSubnet="<subnet-address>" description="rule-description"/>
      </AccessControl>
    </AccessControls>
    <EndpointAcls>
      <EndpointAcl role="<role-name>" endpoint="<endpoint-name>" accessControl="<acl-name>"/>
    </EndpointAcls>
    <Dns>
      <DnsServers>
        <DnsServer name="<server-name>" IPAddress="<server-address>" />
      </DnsServers>
    </Dns>
    <VirtualNetworkSite name="Group <RG-VNet> <VNet-name>"/>
    <AddressAssignments>
      <InstanceAddress roleName="<role-name>">
        <Subnets>
          <Subnet name="<subnet-name>"/>
        </Subnets>
      </InstanceAddress>
      <ReservedIPs>
        <ReservedIP name="<reserved-ip-name>"/>
      </ReservedIPs>
    </AddressAssignments>
  </NetworkConfiguration>
</ServiceConfiguration>
```

A tabela a seguir descreve os elementos filho do elemento `NetworkConfiguration`.

| Elemento       | Descrição |
| ------------- | ----------- |
| AccessControl | Opcional. Especifica as regras de acesso a pontos de extremidade em um serviço de nuvem. O nome do controle de acesso é definido por uma cadeia de caracteres para o atributo `name`. O elemento `AccessControl` contém um ou mais elementos `Rule`. Mais de um elemento `AccessControl` pode ser definido.|
| Regra | Opcional. Especifica a ação que deve ser executada para um intervalo de sub-rede especificado de endereços IP. A ordem da regra é definida por um valor de cadeia de caracteres para o atributo `order`. Quanto menor o número da regra, maior a prioridade. Por exemplo, as regras podem ser especificadas com números de ordem de 100, 200 e 300. A regra com o número de ordem de 100 tem precedência sobre a regra que tem uma ordem de 200.<br /><br /> A ação da regra é definida por uma cadeia de caracteres para o atributo `action`. Os valores possíveis são:<br /><br /> -   `permit` – Especifica que apenas os pacotes do intervalo de sub-rede especificado podem se comunicar com o ponto de extremidade.<br />-   `deny` – Especifica que o acesso é negado aos pontos de extremidade no intervalo de sub-rede especificado.<br /><br /> O intervalo de sub-rede de endereços IP afetados pela regra são definidos por uma cadeia de caracteres para o atributo `remoteSubnet`. A descrição da regra é definida por uma cadeia de caracteres para o atributo `description`.|
| EndpointAcl | Opcional. Especifica a atribuição de regras de controle de acesso a um ponto de extremidade. O nome da função que contém o ponto de extremidade é definido por uma cadeia de caracteres para o atributo `role`. O nome do ponto de extremidade é definido por uma cadeia de caracteres para o atributo `endpoint`. O nome do conjunto de regras `AccessControl` que devem ser aplicadas ao ponto de extremidade é definido em uma cadeia de caracteres para o atributo `accessControl`. Mais de um elemento `EndpointAcl` podem ser definidos.|
| DnsServer | Opcional. Especifica as configurações para um servidor DNS. É possível especificar configurações para servidores DNS sem uma Rede Virtual. O nome do servidor DNS é definido por uma cadeia de caracteres para o atributo `name`. O endereço IP do servidor DNS é definido por uma cadeia de caracteres para o atributo `IPAddress`. O endereço IP deve ser um endereço IPv4 válido.|
| VirtualNetworkSite | Opcional. Especifica o nome do site de rede virtual no qual você deseja implantar seu serviço de nuvem. Essa configuração não cria um site de rede virtual. Ele referencia um site que foi anteriormente definido no arquivo de rede para sua Rede Virtual. Um serviço de nuvem só pode ser um membro de uma rede virtual. Se você não especificar essa configuração, o serviço de nuvem não será implantado em uma rede virtual. O nome do site de rede virtual é definido por uma cadeia de caracteres para o atributo `name`.|
| InstanceAddress | Opcional. Especifica a associação de uma função para uma sub-rede ou conjunto de sub-redes na Rede Virtual. Quando você associa um nome de função a um endereço de instância, é possível especificar as sub-redes às quais você deseja que essa função seja associada. O `InstanceAddress` contém um elemento de Subnets. O nome da função associada à sub-rede ou sub-redes é definido por uma cadeia de caracteres para o atributo `roleName`.|
| Sub-rede | Opcional. Especifica a sub-rede correspondente ao nome da sub-rede no arquivo de configuração de rede. O nome da sub-rede é definido por uma cadeia de caracteres para o atributo `name`.|
| ReservedIP | Opcional. Especifica o endereço IP reservado que deve ser associado à implantação. É necessário usar Criar endereço IP reservado para criar o endereço IP reservado. Cada implantação em um serviço de nuvem pode ser associada a um endereço IP reservado. O nome do endereço IP reservado é definido por uma cadeia de caracteres para o atributo `name`.|

## <a name="see-also"></a>Confira também
[Esquema de configuração do serviço de nuvem (suporte estendido)](schema-cscfg-file.md).