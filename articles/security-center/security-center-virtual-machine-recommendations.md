---
title: Recomendações de máquina virtual na Central de Segurança do Azure | Microsoft Docs
description: Este documento explica as recomendações da Central de Segurança do Azure sobre como ajudar a proteger suas máquinas virtuais e computadores e seus aplicativos Web e ambientes de serviço de aplicativo.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f5ce7f62-7b12-4bc0-b418-4a2f9ec49ca1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2018
ms.author: rkarlin
ms.openlocfilehash: a4aaf440856746895a31914aeee2bddec2ce23f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60544969"
---
# <a name="understand-azure-security-center-resource-recommendations"></a>Compreender as recomendações de recurso da Central de Segurança do Azure


## <a name="recommendations"></a>Recomendações
Use as tabelas abaixo como referência para entender as recomendações de serviços de aplicativo e de computação disponíveis e qual é o efeito ao aplicar cada uma delas.

### <a name="computers"></a>Computadores
| Recomendações | DESCRIÇÃO |
| --- | --- |
| [Habilitar coleta de dados para assinaturas](security-center-enable-data-collection.md) |Recomenda que você ative a coleta de dados na política de segurança para cada uma de suas assinaturas e todas as VMs (máquinas virtuais) em suas assinaturas. |
| [Habilitar a criptografia para a Conta de Armazenamento do Azure](security-center-enable-encryption-for-storage-account.md) | Recomenda a habilitação da Criptografia do Serviço de Armazenamento do Azure para dados em repouso. A SSE (Criptografia do Serviço de Armazenamento) funciona criptografando os dados quando eles são gravados no armazenamento do Azure e descriptografando-os antes da recuperação. Atualmente, a SSE está disponível somente para o serviço Blob do Azure e pode ser usada para blobs de blocos, blobs de páginas e blobs de acréscimo. Para saber mais, confira [Criptografia do Serviço de Armazenamento para dados em repouso](../storage/common/storage-service-encryption.md).</br>A SSE tem suporte apenas nas contas de armazenamento do Resource Manager. Atualmente, não há suporte para contas de armazenamento clássicas. Para entender os modelos de implantação clássicos e do Resource Manager, confira [Modelos de implantação do Azure](../azure-classic-rm.md). |
| [Corrigir as configurações de segurança](security-center-remediate-os-vulnerabilities.md) |Recomenda que você alinhe as configurações do sistema operacional com as regras de configuração de segurança recomendadas, por exemplo, não permitir o armazenamento de senhas. |
| [Aplicar atualizações do sistema](security-center-apply-system-updates.md) |Recomenda que você implante a atualizações críticas e de segurança do sistema ausentes para VMs. |
| [Aplicar um controle de acesso à rede Just-In-Time](security-center-just-in-time.md) | Recomenda que você aplique acesso à VM just in time. O recurso just in time está em versão prévia e está disponível na camada Standard da Central de Segurança. Confira os [Preços](security-center-pricing.md) para saber mais sobre os tipos de preço da Central de Segurança. |
| [Reinicializar após as atualizações do sistema](security-center-apply-system-updates.md#reboot-after-system-updates) |Recomenda que você reinicialize uma VM para concluir o processo de aplicação de atualizações de sistema. |
| [Instalar proteção do ponto de extremidade](security-center-install-endpoint-protection.md) |Recomenda que você provisione programas antimalware para máquinas virtuais (somente VMs do Windows). |
| [Habilitar o Agente de VM](security-center-enable-vm-agent.md) |Permite que você veja quais máquinas virtuais exigem o Agente de VM. O Agente de VM deve ser instalado em VMs para provisionar verificação de linha de base, verificação de patch e programas antimalware. O agente de VM está instalado por padrão nas VMs implantadas do Azure Marketplace. O artigo [Agente de VM e extensões – parte 2](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) fornece informações sobre como instalar o Agente de VM. |
| [Aplicar a criptografia de disco](security-center-apply-disk-encryption.md) |Recomenda de que você criptografe os discos de VM usando o Azure Disk Encryption (VMs do Windows e do Linux). A criptografia é recomendada para volumes de dados e do sistema operacional em sua VM. |
| [Atualizar a versão do sistema operacional](security-center-update-os-version.md) |Recomenda que você atualize a versão do SO (sistema operacional) de seu Serviço de Nuvem para a versão mais recente disponível para a família do SO.  Para saber mais sobre os Serviços de Nuvem, confira a [Visão geral dos serviços de nuvem](../cloud-services/cloud-services-choose-me.md). |
| [Avaliação de vulnerabilidade não instalada](security-center-vulnerability-assessment-recommendations.md) |Recomenda que você instale uma solução de avaliação de vulnerabilidade na VM. |
| [Corrigir vulnerabilidades](security-center-vulnerability-assessment-recommendations.md#review-the-recommendation) |Permite que você veja as vulnerabilidades do sistema e dos aplicativos detectadas pela solução de avaliação de vulnerabilidade instalada na VM. |

### Serviços de aplicativos <a name="app-services"></a>
| Recomendações | DESCRIÇÃO |
| --- | --- |
| O Serviço de Aplicativo deve ser acessível apenas por HTTPS | É recomendável que você limite o acesso do Serviço de Aplicativo apenas por HTTPS. |
| Os soquetes da Web devem ser desabilitados para o aplicativo Web| É recomendável que você examine cuidadosamente o uso de soquetes da Web em aplicativos Web.  O protocolo de soquetes da Web é vulnerável a diferentes tipos de ameaças de segurança. |
| Usar domínios personalizados para seu aplicativo Web | É recomendável o uso de domínios personalizados para proteger um aplicativo Web de ataques comuns como phishing e outros ataques relacionados a DNS. |
| Configurar restrições de IP para o aplicativo Web | É recomendável que você defina uma lista de endereços IP que tem autorização para acessar seu aplicativo.  O uso de restrições de IP protege um aplicativo Web de ataques comuns. |
| Não permita que todos os recursos ('*') acessem o aplicativo | É recomendável que você não defina o parâmetro WEBSITE_LOAD_CERTIFICATES como ‘*’. Definir o parâmetro como '*' significa que todos os certificados serão carregados para seu repositório de certificados pessoal de aplicativos Web.  Isso pode levar a abuso do princípio de privilégio mínimo porque é improvável que o site precise de acesso a todos os certificados em tempo de execução. |
| O CORS não deve permitir o acesso a todos os recursos ao seu aplicativo | É recomendável que você permita que apenas os domínios necessários interajam com seu aplicativo Web. O CORS (compartilhamento de recurso de origem cruzada) não deve permitir que todos os domínios acessem seu aplicativo Web. |
| Use o .NET Framework compatível mais recente para o aplicativo Web | É recomendável que você use a versão mais recente do .NET Framework para as classes de segurança mais recentes. Usar classes e tipos mais antigos pode tornar seu aplicativo vulnerável. |
| Use a versão mais recente compatível do Java para o aplicativo Web | É recomendável que você use a versão mais recente do Java para as classes de segurança mais recentes. Usar classes e tipos mais antigos pode tornar seu aplicativo vulnerável. |
| Use a versão mais recente compatível do PHP para o aplicativo Web | É recomendável que você use a versão mais recente do PHP para as classes de segurança mais recentes. Usar classes e tipos mais antigos pode tornar seu aplicativo vulnerável. |
| [Adicione um firewall do aplicativo Web](security-center-add-web-application-firewall.md) |Recomenda que você implante um WAF (firewall do aplicativo Web) para pontos de extremidade da Web. Uma recomendação WAF é mostrada para qualquer IP voltado para uso público (IP de nível de instância ou IP de balanceamento de carga) que tem um grupo de segurança de rede associado com portas de entrada da Web abertas (80,443).</br></br>A Central de Segurança recomenda que você provisione um WAF para ajudar a proteger contra ataques direcionados a seus aplicativos Web em máquinas virtuais e no Ambiente do Serviço de Aplicativo. Um ASE (Ambiente do Serviço de Aplicativo) é uma opção do plano de serviço [Premium](https://azure.microsoft.com/pricing/details/app-service/) do Serviço de Aplicativo do Azure que fornece um ambiente totalmente isolado e dedicado a executar com segurança os aplicativos do Serviço de Aplicativo do Azure. Para saber mais sobre o ASE, consulte a [Documentação do Ambiente do Serviço de Aplicativo](../app-service/environment/intro.md).</br></br>Você pode proteger vários aplicativos Web na Central de segurança adicionando-os às suas implantações do WAF existentes. |
| [Finalizar a proteção do aplicativo](security-center-add-web-application-firewall.md#finalize-application-protection) |Para concluir a configuração de um WAF, o tráfego deve ser roteado para o dispositivo do WAF. Se essa recomendação for seguida, as alterações de configuração necessárias serão concluídas. |
| Use a versão mais recente compatível do Node.js para o aplicativo Web | É recomendável que você use a versão mais recente do Node.js para as classes de segurança mais recentes. Usar classes e tipos mais antigos pode tornar seu aplicativo vulnerável. |
| O CORS não deve permitir o acesso a todos os recursos ao seu aplicativo de funções | É recomendável que você permita que apenas os domínios necessários interajam com seu aplicativo Web. O CORS (compartilhamento de recurso de origem cruzada) não deve permitir que todos os domínios acessem seu aplicativo de funções. |
| Use domínios personalizados para o aplicativo de funções | É recomendável o uso de domínios personalizados para proteger um aplicativo de funções de ataques comuns como phishing e outros ataques relacionados a DNS. |
| Configure restrições de IP para o aplicativo de funções | É recomendável que você defina uma lista de endereços IP que tem autorização para acessar seu aplicativo. O uso de restrições de IP protege um aplicativo de funções de ataques comuns. |
| O aplicativo de funções deve ser acessível apenas por HTTPS | É recomendável que você limite o acesso de aplicativos de função apenas por HTTPS. |
| A depuração remota deve ser desativada para o aplicativo de funções | É recomendável que você desative a depuração para o aplicativo de funções caso não precise mais usá-la. A depuração remota exige que as portas de entrada estejam abertas em um aplicativo de funções. |
| Os soquetes da Web devem ser desabilitados para o aplicativo de funções | É recomendável que você examine cuidadosamente o uso de soquetes da Web em aplicativos de funções. O protocolo de soquetes da Web é vulnerável a diferentes tipos de ameaças de segurança. |


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as recomendações que se aplicam aos outros tipos de recursos do Azure, consulte o seguinte:

* [Monitorar identidade e acesso na Central de Segurança do Azure](security-center-identity-access.md)
* [Protegendo sua rede na Central de Segurança do Azure](security-center-network-recommendations.md)
* [Protegendo o serviço do SQL Azure na Central de Segurança do Azure](security-center-sql-service-recommendations.md)

Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Protegendo suas máquinas e aplicativos na Central de segurança do Azure](security-center-virtual-machine-protection.md)
* [Configurando políticas de segurança na Central de Segurança do Azure](tutorial-security-policy.md) : saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) : aprenda a gerenciar e a responder a alertas de segurança.
* [Perguntas frequentes da Central de Segurança do Azure](security-center-faq.md) : encontre as perguntas frequentes sobre como usar o serviço.

<!--Image references-->
[1]: ./media/security-center-virtual-machine-recommendations/overview.png
[2]: ./media/security-center-virtual-machine-recommendations/compute.png
[3]: ./media/security-center-virtual-machine-recommendations/monitoring-agent-health-issues.png
[4]: ./media/security-center-virtual-machine-recommendations/compute-recommendations.png
[5]: ./media/security-center-virtual-machine-recommendations/apply-system-updates.png
[6]: ./media/security-center-virtual-machine-recommendations/missing-update-details.png
[7]: ./media/security-center-virtual-machine-recommendations/vm-computers.png
[8]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png
[9]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png
[10]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png
[11]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png
[12]: ./media/security-center-virtual-machine-recommendations/filter.png
[13]: ./media/security-center-virtual-machine-recommendations/vm-detail.png
[14]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png
[15]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new3.png
[16]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png
[17]: ./media/security-center-virtual-machine-recommendations/app-services.png
[18]: ./media/security-center-virtual-machine-recommendations/ase.png
[19]: ./media/security-center-virtual-machine-recommendations/web-app.png
[20]: ./media/security-center-virtual-machine-recommendations/recommendation.png
[21]: ./media/security-center-virtual-machine-recommendations/recommendation-desc.png
[22]: ./media/security-center-virtual-machine-recommendations/passed-assessment.png
[23]: ./media/security-center-virtual-machine-recommendations/healthy-resources.png
[24]: ./media/security-center-virtual-machine-recommendations/function-app.png
