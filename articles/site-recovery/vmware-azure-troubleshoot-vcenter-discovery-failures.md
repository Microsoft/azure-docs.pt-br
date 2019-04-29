---
title: Solucione o failback para local durante a recuperação de desastre de VMware para o Azure com o Azure Site Recovery | Microsoft Docs
description: Este artigo descreve maneiras de solucionar problemas de failback e reprotecção durante a recuperação de desastres de VMs do VMware para o Azure com o Azure Site Recovery.
author: vDonGlover
manager: JarrettRenshaw
ms.service: site-recovery
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: v-doglov
ms.openlocfilehash: c598c5e238458c010500579c5371622b85e71de0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565184"
---
# <a name="troubleshoot-vcenter-discovery-failures"></a>Solucionar problemas de falhas de descoberta do vCenter

Este artigo ajuda você a solucionar problemas que ocorrem devido a falhas de descoberta do VMware vCenter.

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>Valores não numéricos na propriedade maxSnapShots

Em versões anteriores à 9,20, vCenter desconecta quando ele recupera um valor não numérico para a propriedade `snapshot.maxSnapShots` propriedade em uma máquina virtual.

Esse problema é identificado pela ID do erro 95126.

    ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
    System.FormatException: Input string was not in a correct format.
       at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
       at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
       at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
    
Como resolver o problema:

- Identifique a VM e defina o valor como um valor numérico (configurações de VM Editar no vCenter).

Ou

- Atualize seu servidor de configuração para a versão 9,20 ou posterior.

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>Problemas de configuração de proxy para a conectividade do vCenter

vCenter descoberta respeita as configurações de proxy do sistema padrão configuradas pelo usuário do sistema. O serviço do DRA respeita as configurações de proxy fornecidas pelo usuário durante a instalação do servidor de configuração usando o instalador de instalação unificada ou um modelo OVA. 

Em geral, o proxy é usado para se comunicar com redes públicas; como se comunicar com o Azure. Se o proxy estiver configurado e vCenter está em um ambiente local, ele não será capaz de se comunicar com o DRA.

Quando esse problema é encontrado, ocorrem as seguintes situações:

- O servidor do vCenter \<vCenter > não está acessível devido ao erro: O servidor remoto retornou um erro: (503) Server Unavailable
- O servidor do vCenter \<vCenter > não está acessível devido ao erro: O servidor remoto retornou um erro: Não é possível se conectar com o servidor remoto.
- Não é possível se conectar ao servidor vCenter/ESXi.

Como resolver o problema:

Baixe o [ferramenta PsExec](https://aka.ms/PsExec). 

Use a ferramenta PsExec para acessar o contexto de usuário do sistema e determinar se o endereço de proxy está configurado. Em seguida, você pode adicionar o vCenter à lista de bypass usando os procedimentos a seguir.

Para a configuração de proxy de descoberta:

1. Abra o IE no contexto de usuário do sistema usando a ferramenta PsExec.
    
    psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"

2. Modifica as configurações de proxy no Internet Explorer a ignorar o endereço IP do vCenter.
3. Reinicie o serviço tmanssvc.

Para a configuração de proxy do DRA:

1. Abra um prompt de comando e abra a pasta de provedor do Microsoft Azure Site Recovery.
 
    **CD C:\Program Files\Microsoft Azure Site Recovery Provider**

3. No prompt de comando, execute o comando a seguir.
   
   **DRCONFIGURATOR. EXE / configurar /AddBypassUrls [endereço IP/FQDN do vCenter Server fornecido no momento de adicionar vCenter]**

4. Reinicie o serviço de provedor DRA.

## <a name="next-steps"></a>Próximas etapas

[Gerenciar o servidor de configuração para recuperação de desastres de VM do VMware](https://docs.microsoft.com/azure/site-recovery/vmware-azure-manage-configuration-server#refresh-configuration-server) 
