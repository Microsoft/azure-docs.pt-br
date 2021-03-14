---
title: Pré-requisitos para a implantação de serviços de nuvem do Azure (suporte estendido)
description: Pré-requisitos para a implantação de serviços de nuvem do Azure (suporte estendido)
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 6e5994f05187cd25996bcc007d27a7e10eb76427
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103232521"
---
# <a name="prerequisites-for-deploying-azure-cloud-services-extended-support"></a>Pré-requisitos para a implantação de serviços de nuvem do Azure (suporte estendido)

> [!IMPORTANT]
> No momento, os Serviços de Nuvem (suporte estendido) estão em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Para garantir uma implantação bem-sucedida dos serviços de nuvem (suporte estendido), examine as etapas abaixo e conclua cada item antes de tentar qualquer implantação. 

## <a name="register-the-cloudservices-feature"></a>Registrar o recurso Cloudservices
Registre o recurso para sua assinatura. O registro pode levar vários minutos para ser concluído. 

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```

Verifique o status do registro usando o seguinte:  
```powershell
Get-AzProviderFeature 

#Sample output
FeatureName               ProviderName      RegistrationState
CloudServices           Microsoft.Compute    Registered
```

## <a name="required-service-configuration-cscfg-file-updates"></a>Atualizações de arquivo de configuração de serviço (. cscfg) necessárias

### <a name="1-virtual-network"></a>1) rede virtual
As implantações do Serviço de Nuvem (suporte estendido) precisam estar em uma rede virtual. A rede virtual pode ser criada por meio de [portal do Azure](../virtual-network/quick-create-portal.md), [PowerShell](../virtual-network/quick-create-powershell.md), [CLI do Azure](../virtual-network/quick-create-cli.md) ou [modelo ARM](../virtual-network/quick-create-template.md). A rede virtual e as sub-redes também devem ser referenciadas na configuração do serviço (. cscfg) na seção [NetworkConfiguration](schema-cscfg-networkconfiguration.md) . 

Para redes virtuais pertencentes ao mesmo grupo de recursos que o serviço de nuvem, a referência apenas ao nome da rede virtual no arquivo de configuração do serviço (. cscfg) é suficiente. Se a rede virtual e o serviço de nuvem estiverem em dois grupos de recursos diferentes, a ID de Azure Resource Manager completa da rede virtual precisará ser especificada no arquivo de configuração de serviço (. cscfg).
 
#### <a name="virtual-network-located-in-same-resource-group"></a>Rede virtual localizada no mesmo grupo de recursos
```xml
<VirtualNetworkSite name="<vnet-name>"/> 
  <AddressAssignments> 
    <InstanceAddress roleName="<role-name>"> 
     <Subnets> 
       <Subnet name="<subnet-name>"/> 
     </Subnets> 
    </InstanceAddress> 
```

#### <a name="virtual-network-located-in-different-resource-group"></a>Rede virtual localizada em um grupo de recursos diferente
```xml
<VirtualNetworkSite name="/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.Network/virtualNetworks/<vnet-name>"/> 
   <AddressAssignments> 
     <InstanceAddress roleName="<role-name>"> 
       <Subnets> 
        <Subnet name="<subnet-name>"/> 
       </Subnets> 
     </InstanceAddress> 
```
### <a name="2-remove-the-old-plugins"></a>2) remover os plug-ins antigos

Remova as configurações antigas da área de trabalho remota do arquivo de configuração de serviço (. cscfg).  

```xml
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="gachandw" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="XXXX" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="2021-12-17T23:59:59.0000000+05:30" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" /> 
```

## <a name="required-service-definition-file-csdef-updates"></a>Atualizações necessárias do arquivo de definição de serviço (. csdef)

### <a name="1-virtual-machine-sizes"></a>1) tamanhos de máquina virtual
Os tamanhos a seguir são preteridos no Azure Resource Manager. No entanto, se você quiser continuar a usá-los, atualize o `vmsize` nome com a Convenção de nomenclatura de Azure Resource Manager associada.  

| Nome do tamanho anterior | Nome do tamanho atualizado | 
|---|---|
| ExtraSmall | Standard_A0 | 
| Small | Standard_A1 |
| Médio | Standard_A2 | 
| Grande | Standard_A3 | 
| ExtraLarge | Standard_A4 | 
| A5 | Standard_A5 | 
| A6 | Standard_A6 | 
| A7 | Standard_A7 |  
| A8 | Standard_A8 | 
| A9 | Standard_A9 |
| A10 | Standard_A10 | 
| A11 | Standard_A11 | 
| MSODSG5 | Standard_MSODSG5 | 

 Por exemplo, `<WorkerRole name="WorkerRole1" vmsize="Medium"` se tornaria `<WorkerRole name="WorkerRole1" vmsize="Standard_A2"`.
 
> [!NOTE]
> Para recuperar uma lista de tamanhos disponíveis [, consulte SKUs de recursos-List](/rest/api/compute/resourceskus/list) e aplique os seguintes filtros: <br>
`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


### <a name="2-remove-old-remote-desktop-plugins"></a>2) remover plugins antigos da área de trabalho remota
As implantações que utilizaram os plugins de área de trabalho remota antigos precisam ter os módulos removidos do arquivo de definição de serviço (. csdef) e de quaisquer certificados associados. 

```xml
<Imports> 
<Import moduleName="RemoteAccess" /> 
<Import moduleName="RemoteForwarder" /> 
</Imports> 
```

## <a name="key-vault-creation"></a>Criação de Key Vault 

O Key Vault é usado para armazenar certificados associados aos Serviços de Nuvem (suporte estendido). Adicione os certificados a Key Vault e, em seguida, referencie as impressões digitais do certificado no arquivo de configuração de serviço. Você também precisa habilitar o Key Vault para as permissões adequadas para que o recurso dos Serviços de Nuvem (suporte estendido) possa recuperar o certificado armazenado como segredos do Key Vault. Você pode criar um cofre de chaves no [portal do Azure](../key-vault/general/quick-create-portal.md) ou usando o [PowerShell](../key-vault/general/quick-create-powershell.md). O cofre de chaves deve ser criado na mesma região e assinatura que o serviço de nuvem. Para obter mais informações, confira [Usar certificados com os Serviços de Nuvem do Azure (suporte estendido)](certificates-and-key-vault.md).

## <a name="next-steps"></a>Próximas etapas 
- Examine os [pré-requisitos de implantação](deploy-prerequisite.md) para serviços de nuvem (suporte estendido).
- Implante um Serviço de Nuvem (suporte estendido) usando o [portal do Azure](deploy-portal.md), o [PowerShell](deploy-powershell.md), o [Modelo](deploy-template.md) ou o [Visual Studio](deploy-visual-studio.md).
- Examine as [perguntas frequentes](faq.md) sobre os Serviços de Nuvem (suporte estendido).
- Visite o [repositório de exemplos dos Serviços de Nuvem (suporte estendido)](https://github.com/Azure-Samples/cloud-services-extended-support)
