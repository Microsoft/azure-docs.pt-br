---
title: Como configurar programaticamente a sincronização de nuvem usando o MS API do Graph
description: Este tópico descreve como habilitar a sincronização de entrada usando apenas o API do Graph
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c84636ea86b3b640aef365c1c5d8e634b9a1f48
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99593152"
---
# <a name="how-to-programmatically-configure-cloud-sync-using-ms-graph-api"></a>Como configurar programaticamente a sincronização de nuvem usando o MS API do Graph

O documento a seguir descreve como replicar um perfil de sincronização do zero usando apenas APIs MSGraph.  
A estrutura de como fazer isso consiste nas etapas a seguir.  São eles:

- [Configuração básica](#basic-setup)
- [Criar entidades de serviço](#create-service-principals)
- [Criar trabalho de sincronização](#create-sync-job)
- [Atualizar domínio de destino](#update-targeted-domain)
- [Habilitar hashes de senha de sincronização](#enable-sync-password-hashes-on-configuration-blade)
- [Exclusões acidentais](#accidental-deletes)
- [Iniciar trabalho de sincronização](#start-sync-job)
- [Status da revisão](#review-status)

Use esses [módulo Microsoft Azure Active Directory para Windows PowerShell](/powershell/module/msonline/) comandos para habilitar a sincronização para um locatário de produção, um pré-requisito para poder chamar o serviço Web de administração para esse locatário.

## <a name="basic-setup"></a>Configuração básica

### <a name="enable-tenant-flags"></a>Habilitar sinalizadores de locatário

 ```PowerShell
 Connect-MsolService ('-AzureEnvironment <AzureEnvironmnet>')
 Set-MsolDirSyncEnabled -EnableDirSync $true
 ```
O primeiro desses dois comandos exige Azure Active Directory credenciais. Esses commandlets identificam implicitamente o locatário e os habilitam para sincronização.

## <a name="create-service-principals"></a>Criar entidades de serviço
Em seguida, precisamos criar a [entidade de serviço/aplicativo AD2AAD](/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http)

Você precisa usar essa ID de aplicativo 1a4721b3-e57f-4451-ae87-ef078703ec94. O displayName é a URL de domínio do AD, se usada no portal (por exemplo, contoso.com), mas pode ser nomeada outra coisa.

 ```
 POST https://graph.microsoft.com/beta/applicationTemplates/1a4721b3-e57f-4451-ae87-ef078703ec94/instantiate
 Content-type: application/json
 {
    displayName: [your app name here]
 }
 ```


## <a name="create-sync-job"></a>Criar trabalho de sincronização
A saída do comando acima retornará o objectId da entidade de serviço que foi criada. Para este exemplo, o objectId é 614ac0e9-a59b-481f-bd8f-79a73d167e1c.  Use Microsoft Graph para adicionar um synchronizationJob à entidade de serviço.  

A documentação para criar um trabalho de sincronização pode ser encontrada [aqui](/graph/api/synchronization-synchronizationjob-post?tabs=http&view=graph-rest-beta).

Se você não registrou a ID acima, você pode encontrar a entidade de serviço executando a seguinte chamada do MS Graph. Você precisará de Directory. Read. All Permissions para fazer essa chamada:
 
 `GET https://graph.microsoft.com/beta/servicePrincipals `

Em seguida, procure o nome do aplicativo na saída.

Execute os dois comandos a seguir para criar dois trabalhos: um para provisionamento de usuário/grupo e outro para sincronização de hash de senha. É a mesma solicitação duas vezes, mas com IDs de modelo diferentes.


Chame as duas solicitações a seguir:

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADProvisioning"
 } 
 ```

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADPasswordHash"
 }
 ```

Você precisará de duas chamadas se quiser criar ambas.

Exemplo de valor de retorno (para provisionamento):

 ```
HTTP 201/Created
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#servicePrincipals('614ac0e9-a59b-481f-bd8f-79a73d167e1c')/synchronization/jobs/$entity",
    "id": "AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da",
    "templateId": "ADDCInPassthrough",
    "schedule": {
        "expiration": null,
        "interval": "PT40M",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "quarantine": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "troubleshootingUrl": null,
        "progress": [],
        "synchronizedEntryCountByType": []
    }
}
```

## <a name="update-targeted-domain"></a>Atualizar domínio de destino
Para esse locatário, o identificador de objeto e o identificador de aplicativo da entidade de serviço são os seguintes:

ObjectId: 8895955e-2e6c-4d79-8943-4d72ca36878f AppId: 00000014-0000-0000-C000-000000000000 DisplayName: testApp

Vamos precisar atualizar o domínio para o qual essa configuração está sendo direcionada, portanto, atualize os segredos para esse domínio.

Verifique se o nome de domínio usado é a mesma URL que você definiu para o controlador de domínio local

 ```
 PUT – https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 ```
 Adicione o seguinte par de chave/valor na matriz de valores abaixo com base no que você está tentando fazer:
 - Habilite PHS e os sinalizadores de locatário de sincronização {Key: "AppKey", value: "{" appKeyScenario ":" AD2AADPasswordHash "}"}
 
 - Habilitar somente o sinalizador de locatário de sincronização (não ative PHS) {Key: "AppKey", value: "{" appKeyScenario ":" AD2AADProvisioning "}"}
 ```
 Request body –
 {
    "value": [
               {
                 "key": "Domain",
                 "value": "{\"domain\":\"ad2aadTest.com\"}"
               }
             ]
  }
```

A resposta esperada é... HTTP 204/sem conteúdo

Aqui, o valor de "domínio" realçado é o nome do domínio de Active Directory local do qual as entradas devem ser provisionadas para Azure Active Directory.

## <a name="enable-sync-password-hashes-on-configuration-blade"></a>Habilitar hashes de senha de sincronização na folha de configuração

 Esta seção abordará a habilitação de hashes de senha de sincronização para uma configuração específica. Isso é diferente do segredo AppKey que habilita o sinalizador de recurso de nível de locatário – isso é apenas para um único domínio/configuração. Você precisará definir a chave do aplicativo para o PHS um para que isso funcione de ponta a ponta.

1. Pegue o esquema (aviso isso é muito grande) 
 ```
 GET –https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
 ```
2. Use este mapeamento de atributo CredentialData:
 ``` 
 {
 "defaultValue": null,
 "exportMissingReferences": false,
 "flowBehavior": "FlowWhenChanged",
 "flowType": "Always",
 "matchingPriority": 0,
 "targetAttributeName": "CredentialData",
 "source": {
 "expression": "[PasswordHash]",
 "name": "PasswordHash",
 "type": "Attribute",
 "parameters": []
 }
 ```
3. Localize os seguintes mapeamentos de objeto com os seguintes nomes no esquema
 - Provisionar Active Directory usuários
 - Provisionar Active Directory inetOrgPersons

 Os mapeamentos de objeto estão dentro do esquema. synchronizationRules [0]. objectmapeamentos (por enquanto, você pode supor que há apenas uma regra de sincronização)

4. Faça o mapeamento CredentialData da etapa (2) e insira-o nos mapeamentos de objeto na etapa (3)

 O mapeamento de objeto é semelhante a este:
 ```
 {
 "enabled": true,
 "flowTypes": "Add,Update,Delete",
 "name": "Provision Active Directory users",
 "sourceObjectName": "user",
 "targetObjectName": "User",
 "attributeMappings": [
 ...
 } 
 ```
 Copie/cole o mapeamento da etapa **criar trabalhos AD2AADProvisioning e AD2AADPasswordHash** acima na matriz attributeMappings. 

 A ordem dos elementos nesta matriz não importa (o back-end será classificado para você). Tenha cuidado ao adicionar esse mapeamento de atributo se o nome já existir na matriz (por exemplo, se já houver um item em attributeMappings que tenha o targetAttributeName CredentialData)-você pode receber erros de conflito ou os mapeamentos preexistentes e novos podem ser combinados juntos (geralmente não o resultado desejado). O back-end não elimina a duplicação para você. 

 Lembre-se de fazer isso para usuários e inetOrgpersons

5. Salve o esquema que você criou 
 ```
 PUT –
 https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
```

 Adicione o esquema no corpo da solicitação. 

## <a name="accidental-deletes"></a>Exclusões acidentais
Esta seção explicará como habilitar e desabilitar programaticamente e usar [exclusões acidentais](how-to-accidental-deletes.md) programaticamente.


### <a name="enabling-and-setting-the-threshold"></a>Habilitando e definindo o limite
Há duas configurações por trabalho que você pode usar, elas são:

 - DeleteThresholdEnabled – habilita a prevenção acidental de exclusão para o trabalho quando definido como ' true '. Defina como ' true ' por padrão.
 - DeleteThresholdValue – define o número máximo de exclusões que serão permitidas em cada execução do trabalho quando a prevenção de exclusões acidentais estiver habilitada. O valor é definido como 500 por padrão.  Portanto, se o valor for definido como 500, o número máximo de exclusões permitido será 499 em cada execução.

As configurações de limite de exclusão fazem parte do `SyncNotificationSettings` e podem ser modificadas por meio do grafo. 

Vamos precisar atualizar o SyncNotificationSettings que essa configuração está direcionando, portanto, atualize os segredos.

 ```
 PUT – https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 ```

 Adicione o seguinte par de chave/valor na matriz de valores abaixo com base no que você está tentando fazer:

```
 Request body -
 {
   "value":[
             {
               "key":"SyncNotificationSettings",
               "value": "{\"Enabled\":true,\"Recipients\":\"foobar@xyz.com\",\"DeleteThresholdEnabled\":true,\"DeleteThresholdValue\":50}"
              }
            ]
  }


```

A configuração "Enabled" no exemplo acima é para habilitar/desabilitar emails de notificação quando o trabalho é colocado em quarentena.


Atualmente, não há suporte para solicitações de PATCH para segredos, portanto, você precisará adicionar todos os valores no corpo da solicitação PUT (como no exemplo acima) para preservar os outros valores.

Os valores existentes para todos os segredos podem ser recuperados usando 

```
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/secrets 
```

### <a name="allowing-deletes"></a>Permitindo exclusões
Para permitir que as exclusões fluam depois que o trabalho entrar em quarentena, você precisará emitir uma reinicialização com apenas "ForceDeletes" como o escopo. 

```
Request:
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/restart
```

```
Request Body:
{
  "criteria": {"resetScope": "ForceDeletes"}
}
```






## <a name="start-sync-job"></a>Iniciar trabalho de sincronização
O trabalho pode ser recuperado novamente por meio do seguinte comando:

 `GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ ` 

A documentação para recuperar trabalhos pode ser encontrada [aqui](/graph/api/synchronization-synchronizationjob-list?tabs=http&view=graph-rest-beta). 
 
Para iniciar o trabalho, emita essa solicitação, usando o objectId da entidade de serviço criada na primeira etapa e o identificador do trabalho retornado da solicitação que criou o trabalho.

A documentação sobre como iniciar um trabalho pode ser encontrada [aqui](/graph/api/synchronization-synchronizationjob-start?tabs=http&view=graph-rest-beta). 

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/start
 ```

A resposta esperada é... HTTP 204/sem conteúdo.

Outros comandos para controlar o trabalho são documentados [aqui](/graph/api/resources/synchronization-synchronizationjob?view=graph-rest-beta).
 
Para reiniciar um trabalho, ele usaria...

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/restart
 {
   "criteria": {
       "resetScope": "Full"
   }
 }
 ```

## <a name="review-status"></a>Status da revisão
Recupere seus status de trabalho via...

 ```
 GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ 
 ```

Procure na seção ' status ' do objeto de retorno para obter detalhes relevantes

## <a name="next-steps"></a>Próximas etapas 

- [O que é a sincronização em nuvem do Azure AD Connect?](what-is-cloud-sync.md)
- [Transformações](how-to-transformation.md)
- [API de sincronização do Azure AD](/graph/api/resources/synchronization-overview?view=graph-rest-beta)