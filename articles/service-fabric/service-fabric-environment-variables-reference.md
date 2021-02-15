---
title: Variáveis de ambiente de Service Fabric do Azure
description: Saiba mais sobre variáveis de ambiente no Azure Service Fabric. Contém uma referência de uma lista completa de variáveis e seus usos.
ms.topic: reference
ms.date: 12/07/2017
ms.openlocfilehash: b70249daa439b5a631b5a84b10c47f082ce75985
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574574"
---
# <a name="service-fabric-environment-variables"></a>Variáveis de ambiente do Service Fabric

O Service Fabric tem variáveis de ambiente internas definidas para cada instância de serviço. A lista completa de variáveis de ambiente está abaixo:

| Variável de ambiente                         | Descrição                                                            | Exemplo                                                              |
|----------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------------|
| Fabric_ApplicationName                       | O nome do uri da malha do aplicativo                                 | fabric:/MyApplication                                                |
| Fabric_CodePackageName                       | O nome do pacote de código ao qual pertence o processo              | Código                                                                 |
| Fabric_Endpoint\_IPOrFQDN\_*ServiceEndpointName*     | O endereço ip ou FQDN do ponto de extremidade                                 | 10.0.0.1                                                     |
| Fabric\_Endpoint\_*ServiceEndpointName*              | Número da porta do ponto de extremidade                                  | 8234                                                                 |
| Fabric_Folder_App_Log                        | Pasta de logs                                                             | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\log      |
| Fabric_Folder_App_Temp                       | Pasta Temp                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\temp     |
| Fabric_Folder_App_Work                       | Pasta de trabalho                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\work     |
| Fabric_Folder_Application                    | A pasta base de aplicativos                                           | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12             |
| Fabric_IsContainerHost                       | Um booliano que especifica se o processo é um contêiner                   | false                                                                |
| Fabric_NodeId                                | A ID do nó que executa o processo                            | bf865279ba277deb864a976fbf4c200e                                     |
| Fabric_NodeIPOrFQDN                          | O IP ou FQDN do nó, conforme for especificado no arquivo de manifesto do cluster. | localhost ou 10.0.0.1                                                |
| Fabric_NodeName                              | O nome do nó que executa o processo                          | _Node_0                                                              |
| Fabric_ServiceName                           | O nome do uri da malha do serviço, se o serviço estiver hospedado no modo ExclusiveProcess. Esse valor de variável só estará disponível se você criar o serviço com ServicePackageActivationMode ExclusiveProcess.  | fabric:/MyApplication/MyService                                               |
| Fabric_ServicePackageActivationId            | ServicePackageActivationId                                         | UMA GUID                                                               |
| Fabric_ServicePackageName                    | Nome do pacote de serviço do qual o processo faz parte                     | Web1Pkg                                                              |

Variáveis de ambiente interno usadas pelo runtime do Service Fabric:

- Fabric_ApplicationHostId
- Fabric_ApplicationHostType
- Fabric_ApplicationId
- Fabric_CodePackageInstanceId
- Fabric_CodePackageInstanceSeqNum
- Fabric_RuntimeConnectionAddress
- Fabric_ServicePackageActivationGuid
- Fabric_ServicePackageInstanceId
- Fabric_ServicePackageInstanceSeqNum
- Fabric_ServicePackageVersionInstance
- FabricActivatorAddress
- FabricPackageFileName
- HostedServiceName
