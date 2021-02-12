---
title: Resource Health para serviços de nuvem (clássico)
description: Este artigo fala sobre o suporte de verificação de Resource Health (RHC) para Serviços de Nuvem do Microsoft Azure (clássico)
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 37294e681066eb27ace69bcacee3a813b750b8eb
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743501"
---
# <a name="resource-health-check-rhc-support-for-azure-cloud-services-classic"></a>Suporte à verificação de Resource Health (RHC) para serviços de nuvem do Azure (clássico)

> [!IMPORTANT]
> Os [serviços de nuvem do Azure (suporte estendido)](../cloud-services-extended-support/overview.md) são um novo modelo de implantação baseado em Azure Resource Manager para o produto de serviços de nuvem do Azure.Com essa alteração, os serviços de nuvem do Azure em execução no modelo de implantação baseado no Azure Service Manager foram renomeados como serviços de nuvem (clássicos) e todas as novas implantações devem usar os [serviços de nuvem (suporte estendido)](../cloud-services-extended-support/overview.md).

Este artigo fala sobre o suporte de verificação de Resource Health (RHC) para [serviços de nuvem do Microsoft Azure (clássico)](https://azure.microsoft.com/services/cloud-services)

[Azure Resource Health](../service-health/resource-health-overview.md) para serviços de nuvem ajuda você a diagnosticar e obter suporte para problemas de serviço que afetam sua implantação de serviço de nuvem, funções & instâncias de função. Ele relata a integridade atual e passada de seus serviços de nuvem na implantação, função & nível de instância de função.

Relatórios de status do Azure sobre problemas que afetam um amplo conjunto de clientes do Azure. Resource Health fornece um painel personalizado da integridade de seus recursos. Resource Health mostra todas as ocasiões em que os recursos não estão disponíveis devido a problemas de serviço do Azure. Esses dados tornam mais fácil para você ver se um SLA foi violado.

:::image type="content" source="media/cloud-services-allocation-failure/rhc-blade-cloud-services.png" alt-text="Imagem mostra a folha de verificação de integridade do recurso no portal do Azure.":::

## <a name="how-health-is-checked-and-reported"></a>Como a integridade é verificada e relatada?
O Resource Health é relatado em um nível de implantação ou função. A verificação de integridade ocorre no nível de instância de função, agregamos o status e o relatamos no nível de função. Por ex.: Se todas as instâncias de função estiverem disponíveis, o status da função estará disponível. Da mesma forma, agregamos o status de integridade de todas as funções e a relatamos no nível de implantação. Por ex.: Se todas as funções estiverem disponíveis, o status da implantação ficará disponível. 

## <a name="why-i-cannot-see-health-status-for-my-staging-slot-deployment"></a>Por que não consigo ver o status de integridade da minha implantação do slot de preparo?
As verificações de integridade de recursos funcionam apenas para implantação de slot de produção. Ainda não há suporte para a implantação de slot de preparo. 

## <a name="does-resource-health-check-also-check-the-health-of-the-application"></a>O Resource Health verifica também a integridade do aplicativo?
Não, a verificação de integridade ocorre apenas para instâncias de função e não monitora a integridade do aplicativo. Por ex.: Mesmo que 1 das três instâncias de função não estejam íntegras, o aplicativo ainda poderá estar disponível. RHC não usa [investigações de balanceador de carga](../load-balancer/load-balancer-custom-probe-overview.md) ou investigação de agente convidado. Portanto, os clientes devem continuar usando as investigações do balanceador de carga para monitorar a integridade de seus aplicativos. 

## <a name="what-are-the-annotations-for-cloud-services"></a>Quais são as anotações para os serviços de nuvem?
As anotações são o status de integridade da implantação ou das funções. Há diferentes anotações com base no status de integridade, motivo para alteração de status, etc. 

## <a name="what-does-it-mean-by-role-instance-being-unavailable"></a>O que significa que a instância de função está "indisponível"?
Isso significa que a instância de função não está emitindo um sinal íntegro para a plataforma. Verifique o status da instância de função para obter uma explicação detalhada do motivo pelo qual o sinal íntegro não está sendo emitido.

## <a name="what-does-it-mean-by-deployment-being-unknown"></a>O que significa que a implantação é "desconhecida"?
Desconhecido significa que a integridade agregada da implantação do serviço de nuvem não pode ser determinada. Geralmente isso indica que não há nenhuma implantação de produção criada para o serviço de nuvem, a implantação foi recém-criada (e o Azure está começando a coletar eventos de integridade), ou a plataforma está tendo problemas para coletar eventos de integridade para essa implantação.

## <a name="why-does-role-instance-annotations-mentions-vms-instead-of-role-instances"></a>Por que as anotações da instância de função mencionam as VMs em vez das instâncias de função?
Como as instâncias de função são basicamente VMs e a verificação de integridade para VMs é reutilizada para instâncias de função, o termo da VM é usado para representar instâncias de função. 

## <a name="cloud-services-deployment-level-annotations--their-meanings"></a>Anotações de serviços de nuvem (nível de implantação) & seus significados
| Annotation | Descrição | 
| --- | --- | 
| Disponível| Não há nenhum problema conhecido da plataforma Azure afetando esta implantação do serviço de nuvem |
| Unknown | No momento, não é possível determinar a integridade desta implantação do serviço de nuvem | 
| Configurando Resource Health | Configurando a integridade do recurso para este recurso. O Resource Health inspeciona os recursos do Azure para fornecer detalhes sobre eventos passados e em andamento que os afetaram|
| Degradado | A implantação do Serviço de Nuvem está prejudicada. Estamos trabalhando para recuperar automaticamente sua implantação do Serviço de Nuvem e para determinar a origem do problema. Nenhuma ação adicional é necessária neste momento |
| Unhealthy | A implantação do serviço de nuvem não está íntegra porque as {0} instâncias fora da {1} função estão indisponíveis |
| Degradado | A implantação do serviço de nuvem está degradada porque as {0} instâncias fora da {1} função estão indisponíveis | 
| Disponível e talvez afetado | A implantação do serviço de nuvem está em execução, mas uma interrupção contínua do serviço do Azure pode impedi-lo de se conectar a ele. A conectividade será restaurada quando a interrupção for resolvida |
| Não disponível e talvez afetado | A integridade dessa implantação do serviço de nuvem pode ser afetada por uma interrupção do serviço do Azure. A implantação do serviço de nuvem será recuperada automaticamente quando a interrupção for resolvida |
| Desconhecido e talvez afetado | No momento, não é possível determinar a integridade dessa implantação do serviço de nuvem. Isso pode ser causado por uma interrupção de serviço do Azure em andamento que pode estar afetando essa máquina virtual, que será automaticamente recuperada quando a interrupção for resolvida |

## <a name="cloud-services-role-instance-level-annotations--their-meanings"></a>Anotações de serviços de nuvem (nível de instância de função) & seus significados
| Annotation | Descrição | 
| --- | --- | 
| Disponível | Não há nenhum problema conhecido da plataforma Azure afetando esta máquina virtual | 
| Unknown | No momento, não é possível determinar a integridade desta máquina virtual |
| Parado e desalocando | Esta máquina virtual está sendo interrompida e desalocada conforme solicitado por um usuário ou processo autorizado |
| Configurando Resource Health | Configurando a integridade do recurso para este recurso. O Resource Health inspeciona os recursos do Azure para fornecer detalhes sobre eventos passados e em andamento que os afetaram |
| Indisponível | Sua máquina virtual está indisponível. Estamos trabalhando para recuperar sua máquina virtual automaticamente e determinar a origem do problema. Nenhuma ação adicional é necessária neste momento |
| Degradado | Sua máquina virtual está degradada. Estamos trabalhando para recuperar sua máquina virtual automaticamente e determinar a origem do problema. Nenhuma ação adicional é necessária neste momento |
| Falha de hardware do servidor host | Esta máquina virtual é afetada por uma falha fatal de {HardwareCategory} no servidor host. O Azure reimplantará sua máquina virtual em um servidor de host íntegro |
| Migração agendada devido a hardware degradado | O Azure identificou que o servidor host tem um {0} degradado que tem previsão de falha em breve. Se possível, migraremos ao vivo sua máquina virtual assim que possível, ou reimplantaremos ela depois de {1} UTC. Para minimizar o risco para o serviço e, caso o hardware falhe antes que ocorra a migração iniciada pelo sistema, recomendamos que você reimplante sua máquina virtual assim que possível |
| Disponível e talvez afetado | Sua máquina virtual está em execução, no entanto, uma interrupção de serviço do Azure em andamento pode impedi-lo de se conectar a ela. A conectividade será restaurada quando a interrupção for resolvida |
| Não disponível e talvez afetado | A integridade dessa máquina virtual pode ser afetada por uma interrupção do serviço do Azure. Sua máquina virtual será recuperada automaticamente quando a interrupção for resolvida |
| Desconhecido e talvez afetado | No momento, não é possível determinar a integridade dessa máquina virtual. Isso pode ser causado por uma interrupção de serviço do Azure em andamento que pode estar afetando essa máquina virtual, que será automaticamente recuperada quando a interrupção for resolvida |
| Recursos de hardware alocados | Os recursos de hardware foram atribuídos à máquina virtual e estarão online em breve |
| Parando e desalocando | Esta máquina virtual está sendo interrompida e desalocada conforme solicitado por um usuário ou processo autorizado |
| Configuração sendo atualizada | A configuração desta máquina virtual está sendo atualizada conforme solicitado por um usuário ou processo autorizado |
| Reinicializado pelo usuário | Esta máquina virtual está sendo reiniciada conforme solicitado por um usuário ou processo autorizado. Ele ficará online novamente depois que a reinicialização for concluída |
| Reimplantando em host diferente | Esta máquina virtual está sendo reimplantada em um host diferente, conforme solicitado por um usuário ou processo autorizado. Ele ficará online novamente após a reimplantação ser concluída |
| Parado pelo usuário | Esta máquina virtual está sendo interrompida conforme solicitado por um usuário ou processo autorizado |
| Parado pelo usuário ou processo | Esta máquina virtual está sendo interrompida conforme solicitado por um usuário autorizado ou por um processo em execução dentro da máquina virtual |
| Iniciado pelo usuário | Esta máquina virtual está sendo iniciada conforme solicitado por um usuário ou processo autorizado. Ele estará online em breve |
| Reimplantação de manutenção em host diferente | Esta máquina virtual está sendo reimplantada em um servidor host diferente como parte de uma atividade de manutenção planejada. Ele ficará online novamente após a reimplantação ser concluída |
| Reinicialização iniciada de dentro do computador | Uma reinicialização foi disparada de dentro da máquina virtual. Isso pode ser devido a uma falha do sistema operacional da máquina virtual ou conforme solicitado por um usuário ou processo autorizado. A máquina virtual ficará online novamente após a conclusão da reinicialização |
| Redimensionado pelo usuário | Esta máquina virtual está sendo redimensionada conforme solicitado por um usuário ou processo autorizado. Ele ficará online novamente após a conclusão do redimensionamento |
| Computador falhou | Uma reinicialização foi disparada de dentro da máquina virtual. Isso pode ser devido a uma falha do sistema operacional da máquina virtual ou conforme solicitado por um usuário ou processo autorizado. A máquina virtual ficará online novamente após a conclusão da reinicialização |
| Reinicialização de manutenção para atualização do host | Atualizações de manutenção estão sendo aplicadas ao servidor host que executa esta máquina virtual. Nenhuma ação adicional é necessária no momento. Ele ficará online novamente após a conclusão da manutenção |
| Reimplantação de manutenção em novo hardware | Esta máquina virtual não está disponível porque está sendo reimplantada para um hardware mais recente como parte de um evento de manutenção planejada. Nenhuma ação adicional é necessária no momento. Ele ficará online novamente após a conclusão da manutenção planejada |
| Máquina de baixa prioridade admitida anteriormente | Esta máquina virtual admitiu preempção. Esta máquina virtual de baixa prioridade está sendo interrompida e desalocada |
| Reinicialização do servidor host | Sua máquina virtual não está disponível devido a uma reinicialização inesperada do servidor host. O servidor host está sendo reinicializado no momento. A máquina virtual ficará online novamente depois que a reinicialização for concluída. Nenhuma ação adicional é necessária neste momento |
| Reimplantando devido a falha do host | Sua máquina virtual não está disponível e está sendo reimplantada devido a uma falha inesperada no servidor host. O Azure iniciou o processo de recuperação automática e está iniciando a máquina virtual no momento em um host diferente. Nenhuma ação adicional é necessária neste momento |
| Falha inesperada do host | Sua máquina virtual não está disponível devido a uma falha inesperada no servidor host. O Azure iniciou o processo de recuperação automática e está reiniciando o servidor host no momento. Nenhuma ação adicional é necessária no momento. A máquina virtual ficará online novamente após a conclusão da reinicialização |
| Reimplantando devido à manutenção de host não planejada | Sua máquina virtual não está disponível e está sendo reimplantada devido a uma falha inesperada no servidor host. O Azure iniciou o processo de recuperação automática e está iniciando a máquina virtual no momento em um servidor host diferente. Nenhuma ação adicional é necessária neste momento |
| Falha de provisionamento | Infelizmente, sua máquina virtual não está disponível devido a problemas de provisionamento inesperados. O provisionamento da sua máquina virtual falhou devido a um erro inesperado |
| Migração dinâmica | Esta máquina virtual está em pausa devido a uma operação de Migração ao Vivo de preservação de memória. A máquina virtual normalmente é retomada em até 10 segundos. Nenhuma ação adicional é necessária neste momento |
| Migração dinâmica | Esta máquina virtual está em pausa devido a uma operação de Migração ao Vivo de preservação de memória. A máquina virtual normalmente é retomada em até 10 segundos. Nenhuma ação adicional é necessária neste momento | 
| Disco remoto desconectado | Infelizmente, sua máquina virtual não está disponível devido à perda de conectividade com o disco remoto. Estamos trabalhando para restabelecer a conectividade do disco. Nenhuma ação adicional é necessária neste momento |
| Problema de serviço do Azure | Sua máquina virtual é afetada pelo problema do serviço do Azure |
| Problema de rede | Esta máquina virtual é afetada por um dispositivo de rede de topo de rack |
| Indisponível | Sua máquina virtual está indisponível. No momento, não é possível determinar o motivo para esse tempo de inatividade |
| Reinicialização do servidor host | Sua máquina virtual não está disponível devido a uma reinicialização inesperada do servidor host. Um problema inesperado com o servidor host está impedindo a recuperação automática da máquina virtual |
| Reimplantando devido a falha do host | Sua máquina virtual não está disponível devido a uma falha inesperada no servidor host. Um problema inesperado com o host está nos impedindo de recuperar automaticamente sua máquina virtual |
| Falha inesperada do host | Sua máquina virtual não está disponível devido a uma falha inesperada no servidor host. Um problema inesperado com o host está nos impedindo de recuperar automaticamente sua máquina virtual |
| Reimplantando devido à manutenção de host não planejada | Sua máquina virtual não está disponível devido a uma falha inesperada no servidor host. Um problema inesperado com o host está nos impedindo de recuperar automaticamente sua máquina virtual |
| Falha de provisionamento | Infelizmente, sua máquina virtual não está disponível devido a problemas de provisionamento inesperados. O provisionamento da sua máquina virtual falhou devido a um erro inesperado |
| Disco remoto desconectado | Sua máquina virtual não está disponível devido à perda de conectividade com o disco remoto. Um problema inesperado está nos impedindo de recuperar automaticamente sua máquina virtual |
| Reinicialização devido à atualização do SO convidado | Uma reinicialização foi iniciada pela plataforma do Azure para aplicar uma nova atualização do sistema operacional convidado. A máquina virtual ficará online novamente após a conclusão da reinicialização |