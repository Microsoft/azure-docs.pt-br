---
title: Atualizando agentes kubernetes habilitados para o Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Atualizações do agente de controle para kubernetes habilitados para o Azure Arc
keywords: Kubernetes, Arc, Azure, K8s, contêineres, agente, atualização
ms.openlocfilehash: 172654f655e594c295a8807b417c32d7849bd4b1
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121908"
---
# <a name="upgrading-azure-arc-enabled-kubernetes-agents"></a>Atualizando agentes kubernetes habilitados para o Azure Arc

O kubernetes habilitado para Arc do Azure fornece recursos de atualização automática e upgrade manual para seus agentes. Se usar desabilitar a atualização automática e, em vez disso, confiar na atualização manual, a política de suporte à versão será aplicável para agentes Arc e o cluster kubernetes subjacente.

## <a name="toggle-auto-upgrade-on-or-off-when-connecting-cluster-to-azure-arc"></a>Ativar ou desativar a atualização automática ao conectar o cluster ao arco do Azure

O kubernetes habilitado para Arc do Azure fornece seus agentes com recursos de atualização automática prontos para uso.

O comando a seguir conecta um cluster ao arco do Azure com a atualização automática **habilitada**:

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

Com a atualização automática habilitada, o agente pesquisa o Azure por hora para obter a disponibilidade de uma versão mais recente dos agentes. Se o agente encontrar uma versão disponível mais recente, ele disparará uma atualização do gráfico Helm para os agentes Arc do Azure.

Para recusar a atualização automática, especifique o `--disable-auto-upgrade` parâmetro ao conectar o cluster ao arco do Azure. O comando a seguir conecta um cluster ao arco do Azure com a atualização automática **desabilitada**:

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest --disable-auto-upgrade
```

> [!TIP]
> Se você planeja desabilitar a atualização automática, consulte a [política de suporte de versão](#version-support-policy) para o Azure Arc habilitado kubernetes.

## <a name="toggle-auto-upgrade-onoff-after-connecting-cluster-to-azure-arc"></a>Ativar/desativar a atualização automática após conectar o cluster ao arco do Azure

Depois de conectar um cluster ao arco do Azure, você pode alternar o recurso de atualização automática com o `az connectedk8s update` comando, conforme mostrado abaixo:

```console
az connectedk8s update --name AzureArcTest1 --resource-group AzureArcTest --auto-upgrade false
```

## <a name="manually-upgrade-agents"></a>Atualizar agentes manualmente

Se você tiver desabilitado a atualização automática para agentes, poderá iniciar manualmente as atualizações para esses agentes usando o `az connectedk8s upgrade` comando, conforme mostrado abaixo:

```console
az connectedk8s upgrade -g AzureArcTest1 -n AzureArcTest --agent-version 1.0.1
```

O kubernetes habilitado para Arc do Azure segue o [esquema de controle de versão semântico](https://semver.org/) padrão do `MAJOR.MINOR.PATCH` para o controle de versão de seus agentes. 

Cada número na versão indica compatibilidade geral com a versão anterior:

* **As versões principais** mudam quando há atualizações de API incompatíveis ou a compatibilidade com versões anteriores podem ser interrompidas.
* **As versões secundárias** são alteradas quando as alterações de funcionalidade são compatíveis com versões secundárias.
* **As versões de patch** são alteradas quando são feitas correções de bugs compatíveis com versões anteriores.

## <a name="version-support-policy"></a>Política de suporte de versão

Quando você cria problemas de suporte, o Arc do Azure habilitou kubernetes Practices a seguinte política de suporte de versão:

* Os agentes kubernetes habilitados para o Azure Arc têm uma janela de suporte de "N-2", onde "n' é a versão secundária mais recente dos agentes. 
  * Por exemplo, se o arco do Azure habilitado kubernetes apresenta 0.28. a hoje, as versões 0.28. a, 0.28. b, 0.27. c, 0.27. d, 0.26. e e 0.26. f têm suporte no arco do Azure.

* Os clusters kubernetes que se conectam ao Azure Arc têm uma janela de suporte de "N-2", onde "n' é a versão secundária estável mais recente do [kubernetes upstream](https://github.com/kubernetes/kubernetes/releases). 
  * Por exemplo, se kubernetes apresenta 1,20. a hoje, as versões 1,20. a, 1,20. b, 1.19. c, 1.19. d, 18E. e e 18E. f têm suporte.

### <a name="how-often-are-minor-version-releases-of-azure-arc-enabled-kubernetes-available"></a>Com que frequência as versões secundárias dos kubernetes habilitados para o Azure Arc estão disponíveis?

Uma versão secundária dos agentes kubernetes habilitados para o Arc do Azure é lançada aproximadamente uma vez por mês.

### <a name="what-happens-if-im-using-an-agent-version-or-a-kubernetes-version-outside-the-official-support-window"></a>O que acontece se eu estiver usando uma versão do agente ou uma versão do kubernetes fora da janela de suporte oficial?

' Fora do suporte ' significa que as versões que você está executando estão fora das versões com suporte do "N-2" de agentes e clusters de upstream kubernetes. Para prosseguir com o problema de suporte, você será solicitado a atualizar o cluster e os agentes para uma versão com suporte.

## <a name="next-steps"></a>Próximas etapas

* Percorra nosso guia de início rápido para [conectar um cluster kubernetes ao Azure Arc](./connect-cluster.md).
* Já tem um kubernetes do Azure conectado ao cluster? [Crie configurações em seu cluster kubernetes habilitado para Arc](./use-gitops-connected-cluster.md).
* Saiba como [usar Azure Policy para aplicar configurações em escala](./use-azure-policy.md).