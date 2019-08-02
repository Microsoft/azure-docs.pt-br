---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 10/11/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: a2729af6a689daa551fc01f585324d53a8770a9b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172285"
---
| Resource | Limite padrão |
| --- | :--- |
| Clusters máximo por assinatura | 100 |
| Número máximo de nós por cluster | 100 |
| Máximo pods por nó: [Rede básica][basic-networking] com Kubenet | 110 |
| Máximo pods por nó: [Rede avançada] [ advanced-networking] com Interface de rede de contêiner do Azure | Implantação da CLI do Azure: 30<sup>1</sup><br />Modelo do Gerenciador de recursos do Azure: 30<sup>1</sup><br />Implantação do portal: 30 |

<sup>1</sup>quando você implanta um cluster do serviço de Kubernetes do Azure (AKS) com a CLI do Azure ou um modelo do Resource Manager, esse valor é configurável até 250 pods por nó. Depois que você já implantou um cluster do AKS, ou se você implantar um cluster usando o portal do Azure, é possível configurar máximo pods por nó.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
