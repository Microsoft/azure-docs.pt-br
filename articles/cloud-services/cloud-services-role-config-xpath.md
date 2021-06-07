---
title: Folha de consulta XPath de configuração de função dos serviços de nuvem (clássico) | Microsoft Docs
description: As várias configurações do XPath que podem ser usadas na configuração da função do serviço de nuvem para expor as configurações como uma variável de ambiente.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 397bc6845dc8d2d8bc44c00c27f6c12037651337
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98741376"
---
# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>Expor as definições de configuração da função como uma variável de ambiente com o XPath

> [!IMPORTANT]
> Os [serviços de nuvem do Azure (suporte estendido)](../cloud-services-extended-support/overview.md) são um novo modelo de implantação baseado em Azure Resource Manager para o produto de serviços de nuvem do Azure.Com essa alteração, os serviços de nuvem do Azure em execução no modelo de implantação baseado no Azure Service Manager foram renomeados como serviços de nuvem (clássicos) e todas as novas implantações devem usar os [serviços de nuvem (suporte estendido)](../cloud-services-extended-support/overview.md).

No arquivo de definição de serviço de função web ou do trabalho do serviço de nuvem, é possível expor os valores de configuração do runtime como variáveis de ambiente. Há suporte para os valores do XPath a seguir (que correspondem aos valores da API).

Esses valores do XPath também estão disponíveis por meio da biblioteca [Microsoft.WindowsAzure.ServiceRuntime](/previous-versions/azure/reference/ee773173(v=azure.100)) . 

## <a name="app-running-in-emulator"></a>Aplicativo em execução no emulador
Indica que o aplicativo está em execução no emulador.

| Type | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/Deployment/@emulated" |
| Código |var x = RoleEnvironment.IsEmulated; |

## <a name="deployment-id"></a>ID de Implantação
Recupera a ID de implantação da instância.

| Type | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/Deployment/@id" |
| Código |var deploymentId = RoleEnvironment.DeploymentId; |

## <a name="role-id"></a>ID de Função
Recupera a ID de função atual da instância.

| Type | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@id" |
| Código |var id = RoleEnvironment.CurrentRoleInstance.Id; |

## <a name="update-domain"></a>Domínio de atualização
Recupera o domínio de atualização da instância.

| Type | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@updateDomain" |
| Código |var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |

## <a name="fault-domain"></a>Domínios de falha
Recupera o domínio de falha da instância.

| Type | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@faultDomain" |
| Código |var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |

## <a name="role-name"></a>Nome da função
Recupera o nome da função das instâncias.

| Type | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@roleName" |
| Código |var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |

## <a name="config-setting"></a>Definição de configuração
Recupera o valor da definição de configuração especificada.

| Type | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting[@name='Setting1']/@value" |
| Código |var setting = RoleEnvironment.GetConfigurationSettingValue("Setting1"); |

## <a name="local-storage-path"></a>Caminho do armazenamento local
Recupera o caminho do armazenamento local da instância.

| Type | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@path" |
| Código |var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1").RootPath; |

## <a name="local-storage-size"></a>Tamanho do armazenamento local
Recupera o tamanho do armazenamento local da instância.

| Type | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@sizeInMB" |
| Código |var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1").MaximumSizeInMegabytes; |

## <a name="endpoint-protocol"></a>Protocolo do ponto de extremidade
Recupera o protocolo do ponto de extremidade da instância.

| Type | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@protocol" |
| Código |var prot = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].Protocol; |

## <a name="endpoint-ip"></a>IP do ponto de extremidade
Obtém o endereço IP do ponto de extremidade especificado.

| Type | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@address" |
| Código |var address = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].IPEndpoint.Address |

## <a name="endpoint-port"></a>Porta do ponto de extremidade
Recupera a porta do ponto de extremidade da instância.

| Type | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@port" |
| Código |var port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].IPEndpoint.Port; |

## <a name="example"></a>Exemplo
Aqui está um exemplo de uma função de trabalho que cria uma tarefa de inicialização com uma variável de ambiente nomeada `TestIsEmulated` definida como o [ @emulated valor XPath](#app-running-in-emulator). 

```xml
<WorkerRole name="Role1">
    <ConfigurationSettings>
      <Setting name="Setting1" />
    </ConfigurationSettings>
    <LocalResources>
      <LocalStorage name="LocalStore1" sizeInMB="1024"/>
    </LocalResources>
    <Endpoints>
      <InternalEndpoint name="Endpoint1" protocol="tcp" />
    </Endpoints>
    <Startup>
      <Task commandLine="example.cmd inputParm">
        <Environment>
          <Variable name="TestConstant" value="Constant"/>
          <Variable name="TestEmptyValue" value=""/>
          <Variable name="TestIsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
          </Variable>
          ...
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="TestConstant" value="Constant"/>
        <Variable name="TestEmptyValue" value=""/>
        <Variable name="TestIsEmulated">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
        </Variable>
        ...
      </Environment>
    </Runtime>
    ...
</WorkerRole>
```

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre o arquivo [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) .

Crie um pacote [ServicePackage.cspkg](cloud-services-model-and-package.md#servicepackagecspkg) .

Habilite a [área de trabalho remota](cloud-services-role-enable-remote-desktop-new-portal.md) para uma função.




