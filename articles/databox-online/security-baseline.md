---
title: Linha de base de segurança do Azure para Azure Stack Edge
description: A linha de base de segurança do Azure Stack Edge fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: databox
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 335056260ddea0c15db761df109e69333ef474f3
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641375"
---
# <a name="azure-security-baseline-for-azure-stack-edge"></a>Linha de base de segurança do Azure para Azure Stack Edge

Essa linha de base de segurança aplica as diretrizes da [2,0 versão de avaliação de segurança do Azure](../security/benchmarks/overview.md) para Microsoft Azure Stack borda. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure. O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas orientações relacionadas aplicáveis ao Azure Stack Edge. Os **controles** não aplicáveis ao Azure Stack Edge foram excluídos.

Para ver como Azure Stack borda é completamente mapeada para o benchmark de segurança do Azure, consulte o [arquivo completo de mapeamento de linha de base de segurança do Azure Stack Edge](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, confira o [Azure Security Benchmark: Segurança de Rede](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementar a segurança para tráfego interno

**Orientação**: os clientes implantam um dispositivo de borda Azure Stack, fornecido pela Microsoft em sua rede privada para acesso interno e têm opções para protegê-lo ainda mais. Por exemplo, o dispositivo de borda Azure Stack pode ser acessado pela rede interna do cliente e requer um IP configurado pelo cliente. Além disso, uma senha de acesso é escolhida pelo cliente para acessar a interface do usuário do dispositivo. 

O tráfego interno é mais protegido por:

- A versão 1,2 do protocolo TLS é necessária para o gerenciamento de portal do Azure e SDK do dispositivo Azure Stack Edge.

- Qualquer acesso de cliente ao dispositivo é por meio da interface do usuário da Web local usando o TLS 1,2 padrão como o protocolo de segurança padrão.

- Somente um dispositivo autorizado Azure Stack Edge Pro tem permissão para ingressar no serviço do Azure Stack Edge que o cliente cria em sua assinatura do Azure.

Há informações adicionais disponíveis nos links referenciados.
 
- [Configurar o TLS 1,2 em clientes Windows acessando Azure Stack dispositivo de GPU pro de borda](./azure-stack-edge-gpu-configure-tls-settings.md)

- [Início rápido-Introdução ao Azure Stack Edge pro com GPU](azure-stack-edge-gpu-quickstart.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ns-2-connect-private-networks-together"></a>NS-2: conectar redes privadas juntas

**Orientação**: os clientes podem escolher uma VPN (rede virtual privada) ponto a site para conectar um dispositivo de borda Azure Stack de sua rede privada local à rede do Azure. A VPN fornece uma segunda camada de criptografia para os dados em movimento sobre a segurança da camada de transporte do dispositivo do cliente para o Azure. 

Os clientes podem configurar uma rede virtual privada em seu dispositivo de borda Azure Stack pelo portal do Azure ou por meio do Azure PowerShell.

- [Configurar a VPN do Azure via script Azure PowerShell para o Azure Stack Edge pro R e Azure Stack mini R do Edge](azure-stack-edge-mini-r-configure-vpn-powershell.md)

- [Configurar o TLS 1,2 em clientes Windows acessando Azure Stack dispositivo de GPU pro de borda](./azure-stack-edge-gpu-configure-tls-settings.md)

- [Tutorial: Configurar certificados para o Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: estabelecer o acesso de rede privada aos serviços do Azure

**Orientação**: os clientes podem escolher uma VPN (rede virtual privada) ponto a site para conectar um dispositivo de borda Azure Stack de sua rede privada local à rede do Azure. A VPN fornece uma segunda camada de criptografia para os dados em movimento sobre a segurança da camada de transporte do dispositivo do cliente para o Azure. 

- [Configurar a VPN do Azure via script Azure PowerShell para o Azure Stack Edge pro R e Azure Stack mini R do Edge](azure-stack-edge-mini-r-configure-vpn-powershell.md)

- [Configurar o TLS 1,2 em clientes Windows acessando Azure Stack dispositivo de GPU pro de borda](./azure-stack-edge-gpu-configure-tls-settings.md)

- [Tutorial: Configurar certificados para o Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: proteger aplicativos e serviços de ataques de rede externa

**Orientação**: o dispositivo de borda Azure Stack incorpora recursos padrão de proteção de rede do Windows Server, que não são configuráveis pelos clientes.

Os clientes podem optar por proteger sua rede privada conectada com Azure Stack dispositivo de borda de ataques externos usando uma solução de virtualização de rede, como um firewall com proteções de DDoS (negação de serviço distribuído) avançadas.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: implantar sistemas de detecção de intrusões/prevenção de intrusão (IDS/IPS)

**Orientação**: os pontos de extremidade usados pelo dispositivo Azure Stack Edge são todos gerenciados pela Microsoft. Os clientes são responsáveis por quaisquer controles adicionais que desejarem implantar em seus sistemas locais.

O dispositivo Azure Stack Edge usa seus próprios recursos de detecção de intrusão para proteger o serviço. 

- [Entender Azure Stack segurança de borda](azure-stack-edge-security.md)

- [Informações de porta para Azure Stack borda](azure-stack-edge-gpu-system-requirements.md)

- [Obter logs de detecção de intrusão de hardware e software](azure-stack-edge-gpu-troubleshoot.md#gather-advanced-security-logs)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="identity-management"></a>Gerenciamento de Identidades

*Para obter mais informações, confira o [Azure Security Benchmark: gerenciamento de identidades](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: gerenciar identidades de aplicativo de maneira segura e automática

**Diretrizes**: todos os dispositivos de borda Azure Stack têm automaticamente uma identidade gerenciada atribuída pelo sistema no Azure Active Directory (AD do Azure). Atualmente, a identidade gerenciada é usada para o gerenciamento de nuvem de máquinas virtuais hospedadas no Azure Stack Edge.

Os dispositivos Azure Stack Edge são inicializados em um estado bloqueado para acesso local. Para a conta de administrador do dispositivo local, você precisará se conectar ao seu dispositivo por meio da interface do usuário da Web local ou da interface do PowerShell e definir uma senha forte. Armazene e gerencie suas credenciais de administrador de dispositivo em um local seguro, como um Azure Key Vault e gire a senha de administrador de acordo com seus padrões organizacionais.

- [Proteger Azure Stack dispositivo de borda via senha](azure-stack-edge-security.md#protect-the-device-via-password)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: usar o SSO (logon único) do Azure AD para acesso ao aplicativo

**Orientação**: o logon único não tem suporte para dispositivos de ponto de extremidade do Azure Stack Edge. No entanto, você pode optar por habilitar o logon único baseado no Azure Active Directory (Azure AD) padrão para proteger o acesso aos recursos de nuvem do Azure.

- [Entender o SSO do aplicativo com o Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: usar controles de autenticação forte para todo o acesso baseado no Azure Active Directory

**Diretrizes**: a autenticação multifator é um controle de autenticação forte, mas um recurso de aceitação para os usuários do serviço Azure Stack Edge. Ele pode ser utilizado para cenários com suporte, como o gerenciamento de borda de dispositivos Azure Stack Edge no portal do Azure. Observe que o acesso local aos dispositivos de borda Azure Stack não dá suporte à autenticação multifator.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: monitorar anomalias nas contas e fornecer alertas sobre elas

**Diretrizes**: habilite Azure monitor para coletar logs de dispositivo ou de contêiner para o serviço do Azure Stack Edge. Monitore contêineres, como aqueles que executam vários aplicativos de computação no cluster kubernetes em seu dispositivo.

Além disso, um pacote de suporte, que inclui logs de auditoria, pode ser coletado na interface do usuário da Web local do dispositivo Azure Stack Edge. Observe que o pacote de suporte não está disponível no portal do Azure.
 
- [Habilitar Azure Monitor em seu dispositivo do Azure Stack Edge pro](azure-stack-edge-gpu-enable-azure-monitor.md) 

- [Reunir um pacote de suporte](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: restringir o acesso aos recursos do Azure com base em condições

**Diretrizes**: o acesso condicional do Azure Active Directory (AD do Azure) é um recurso opcional para autenticação para Azure Stack serviço do Edge. Azure Stack serviço do Edge é um recurso no portal do Azure que permite gerenciar um dispositivo do Azure Stack Edge pro em diferentes localizações geográficas. 

- [O que é o acesso condicional](../active-directory/conditional-access/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: eliminar a exposição involuntária de credenciais

**Orientação**: siga as práticas recomendadas para proteger as credenciais, como:

- Chave de ativação usada para ativar o dispositivo com o Azure Stack recurso de borda no Azure.
- Credenciais de entrada para acessar o dispositivo do Azure Stack Edge.
- Arquivos de chave que podem facilitar uma recuperação de Azure Stack dispositivo de borda.
- Chave de criptografia do canal

Gire e sincronize suas chaves de conta de armazenamento regularmente para ajudar a proteger sua conta de armazenamento de usuários não autorizados.

- [Segurança e proteção de dados do Azure Stack Edge pro](azure-stack-edge-security.md)

- [Sincronizar chaves de armazenamento](azure-stack-edge-manage-shares.md#sync-storage-keys)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="privileged-access"></a>Acesso privilegiado

*Para obter mais informações, confira o [Azure Security Benchmark: acesso privilegiado](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: restringir o acesso administrativo a sistemas críticos para os negócios

**Diretrizes**: a solução Azure Stack Edge tem vários componentes com controles de acesso fortes para restringir o acesso a dispositivos críticos de negócios. Sua organização precisará de um Contrato Enterprise (EA) ou provedor de soluções na nuvem (CSP) ou uma assinatura Microsoft Azure Sponsorship para configurar e gerenciar o dispositivo: 

Azure Stack serviço do Edge é protegido pelos recursos de segurança do Azure como um serviço de gerenciamento hospedado no Azure. Você pode obter a chave de criptografia para seu recurso nas propriedades do dispositivo para qualquer Software Development Kit operações de gerenciamento, mas pode exibir a chave de criptografia somente se você tiver as permissões apropriadas para o API do Graph de recursos.

O dispositivo pro Edge Azure Stack é um dispositivo local que ajuda a transformar seus dados processando-os localmente e, em seguida, enviando-os ao Azure. Seu dispositivo:

- precisa de uma chave de ativação para acessar o serviço do Azure Stack Edge.
- o é protegido sempre por uma senha de dispositivo.
- tem inicialização segura habilitada.

- é um dispositivo bloqueado. O BMC (controlador de gerenciamento de placa-base) do dispositivo e o BIOS são protegidos por senha. O BIOS é protegido por acesso limitado ao usuário.
- executa o Windows Defender Device Guard. A proteção de dispositivo permite que você execute somente aplicativos confiáveis que você define em suas políticas de integridade de código.

Siga as práticas recomendadas para proteger todas as credenciais e segredos usados para acessar Azure Stack Edge. 

- [Práticas recomendadas para senha](azure-stack-edge-security.md#protect-the-device-via-password)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: examinar e reconciliar regularmente o acesso do usuário

**Orientação**: Azure Stack Edge tem um usuário chamado ' EdgeUser ' que pode configurar o dispositivo. Ele também tem Azure Resource Manager usuário ' EdgeArmUser ' para os recursos de Azure Resource Manager locais no dispositivo. 

As práticas recomendadas devem ser seguidas para proteger o:

- Credenciais usadas para acessar o dispositivo local

- Credenciais de compartilhamento SMB.

- Acesso a sistemas cliente que foram configurados para usar compartilhamentos NFS.

- Chaves de conta de armazenamento local usadas para acessar as contas de armazenamento local ao usar a API REST de BLOB.

Informações adicionais estão disponíveis no link referenciado.

- [Informações sobre segurança para seus dispositivos Azure Stack Edge](azure-stack-edge-security.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: usar estações de trabalho com acesso privilegiado

**Diretrizes**: estações de trabalho seguras e isoladas são extremamente importantes para a segurança de funções confidenciais, como administradores, desenvolvedores e operadores de serviço críticos. Use estações de trabalho de usuário altamente protegidas com ou sem a bastiões do Azure para tarefas administrativas. Use o Azure Active Directory (Azure AD), a ATP (proteção avançada contra ameaças) do Microsoft defender e o Microsoft Intune para implantar uma estação de trabalho de usuário segura e gerenciada para tarefas administrativas. 

As estações de trabalho protegidas podem ser gerenciadas de modo central para impor a configuração protegida, incluindo autenticação forte, linhas de base de software e hardware, acesso lógico restrito e de rede.

- [Entender as estações de trabalho com acesso privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Implantar uma estação de trabalho com acesso privilegiado](/security/compass/privileged-access-deployment)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, confira o [Azure Security Benchmark: proteção de dados](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: proteger dados confidenciais

**Diretrizes**: Azure Stack Edge trata todos os dados interagindo como confidenciais com apenas usuários autorizados que têm acesso a esses dados. Você deve seguir as práticas recomendadas para proteger as credenciais usadas para acessar o serviço do Azure Stack Edge.

- [Segurança e proteção de dados do Azure Stack Edge pro](azure-stack-edge-security.md#protect-the-device-via-password)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: criptografar informações confidenciais em trânsito

**Orientação**: Azure Stack Edge usa canais seguros para dados em trânsito. Eles são:

- O TLS 1,2 padrão é usado para dados que trafegam entre o dispositivo e a nuvem do Azure. Não há nenhum fallback para o TLS 1,1 e anterior. A comunicação do agente será bloqueada se o TLS 1,2 não tiver suporte. O TLS 1,2 também é necessário para o gerenciamento de portal do Azure e Software Development Kit (SDK).

- Quando os clientes acessam seu dispositivo por meio da interface do usuário da Web local de um navegador, o TLS 1,2 padrão é usado como o protocolo de segurança padrão

A prática recomendada é configurar o navegador para usar o TLS 1,2. Use o SMB 3,0 com criptografia para proteger os dados ao copiá-los de seus servidores de dados.

- [Práticas recomendadas para proteger dados em trânsito](azure-stack-edge-security.md#protect-data-in-flight)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="asset-management"></a>Gerenciamento de Ativos

*Para obter mais informações, confira o [Azure Security Benchmark: gerenciamento de ativos](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: garantir que a equipe de segurança tenha visibilidade dos riscos de ativos

**Diretriz**: verifique se as equipes de segurança receberam as permissões Leitor de segurança no seu locatário e nas suas assinaturas do Azure, de modo que possam monitorar os riscos de segurança usando a Central de Segurança do Azure. 

Dependendo de como as responsabilidades da equipe de segurança são estruturadas, o monitoramento dos riscos de segurança pode ser a responsabilidade de uma equipe de segurança central ou de uma equipe local. Dito isso, os insights e os riscos de segurança sempre precisam ser agregados de maneira centralizada em uma organização. 

As permissões de Leitor de segurança podem ser aplicadas amplamente a um locatário inteiro (grupo de gerenciamento raiz) ou terem como escopo grupos de gerenciamento ou assinaturas específicas. 

Observe que podem ser necessárias permissões adicionais para obter visibilidade de cargas de trabalho e serviços. 

- [Visão geral da função Leitor de segurança](../role-based-access-control/built-in-roles.md#security-reader)

- [Visão geral dos grupos de gerenciamento do Azure](../governance/management-groups/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Diretrizes**: somente usuários autorizados, por exemplo, o ' EdgeArmUser ', podem acessar as APIs de dispositivo do Azure Stack Edge por meio do Azure Resource Manager local. As senhas de conta de usuário só podem ser gerenciadas no portal do Azure. 

- [Configurar a senha do Azure Resource Manager](/azure/databox-online/azure-stack-edge-gpu-set-azure-resource-manager-password)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6: usar somente aplicativos aprovados em recursos de computação

**Orientação**: você pode colocar seus próprios aplicativos para executar em qualquer máquina virtual criada localmente. Use scripts do PowerShell para criar máquinas virtuais de computação local em seu dispositivo de borda de pilha. É altamente recomendável que você coloque somente aplicativos confiáveis para executar nas máquinas virtuais locais. 

- [Como controlar a execução de script do PowerShell no ambiente do Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-threat-detection"></a>Log e detecção de ameaças

*Para obter mais informações, confira o [Azure Security Benchmark: log e detecção de ameaças](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: habilitar a detecção de ameaças para recursos do Azure

**Orientação**: o Azure Stack Edge não oferece recursos de detecção de ameaças. No entanto, os clientes podem coletar logs de auditoria em um pacote de suporte para detecção e análise de ameaças offline. 

- [Coletar pacote de suporte para o dispositivo Azure Stack Edge](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: habilitar o registro em log para atividades de rede do Azure

**Orientação**: Azure Stack Edge tem logs de auditoria de rede habilitados como parte do pacote de suporte baixável. Esses logs podem ser analisados para implementar um monitoramento de tempo semireal para seus dispositivos Azure Stack Edge.

- [Azure Stack o Edge Reúna um pacote de suporte](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: habilitar o registro em log para recursos do Azure

**Diretrizes**: atualmente, não há suporte para o monitoramento em tempo real com logs no Azure Stack Edge. Existe um recurso para coletar um pacote de suporte que permite analisar os vários logs incluídos nele, como firewall, software, intrusão de hardware e logs de eventos do sistema para seu dispositivo Azure Stack Edge pro. Observe que a invasão de software ou os logs de firewall padrão são coletados para tráfego de entrada e de saída.

- [Coletar um pacote de suporte para o Azure Stack Edge](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: centralizar o gerenciamento e a análise do log de segurança

**Diretrizes**: atualmente, não há suporte para o monitoramento em tempo real com logs no Azure Stack Edge. No entanto, você pode coletar um pacote de suporte que permite analisar os vários logs incluídos nele.  O pacote de suporte é compactado e é baixado para o caminho de sua escolha. Descompacte o pacote e exiba os arquivos de log do sistema contidos nele. Você também pode enviar esses logs para uma ferramenta de gerenciamento de eventos de informações de segurança ou outro local de armazenamento central para análise.

- [Coletar um pacote de suporte para o Azure Stack Edge](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: configurar a retenção do armazenamento de log

**Diretrizes**: o período de retenção do armazenamento de log não pode ser alterado no dispositivo Azure Stack Edge. Os logs mais antigos são limpos conforme necessário. Você pode coletar pacotes de suporte do dispositivo em intervalos periódicos para quaisquer requisitos para manter os logs por um período de tempo maior. 

- [Coletar um pacote de suporte para o Azure Stack Edge](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="lt-7-use-approved-time-synchronization-sources"></a>LT-7: usar fontes de sincronização de tempo aprovadas

**Orientação**: Azure Stack Edge usa o time.Windows.com, um servidor de provedor de tempo de rede da Microsoft.  O Azure Stack Edge também permite que o cliente configure o servidor de protocolo de tempo de rede de sua escolha.

- [Definir configurações de hora do dispositivo Azure Stack Edge](azure-stack-edge-gpu-deploy-set-up-device-update-time.md#configure-time)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, confira o [Azure Security Benchmark: resposta a incidentes](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: preparação – atualizar o processo de resposta a incidentes do Azure

**Orientação**: Verifique se o seu plano de resposta a incidentes organizacionais inclui processos: 

- para responder a incidentes de segurança

- que foram atualizados para a nuvem do Azure
 
- são regularmente exercidos para garantir a preparação

É recomendável implementar a segurança usando processos de resposta a incidentes padronizados em todo o ambiente corporativo.

- [Implementar a segurança em todo o ambiente empresarial](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guia de referência da resposta a incidentes](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: preparação – configurar a notificação de incidentes

**Diretriz**: configure as informações de contato de incidentes de segurança na Central de Segurança do Azure. Essas informações de contato serão usadas pela Microsoft para entrar em contato com você se o MSRC (Microsoft Security Response Center) descobrir que os seus dados foram acessados por uma pessoa não autorizada ou ilegal. Você também tem opções para personalizar o alerta de incidente e a notificação em diferentes serviços do Azure de acordo com as suas necessidades de resposta a incidentes. 

- [Como definir o contato de segurança da Central de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: detecção e análise – crie incidentes com base em alertas de alta qualidade 

**Orientação**: Verifique se você tem um processo para criar alertas de alta qualidade e para medir sua qualidade. Isso permite que você aprenda as lições dos últimos incidentes e Priorize os alertas para analistas, para evitar desperdício de tempo processando alertas positivos falsos. Crie alertas de alta qualidade com base em suas experiências de incidentes anteriores, fontes de comunidade validadas e ferramentas criadas para gerar e limpar alertas com registro em log e correlação para diversas fontes de alerta. 

A central de segurança do Azure fornece alertas de alta qualidade em vários ativos do Azure. Você pode usar o conector de dados da central de segurança para transmitir os alertas para o Azure Sentinel. Crie regras de alerta avançadas com o Azure Sentinel para gerar incidentes automáticos para uma investigação. 

Exporte seus alertas e recomendações da central de segurança no Azure Sentinel com o recurso de exportação para ajudar a identificar os riscos para os recursos do Azure. É recomendável criar um processo para exportar alertas e recomendações de maneira automatizada para segurança contínua.

- [Como configurar a exportação](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Detecção e análise – investigar um incidente

**Orientação**: garanta que os analistas possam consultar e usar diversas fontes de dados para criar uma visão completa da atividade que ocorreu durante a investigação de incidentes potenciais. Diversos tipos de log devem ser coletados para acompanhar as atividades de um invasor potencial na cadeia de Kill para evitar manchas cegas.  Além disso, verifique se as informações e os aprendizados são capturados para referência histórica.  

As fontes de dados para investigação incluem as fontes de log centralizadas que já estão sendo coletadas dos serviços no escopo e dos sistemas em execução, mas também podem incluir:

- Dados de rede – Use os logs de fluxo dos grupos de segurança de rede, o Observador de Rede do Azure e o Azure Monitor para capturar logs de fluxo de rede e outras informações de análise. 

- Instantâneos dos sistemas em execução: 

    - Use a funcionalidade de instantâneo da máquina virtual do Azure para criar um instantâneo do disco do sistema em execução. 

    - Escolha o recurso de despejo de memória nativo do sistema operacional para criar um instantâneo da memória do sistema em execução.

    - Use o recurso de instantâneo dos serviços do Azure ou um recurso de software de terceiros para criar instantâneos dos sistemas em execução.

O Azure Sentinel fornece ampla análise de dados em praticamente qualquer origem de log e um portal de gerenciamento de casos para gerenciar o ciclo de vida completo de incidentes. As informações de inteligência coletadas durante uma investigação podem ser associadas a um incidente para fins de rastreamento e relatório. 

- [Criar um instantâneo de disco de um computador Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Criar um instantâneo de disco de um computador Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Informações de diagnóstico e coleta de despejo de memória do Suporte do Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Investigar incidentes com o Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: detecção e análise – priorizar incidentes

**Diretriz**: forneça aos analistas o contexto dos incidentes nos quais eles devem se concentrar primeiro com base na severidade do alerta e na confidencialidade do ativo. Use a severidade atribuída a cada alerta da central de segurança do Azure para ajudá-lo a priorizar quais alertas devem ser investigados primeiro. A severidade se baseia na confiança que a Central de Segurança tem na conclusão ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que gerou o alerta.

Além disso, marque os recursos usando marcas e crie um sistema de nomenclatura para identificar e categorizar os recursos do Azure, em especial aqueles que processam dados confidenciais.  É sua responsabilidade priorizar a correção de alertas com base na criticalidade dos recursos do Azure e do ambiente em que o incidente ocorreu.

- [Alertas na Central de Segurança do Azure](../security-center/security-center-alerts-overview.md)

- [Usar marcas para organizar seus recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: confinamento, erradicação e recuperação – automatizar o tratamento de incidentes

**Diretriz**: automatize tarefas repetitivas manuais para acelerar o tempo de resposta e reduzir a carga dos analistas. As tarefas manuais demoram mais para serem executadas, atrasando cada incidente e reduzindo o número de incidentes que um analista pode processar. Elas também aumentam a fadiga do analista, o que aumenta o risco de erros humanos que causam atrasos, prejudicando a capacidade dos analistas de se concentrarem efetivamente em tarefas complexas. Use os recursos de automação de fluxo de trabalho da Central de Segurança do Azure e do Azure Sentinel para disparar ações automaticamente ou executar um guia estratégico para responder aos alertas de segurança recebidos. O guia estratégico executa ações, como enviar notificações, desabilitar contas e isolar redes problemáticas. 

- [Configurar a automação de fluxo de trabalho na Central de Segurança](../security-center/workflow-automation.md)

- [Configurar respostas automatizadas a ameaças na Central de Segurança do Azure](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Configurar respostas de ameaças automatizadas no Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="posture-and-vulnerability-management"></a>Gerenciamento de postura e vulnerabilidades

*Para obter mais informações, confira o [Azure Security Benchmark: gerenciamento de postura e vulnerabilidades](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md).*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: estabelecer configurações seguras para recursos de computação

**Diretrizes**: o Azure Stack Edge oferece suporte para criar configurações seguras para as máquinas virtuais locais que são criadas pelos clientes. É altamente recomendável usar as diretrizes fornecidas pela Microsoft para estabelecer linhas de base de segurança ao criar máquinas virtuais locais,

- [Baixar as linhas de base de segurança incluídas no Security Compliance Toolkit](/windows/security/threat-protection/windows-security-baselines)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pv-4-sustain-secure-configurations-for-compute-resources"></a>PV-4: sustentar configurações seguras para recursos de computação

**Orientação**: o Azure Stack Edge não oferece nenhum suporte para sustentar configurações seguras para as máquinas virtuais locais que foram criadas pelos clientes. É altamente recomendável que os clientes usem o SCT (Security Compliance toolkits) para ajudar a sustentar configurações seguras para seus recursos de computação.

O sistema operacional do host e as máquinas virtuais gerenciadas pelo Azure Stack Edge mantêm suas configurações de segurança. 

- [Linhas de base de segurança do Windows](/windows/security/threat-protection/windows-security-baselines#using-security-baselines-in-your-organization)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="pv-5-securely-store-custom-operating-system-and-container-images"></a>PV-5: armazenar com segurança imagens de contêiner e do sistema operacional personalizado

**Orientação**: sistemas operacionais de host e máquinas virtuais gerenciados pelo Azure Stack Edge são armazenados com segurança. 

Os clientes podem trazer suas próprias imagens de máquina virtual e contêineres e são responsáveis pelo gerenciamento seguro.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7: corrigir de maneira rápida e automática as vulnerabilidades de software

**Orientação**: Azure Stack Edge fornece atualizações de patch regulares e alertas de clientes quando essas atualizações estão disponíveis para corrigir vulnerabilidades. É responsabilidade do cliente manter seus dispositivos e máquinas virtuais (criados por eles) atualizados com os patches mais recentes.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: realizar uma simulação de ataque regular

**Diretriz**: conforme necessário, realize testes de penetração ou atividades de equipe vermelhas nos seus recursos do Azure e garanta a correção de todas as conclusões de segurança críticas.
Siga as Regras de Participação no Teste de Penetração do Microsoft Cloud para garantir que os testes de penetração não violem as políticas da Microsoft. Use a estratégia da Microsoft, a execução de Equipes Vermelhas e os testes de penetração de sites online na infraestrutura, nos serviços e nos aplicativos de nuvem gerenciados pela Microsoft.

- [Teste de penetração no Azure](../security/fundamentals/pen-testing.md)

- [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="endpoint-security"></a>Segurança de ponto de extremidade

*Para obter mais informações, consulte o [benchmark de segurança do Azure: segurança do ponto de extremidade](../security/benchmarks/security-controls-v2-endpoint-security.md).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: usar a detecção e resposta de ponto de extremidade (EDR)

**Orientação**: o Azure Stack Edge não dá suporte diretamente à detecção e à resposta do ponto de extremidade (EDR). No entanto, você pode coletar um pacote de suporte e recuperar logs de auditoria. Esses logs podem ser analisados para verificar se há atividades anormais. 

- [Coletar pacote de suporte para o dispositivo Azure Stack Edge](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2: usar software antimalware moderno gerenciado centralmente

**Orientação**: o Azure Stack Edge usa o Windows Defender Application Control (WDAC) com uma política de CI (integridade de código estrita) que permite que apenas aplicativos e scripts predeterminados sejam executados. O antimalware do RTP (proteção em tempo real) do Windows Defender também está habilitado. O cliente pode optar por executar o anti-malware nas VMs de computação que eles criam para serem executados localmente no dispositivo Azure Stack Edge.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="backup-and-recovery"></a>Backup e recuperação

*Para obter mais informações, confira o [Azure Security Benchmark: backup e recuperação](../security/benchmarks/security-controls-v2-backup-recovery.md).*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: garantir backups automatizados regulares

**Orientação**: os backups periódicos de seu dispositivo Azure Stack Edge são recomendados e podem ser executados com o backup do Azure e outras soluções de proteção de dados de terceiros para proteger os dados contidos nas máquinas virtuais implantadas no dispositivo. 

Soluções de proteção de dados de terceiros, como Cohesity, CommVault e Veritas, também podem fornecer uma solução de backup para os dados nos compartilhamentos SMB locais ou NFS,. 

Há informações adicionais disponíveis nos links referenciados.

- [Preparar para uma falha do dispositivo](azure-stack-edge-gpu-prepare-device-failure.md)

- [Proteger arquivos e pastas em VMs](azure-stack-edge-gpu-prepare-device-failure.md#protect-files-and-folders-on-vms)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="br-2-encrypt-backup-data"></a>BR-2: criptografar dados de backup

**Orientação**: Verifique se os backups estão protegidos contra ataques. Isso deve incluir a criptografia dos backups para proteger contra perda de confidencialidade.  Para obter mais informações, consulte sua solução de backup de sua escolha para obter detalhes.

- [Proteger dados em compartilhamentos locais de borda](azure-stack-edge-gpu-prepare-device-failure.md#protect-data-in-edge-local-shares)

- [Proteger arquivos e pastas em máquinas virtuais](azure-stack-edge-gpu-prepare-device-failure.md#protect-files-and-folders-on-vms)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Diretrizes**: execute periodicamente a restauração de dados de seus backups. 

- [Procedimentos de recuperação para o Azure Stack Edge](azure-stack-edge-gpu-recover-device-failure.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: reduzir o risco de perda de chaves

**Orientação**: garanta que todos os backups de borda Azure Stack, incluindo quaisquer chaves gerenciadas pelo cliente, sejam protegidos de acordo com as práticas recomendadas da organização. O dispositivo do Azure Stack Edge está associado a uma conta de armazenamento do Azure, usada como um repositório de destino para seus dados no Azure. 

O acesso à conta de armazenamento do Azure é controlado pelas assinaturas do Azure e pelas chaves de acesso de armazenamento de 2 512 bits associadas a essa conta de armazenamento. Uma das chaves de acesso é usada para fins de autenticação quando o dispositivo de borda Azure Stack acessa a conta de armazenamento. A outra chave é mantida em reservada para rotação de chave periódica. Verifique se as práticas recomendadas de segurança são usadas para a rotação de chaves.

- [Proteger Azure Stack dados do dispositivo de borda nas contas de armazenamento do Azure](azure-stack-edge-pro-r-security.md#protect-data-in-storage-accounts)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="governance-and-strategy"></a>Governança e estratégia

*Para obter mais informações, confira o [Azure Security Benchmark: governança e estratégia](../security/benchmarks/security-controls-v2-governance-strategy.md).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: definir a estratégia de proteção de dados e gerenciamento de ativos 

**Diretriz**: documente e comunique uma estratégia clara para o monitoramento contínuo e a proteção de sistemas e dados. Priorize a descoberta, a avaliação, a proteção e o monitoramento de sistemas e dados comercialmente críticos. 

Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos: 

-   Padrão de classificação de dados de acordo com os riscos de negócios

-   Visibilidade da organização de segurança dos riscos e do inventário de ativos 

-   Aprovação da organização de segurança dos serviços do Azure para uso 

-   Segurança de ativos por meio do ciclo de vida

-   Estratégia de controle de acesso obrigatório de acordo com a classificação de dados organizacionais

-   Uso de funcionalidades de proteção de dados de terceiros e nativas do Azure

-   Requisitos de criptografia de dados para casos de uso em trânsito e em repouso

-   Padrões de criptografia apropriados

Para saber mais, consulte as referências a seguir:
- [Recomendação da arquitetura de segurança do Azure – Armazenamento, dados e criptografia](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Conceitos básicos de segurança do Azure – Segurança, criptografia e armazenamento de dados do Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework – Melhores práticas de segurança e criptografia de dados do Azure](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Security Benchmark – Gerenciamento de ativos](../security/benchmarks/security-controls-v2-asset-management.md)

- [Azure Security Benchmark – Proteção de dados](../security/benchmarks/security-controls-v2-data-protection.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: definir uma estratégia de segmentação corporativa 

**Diretriz**: estabeleça uma estratégia de toda a empresa para segmentar o acesso aos ativos usando uma combinação de identidade, rede, aplicativo, assinatura, grupo de gerenciamento e outros controles.

Equilibre cuidadosamente a necessidade de separação de segurança com a necessidade de habilitação da operação diária dos sistemas que precisam se comunicar entre si e acessar dados.

Garanta que a estratégia de segmentação seja implementada de maneira consistente nos tipos de controle, incluindo segurança de rede, modelos de identidade e acesso, além de modelos de acesso/permissão de aplicativo e controles de processos humanos.

- [Diretrizes sobre a estratégia de segmentação no Azure (vídeo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Diretrizes sobre a estratégia de segmentação no Azure (documento)](/security/compass/governance#enterprise-segmentation-strategy)

- [Alinhar a segmentação de rede com a estratégia de segmentação corporativa](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: definir uma estratégia de gerenciamento de postura de segurança

**Diretriz**: Meça e atenue continuamente os riscos para seus ativos individuais e o ambiente no qual eles estão hospedados. Priorize os ativos de alto valor e as superfícies de ataque altamente expostas, como aplicativos publicados, pontos de entrada e saída de rede, pontos de extremidade de usuário e administrador etc.

- [Azure Security Benchmark – Gerenciamento de postura e vulnerabilidade](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: alinhar funções e responsabilidades da organização

**Diretriz**: documente e comunique uma estratégia clara para funções e responsabilidades na sua organização de segurança. Priorize o fornecimento de responsabilidade clara por decisões de segurança, educando todos no modelo de responsabilidade compartilhada e instrua equipes técnicas sobre a tecnologia usada para proteger a nuvem.

- [Melhor prática de segurança do Azure 1 – Pessoas: educar as equipes na jornada de segurança na nuvem](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Melhor prática de segurança do Azure 2 – Pessoas: educar as equipes na tecnologia de segurança na nuvem](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Melhor prática de segurança do Azure 3 – Processo: atribuir responsabilidade por decisões de segurança na nuvem](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-5-define-network-security-strategy"></a>GS-5: definir uma estratégia de segurança de rede

**Diretriz**: estabeleça uma abordagem de segurança de rede do Azure como parte da estratégia geral de controle de acesso de segurança da sua organização.  

Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos: 

-   Gerenciamento de rede centralizado e responsabilidade pela segurança

-   Modelo de segmentação de rede virtual alinhado com a estratégia de segmentação corporativa

-   Estratégia de correção em diferentes cenários de ameaças e ataques

-   Estratégia de entrada e saída e borda da Internet

-   Estratégia de interconectividade local e de nuvem híbrida

-   Artefatos de segurança de rede atualizados (por exemplo, diagramas de rede, arquitetura de rede de referência)

Para saber mais, consulte as referências a seguir:
- [Melhor prática de segurança do Azure 11 – Arquitetura: uma estratégia de segurança unificada](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark – Segurança de rede](../security/benchmarks/index.yml)

- [Visão geral da segurança de rede do Azure](../security/fundamentals/network-overview.md)

- [Estratégia de arquitetura de rede corporativa](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: definir uma estratégia de identidade e acesso privilegiado

**Diretriz**: estabeleça abordagens de identidade e acesso privilegiado do Azure como parte da estratégia geral de controle de acesso de segurança da sua organização.  

Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos: 

-   Um sistema de identidade e autenticação centralizado e a interconectividade dele com outros sistemas de identidade internos e externos

-   Métodos de autenticação forte em diferentes casos de uso e condições

-   Proteção de usuários altamente privilegiados

-   Monitoramento e tratamento de atividades de usuário de anomalias  

-   Processo de revisão de acesso e reconciliação e identidade do usuário

Para saber mais, consulte as referências a seguir:

- [Azure Security Benchmark – Gerenciamento de identidades](../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure Security Benchmark – Acesso privilegiado](../security/benchmarks/security-controls-v2-privileged-access.md)

- [Melhor prática de segurança do Azure 11 – Arquitetura: uma estratégia de segurança unificada](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Visão geral da segurança de gerenciamento de identidade do Azure](../security/fundamentals/identity-management-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: definir uma estratégia de log e resposta a ameaças

**Diretriz**: estabeleça uma estratégia de log e resposta a ameaças para detectar e corrigir as ameaças rapidamente, atendendo aos requisitos de conformidade. Priorize o fornecimento de alertas de alta qualidade e experiências perfeitas aos analistas para que eles possam se concentrar em ameaças em vez de etapas manuais e de integração. 

Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos: 

-   A função e as responsabilidades da organização de SecOps (operações de segurança) 

-   Um processo de resposta a incidentes bem definido, alinhando-se ao NIST ou a outra estrutura do setor 

-   Captura e retenção de log para dar suporte às necessidades de conformidade, detecção de ameaças e resposta a incidentes

-   Visibilidade centralizada das ameaças e informações de correlação sobre elas por meio do SIEM, de funcionalidades nativas do Azure e de outras fontes 

-   Plano de comunicação e notificação com seus clientes, fornecedores e partes públicas interessadas

-   Uso de plataformas nativas e de terceiros do Azure para tratamento de incidentes, como log e detecção de ameaças, análise forense, além de correção e erradicação de ataques

-   Processos para tratamento de incidentes e atividades pós-incidente, como lições aprendidas e retenção de evidências

Para saber mais, consulte as referências a seguir:

- [Azure Security Benchmark – Log e detecção de ameaças](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure Security Benchmark – Resposta a incidentes](../security/benchmarks/security-controls-v2-incident-response.md)

- [Melhor prática de segurança do Azure 4 – Processo: atualizar processos de resposta a incidentes para a nuvem](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guia de log, de decisão de relatórios e do Adoption Framework do Azure](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="next-steps"></a>Próximas etapas

- Confira a [Visão geral do Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Saiba mais sobre a [Linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)