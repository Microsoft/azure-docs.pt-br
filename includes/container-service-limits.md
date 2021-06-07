---
title: incluir arquivo
description: incluir arquivo
services: container-service
author: mlearned
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: mlearned
ms.custom: include file
ms.openlocfilehash: 1c2dec106ae72ddead7bda54792fa74e38eb6660
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081056"
---
| Recurso                                                                                                           | Limite                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Máximo de clusters por assinatura                                                                                  | 1000                                                                                                                                                                                                        |
| Máximo de nós por cluster com conjuntos de disponibilidade de máquinas virtuais e SKU de Load Balancer Básico                       | 100                                                                                                                                                                                                         |
| Máximo de nós por cluster com Conjuntos de Dimensionamento de Máquinas Virtuais e [SKU do Standard Load Balancer][standard-load-balancer] | 1000 (100 nós por [pool de nós][node-pool])                                                                                                                                                                 |
| Máximo de pods por nó: [rede básica][basic-networking] com Kubenet                                           | 110                                                                                                                                                                                                         |
| Máximo de pods por nó: [Rede avançada][advanced-networking] com a Interface de Rede de Contêiner do Azure        | Implantação da CLI do Azure: 30<sup>1</sup><br />Modelo do Azure Resource Manager: 30<sup>1</sup><br />Implantação do portal: 30                                                                                        |
| Versão prévia do complemento OSM (Open Service Mesh) para AKS                                                                          | Versão do cluster do Kubernetes: 1.19+<sup>2</sup><br />Controladores de OSM por cluster: 1<sup>2</sup><br />Pods por controlador do OSM: 500<sup>2</sup><br />Contas de serviço do Kubernetes gerenciadas por OSM: 50<sup>2</sup> |

<sup>1</sup>Quando você implanta um cluster do AKS (Serviço de Kubernetes do Azure) com a CLI do Azure ou com um modelo do Resource Manager esse valor pode ser configurado para até 250 pods por nó. Não será possível configurar o máximo de pods por nó após implantar um cluster do AKS ou se você implantar um cluster usando o portal do Azure.<br />

<sup>2</sup>O complemento OSM para AKS está em versão prévia e passará por aprimoramentos adicionais antes da GA (disponibilidade geral). Durante a fase de versão prévia, é recomendável não ultrapassar os limites mostrados.<br />

<!-- LINKS - Internal -->

[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->

[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
