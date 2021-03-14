---
title: Melhores práticas de segurança da Área de Trabalho Virtual do Windows – Azure
description: Melhores práticas para manter a segurança do seu ambiente da Área de Trabalho Virtual do Windows.
author: heidilohr
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0ddbd4b798d37498af92cec40af6a80a88115fab
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103014886"
---
# <a name="security-best-practices"></a>Melhores práticas de segurança

A Área de Trabalho Virtual do Windows é um serviço de área de trabalho virtual gerenciado que inclui várias funcionalidades de segurança para manter sua organização segura. Em uma implantação da Área de Trabalho Virtual do Windows, a Microsoft gerencia partes dos serviços e nome do cliente. O serviço tem vários recursos internos de segurança avançada, como a Conexão Reversa, que reduz o risco envolvido em ter áreas de trabalho remotas acessíveis de qualquer lugar.

Este artigo descreve as etapas adicionais que você pode executar como administrador para manter seguras as implantações de Área de Trabalho Virtual do Windows de seus clientes.

## <a name="security-responsibilities"></a>Responsabilidades de segurança

O que torna os serviços de nuvem diferentes das VDIs (infraestruturas de área de trabalho virtuais) locais tradicionais é a forma como eles lidam com as responsabilidades de segurança. Por exemplo, em uma VDI local tradicional, o cliente seria responsável por todos os aspectos da segurança. Porém, na maioria dos serviços de nuvem, essas responsabilidades são compartilhadas entre o cliente e a empresa.

Quando você usa a Área de Trabalho Virtual do Windows, é importante entender que, embora alguns componentes já estejam protegidos para seu ambiente, você mesmo precisará configurar outras áreas para se adequar às necessidades de segurança da sua organização.

Estas são as necessidades de segurança pelas quais você é responsável na implantação da sua Área de Trabalho Virtual do Windows:

| Necessidade de segurança | O cliente é responsável por isso? |
|---------------|:-------------------------:|
|Identidade|Sim|
|Dispositivos de usuário (móvel e PC)|Sim|
|Segurança do aplicativo|Sim|
|SO do host da sessão|Sim|
|Configuração de implantação|Sim|
|Controles de rede|Sim|
|Plano de controle de virtualização|Não|
|Hosts físicos|Não|
|Rede física|Não|
|Datacenter físico|Não|

As necessidades de segurança pelas quais o cliente não é responsável são tratadas pela Microsoft.

## <a name="azure-security-best-practices"></a>Melhores práticas de segurança do Azure

A Área de Trabalho Virtual do Windows é um serviço do Azure. Para maximizar a segurança de sua implantação de Área de Trabalho Virtual do Windows, você também deve garantir a proteção da infraestrutura e do plano de gerenciamento em torno do Azure. Para proteger sua infraestrutura, considere como a Área de Trabalho Virtual do Windows se adapta ao seu ecossistema maior do Azure. Para saber mais sobre o ecossistema do Azure, consulte [Padrões e melhores práticas de segurança do Azure](../security/fundamentals/best-practices-and-patterns.md).

Essa seção descreve as melhores práticas para proteger seu ecossistema do Azure.

### <a name="enable-azure-security-center"></a>Habilitar a Central de Segurança do Azure

Recomendamos que você habilite o nível Standard Central de Segurança do Azure Standard para assinaturas, máquinas virtuais, cofres de chaves e contas de armazenamento.

Com o nível Standard da Central de Segurança do Azure, você pode:

* Gerenciar vulnerabilidades.
* Avaliar a conformidade com estruturas comuns como a PCI.
* Fortalecer a segurança geral do seu ambiente.

Para saber mais, consulte [Integrar sua assinatura do Azure ao Centro de Segurança Standard](../security-center/security-center-get-started.md).

### <a name="improve-your-secure-score"></a>Aprimorar sua classificação de segurança

A classificação segura fornece recomendações e conselhos de melhores práticas para melhorar sua segurança geral. Essas recomendações são priorizadas para ajudar você a escolher quais são as mais importantes, e as opções de Correção Rápida ajudam você a resolver vulnerabilidades potenciais rapidamente. Essas recomendações também são atualizadas com o passar do tempo, mantendo você atualizado sobre as melhores formas de manter a segurança do seu ambiente. Para saber mais, consulte [Melhorar sua classificação de segurança na Central de Segurança do Azure](../security-center/secure-score-security-controls.md).

## <a name="windows-virtual-desktop-security-best-practices"></a>Melhores práticas de segurança da Área de Trabalho Virtual do Windows

A Área de Trabalho Virtual do Windows tem muitos controles de segurança internos. Nesta seção, você aprenderá sobre os controles de segurança que pode usar para manter seus usuários e dados seguros.

### <a name="require-multi-factor-authentication"></a>Exigir autenticação multifator

Exigir a autenticação multifator para todos os usuários e administradores na Área de Trabalho Virtual do Windows melhora a segurança de toda a implantação. Para saber mais, confira [habilitar a autenticação multifator do Azure ad para área de trabalho virtual do Windows](set-up-mfa.md).

### <a name="enable-conditional-access"></a>Habilitar o Acesso Condicional

Habilitar o [Acesso Condicional](../active-directory/conditional-access/overview.md) permite que você gerencie os riscos antes de conceder acesso ao seu ambiente de Área de Trabalho Virtual do Windows. Ao decidir a quais usuários conceder acesso, recomendamos que você também considere quem é o usuário, como eles se conectam e qual dispositivo ele está usando.

### <a name="collect-audit-logs"></a>Coletar logs de auditoria

Habilitar a coleta de log de auditoria permite exibir a atividade de usuário e de administrador relacionada à Área de Trabalho Virtual do Windows. Alguns exemplos de logs de auditoria de chave são:

-   [Log de atividades do Azure](../azure-monitor/essentials/activity-log.md)
-   [Log de atividades do Azure Active Directory](../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md)
-   [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)
-   [Hosts de sessão](../azure-monitor/agents/agent-windows.md)
-   [Log de diagnóstico da Área de Trabalho Virtual do Windows](../virtual-desktop/diagnostics-log-analytics.md)
-   [Logs do Key Vault](../key-vault/general/logging.md)

### <a name="use-remoteapps"></a>Usar RemoteApps

Ao escolher um modelo de implantação, você pode fornecer aos usuários remotos o acesso a áreas de trabalho virtuais inteiras ou apenas a aplicativos selecionados. Os aplicativos remotos, ou RemoteApps, fornecem uma experiência perfeita à medida que o usuário trabalha com aplicativos em sua área de trabalho virtual. Os RemoteApps reduzem o risco ao permitir que o usuário trabalhe com um subconjunto do computador remoto exposto pelo aplicativo.

### <a name="monitor-usage-with-azure-monitor"></a>Monitorar o uso com o Azure Monitor

Monitore o uso e a disponibilidade do serviço de Área de Trabalho Virtual do Windows com o [Azure Monitor](https://azure.microsoft.com/services/monitor/). Cogite criar [alertas de integridade do serviço](../service-health/alerts-activity-log-service-notifications-portal.md) para o serviço de Área de Trabalho Virtual do Windows para receber notificações sempre que houver um evento de impacto no serviço.

## <a name="session-host-security-best-practices"></a>Melhores práticas de segurança do host da sessão

Os hosts de sessão são máquinas virtuais que são executadas dentro de uma assinatura e rede virtual do Azure. A segurança geral da implantação de Área de Trabalho Virtual do Windows depende dos controles de segurança que você colocou em seus hosts de sessão. Esta seção descreve as melhores práticas para manter seus hosts de sessão seguros.

### <a name="enable-screen-capture-protection-preview"></a>Habilitar proteção de captura de tela (visualização)

O recurso proteção de captura de tela impede que informações confidenciais sejam capturadas nos pontos de extremidade do cliente. Quando você habilita esse recurso, o conteúdo remoto será bloqueado automaticamente ou oculto em capturas de tela e compartilhamentos de telas. Ele também será ocultado de software mal-intencionado que pode estar capturando continuamente o conteúdo da tela. Recomendamos que você desabilite o redirecionamento da área de transferência para evitar a cópia de conteúdo remoto para pontos de extremidade ao usar esse recurso.

Essa política é imposta no nível do host por meio da configuração de uma chave do registro. Para habilitar essa política, abra o PowerShell e defina a chave do registro **fEnableScreenCaptureProtection** executando este cmdlet:

```powershell
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableScreenCaptureProtection /t REG_DWORD /d 1
```

Para testar esse novo recurso:

- Verifique se os pools de hosts estão provisionados no ambiente de validação.
- Verifique se você baixou e instalou o cliente de área de trabalho do Windows, versão 1.2.1526 ou posterior.

>[!NOTE]
>Durante a visualização, somente as conexões de área de trabalho completas dos pontos de extremidade do Windows 10 dão suporte a esse recurso.

### <a name="enable-endpoint-protection"></a>Habilitar a proteção de ponto de extremidade

Para proteger sua implantação contra softwares mal-intencionados conhecidos, é recomendável habilitar a proteção de ponto de extremidade em todos os hosts de sessão. Você pode usar o Windows Defender Antivírus ou um programa de terceiros. Para saber mais, consulte [Guia de implantação do Windows Defender Antivírus em um ambiente da VDI](/windows/security/threat-protection/windows-defender-antivirus/deployment-vdi-windows-defender-antivirus).

Para soluções de perfil como FSLogix ou outras soluções que montam arquivos VHD, é recomendável a exclusão de extensões de arquivo VHD.

### <a name="install-an-endpoint-detection-and-response-product"></a>Instalar um produto de detecção e resposta de ponto de extremidade

É recomendável que você instale um produto de EDR (detecção e resposta de ponto de extremidade) para fornecer recursos avançados de detecção e resposta. Para sistemas operacionais de servidor com a [Central de Segurança do Azure](../security-center/security-center-services.md) habilitada, a instalação de um produto de EDR implantará o Defender ATP. Para sistemas operacionais cliente, você pode implantar o [Defender ATP](/windows/security/threat-protection/microsoft-defender-atp/onboarding) ou um produto de terceiros para esses pontos de extremidade.

### <a name="enable-threat-and-vulnerability-management-assessments"></a>Habilitar avaliações de gerenciamento de ameaças e vulnerabilidades

A identificação de vulnerabilidades de software existentes em sistemas operacionais e aplicativos é essencial para manter seu ambiente seguro. A Central de Segurança do Azure pode ajudar você a identificar pontos de problemas por meio de avaliações de vulnerabilidade para sistemas operacionais de servidor. Você também pode usar o Defender ATP, que fornece gerenciamento de ameaças e vulnerabilidades para sistemas operacionais de área de trabalho. Você também pode usar produtos de terceiros caso prefira, embora seja recomendável usar a Central de Segurança do Azure e o Defender ATP.

### <a name="patch-software-vulnerabilities-in-your-environment"></a>Corrigir vulnerabilidades de software no seu ambiente

Assim que identificar uma vulnerabilidade, você deve corrigi-la. Isso também se aplica a ambientes virtuais, que inclui os sistemas operacionais em execução, os aplicativos implantados dentro deles e as imagens usadas para cria novos computadores. Siga suas comunicações de notificação de patch do fornecedor e aplique patches em tempo oportuno. É recomendável aplicar patches de suas imagens base mensalmente para garantir que os computadores implantados recentemente estejam o mais seguro possível.

### <a name="establish-maximum-inactive-time-and-disconnection-policies"></a>Estabelecer políticas de tempo máximo inativo e de desconexão

Ao desconectar usuários quando eles estão inativos, você preserva recursos e impede o acesso de usuários não autorizados. É recomendável que os tempos limite equilibrem a produtividade do usuário, bem como o uso de recursos. Para usuários que interagem com aplicativos sem estado, cogite usar políticas mais agressivas que desliguem computadores e preservem recursos. A desconexão de aplicativos de execução prolongada que continuam sendo executados caso um usuário esteja ocioso, como uma simulação ou renderização CAD, poderá interromper o trabalho do usuário e poderá até mesmo exigir a reinicialização do computador.

### <a name="set-up-screen-locks-for-idle-sessions"></a>Configurar bloqueios de tela para sessões ociosas

Você pode impedir o acesso indesejado ao sistema configurando a Área de Trabalho Virtual do Windows para bloquear a tela de um computador durante o tempo ocioso e exigir autenticação para desbloqueá-la.

### <a name="establish-tiered-admin-access"></a>Estabelecer o acesso de administrador em camadas

É recomendável que você não conceda aos usuários o acesso de administrador a áreas de trabalho virtuais. Caso precise de pacotes de software, é recomendável torná-los disponíveis por meio de utilitários de gerenciamento de configuração, como o Microsoft Endpoint Manager. Em um ambiente de várias sessões, é recomendável não permitir que os usuários instalem o software de modo direto.

### <a name="consider-which-users-should-access-which-resources"></a>Considere quais usuários devem acessar quais recursos

Pense nos hosts de sessão como uma extensão da implantação de área de trabalho existente. É recomendável que você controle o acesso a recursos de rede do mesmo modo que seria feito para outras áreas de trabalho do seu ambiente, como usar a segmentação e a filtragem de rede. Por padrão, os hosts de sessão podem ser conectados a qualquer recurso na Internet. Há várias maneiras de limitar o tráfego, incluindo o uso do Firewall do Azure, de proxies ou de soluções de virtualização de rede. Caso precise limitar o tráfego, adicione as regras apropriadas para que a Área de Trabalho Virtual do Windows possa funcionar adequadamente.

### <a name="manage-office-pro-plus-security"></a>Gerenciar a segurança do Office Pro Plus

Além de proteger seus hosts de sessão, também é importante proteger os aplicativos em execução dentro deles. O Office Pro Plus é um dos aplicativos implantados em hosts de sessão mais comuns. Para melhorar a segurança de implantação do Office, recomendamos que você use o [Orientador de política de segurança](/DeployOffice/overview-of-security-policy-advisor) para aplicativos Microsoft 365 para empresas. Essa ferramenta identifica as políticas que podem ser aplicadas à sua implantação para obter mais segurança. O Assistente de Política de Segurança também recomenda políticas com base no impacto delas na sua segurança e produtividade.

### <a name="other-security-tips-for-session-hosts"></a>Outras dicas de segurança para hosts de sessão

Ao restringir recursos do sistema operacional, você pode reforçar a segurança dos seus hosts de sessão. Aqui estão algumas coisas que você pode fazer:

- Controlar o redirecionamento de dispositivo ao redirecionar unidades, impressoras e dispositivos USB para o dispositivo local de um usuário em uma sessão de área de trabalho remota. É recomendável que você avalie os requisitos de segurança e verifique se esses recursos devam ser desabilitados ou não.

- Restrinja o acesso ao Windows Explorer ocultando os mapeamentos de unidade locais e remotas. Isso impede que os usuários descubram informações indesejadas sobre a configuração do sistema e sobre os usuários.

- Evite o acesso RDP direto a hosts de sessão no seu ambiente. Caso precise de acesso RDP direto para administração ou solução de problemas, habilite o [acesso just-in-time](../security-center/security-center-just-in-time.md) para limitar a possível superfície de ataque em um host de sessão.

- Conceda permissões limitadas aos usuários quando eles acessarem sistemas de arquivos locais e remotos. Você pode restringir as permissões ao fazer com que seus sistemas de arquivos locais e remotos usem listas de controle de acesso com privilégios mínimos. Dessa forma, os usuários só podem acessar aquilo de que precisam, não podendo alterar ou excluir recursos críticos.

- Impedir que um software indesejado seja executado em hosts de sessão. Você pode habilitar o Bloqueador de Aplicativo para segurança adicional em hosts de sessão, garantindo que apenas aplicativos permitidos possam ser executados no host.

## <a name="windows-virtual-desktop-support-for-trusted-launch"></a>Suporte de área de trabalho virtual do Windows para inicialização confiável

A inicialização confiável é Gen2 VMs do Azure com recursos de segurança aprimorados destinados a proteger contra ameaças de "fim da pilha" por meio de vetores de ataque, como rootkits, kits de inicialização e malware em nível de kernel. A seguir estão os recursos de segurança aprimorados da inicialização confiável, todos com suporte na área de trabalho virtual do Windows. Para saber mais sobre o lançamento confiável, visite [inicialização confiável para máquinas virtuais do Azure (versão prévia)](../virtual-machines/trusted-launch.md).

### <a name="secure-boot"></a>Inicialização Segura

A inicialização segura é um modo que o firmware de plataforma suporta para proteger seu firmware de rootkits baseados em malware e kits de inicialização. Esse modo permite apenas sistemas operacionais e drivers assinados para iniciar o computador. 

### <a name="monitor-boot-integrity-using-remote-attestation"></a>Monitorar a integridade da inicialização usando o atestado remoto

O atestado remoto é uma ótima maneira de verificar a integridade de suas VMs. O atestado remoto verifica se os registros de inicialização medidos estão presentes, são originais e se originam do Trusted Platform Module virtual (vTPM). Como uma verificação de integridade, ele fornece certeza de que uma plataforma foi iniciada corretamente. 

### <a name="vtpm"></a>vTPM

Um vTPM é uma versão virtualizada de um Trusted Platform Module de hardware (TPM), com uma instância virtual de um TPM por VM. o vTPM habilita o atestado remoto executando a medição de integridade de toda a cadeia de inicialização da VM (UEFI, so, sistema e drivers). 

É recomendável habilitar o vTPM para usar o atestado remoto em suas VMs. Com o vTPM habilitado, você também pode habilitar a funcionalidade do BitLocker, que fornece criptografia de volume completo para proteger os dados em repouso. Todos os recursos que usam vTPM resultarão em segredos vinculados à VM específica. Quando os usuários se conectam ao serviço de área de trabalho virtual do Windows em um cenário em pool, os usuários podem ser redirecionados para qualquer VM no pool de hosts. Dependendo de como o recurso foi projetado, isso pode ter um impacto.

>[!NOTE]
>O BitLocker não deve ser usado para criptografar o disco específico em que você está armazenando os dados de perfil do FSLogix.

### <a name="virtualization-based-security"></a>Segurança baseada em virtualização

A proteção baseada em virtualização (VBS) usa o hipervisor para criar e isolar uma região segura de memória que está inacessível ao sistema operacional. A política HVAC (integridade de código de Hypervisor-Protected) e o Windows Defender Credential Guard usam VBS para fornecer maior proteção contra vulnerabilidades. 

#### <a name="hypervisor-protected-code-integrity"></a>Integridade de código de Hypervisor-Protected

O política HVAC é uma poderosa mitigação do sistema que usa VBS para proteger os processos do modo kernel do Windows contra injeção e execução de código mal-intencionado ou não verificado.

#### <a name="windows-defender-credential-guard"></a>Windows Defender Credential Guard

O Windows Defender Credential Guard usa VBS para isolar e proteger segredos para que somente o software de sistema privilegiado possa acessá-los. Isso impede o acesso não autorizado a esses segredos e ataques de roubo de credenciais, como ataques de passagem de hash.

### <a name="deploy-trusted-launch-in-your-windows-virtual-desktop-environment"></a>Implantar a inicialização confiável no seu ambiente de área de trabalho virtual do Windows

A área de trabalho virtual do Windows não dá suporte à configuração automática de inicialização confiável durante o processo de instalação do pool de hosts. Para usar a inicialização confiável no seu ambiente de área de trabalho virtual do Windows, você precisará implantar o lançamento confiável normalmente e, em seguida, adicionar manualmente a máquina virtual ao pool de hosts desejado.

## <a name="nested-virtualization"></a>Virtualização aninhada

Os seguintes sistemas operacionais dão suporte à execução de virtualização aninhada na área de trabalho virtual do Windows:

- Windows Server 2016
- Windows Server 2019
- Windows 10 Enterprise
- Windows 10 Enterprise Multi-Session.

## <a name="next-steps"></a>Próximas etapas

Para saber como habilitar a autenticação multifator, consulte [Configurar a autenticação multifator](set-up-mfa.md).
