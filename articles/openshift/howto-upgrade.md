---
title: Atualizar um cluster do Azure Red Hat OpenShift
description: Saiba como atualizar um cluster do Azure Red Hat OpenShift que executa o OpenShift 4
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 1/10/2021
author: sakthi-vetrivel
ms.author: suvetriv
keywords: aro, openshift, az aro, red hat, cli
ms.openlocfilehash: 742da12bd3a10cd1f541e9c43f654cfe7df04340
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101720878"
---
# <a name="upgrade-an-azure-red-hat-openshift-aro-cluster"></a>Atualizar um cluster do Azure Red Hat OpenShift (ARO)

Parte do ciclo de vida do cluster de ARO envolve a execução de atualizações periódicas para a versão mais recente do OpenShift. É importante que você aplique as últimas versões de segurança ou atualize para obter os recursos mais recentes. Este artigo mostra como atualizar todos os componentes em um cluster OpenShift usando o console Web do OpenShift.

## <a name="before-you-begin"></a>Antes de começar

Este artigo requer que você esteja executando o CLI do Azure versão 2.0.65 posterior. Execute `az --version` para localizar a versão atual. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli)

Este artigo pressupõe que você tenha acesso a um cluster do Azure Red Hat OpenShift existente como um usuário com `admin` privilégios.

## <a name="check-for-available-aro-cluster-upgrades"></a>Verificar atualizações de cluster de ARO disponíveis

No console Web do OpenShift, selecione **Administração**  >  **configurações de cluster** e abra a guia **detalhes** .

Se o **status da atualização** do cluster refletir **as atualizações disponíveis**, você poderá atualizar o cluster.

## <a name="upgrade-your-aro-cluster"></a>Atualizar o cluster toa

No console Web na etapa anterior, defina o **canal** como o canal correto para a versão para a qual você deseja atualizar, como `stable-4.5` .

Selecione uma versão para a qual atualizar e escolha **Atualizar**. Você verá o status da atualização alterar para: `Update to <product-version> in progress` . Você pode examinar o progresso da atualização do cluster observando as barras de progresso dos operadores e dos nós.

## <a name="next-steps"></a>Próximas etapas
- [Saiba como atualizar um cluster toa usando a CLI do OC](https://docs.openshift.com/container-platform/4.6/updating/updating-cluster-between-minor.html)
- Você pode encontrar informações sobre as recomendações e as atualizações da plataforma de contêiner OpenShift disponíveis na [seção errata](https://access.redhat.com/downloads/content/290/ver=4.6/rhel---8/4.6.0/x86_64/product-errata) do portal do cliente.
