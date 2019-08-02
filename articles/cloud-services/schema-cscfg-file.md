---
title: Esquema de definição (arquivo .cscfg) dos Serviços de Nuvem do Azure | Microsoft Docs
services: cloud-services
ms.custom: ''
ms.date: 12/07/2016
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 35
author: georgewallace
ms.author: gwallace
ms.openlocfilehash: 0009f843f8de31b92817dc86ccd718fa5eeeb1ba
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358916"
---
# <a name="azure-cloud-services-config-schema-cscfg-file"></a>Esquema de configuração dos Serviços de Nuvem do Azure (arquivo .cscfg)
O arquivo de configuração de serviço especifica o número de instâncias de função a ser implantado para cada função no serviço, os valores das configurações e as impressões digitais de todos os certificados associados a uma função. Se o serviço fizer parte de uma Rede Virtual, as informações de configuração da rede deverão ser fornecidas no arquivo de configuração de serviço, bem como no arquivo de configuração de rede virtual. A extensão padrão do arquivo de configuração de serviço é .cscfg.

O modelo de serviço é descrito pelo [Esquema de definição do Serviço de Nuvem (clássico)](schema-csdef-file.md).

Por padrão, o arquivo de esquema de configuração do Diagnóstico do Azure é instalado no diretório `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas`. Substitua `<version>` pela versão instalada do [SDK do Azure](https://azure.microsoft.com/downloads/).

Para obter mais informações sobre como configurar funções em um serviço, consulte [What is the Cloud Service model](cloud-services-model-and-package.md) (O que é o modelo do Serviço de Nuvem).

## <a name="basic-service-configuration-schema"></a>Esquema de configuração de serviço básico
O formato básico do arquivo de configuração de serviço é o seguinte.

```xml
<ServiceConfiguration serviceName="<service-name>" osFamily="<osfamily-number>" osVersion="<os-version>" schemaVersion="<schema-version>">

  <Role …>
    …
  </Role>

  <NetworkConfiguration>
    …
  </NetworkConfiguration>

</ServiceConfiguration>
```

## <a name="schema-definitions"></a>Definições do esquema
Os tópicos a seguir descrevem o esquema para o elemento `ServiceConfiguration`:

- [Esquema de Função](schema-cscfg-role.md)
- [Esquema NetworkConfiguration](schema-cscfg-networkconfiguration.md)

## <a name="service-configuration-namespace"></a>Namespace de configuração de serviço
O namespace de XML do arquivo de configuração de serviço é: `http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration`.

##  <a name="ServiceConfiguration"></a> Elemento ServiceConfiguration
O elemento `ServiceConfiguration` é o elemento de nível superior do arquivo de configuração de serviço.

A tabela a seguir descreve os atributos do elemento `ServiceConfiguration`. Todos os valores de atributos são tipos de cadeia de caracteres.

| Atributo | Descrição |
| --------- | ----------- |
|serviceName|Obrigatória. O nome do serviço de nuvem. O nome fornecido aqui deve corresponder ao nome especificado no arquivo de definição de serviço.|
|osFamily|Opcional. Especifica o SO convidado que será executado em instâncias de função no serviço de nuvem. Para obter informações sobre versões do SO convidado com suporte, consulte [Azure Guest OS Releases and SDK Compatibility Matrix](cloud-services-guestos-update-matrix.md) (Matriz de compatibilidade do SDK e lançamentos do SO convidado do Azure).<br /><br /> Se você não incluir um valor `osFamily` e não tiver definido o atributo `osVersion` como uma versão específica do SO convidado, será usado um valor padrão de 1.|
|osVersion|Opcional. Especifica a versão do SO convidado que será executado em instâncias de função no serviço de nuvem. Para obter mais informações sobre as versões do SO convidado, consulte [Azure Guest OS Releases and SDK Compatibility Matrix](cloud-services-guestos-update-matrix.md) (Matriz de compatibilidade do SDK e lançamentos do SO convidado do Azure).<br /><br /> É possível especificar que o SO convidado deve ser atualizado automaticamente para a versão mais recente. Para fazer isso, defina o valor do atributo `osVersion` como `*`. Quando definido como `*`, as instâncias de função são implantadas usando a versão mais recente do SO convidado para a família do sistema operacional especificado e serão automaticamente atualizadas quando as novas versões do SO convidado forem lançadas.<br /><br /> Para especificar uma versão específica manualmente, use o `Configuration String` da tabela na seção **Versões do SO convidado futuras, atuais e de transição** da [Azure Guest OS Releases and SDK Compatibility Matrix](cloud-services-guestos-update-matrix.md) (Matriz de compatibilidade do SDK e lançamentos do SO convidado do Azure).<br /><br /> O valor padrão do atributo `osVersion` é `*`.|
|schemaVersion|Opcional. Especifica a versão do esquema de configuração de serviço. A versão do esquema permitirá que o Visual Studio selecione as ferramentas do SDK corretas para usar para a validação de esquema se mais de uma versão do SDK for instalada lado a lado. Para obter mais informações sobre a compatibilidade do esquema e da versão, consulte [Azure Guest OS Releases and SDK Compatibility Matrix](cloud-services-guestos-update-matrix.md) (Matriz de compatibilidade do SDK e lançamentos do SO convidado do Azure)|

O arquivo de configuração de serviço deve conter um elemento `ServiceConfiguration`. O elemento `ServiceConfiguration` pode incluir qualquer número de elementos `Role` e zero ou 1 elementos `NetworkConfiguration`.