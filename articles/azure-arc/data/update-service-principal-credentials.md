---
title: Atualizar credenciais de entidade de serviço
description: Atualizar credencial para uma entidade de serviço
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: mikeray
ms.reviewer: mikeray
ms.date: 12/09/2020
ms.topic: how-to
ms.openlocfilehash: 1c7ccec6228a6dcfb457bda8f6ecd384775d4b27
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669535"
---
# <a name="update-service-principal-credentials"></a>Atualizar credenciais de entidade de serviço

Quando as credenciais da entidade de serviço forem alteradas, você precisará atualizar os segredos no controlador de dados.

Por exemplo, se você implantou o controlador de dados usando um conjunto específico de valores para ID de locatário da entidade de serviço, ID do cliente e segredo do cliente e, em seguida, alterar um ou mais desses valores, será necessário atualizar os segredos no controlador de dados.  A seguir, as instruções para atualizar a ID do locatário, a ID do cliente ou o segredo do cliente. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="background"></a>Tela de fundo

A entidade de serviço foi criada em [criar entidade de serviço](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal). 

## <a name="steps"></a>Etapas

1. Acesse o segredo da entidade de serviço no editor padrão.

   ```console
   kubectl edit secret/upload-service-principal-secret -n <name of namespace>
   ```

   Por exemplo, para editar o segredo da entidade de serviço para um controlador de dados no `arc` namespace, execute o seguinte comando:

   ```console
   kubectl edit secret/upload-service-principal-secret -n arc
   ```

   O `kubecl edit` comando abre o arquivo Credentials. yml no editor padrão. 


1. Edite o segredo da entidade de serviço. 

   No editor padrão, substitua os valores na seção de dados pelas informações de credenciais atualizadas.

   Por exemplo:

   ```console
   # Please edit the object below. Lines beginning with a '#' will be ignored,
   # and an empty file will abort the edit. If an error occurs while saving this file will be
   # reopened with the relevant failures.
   #
   apiVersion: v1
   data:
     authority: aHR0cHM6Ly9sb2dpbi5taWNyb3NvZnRvbmxpbmUuY29t
     clientId: NDNiNDcwYrFTGWYzOC00ODhkLTk0ZDYtNTc0MTdkN2YxM2Uw
     clientSecret: VFA2RH125XU2MF9+VVhXenZTZVdLdECXFlNKZi00Lm9NSw==
     tenantId: NzJmOTg4YmYtODZmMRFVBGTJLSATkxYWItMmQ3Y2QwMTFkYjQ3
   kind: Secret
   metadata:
     creationTimestamp: "2020-12-02T05:02:04Z"
     name: upload-service-principal-secret
     namespace: arc
     resourceVersion: "7235659"
     selfLink: /api/v1/namespaces/arc/secrets/upload-service-principal-secret
     uid: 7fb693ff-6caa-4a31-b83e-9bf22be4c112
   type: Opaque
   ```

   Edite os valores para `clientID` , `clientSecret` e/ou `tenantID` conforme apropriado. 

> [!NOTE]
>Os valores precisam ser codificados em base64. Não edite nenhuma outra propriedade.

Se um valor incorreto for fornecido `clientId` para `clientSecret` o, ou `tenantID` você receberá uma mensagem de erro da seguinte maneira nos `control-xxxx` logs de contêiner Pod/controlador:

```output
YYYY-MM-DD HH:MM:SS.mmmm | ERROR | [AzureUpload] Upload task exception: A configuration issue is preventing authentication - check the error message from the server for details.You can modify the configuration in the application registration portal. See https://aka.ms/msal-net-invalid-client for details.  Original exception: AADSTS7000215: Invalid client secret is provided.
```



## <a name="next-steps"></a>Próximas etapas

[Recuperar o nome de usuário e a senha para se conectar ao controlador de dados Arc](retrieve-the-username-password-for-data-controller.md)

[Criar uma entidade de serviço](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)
