---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 01/15/2021
ms.author: mikeray
ms.openlocfilehash: 6c8dbeea83cba306cfb788cf447236088045ffc9
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493998"
---
Esta seção explica como aplicar uma restrição de contexto de segurança (SCC). Para a versão de visualização, elas liberam as restrições de segurança. 

1. Baixe a restrição de contexto de segurança personalizada (SCC). Use um dos seguintes: 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - [Bruta](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml)
   - `curl`
   
      O comando a seguir baixa o Arc-data-SCC. YAML:

      ```console
      curl https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml -o arc-data-scc.yaml
      ```

1. Crie SCC.

   ```console
   oc create -f arc-data-scc.yaml
   ```

1. Aplique o SCC à conta de serviço.

   > [!NOTE]
   > Use o mesmo namespace aqui e no `azdata arc dc create` comando a seguir. O exemplo é `arc` .

   ```console
   oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
   ```

   > [!NOTE]
   > O RedHat OpenShift 4,5 ou superior altera como aplicar o SCC à conta de serviço.
   > Use o mesmo namespace aqui e no `azdata arc dc create` comando a seguir. O exemplo é `arc` . 
   > 
   > Se você estiver usando o RedHat OpenShift 4,5 ou superior, execute: 
   >
   >```console
   >oc create rolebinding arc-data-rbac --clusterrole=system:openshift:scc:arc-data-scc --serviceaccount=arc:default
   >```
