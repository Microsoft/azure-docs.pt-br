---
title: "Solução de problemas do Azure Site Recovery por meio do VMware no Azure | Microsoft Docs"
description: "Solução de erros durante a replicação de máquinas virtuais do Azure"
services: site-recovery
documentationcenter: 
author: asgang
manager: srinathv
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/28/2017
ms.author: asgang
ms.openlocfilehash: b7b03442ba815c86e5defa1018b66f56c0b379df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Solução de problemas da instalação por push do Serviço de Mobilidade

Este artigo descreve problemas comuns que você poderá enfrentar ao tentar instalar o Serviço de Mobilidade do Azure Site Recovery no servidor de origem para habilitar a proteção.

## <a name="error-78007---the-requested-operation-could-not-be-completed"></a>Erro 78007 - Não foi possível concluir a operação solicitada
Esse erro pode ser gerado pelo serviço por vários motivos. Escolha o erro do provedor correspondente para fazer a resolução de problemas.

* [Erro 95103](#error-95103---protection-could-not-be-enabled-ep0854) 
* [Erro 95105](#error-95105---protection-could-not-be-enabled-ep0856) 
* [Erro 95107](#error-95107---protection-could-not-be-enabled-ep0858) 
* [Erro 95108](#error-95108---protection-could-not-be-enabled-ep0859) 
* [Erro 95117](#error-95117---protection-could-not-be-enabled-ep0865) 
* [Erro 95213](#error-95213---protection-could-not-be-enabled-ep0874) 
* [Erro 95224](#error-95224---protection-could-not-be-enabled-ep0883) 
* [Erro 95265](#error-95265---protection-could-not-be-enabled-ep0902) 


## <a name="error-95105---protection-could-not-be-enabled-ep0856"></a>Erro 95105 - Não foi possível habilitar a proteção (EP0856)

**Código de erro** | **Possíveis causas:** | **Recomendações específicas ao erro**
--- | --- | ---
95105 </br>**Mensagem:** falha na instalação por push do Serviço de Mobilidade no computador de origem com o código de erro **EP0856**. <br> O **Compartilhamento de Arquivos e Impressoras** não é permitido no computador de origem ou há problemas de conectividade de rede entre o servidor em processo e o computador de origem.| A opção **Compartilhamento de Arquivos e Impressoras** não está habilitada. | Permita o **Compartilhamento de Arquivos e Impressoras** no computador de origem no Firewall do Windows. No computador de origem, em **Firewall do Windows** > **Permitir um aplicativo ou recurso pelo Firewall**, selecione **Compartilhamento de Arquivos e Impressoras para todos os perfis**. </br> Além disso, verifique os pré-requisitos a seguir para concluir com êxito a instalação por push.<br> Leia mais sobre [os problemas de WMI na resolução de problemas](#troubleshoot-wmi-issues)


## <a name="error-95107---protection-could-not-be-enabled-ep0858"></a>Erro 95107 - Não foi possível habilitar a proteção (EP0858)

**Código de erro** | **Possíveis causas:** | **Recomendações específicas ao erro**
--- | --- | ---
95107 </br>**Mensagem:** falha na instalação por push do Serviço de Mobilidade no computador de origem com o código de erro **EP0858**. <br> As credenciais fornecidas para instalar o Serviço de Mobilidade estão incorretas ou a conta de usuário não tem privilégios suficientes. | As credenciais do usuário fornecidas para instalar o Serviço de Mobilidade no computador de origem estão incorretas. | Verifique se as credenciais do usuário fornecidas para o computador de origem no servidor de configuração estão corretas. <br> Para adicionar/editar as credenciais do usuário, acesse o servidor de configuração e selecione **Cspsconfigtool** > **Gerenciar conta**. </br> Além disso, verifique os [pré-requisitos](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery) a seguir para concluir com êxito a instalação por push.


## <a name="error-95117---protection-could-not-be-enabled-ep0865"></a>Erro 95117 - Não foi possível habilitar a proteção (EP0865)

**Código de erro** | **Possíveis causas:** | **Recomendações específicas ao erro**
--- | --- | ---
95117 </br>**Mensagem:** falha na instalação por push do Serviço de Mobilidade no computador de origem com o código de erro **EP0865**. <br> O computador de origem não está em execução ou há problemas de conectividade de rede entre o servidor em processo e o computador de origem. | Problemas de conectividade de rede entre o servidor em processo e o servidor de origem. | Verifique a conectividade entre o servidor em processo e o servidor de origem. </br> Além disso, verifique os [pré-requisitos](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery) a seguir para concluir com êxito a instalação por push.|

## <a name="error-95103---protection-could-not-be-enabled-ep0854"></a>Erro 95103 - Não foi possível habilitar a proteção (EP0854)

**Código de erro** | **Possíveis causas:** | **Recomendações específicas ao erro**
--- | --- | ---
95103 </br>**Mensagem:** falha na instalação por push do Serviço de Mobilidade no computador de origem com o código de erro **EP0854**. <br> O WMI (Instrumentação de Gerenciamento do Windows) não é permitido no computador de origem ou há problemas de conectividade de rede entre o servidor em processo e o computador de origem.| O WMI está bloqueado no Firewall do Windows. | Permita o WMI no Firewall do Windows. Em **Firewall do Windows** > **Permitir um aplicativo ou recurso pelo Firewall**, selecione **WMI para todos os perfis**. </br> Além disso, verifique os [pré-requisitos](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery) a seguir para concluir com êxito a instalação por push.|

## <a name="error-95213---protection-could-not-be-enabled-ep0874"></a>Erro 95213 - Não foi possível habilitar a proteção (EP0874)

**Código de erro** | **Possíveis causas:** | **Recomendações específicas ao erro**
--- | --- | ---
95213 </br>**Mensagem:** Falha na instalação do serviço de mobilidade no computador de origem %SourceIP; falha com código de erro **EP0874**. <br> | A versão do Sistema Operacional no computador de origem não é suportada. <br>| Certifique-se de que haja suporte para a versão OS do computador de origem. Leia a [matriz de suporte](https://aka.ms/asr-os-support). </br> Além disso, verifique os [pré-requisitos](https://aka.ms/pushinstallerror) a seguir para concluir com êxito a instalação por push.| 


## <a name="error-95108---protection-could-not-be-enabled-ep0859"></a>Erro 95108 - Não foi possível habilitar a proteção (EP0859)

**Código de erro** | **Possíveis causas:** | **Recomendações específicas ao erro**
--- | --- | ---
95108 </br>**Mensagem:** falha na instalação por push do serviço de mobilidade no computador de origem com o código de erro **EP0859**. <br>| As credenciais fornecidas para instalar o serviço de mobilidade estão incorretas ou a conta de usuário não tem privilégios suficientes <br>| Certifique-se de que as credenciais fornecidas sejam as credenciais da **raiz** da conta. Para [adicionar/editar](site-recovery-vmware-to-azure-manage-configuration-server.md#modify-user-accounts-and-passwords) as credenciais do usuário, acesse o servidor de configuração e clique no ícone de atalho “Cspsconfigtool” no desktop. Clique em "Gerenciar conta" para adicionar/editar as credenciais.|

## <a name="error-95265---protection-could-not-be-enabled-ep0902"></a>Erro 95265 - Não foi possível habilitar a proteção (EP0902)

**Código de erro** | **Possíveis causas:** | **Recomendações específicas ao erro**
--- | --- | ---
95265 </br>**Mensagem:** A instalação por push do serviço de mobilidade para o computador de origem foi bem-sucedida, mas o computador de origem requer uma reinicialização para que algumas mudanças façam efeito no sistema. <br>| Uma versão mais antiga do serviço de mobilidade já foi instalada no servidor.| A replicação da máquina virtual continua perfeitamente.<br> Reinicialize o servidor durante a próxima janela de manutenção para obter os benefícios das novas melhorias no serviço de mobilidade.|


## <a name="error-95224---protection-could-not-be-enabled-ep0883"></a>Erro 95224 - Não foi possível habilitar a proteção (EP0883)

**Código de erro** | **Possíveis causas:** | **Recomendações específicas ao erro**
--- | --- | ---
95224 </br>**Mensagem:** Falha na instalação por push do serviço de mobilidade no computador de origem %SourceIP; com código de erro EP0883. Um sistema reinicia a partir de uma instalação / atualização anterior que está pendente.| O sistema não foi reiniciado para desinstalar uma versão mais antiga/incompatível do serviço de mobilidade.| Certifique-se de que não exista nenhuma versão do serviço de mobilidade no servidor. <br> Reinicialize o servidor e execute novamente o trabalho de habilitação de proteção|

## <a name="resource-to-troubleshoot-push-installation-problems"></a>Acesse o recurso para solucionar problemas de instalação por push

#### <a name="troubleshoot-file-and-print-sharing-issues"></a>Solucionar problemas de compartilhamento de arquivos e impressão
*  [Habilitar ou desabilitar o compartilhamento de arquivo com a Política de Grupo](https://technet.microsoft.com/en-us/library/cc754359(v=ws.10).aspx)
* [Como habilitar o compartilhamento de arquivo e impressão através do Firewall do Windows](https://technet.microsoft.com/en-us/library/ff633412(v=ws.10).aspx)

#### <a name="troubleshoot-wmi-issues"></a>Solucionar problemas de WMI
* [Teste de WMI básico](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [Solucionar problemas de WMI](https://msdn.microsoft.com/en-us/library/aa394603(v=vs.85).aspx)
* [Solucionar problemas com scripts de WMI e Serviços de WMI](https://technet.microsoft.com/en-us/library/ff406382.aspx#H22)

## <a name="next-steps"></a>Próximas etapas
- [Habilitar a replicação em máquinas virtuais do VMware](vmware-walkthrough-enable-replication.md)
