---
title: Como parar de monitorar o cluster do Azure e do Red Hat OpenShift v4 | Microsoft Docs
description: Este artigo descreve como você pode parar o monitoramento de seu cluster do Azure Red Hat OpenShift e do Red Hat OpenShift versão 4 com insights de contêiner.
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 09ca05a25ce9bb02b8a3d515acf060e2e9e7e8c2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731792"
---
# <a name="how-to-stop-monitoring-your-azure-and-red-hat-openshift-v4-cluster"></a>Como parar de monitorar o cluster do Azure e do Red Hat OpenShift v4

Depois de habilitar o monitoramento do cluster do Azure Red Hat OpenShift e do Red Hat OpenShift versão 4. x, você poderá parar de monitorar o cluster com o contêiner insights se decidir que não deseja mais monitorá-lo. Este artigo mostra como fazer isso.  

## <a name="how-to-stop-monitoring-using-helm"></a>Como interromper o monitoramento usando o Helm

1. Para identificar primeiro a versão do gráfico do Helm insights do contêiner instalada no cluster, execute o seguinte comando Helm.

    ```
    helm list
    ```

    A saída será semelhante à seguinte:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *Azmon-contêineres-Release-1* representa a versão do gráfico de Helm para insights de contêiner.

2. Para excluir a versão do gráfico, execute o comando Helm a seguir.

    `helm delete <releaseName>`

    Exemplo:

    `helm delete azmon-containers-release-1`

    Isso removerá a versão do cluster. Você pode verificar executando o `helm list` comando:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

A alteração da configuração pode levar alguns minutos para ser concluída. Como o Helm rastreia suas versões mesmo depois de excluí-las, você pode auditar o histórico de um cluster e até mesmo cancelar a exclusão de uma versão com `helm rollback` .

## <a name="next-steps"></a>Próximas etapas

Se o espaço de trabalho Log Analytics foi criado apenas para dar suporte ao monitoramento do cluster e ele não for mais necessário, você precisará excluí-lo manualmente. Se você não estiver familiarizado com como excluir um espaço de trabalho, consulte [excluir um espaço de trabalho do Azure log Analytics](../logs/delete-workspace.md).