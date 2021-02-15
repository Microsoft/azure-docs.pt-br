---
title: Esquema de função dos serviços de nuvem do Azure (suporte estendido) | Microsoft Docs
description: Informações relacionadas ao esquema de função para serviços de nuvem (suporte estendido)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2567f5bb817a34f6274d5e265a266d67a9c81413
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744156"
---
# <a name="azure-cloud-services-extended-support-config-role-schema"></a>Esquema de função de configuração dos serviços de nuvem do Azure (suporte estendido)

O elemento `Role` do arquivo de configuração especifica o número de instâncias de função a ser implantado para cada função no serviço, os valores das configurações e as impressões digitais de todos os certificados associados a uma função.

Para obter mais informações sobre o esquema de configuração do serviço do Azure, consulte [esquema de configuração do serviço de nuvem (suporte estendido)](schema-cscfg-file.md). Para obter mais informações sobre o esquema de definição de serviço do Azure, consulte [esquema de definição do serviço de nuvem (suporte estendido)](schema-csdef-file.md).

##  <a name="role-element"></a><a name="Role"></a> elemento Role
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

## <a name="see-also"></a>Confira também
[Esquema de configuração do serviço de nuvem (suporte estendido)](schema-cscfg-file.md).