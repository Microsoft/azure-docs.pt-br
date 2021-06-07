---
title: Esquema de função dos serviços de nuvem do Azure (clássico) | Microsoft Docs
description: O elemento Role de um arquivo de configuração de serviço especifica quantas instâncias de função implantar para cada função, valores de configuração e impressões digitais de certificado.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2dc8e14a4e4d8855abb615632bb7d43b9034d360
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743433"
---
# <a name="azure-cloud-services-classic-config-role-schema"></a>Esquema da função de configuração dos serviços de nuvem do Azure (clássico)

> [!IMPORTANT]
> Os [serviços de nuvem do Azure (suporte estendido)](../cloud-services-extended-support/overview.md) são um novo modelo de implantação baseado em Azure Resource Manager para o produto de serviços de nuvem do Azure.Com essa alteração, os serviços de nuvem do Azure em execução no modelo de implantação baseado no Azure Service Manager foram renomeados como serviços de nuvem (clássicos) e todas as novas implantações devem usar os [serviços de nuvem (suporte estendido)](../cloud-services-extended-support/overview.md).

O elemento `Role` do arquivo de configuração especifica o número de instâncias de função a ser implantado para cada função no serviço, os valores das configurações e as impressões digitais de todos os certificados associados a uma função.

Para obter mais informações sobre o Esquema de configuração do Serviço do Azure, consulte [Cloud Service (classic) Configuration Schema](schema-cscfg-file.md) (Esquema de configuração do Serviço de Nuvem (clássico)). Para obter mais informações sobre o Esquema de definição do Serviço do Azure, consulte [Cloud Service (classic) Definition Schema](schema-csdef-file.md) (Esquema de definição do Serviço de Nuvem (clássico)).

##  <a name="role-element"></a>Elemento de função <a name="Role"></a>
O exemplo a seguir mostra o elemento `Role` e seus elementos filho.

```xml 
<ServiceConfiguration>
  <Role name="<role-name>" vmName="<vm-name>">
    <Instances count="<number-of-instances>"/>
    <ConfigurationSettings>
      <Setting name="<setting-name>" value="<setting-value>" />
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="<certificate-name>" thumbprint="<certificate-thumbprint>" thumbprintAlgorithm="<algorithm>"/>
    </Certificates>
  </Role>
</ServiceConfiguration>
```

A tabela a seguir descreve os atributos do elemento `Role`.

| Atributo | Descrição |
| --------- | ----------- |
| name   | Obrigatórios. Especifica o nome da função. O nome deve corresponder ao nome fornecido para a função no arquivo de definição de serviço.|
| vmName | Opcional. Especifica um nome DNS para uma máquina virtual. O nome deve ter dez caracteres ou menos.|

A tabela a seguir descreve os elementos filho do elemento `Role`.

| Elemento | Descrição |
| ------- | ----------- |
| Instâncias | Obrigatórios. Especifica o número de instâncias a serem implantadas para a função. O número de instâncias é definido por um inteiro para o atributo `count`.|
| Configuração   | Opcional. Especifica um nome e valor da configuração em uma coleção de configurações para uma função. O nome da configuração é definido por uma cadeia de caracteres para o atributo `name` e o valor da configuração é definido por uma cadeia de caracteres para o atributo `value`.|
| Certificado | Opcional. Especifica o nome, a impressão digital e o algoritmo de um certificado de serviço a ser associado à função. O nome do certificado é definido por uma cadeia de caracteres para o atributo `name`. A impressão digital do certificado é definida por uma cadeia de caracteres de números hexadecimais sem espaços para o atributo `thumbprint`. Os números hexadecimais devem ser representados usando dígitos e caracteres alfa maiúsculos. O algoritmo do certificado é definido por uma cadeia de caracteres para o atributo `thumbprintAlgorithm`.|

## <a name="see-also"></a>Consulte Também
[Esquema de configuração do Serviço de Nuvem (clássico)](schema-cscfg-file.md)