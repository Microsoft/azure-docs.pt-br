---
title: Atualizar controlador de entrada com Helm
description: Este artigo fornece informações sobre como atualizar uma entrada do gateway de aplicativo usando o Helm.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: f20302a4993da1754255254ce6d69c000750d4ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84806771"
---
# <a name="how-to-upgrade-application-gateway-ingress-controller-using-helm"></a>Como atualizar o controlador de entrada do Gateway de Aplicativo usando o Helm 

O controlador de entrada do gateway Aplicativo Azure para kubernetes (AGIC) pode ser atualizado usando um repositório Helm hospedado no armazenamento do Azure.

Antes de começarmos o procedimento de atualização, verifique se você adicionou o repositório necessário:

- Exiba os repositórios do Helm adicionados no momento com:

    ```bash
    helm repo list
    ```

- Adicione o repositório AGIC com:

    ```bash
    helm repo add \
        application-gateway-kubernetes-ingress \
        https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    ```

## <a name="upgrade"></a>Atualizar

1. Atualize o repositório AGIC Helm para obter a versão mais recente:

    ```bash
    helm repo update
    ```

1. Exiba as versões disponíveis do `application-gateway-kubernetes-ingress` gráfico:

    ``` bash
    helm search -l application-gateway-kubernetes-ingress
    ```

    Exemplo de resposta:

    ```bash
    NAME                                                    CHART VERSION   APP VERSION     DESCRIPTION
    application-gateway-kubernetes-ingress/ingress-azure    0.7.0-rc1       0.7.0-rc1       Use Azure Application Gateway as the ingress for an Azure...
    application-gateway-kubernetes-ingress/ingress-azure    0.6.0           0.6.0           Use Azure Application Gateway as the ingress for an Azure...
    ```

    A versão mais recente disponível da lista acima é: `0.7.0-rc1`

1. Exibir os gráficos do Helm instalados no momento:

    ```bash
    helm list
    ```

    Exemplo de resposta:

    ```bash
    NAME            REVISION        UPDATED                         STATUS  CHART                   APP VERSION     NAMESPACE
    odd-billygoat   22              Fri Jun 21 15:56:06 2019        FAILED  ingress-azure-0.7.0-rc1 0.7.0-rc1       default
    ```

    A instalação do gráfico Helm da resposta de exemplo acima é denominada `odd-billygoat` . Usaremos esse nome para o restante dos comandos. Seu nome de implantação real provavelmente será diferente.

1. Atualize a implantação do Helm para uma nova versão:

    ```bash
    helm upgrade \
        odd-billygoat \
        application-gateway-kubernetes-ingress/ingress-azure \
        --version 0.9.0-rc2
    ```

## <a name="rollback"></a>Reversão

Se a implantação do Helm falhar, você poderá reverter para uma versão anterior.

1. Obtenha o último número de versão íntegro conhecido:

    ```bash
    helm history odd-billygoat
    ```

    Saída de exemplo:

    ```bash
    REVISION        UPDATED                         STATUS          CHART                   DESCRIPTION
    1               Mon Jun 17 13:49:42 2019        DEPLOYED        ingress-azure-0.6.0     Install complete
    2               Fri Jun 21 15:56:06 2019        FAILED          ingress-azure-xx        xxxx
    ```

    Da saída de exemplo do `helm history` comando, parece ser a última implantação bem-sucedida de nossa `odd-billygoat` revisão `1`

1. Reverter para a última revisão bem-sucedida:

    ```bash
    helm rollback odd-billygoat 1
    ```
