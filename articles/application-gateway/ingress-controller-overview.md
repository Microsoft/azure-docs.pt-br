---
title: O que é Aplicativo Azure controlador de entrada do gateway?
description: Este artigo fornece uma introdução ao que é o controlador de entrada do gateway de aplicativo.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: 26f53a8f93d4d51ec8f8fd91051496a46670f432
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397341"
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
Há duas maneiras de implantar o AGIC para o cluster AKS. A primeira maneira é por meio de Helm; a segunda é por meio de AKS como um complemento. O principal benefício de implantar o AGIC como um complemento do AKS é que ele é muito mais simples do que a implantação por meio do Helm. Para uma nova configuração, você pode implantar um novo gateway de aplicativo e um novo cluster AKS com AGIC habilitado como um complemento em uma linha no CLI do Azure. O complemento também é um serviço totalmente gerenciado, que fornece benefícios adicionais, como atualizações automáticas e maior suporte. O AGIC implantado por meio de Helm não tem suporte do AKS, no entanto, o AGIC implantado como um complemento de AKS é suportado pelo AKS. 

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
> O método AGIC AKS do complemento de implantação está atualmente em versão prévia. Não recomendamos a execução de cargas de trabalho de produção em recursos que ainda estão em versão prévia. portanto, se você estiver curioso para experimentá-lo, recomendamos configurar um novo cluster para testá-lo com o. 

As tabelas a seguir classificam quais cenários têm suporte no momento com a versão de implantação do Helm e a versão do complemento AKS do AGIC. 

### <a name="aks-add-on-agic-single-aks-cluster"></a>AKS complemento AGIC (cluster de AKS único)
|                  |1 gateway de aplicativo |2 + gateways de aplicativo |
|------------------|---------|--------|
|**1 AGIC**|Sim, há suporte para isso |Não, isso está em nossa pendência |
|**2 + AGICs**|Não, somente 1 AGIC com suporte/cluster |Não, somente 1 AGIC com suporte/cluster |

### <a name="helm-deployed-agic-single-aks-cluster"></a>Helm implantou o AGIC (cluster de AKS único)
|                  |1 gateway de aplicativo |2 + gateways de aplicativo |
|------------------|---------|--------|
|**1 AGIC**|Sim, há suporte para isso |Não, isso está em nossa pendência |
|**2 + AGICs**|Deve usar a funcionalidade ProhibitedTarget compartilhada e observar namespaces separados |Sim, há suporte para isso |

### <a name="helm-deployed-agic-2-aks-clusters"></a>Helm implantou o AGIC (2 + clusters AKS)
|                  |1 gateway de aplicativo |2 + gateways de aplicativo |
|------------------|---------|--------|
|**1 AGIC**|N/D |N/D |
|**2 + AGICs**|Deve usar a funcionalidade ProhibitedTarget compartilhada |N/D |

## <a name="next-steps"></a>Próximas etapas
- [**Implantação do AKS Add-On Greenfield**](tutorial-ingress-controller-add-on-new.md): instruções sobre como instalar o AGIC Add-on, o AKs e o gateway de aplicativo em uma infraestrutura de Slate em branco.
- [**Implantação do AKS Add-On Brownfield**](tutorial-ingress-controller-add-on-existing.md): instalar o complemento do AGIC em um cluster do AKS com um gateway de aplicativo existente.
- [**Implantação do Helm Greenfield**](ingress-controller-install-new.md): Instale o AGIC por meio do Helm, novo cluster AKs e novo gateway de aplicativo em uma infraestrutura de Slate em branco.
- [**Implantação do Helm Brownfield**](ingress-controller-install-existing.md): implante o AGIC por meio do Helm em um cluster existente do AKs e no gateway de aplicativo.