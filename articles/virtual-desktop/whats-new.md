---
title: O que há de novo na Área de Trabalho Virtual do Windows? - Azure
description: Novos recursos e atualizações de produto para a Área de Trabalho Virtual do Windows.
author: Heidilohr
ms.topic: overview
ms.date: 03/20/2021
ms.author: helohr
ms.reviewer: thhickli; darank
manager: femila
ms.custom: references_regions
ms.openlocfilehash: 1c5d846963d7fc0c2207893f5c4094e3f94351b5
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505409"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>O que há de novo na Área de Trabalho Virtual do Windows?

A Área de Trabalho Virtual do Windows é atualizada regularmente. Neste artigo, você descobrirá:

- As últimas atualizações
- Novos recursos
- Melhorias aos recursos existentes
- Correções de bug

Este artigo é atualizado mensalmente. Volte a consultá-lo com frequência para acompanhar as atualizações.

## <a name="client-updates"></a>Atualizações do cliente

Confira estes artigos para saber mais sobre as atualizações para nossos clientes para Área de Trabalho Virtual do Windows e Serviços de Área de Trabalho Remota:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)

## <a name="windows-virtual-desktop-agent-updates"></a>Atualizações do Agente da Área de Trabalho Virtual do Windows

O agente da Área de Trabalho Virtual do Windows é atualizado pelo menos uma vez por mês.

Veja as alterações no agente da Área de Trabalho Virtual do Windows:

- Versão 1.0.2866.1500: esta atualização foi lançada em 26 de março de 2021 e corrigiu um problema com a verificação de integridade da pilha.
- Versão 1.0.2800.2802: esta atualização foi lançada em 10 de março de 2021 e trouxe aprimoramentos gerais e correções de bugs.
- Versão 1.0.2800.2800: esta atualização foi lançada em 2 de março de 2021 e corrigiu um problema de conexão reversa.
- Versão 1.0.2800.2700: esta atualização foi lançada em 10 de fevereiro de 2021 e trouxe aprimoramentos gerais e correções de bugs.
- Versão 1.0.2800.2700: esta atualização foi lançada em 4 de fevereiro de 2021 e corrigiu um problema de orquestração de acesso negado.

## <a name="fslogix-updates"></a>Atualizações do FSLogix

Curioso sobre as últimas atualizações do FSLogix? Confira [Novidades do FSLogix](/fslogix/whats-new).

## <a name="february-2021"></a>Fevereiro de 2021

Veja as alterações de fevereiro de 2021.

### <a name="portal-experience"></a>Experiência do Portal

Aprimoramos a experiência do portal do Azure do seguinte modo:

- Na guia de grade do host da sessão, incluímos nos hosts o modo esvaziar em massa. 
- O anexo do aplicativo MSIX já está disponível para versão prévia pública.
- Correção das informações de visão geral do pool de host para o modo escuro.

### <a name="eu-metadata-storage-now-in-public-preview"></a>O armazenamento de metadados da EU já está em versão prévia pública

Agora estamos hospedando uma versão prévia pública da localização Europa (EU) como uma opção de armazenamento para metadados de serviço na Área de Trabalho Virtual do Windows. Os clientes podem escolher entre a opção Oeste ou Norte da Europa durante a criação de objetos de serviço. Os objetos de serviço e os metadados dos pools de hosts serão armazenados na localização do Azure associada a cada região. Para saber mais, leia [nossa postagem no blog anunciando a versão prévia pública](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/announcing-public-preview-of-windows-virtual-desktop-service/m-p/2143939).

### <a name="teams-on-windows-virtual-desktop-plugin-updates"></a>Atualizações de plug-in do Teams na Área de Trabalho Virtual do Windows

Aprimoramos a qualidade da chamada de vídeo no plug-in da Área de Trabalho Virtual do Windows, abordando problemas frequentemente relatados, como uma tela que fica escura de modo repentino ou um vídeo/áudio fora de sincronia. Esses aprimoramentos deverão aumentar o desempenho da exibição de vídeo usando uma alternância de alto-falantes ativos. Também corrigimos um problema em que dispositivos de hardware com caracteres especiais não estavam disponíveis no Teams.

## <a name="january-2021"></a>Janeiro de 2021

Estas foram as alterações de janeiro de 2021:

### <a name="new-windows-virtual-desktop-offer"></a>Nova oferta da Área de Trabalho Virtual do Windows

Os novos clientes economizam 30% nos custos de computação da Área de Trabalho Virtual do Windows para máquinas virtuais das séries D e BS por até 90 dias ao usar a solução nativa da Microsoft. Resgate essa oferta no portal do Azure antes de 31 de março de 2021. Saiba mais em nossa [página de oferta da Área de Trabalho Virtual do Windows](https://azure.microsoft.com/services/virtual-desktop/offer/).

### <a name="networksecuritygrouprules-value-change"></a>alteração do valor de networkSecurityGroupRules 

No modelo aninhado do Azure Resource Manager, alteramos o valor padrão de networkSecurityGroupRules de um objeto para uma matriz. Isso impedirá erros se você usar managedDisks-customimagevm.json sem especificar um valor para networkSecurityGroupRules. Essa não era uma alteração interruptiva e é compatível com versões anteriores.

### <a name="fslogix-hotfix-update"></a>Atualização de hotfix do FSLogix

Lançamos o FSLogix versão 2009 HF_01 (2.9.7654.46150) para resolver problemas da versão anterior (2.9.7621.30127). Recomendamos que você pare de usar a versão anterior e atualize o FSLogix assim que possível.

Para obter mais informações, confira as notas sobre a versão em [Novidades do FSLogix](/fslogix/whats-new#fslogix-apps-2009-hf_01-29765446150).

### <a name="azure-portal-experience-improvements"></a>Aprimoramentos na experiência do portal do Azure

Fizemos os seguintes aprimoramentos na experiência do portal do Azure:

- Agora você pode adicionar credenciais locais de administrador de VM diretamente em vez de precisar adicionar uma conta local criada com as credenciais da conta de ingresso no domínio do Active Directory.
- Agora, os usuários podem listar as atribuições individuais e de grupo em guias separadas para usuários individuais e grupos.
- O número de versão do Agente da Área de Trabalho Virtual do Windows agora está visível na visão geral da Máquina Virtual para pools de hosts.
- Adição de exclusão em massa a pools de hosts e grupos de aplicativos.
- Agora você pode habilitar ou desabilitar o modo esvaziar para vários hosts de sessão em um pool de host.
- Remoção do campo de IP público da página de detalhes da VM.

### <a name="windows-virtual-desktop-agent-troubleshooting"></a>Solução de problemas do Agente da Área de Trabalho Virtual do Windows

Recentemente, elaboramos o [guia de solução de problemas do Agente da Área de Trabalho Virtual do Windows](troubleshoot-agent.md) para ajudar os clientes que encontraram problemas comuns.

### <a name="microsoft-defender-for-endpoint-integration"></a>Integração do Microsoft Defender para Ponto de Extremidade

A integração do Microsoft Defender para Ponto de Extremidade já está em disponibilidade geral. Esse recurso fornece às VMs da Área de Trabalho Virtual do Windows a mesma experiência de investigação de um computador local Windows 10. Se você estiver usando o Windows 10 Enterprise multissessão, o Microsoft Defender para Ponto de Extremidade dará suporte a até 50 conexões de usuário simultâneas, oferecendo a você a economia do Windows 10 Enterprise multissessão e a confiança do Microsoft Defender para Ponto de Extremidade. Para obter mais informações, confira nossa [postagem no blog](https://techcommunity.microsoft.com/t5/microsoft-defender-for-endpoint/windows-virtual-desktop-support-is-now-generally-available/ba-p/2103712).

### <a name="azure-security-baseline-for-windows-virtual-desktop"></a>Linha de base de segurança do Azure para a Área de Trabalho Virtual do Windows

Recentemente, publicamos [um artigo sobre a linha de base de segurança do Azure](security-baseline.md) para a Área de Trabalho Virtual do Windows para o qual desejamos chamar sua atenção. Essas diretrizes incluem informações sobre como aplicar o Azure Security Benchmark versão 2.0 à Área de Trabalho Virtual do Windows. O Azure Security Benchmark descreve as configurações e as práticas que recomendamos que você use para proteger suas soluções de nuvem no Azure.

## <a name="december-2020"></a>Dezembro de 2020

Isto é o que mudou em dezembro de 2020: 

### <a name="azure-monitor-for-windows-virtual-desktop"></a>Azure Monitor para Área de Trabalho Virtual do Windows

A versão prévia pública para o Azure Monitor para Área de Trabalho Virtual do Windows já está disponível. Esse novo recurso inclui um painel robusto criado com base em Pastas de Trabalho do Azure Monitor para ajudar os profissionais de TI a entender os ambientes de Área de Trabalho Virtual do Windows. Confira mais detalhes [no comunicado em nosso blog](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/azure-monitor-for-windows-virtual-desktop-public-preview/m-p/1946587). 

### <a name="azure-resource-manager-template-change"></a>Alteração do modelo do Azure Resource Manager 

Na atualização mais recente, removemos todo o parâmetro de endereço IP público do modelo do Azure Resource Manager para criar e provisionar pools de hosts. É altamente recomendável evitar o uso de IPs públicos para Área de Trabalho Virtual do Windows para manter sua implantação segura. Se a sua implantação depender de IPs públicos, você precisará reconfigurá-la para usar IPs privados, caso contrário, sua implantação não funcionará corretamente.

### <a name="msix-app-attach-public-preview"></a>Versão prévia pública do anexo de aplicativo MSIX 

O anexo de aplicativo MSIX é outro serviço que iniciou sua versão prévia pública neste mês. O anexo de aplicativo MSIX é um serviço que apresenta dinamicamente aplicativos MSIX para VMs de host da Sessão de Área de Trabalho Virtual do Windows. Confira mais detalhes [no comunicado em nosso blog](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/msix-app-attach-azure-portal-integration-public-preview/m-p/1986231). 

### <a name="screen-capture-protection"></a>Proteção de captura de tela 

Este mês também marcou o início da versão prévia pública da proteção de captura de tela. Você pode usar esse recurso para impedir a captura de informações confidenciais nos pontos de extremidade do cliente. Dê uma chave à proteção de captura de tela acessando [esta página](https://aka.ms/WVDScreenCaptureProtection).  

### <a name="built-in-roles"></a>Funções internas

Adicionamos funções internas à Área de Trabalho Virtual do Windows para permissões de administrador. Confirma mais informações em [Funções internas para a Área de Trabalho Virtual do Windows](rbac.md). 

### <a name="application-group-limit-increase"></a>Aumento do limite do grupo de aplicativos

Aumentamos o limite do grupo de aplicativos padrão por locatário do Azure Active Directory para 200 grupos.

### <a name="client-updates-for-december-2020"></a>Atualizações do cliente para dezembro de 2020

Lançamos novas versões dos seguintes clientes: 

- Android
- macOS
- Windows

Para obter mais informações sobre atualizações do cliente, confira [Atualizações do cliente](whats-new.md#client-updates).

## <a name="november-2020"></a>Novembro de 2020

### <a name="azure-portal-experience"></a>Experiência do portal do Azure

Corrigimos dois bugs na experiência do usuário do portal do Azure:

- O nome amigável do aplicativo de desktop não é mais substituído no fluxo de trabalho "Adicionar VM".
- A guia de host de sessão será carregada agora se os hosts de sessão fizerem parte dos conjuntos de dimensionamento.

### <a name="fslogix-client-version-2009"></a>Cliente FSLogix, versão 2009 

Lançamos uma nova versão do cliente FSLogix com muitas correções e aprimoramentos. Saiba mais em [nossa postagem no blog](https://social.msdn.microsoft.com/Forums/en-US/defe5828-fba4-4715-a68c-0e4d83eefa6b/release-notes-for-fslogix-apps-release-2009-29762130127?forum=FSLogix).

### <a name="rdp-shortpath-public-preview"></a>Versão prévia pública do Shortpath RDP

O Shortpath RDP introduz a conectividade direta com o host de sessão da Área de Trabalho Virtual do Windows usando VPNs ponto a site e site a site e o ExpressRoute. Ele também introduz o protocolo de transporte URCP. O Shortpath RDP foi criado para reduzir a latência e os saltos de rede a fim de aprimorar a experiência do usuário. Saiba mais em [Shortpath RDP da Área de Trabalho Virtual do Windows](shortpath.md).

### <a name="azdesktopvirtualization-version-201"></a>Az.DesktopVirtualization, versão 2.0.1

Lançamos a versão 2.0.1 dos cmdlets da Área de Trabalho Virtual do Windows. Essa atualização inclui cmdlets que permitirão que você gerencie a Anexação de Aplicativo MSIX. Você pode baixar a nova versão na [galeria do PowerShell](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.0.1).

### <a name="azure-advisor-updates"></a>Atualizações do Assistente do Azure

Agora, o Assistente do Azure tem uma nova recomendação para diretrizes de proximidade na Área de Trabalho Virtual do Windows e uma nova recomendação para otimizar o desempenho em pools de hosts com balanceamento de carga em profundidade. Saiba mais no [site do Azure](https://azure.microsoft.com/updates/new-recommendations-from-azure-advisor/).

## <a name="october-2020"></a>Outubro de 2020

Estas foram as alterações de outubro de 2020:

### <a name="improved-performance"></a>desempenho aprimorado

- Otimizamos o desempenho reduzindo a latência de conexão nas seguintes regiões geográficas do Azure:
    - Suíça
    - Canadá

Agora você pode usar o [avaliador de experiência](https://azure.microsoft.com/services/virtual-desktop/assessment/) para estimar a qualidade da experiência do usuário nessas áreas.

### <a name="azure-government-cloud-availability"></a>Disponibilidade da Nuvem do Azure Government

A Nuvem do Azure Government já está em disponibilidade geral. Saiba mais em [nossa postagem no blog](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-generally-available-in-the-azure-government-cloud/).

### <a name="windows-virtual-desktop-azure-portal-updates"></a>Atualizações da Área de Trabalho Virtual do Windows no portal do Azure

Fizemos algumas atualizações na Área de Trabalho Virtual do Windows no portal do Azure:

- Correção de um erro de resourceID que impedia os usuários de abrir a guia "Sessões".
- Simplificação da interface do usuário na guia "Hosts da sessão".
- Correção das configurações "Padrões", "Usabilidade" e "Restaurar padrões" em Propriedades de RDP.
- Padronização das funções "Remover" e "Excluir" em todas as guias.
- O portal agora valida os nomes de aplicativos no fluxo de trabalho "Adicionar um aplicativo".
- Correção de um problema em que os dados de exportação do host da sessão não estavam alinhados nas colunas.
- Correção de um problema em que o portal não podia recuperar as sessões de usuário.
- Correção de um problema na recuperação do host da sessão que ocorria quando a máquina virtual era criada em outro grupo de recursos.
- Atualização da guia "Host da sessão" para listar as sessões ativas e desconectadas.
- A guia "Aplicativos" agora tem páginas.
- Correção de um problema em que o texto "exige a linha de comando" não era exibido corretamente na guia "Lista de aplicativos".
- Correção de um problema em que o portal não podia implantar pools de host nem máquinas virtuais durante o uso da versão no idioma alemão da Galeria de Imagens Compartilhadas.

### <a name="client-updates-for-october-2020"></a>Atualizações do cliente em outubro de 2020

Lançamos novas versões dos clientes. Confira estes artigos para saber mais:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)

Para obter mais informações sobre os outros clientes, confira [Atualizações do cliente](#client-updates).

## <a name="september-2020"></a>Setembro de 2020

Estas foram as alterações de setembro de 2020:

- Otimizamos o desempenho reduzindo a latência de conexão nas seguintes regiões geográficas do Azure:
    - Alemanha
    - África do Sul (somente para ambientes de validação)

Agora você pode usar o [avaliador de experiência](https://azure.microsoft.com/services/virtual-desktop/assessment/) para estimar a qualidade da experiência do usuário nessas áreas.

- Lançamos a versão 1.2.1364 do cliente da Área de Trabalho do Windows para a Área de Trabalho Virtual do Windows. Nesta atualização, fizemos as seguintes alterações:
    - Correção de um problema em que o SSO (logon único) não funcionava no Windows 7.
    - Correção de um problema que fazia com que o cliente fosse desconectado quando um usuário que habilitou a otimização de mídia para o Teams tentasse fazer uma chamada ou ingressar em uma reunião do Teams, enquanto outro aplicativo tinha um fluxo de áudio aberto no modo exclusivo.
    - Correção de um problema em que o Teams não enumerava dispositivos de áudio ou de vídeo quando a otimização de mídia para o Teams estava habilitada.
    - Adicionado um link "Precisa de ajuda com as configurações?" para a página de configurações da área de trabalho.
    - Corrigido um problema com o botão "Assinar" que acontecia ao usar temas escuros de alto contraste.
    
- Graças à enorme ajuda dos nossos usuários, corrigimos dois problemas críticos no cliente da Área de Trabalho Remota da Microsoft Store. Continuaremos a examinar os comentários e corrigir os problemas, conforme ampliamos nossa versão em fases do cliente para mais usuários em todo o mundo.
    
- Adicionamos um novo recurso que permite alterar a localização da VM, a imagem, o grupo de recursos, o nome do prefixo, a configuração de rede como parte do fluxo de trabalho para adicionar uma VM à sua implantação no portal do Azure.

- Agora, os profissionais de TI podem gerenciar VMs do Windows 10 Enterprise ingressadas no Azure Active Directory híbrido usando o Microsoft Endpoint Manager. Para saber mais, confira [nossa postagem no blog](https://techcommunity.microsoft.com/t5/microsoft-endpoint-manager-blog/microsoft-endpoint-manager-announces-support-for-windows-virtual/ba-p/1681048).

## <a name="august-2020"></a>Agosto de 2020

Estas foram as alterações de agosto de 2020:

- Aprimoramos o desempenho para reduzir a latência de conexão nas seguintes regiões do Azure: 

    - Reino Unido
    - França
    - Noruega
    - Coreia do Sul

   Você pode usar o [avaliador de experiência](https://azure.microsoft.com/services/virtual-desktop/assessment/) para ter uma ideia geral de como essas alterações afetarão os usuários.

- O Cliente de Área de Trabalho Remota da Microsoft Store (v10.2.1522 e posterior) já está em disponibilidade geral. Esta versão do Cliente de Área de Trabalho Remota da Microsoft Store é compatível com a Área de Trabalho Virtual do Windows. Também introduzimos fluxos de interface do usuário atualizados para proporcionar experiências aprimoradas para os usuários. Essa atualização inclui os modos de Fluent Design, claro e escuro e muitas outras alterações interessantes. Também reescrevemos o cliente para que ele use o mesmo mecanismo subjacente do protocolo RDP dos clientes iOS, macOS e Android. Isso nos permite fornecer novos recursos com maior velocidade em todas as plataformas. [Baixe o cliente](https://www.microsoft.com/p/microsoft-remote-desktop/9wzdncrfj3ps?rtc=1&activetab=pivot:overviewtab) e experimente-o.

- Corrigimos um problema no cliente do Teams Desktop (versão 1.3.00.21759) em que o cliente só mostrava o fuso horário UTC no chat, nos canais e no calendário. O cliente atualizado agora mostra o fuso horário da sessão remota.

- O Assistente do Azure agora faz parte da Área de Trabalho Virtual do Windows. Ao acessar a Área de Trabalho Virtual do Windows por meio do portal do Azure, você poderá ver recomendações para otimizar o ambiente da Área de Trabalho Virtual do Windows. Saiba mais em [Assistente do Azure](azure-advisor.md).

- A CLI do Azure agora dá suporte à Área de Trabalho Virtual do Windows (`az desktopvirtualization`) para ajudar você a automatizar suas implantações da Área de Trabalho Virtual do Windows. Confira [desktopvirtualization](/cli/azure/ext/desktopvirtualization/) para obter uma lista de comandos de extensão.

- Atualizamos nossos modelos de implantação para torná-los totalmente compatíveis com as interfaces do Azure Resource Manager na Área de Trabalho Virtual do Windows. Encontre os modelos no [GitHub](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates).

- O portal de US Gov da Área de Trabalho Virtual do Windows já está em versão prévia pública. Para saber mais, confira [nossos comunicados](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-available-in-the-azure-government-cloud-in-preview/).

## <a name="july-2020"></a>Julho de 2020  

Foi em julho que a integração da Área de Trabalho Virtual do Windows ao Azure Resource Manager entrou em disponibilidade geral.

Aqui está o que mudou nesta nova versão: 

- A "versão Fall 2019" agora é conhecida como "Área de Trabalho Virtual do Windows (Clássica)", enquanto a "versão Spring 2020" agora é apenas "Área de Trabalho Virtual do Windows". Para obter mais informações, confira [esta postagem no blog](https://azure.microsoft.com/blog/new-windows-virtual-desktop-capabilities-now-generally-available/). 

Para saber mais sobre os novos recursos, confira [esta postagem no blog](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245). 

### <a name="autoscaling-tool-update"></a>Atualização da ferramenta de dimensionamento automático

A versão mais recente da ferramenta de dimensionamento automático que estava em versão prévia agora está em disponibilidade geral. Essa ferramenta usa uma conta de automação do Azure e o Aplicativo Lógico do Azure para desligar e reiniciar automaticamente as VMs (máquinas virtuais) do host da sessão em um pool de host, reduzindo os custos de infraestrutura. Saiba mais em [Dimensionar hosts da sessão usando a Automação do Azure](set-up-scaling-script.md).

### <a name="azure-portal"></a>Portal do Azure

Agora você pode fazer o seguinte com o portal do Azure na Área de Trabalho Virtual do Windows: 

- Atribuir usuários diretamente a hosts da sessão de área de trabalho pessoal  
- Alterar a configuração do ambiente de validação para pools de hosts 

### <a name="diagnostics"></a>Diagnósticos

Lançamos algumas novas consultas predefinidas para o workspace do Log Analytics. Para acessar as consultas, acesse **Logs** e, em **Categoria**, selecione **Área de Trabalho Virtual do Windows**. Saiba mais em [Usar o Log Analytics para o recurso de diagnóstico](diagnostics-log-analytics.md).

### <a name="update-for-remote-desktop-client-for-android"></a>Atualização para o cliente da Área de Trabalho Remota para Android

O [cliente da Área de Trabalho Remota para Android](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) agora dá suporte a conexões de área de trabalho virtual do Windows. Da versão 10.0.7 em diante, o cliente Android apresenta uma nova interface do usuário para melhorar a experiência dos usuários. O cliente também se integra ao Microsoft Authenticator em dispositivos Android para habilitar o acesso condicional ao assinar workspaces da Área de Trabalho Virtual do Windows.  

A versão anterior do cliente da Área de Trabalho Remota agora é chamada de "Área de Trabalho Remota 8". Todas as conexões existentes que você tiver na versão anterior do cliente serão transferidas diretamente para o novo cliente. O novo cliente foi reescrito para o mesmo mecanismo de núcleo de RDP subjacente que os clientes iOS e macOS, com uma versão mais rápida de novos recursos em todas as plataformas. 

### <a name="teams-update"></a>Atualização do Teams

Fizemos melhorias ao Microsoft Teams para Área de Trabalho Virtual do Windows. Mais importante, a Área de Trabalho Virtual do Windows agora dá suporte à otimização de áudio e vídeo para o cliente de Área de Trabalho do Windows. O redirecionamento melhora a latência criando caminhos diretos entre os usuários quando eles usam áudio ou vídeo em chamadas e reuniões. Menos distância significa menos saltos, tornando as chamadas mais tranquilas. Saiba mais em [Usar o Teams a Área de Trabalho Virtual do Windows](teams-on-wvd.md).

## <a name="june-2020"></a>Junho de 2020

No mês passado, introduzimos a Área de Trabalho Virtual do Windows com integração do Azure Resource Manager em versão prévia. Essa atualização tem muitos recursos novos incríveis que adoraríamos apresentar. Aqui estão as novidades dessa versão da Área de Trabalho Virtual do Windows.

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager"></a>A Área de Trabalho Virtual do Windows agora está integrada ao Azure Resource Manager

A Área de Trabalho Virtual do Windows agora está integrada ao Azure Resource Manager. Na atualização mais recente, todos os objetos da Área de Trabalho Virtual do Windows agora são recursos do Azure Resource Manager. Essa atualização também é integrada ao Azure RBAC (Controle de Acesso Baseado em Função do Azure). Confira [O que é o Azure Resource Manager?](../azure-resource-manager/management/overview.md) para saber mais.

Isto é o que essa alteração faz para você:

- A Área de Trabalho Virtual do Windows agora está integrada ao portal do Azure. Isso significa que você pode gerenciar tudo diretamente no portal, sem o PowerShell, aplicativos Web ou ferramentas de terceiros. Para começar, confira nosso tutorial em [Criar um pool de host com o portal do Azure](create-host-pools-azure-marketplace.md).

- Antes dessa atualização, você só podia publicar RemoteApps e Desktops para usuários individuais. Com o Azure Resource Manager, agora você pode publicar recursos em grupos do Azure Active Directory.

- A versão anterior da Área de Trabalho Virtual do Windows tinha quatro funções de administrador internas que você podia atribuir a um locatário ou a um pool de host. Essas funções agora estão no [Azure RBAC (controle de acesso baseado em função do Azure)](../role-based-access-control/overview.md). Você pode aplicar essas funções a cada objeto do Azure Resource Manager da Área de Trabalho Virtual do Windows, o que lhe permite ter um modelo de delegação completo e avançado.

- Nessa atualização, você não precisa mais executar o Azure Marketplace nem o modelo do GitHub repetidamente para expandir um pool de host. Para expandir um pool de host, basta acessá-lo no portal do Azure e selecionar **+ Adicionar** para implantar hosts de sessão adicionais.

- A implantação do pool de host agora está totalmente integrada à [Galeria de Imagens Compartilhadas do Azure](../virtual-machines/shared-image-galleries.md). A Galeria de Imagens Compartilhadas é um serviço do Azure separado que armazena definições de imagem de VM (máquina virtual), incluindo o controle de versão de imagem. Você também pode usar a replicação global para copiar e enviar suas imagens para outras regiões do Azure para implantação local.

- As funções de monitoramento que costumavam ser feitas por meio do PowerShell ou do aplicativo Web do Serviço de Diagnóstico agora foram movidas para Log Analytics no portal do Azure. Agora, você também tem duas opções para visualizar seus relatórios. Você pode executar consultas do Kusto e usar Pastas de Trabalho para criar relatórios visuais.

- Não é mais necessário o consentimento completo do Azure Active Directory (Azure AD) para usar a Área de Trabalho Virtual do Windows. Nessa atualização, o locatário do Azure AD na sua assinatura do Azure autentica os usuários e fornece controles RBAC do Azure para seus administradores.

### <a name="powershell-support"></a>Suporte ao PowerShell

Adicionamos novos cmdlets AzWvd ao módulo AZ do Azure PowerShell com essa atualização. Esse novo módulo tem suporte no PowerShell Core, que é executado no .NET Core.

Para instalar o módulo, siga as instruções em [Configurar o módulo do PowerShell para a Área de Trabalho Virtual do Windows](powershell-module.md).

Você também pode ver uma lista de comandos disponíveis na [referência do PowerShell AzWvd](/powershell/module/az.desktopvirtualization/#desktopvirtualization).

Para obter mais informações sobre os novos recursos, confira [nossa postagem no blog](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245).

### <a name="additional-gateways"></a>Gateways adicionais

Adicionamos um cluster de gateway na África do Sul para reduzir a latência de conexão.

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Microsoft Teams na Área de Trabalho Virtual do Windows (versão prévia)

Fizemos algumas melhorias ao Microsoft Teams para Área de Trabalho Virtual do Windows. O mais importante é que a Área de Trabalho Virtual do Windows agora dá suporte ao redirecionamento audiovisual para chamadas. O redirecionamento melhora a latência criando caminhos diretos entre os usuários quando eles chamam o uso de áudio ou vídeo. Menos distância significa menos saltos, tornando as chamadas mais tranquilas.

Para saber mais, confira [nossa postagem no blog](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os planos futuros no [roteiro da Área de Trabalho Virtual do Windows no Microsoft 365](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop).
