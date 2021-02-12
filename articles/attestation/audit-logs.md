---
title: Logs de auditoria do atestado do Azure
description: Descreve os logs de auditoria completos que são coletados para o atestado do Azure
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 11/23/2020
ms.author: mbaldwin
ms.openlocfilehash: 1fa4a458a4e3e1df1d84c343a32e3a41a4a25e75
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95758959"
---
# <a name="audit-logs-for-azure-attestation"></a>Logs de auditoria para o atestado do Azure

Os logs de auditoria são registros seguros, imutáveis e com carimbo de data/hora de eventos discretos que ocorreram ao longo do tempo. Esses logs capturam eventos importantes que podem afetar a funcionalidade de sua instância de atestado.

O atestado do Azure gerencia instâncias de atestado e as políticas associadas a elas. As ações associadas ao gerenciamento de instâncias e às alterações de política são auditadas e registradas em log.

Este artigo contém informações sobre os eventos que são registrados, as informações coletadas e o local desses logs.

## <a name="about-audit-logs"></a>Sobre logs de auditoria

O atestado do Azure usa código para produzir logs de auditoria para eventos que afetam o modo como o atestado é executado. Isso normalmente se resume a como ou quando as alterações de política são feitas em sua instância de atestado, bem como algumas ações de administrador.

### <a name="auditable-events"></a>Eventos auditáveis
Aqui estão alguns dos logs de auditoria que coletamos:

|     Evento/API                              |     Descrição do Evento                                                                         |
|--------------------------------------------|-----------------------------------------------------------------------------------------------|
|     Criar Instância                        |     Cria uma nova instância de um serviço de atestado. |
|     Destruir instância                       |     Destrói uma instância de um serviço de atestado. |
|     Adicionar certificado de política                 |     Adição de um certificado ao conjunto atual de certificados de gerenciamento de política. |
|     Remover certificado de política              |     Remova um certificado do conjunto atual de certificados de gerenciamento de política. |
|     Definir política atual                     |     Define a política de atestado para um determinado tipo de t. |
|     Redefinir política de atestado               |     Redefine a política de atestado para um determinado tipo de t. |
|     Preparar para atualizar a política               |     Prepare-se para atualizar a política de atestado para um determinado tipo de t. |
|     Locatários do reidratar após o desastre       |     Lacra novamente todos os locatários de atestado nesta instância do serviço de atestado. Isso só pode ser executado por administradores de serviço de atestado. |

### <a name="collected--information"></a>Informações coletadas
Para cada um desses eventos, o atestado do Azure coleta as seguintes informações:

- Nome de operação
- Êxito na operação
- Chamador de operação, que pode ser qualquer um dos seguintes:
    - UPN do Azure AD
    - ID de objeto
    - Certificado
    - ID de Locatário do Azure AD
- Destino da operação, que pode ser qualquer um dos seguintes:
    - Ambiente
    - Região de serviço
    - Função de serviço
    - Instância de função de serviço
    - ID de Recurso
    - Região de recursos

### <a name="sample-audit-log"></a>Log de auditoria de exemplo

Os logs de auditoria são fornecidos no formato JSON. Aqui está um exemplo de como pode ser um log de auditoria.

```json
{"operationName":"SetCurrentPolicy","resultType":"Success","resultDescription":null,"auditEventCategory":["ApplicationManagement"],"nCloud":null,"requestId":null,"callerIpAddress":null,"callerDisplayName":null,"callerIdentities":[{"callerIdentityType":"ObjectID","callerIdentity":"<some object ID>"},{"callerIdentityType":"TenantId","callerIdentity":"<some tenant ID>"}],"targetResources":[{"targetResourceType":"Environment","targetResourceName":"PublicCloud"},{"targetResourceType":"ServiceRegion","targetResourceName":"EastUS2"},{"targetResourceType":"ServiceRole","targetResourceName":"AttestationRpType"},{"targetResourceType":"ServiceRoleInstance","targetResourceName":"<some service role instance>"},{"targetResourceType":"ResourceId","targetResourceName":"/subscriptions/<some subscription ID>/resourceGroups/<some resource group name>/providers/Microsoft.Attestation/attestationProviders/<some instance name>"},{"targetResourceType":"ResourceRegion","targetResourceName":"EastUS2"}],"ifxAuditFormat":"Json","env_ver":"2.1","env_name":"#Ifx.AuditSchema","env_time":"2020-11-23T18:23:29.9427158Z","env_epoch":"MKZ6G","env_seqNum":1277,"env_popSample":0.0,"env_iKey":null,"env_flags":257,"env_cv":"##00000000-0000-0000-0000-000000000000_00000000-0000-0000-0000-000000000000_00000000-0000-0000-0000-000000000000","env_os":null,"env_osVer":null,"env_appId":null,"env_appVer":null,"env_cloud_ver":"1.0","env_cloud_name":null,"env_cloud_role":null,"env_cloud_roleVer":null,"env_cloud_roleInstance":null,"env_cloud_environment":null,"env_cloud_location":null,"env_cloud_deploymentUnit":null}
```

## <a name="access-audit-logs"></a>Acessar logs de auditoria

Esses logs são armazenados no Azure e não podem ser acessados diretamente. Se você precisar acessar esses logs, registre um tíquete de suporte. Para obter mais informações, consulte [Contate o Suporte da Microsoft](https://azure.microsoft.com/support/options/). 

Depois que o tíquete de suporte for arquivado, a Microsoft baixará e fornecerá acesso a esses logs.
