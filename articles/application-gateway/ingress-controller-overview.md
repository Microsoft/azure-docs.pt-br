---
title: O que é Aplicativo Azure controlador de entrada do gateway?
description: Este artigo fornece uma introdução ao que é o controlador de entrada do gateway de aplicativo.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: 2564fd38056241fd48f58f5f6039bf64f92b6741
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101714401"
---
# <a name="what-is-application-gateway-ingress-controller"></a>O que é o controlador de entrada do gateway de aplicativo?
O AGIC (controlador de entrada do gateway de aplicativo) é um aplicativo kubernetes, que possibilita aos clientes [do AKS (Azure kubernetes Service)](https://azure.microsoft.com/services/kubernetes-service/) aproveitar o balanceador de carga L7 do [Gateway de aplicativo](https://azure.microsoft.com/services/application-gateway/) nativo do Azure para expor o software de nuvem à Internet. O AGIC monitora o cluster kubernetes em que ele está hospedado e atualiza continuamente um gateway de aplicativo, para que os serviços selecionados sejam expostos à Internet.

O controlador de entrada é executado em seu próprio pod no AKS do cliente. O AGIC monitora um subconjunto de recursos do kubernetes para alterações. O estado do cluster AKS é convertido na configuração específica do gateway de aplicativo e aplicado ao [Azure Resource Manager (ARM)](../azure-resource-manager/management/overview.md).

## <a name="benefits-of-application-gateway-ingress-controller"></a>Benefícios do controlador de entrada do gateway de aplicativo
O AGIC ajuda a eliminar a necessidade de ter outro IP de balanceador de carga/público na frente do cluster AKS e evita vários saltos em seu caminho de seu DataPath antes que as solicitações alcancem o cluster AKS. O gateway de aplicativo conversa com pods usando seu IP privado diretamente e não requer serviços NodePort ou KubeProxy. Isso também traz um melhor desempenho para suas implantações.

O controlador de entrada tem suporte exclusivo por Standard_v2 e WAF_v2 SKUs, o que também traz os benefícios de dimensionamento automático. O gateway de aplicativo pode reagir em resposta a um aumento ou redução na carga de tráfego e no dimensionamento de acordo, sem consumir nenhum recurso do cluster AKS.

O uso do gateway de aplicativo além do AGIC também ajuda a proteger o cluster do AKS fornecendo a funcionalidade de política TLS e de firewall do aplicativo Web (WAF).

![Aplicativo Azure gateway + AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

O AGIC é configurado por meio do [recurso de entrada](https://kubernetes.io/docs/user-guide/ingress/)do kubernetes, juntamente com o serviço e as implantações/pods. Ele fornece uma série de recursos, aproveitando o balanceador de carga L7 do gateway de aplicativo nativo do Azure. A saber:
  - Roteamento de URL
  - Afinidade baseada em cookie
  - Encerramento de TLS
  - TLS de ponta a ponta
  - Suporte para sites públicos, privados e híbridos
  - Firewall do aplicativo Web integrado

## <a name="difference-between-helm-deployment-and-aks-add-on"></a>Diferença entre a implantação Helm e a AKS Add-On
Há duas maneiras de implantar o AGIC para o cluster AKS. A primeira maneira é por meio de Helm; a segunda é por meio de AKS como um complemento. O principal benefício de implantar o AGIC como um complemento do AKS é que ele é muito mais simples do que a implantação por meio do Helm. Para uma nova configuração, você pode implantar um novo gateway de aplicativo e um novo cluster AKS com AGIC habilitado como um complemento em uma linha no CLI do Azure. O complemento também é um serviço totalmente gerenciado, que fornece benefícios adicionais, como atualizações automáticas e maior suporte. As duas maneiras de implantar AGIC (Helm e AKS Add-on) são totalmente suportadas pela Microsoft. Além disso, o complemento permite uma melhor integração com o AKS como um complemento de primeira classe.

O complemento AGIC ainda é implantado como um pod no cluster AKS do cliente. no entanto, há algumas diferenças entre a versão de implantação Helm e a versão do complemento do AGIC. Abaixo está uma lista de diferenças entre as duas versões: 
  - Os valores de implantação Helm não podem ser modificados no complemento AKS:
    - `verbosityLevel` será definido como 5 por padrão
    - `usePrivateIp` será definido como false por padrão; Isso pode ser substituído pela [anotação use-Private-IP](ingress-controller-annotations.md#use-private-ip)
    - `shared` Não tem suporte no complemento 
    - `reconcilePeriodSeconds` Não tem suporte no complemento
    - `armAuth.type` Não tem suporte no complemento
  - AGIC implantado por meio de Helm dá suporte a ProhibitedTargets, o que significa que AGIC pode configurar o gateway de aplicativo especificamente para clusters AKS sem afetar outros back-ends existentes. O complemento AGIC atualmente não dá suporte a isso. 
  - Como o complemento do AGIC é um serviço gerenciado, os clientes serão automaticamente atualizados para a versão mais recente do complemento do AGIC, diferentemente do AGIC implantado por meio do Helm, onde o cliente deve atualizar manualmente o AGIC. 

> [!NOTE]
> Os clientes só podem implantar um complemento AGIC por cluster AKS e cada complemento do AGIC só pode direcionar um gateway de aplicativo. Para implantações que exigem mais de um AGIC por cluster ou vários AGICs direcionados a um gateway de aplicativo, continue a usar o AGIC implantado por meio de Helm. 

## <a name="next-steps"></a>Próximas etapas
- [**Implantação do AKS Add-On Greenfield**](tutorial-ingress-controller-add-on-new.md): instruções sobre como instalar o AGIC Add-on, o AKs e o gateway de aplicativo em uma infraestrutura de Slate em branco.
- [**Implantação do AKS Add-On Brownfield**](tutorial-ingress-controller-add-on-existing.md): instalar o complemento do AGIC em um cluster do AKS com um gateway de aplicativo existente.
- [**Implantação do Helm Greenfield**](ingress-controller-install-new.md): Instale o AGIC por meio do Helm, novo cluster AKs e novo gateway de aplicativo em uma infraestrutura de Slate em branco.
- [**Implantação do Helm Brownfield**](ingress-controller-install-existing.md): implante o AGIC por meio do Helm em um cluster existente do AKs e no gateway de aplicativo.