---
title: Aprimore a segurança de gerenciamento remoto no Azure | Microsoft Docs
description: Este artigo discute medidas para melhorar a segurança do gerenciamento remoto durante a administração dos ambientes do Microsoft Azure, incluindo serviços de nuvem, Máquinas Virtuais e aplicativos personalizados.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 2431feba-3364-4a63-8e66-858926061dd3
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: 2d6d1d121e41b0446e7f63b9aa530df89697ef67
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586671"
---
# <a name="security-management-in-azure"></a>Gerenciamento de segurança no Azure
Os assinantes do Azure podem gerenciar ambientes de nuvem de vários dispositivos, incluindo estações de trabalho, computadores de desenvolvedores e até mesmo dispositivos de usuário final com privilégios que têm permissões de tarefas específicas. Em alguns casos, as funções administrativas são executadas por meio de consoles baseado na Web, como o [Portal do Azure](https://azure.microsoft.com/features/azure-portal/). Em outros casos, pode haver conexões diretas ao Azure de sistemas locais por meio de VPNs (Redes Virtuais Privadas), Serviços de Terminal, protocolos de aplicativos de cliente ou (programaticamente) a SMAPI (API de Gerenciamento de Serviços do Azure). Além disso, os pontos de extremidade do cliente podem ser unidos ao domínio ou isolados e não gerenciados, como tablets ou smartphones.

Embora vários recursos de gerenciamento e acesso forneçam um conjunto avançado de opções, essa variação pode adicionar um risco significativo para uma implantação de nuvem. Pode ser difícil gerenciar, controlar e auditar as ações administrativas. Essa variação também pode introduzir ameaças de segurança por meio do acesso não regulado a pontos de extremidade do cliente que são usados para gerenciar serviços de nuvem. O uso de estações de trabalho pessoais ou gerais para desenvolvimento e gerenciamento de infraestrutura abre vetores de ameaça imprevisíveis, como navegação na Web (por exemplo, ataques do tipo "watering hole") ou email (por exemplo, engenharia social e phishing).

![][1]

O potencial para ataques aumenta nesse tipo de ambiente porque é muito difícil criar políticas de segurança e mecanismos para gerenciar adequadamente o acesso às interfaces do Azure (como a SMAPI) de pontos de extremidade amplamente variados.

### <a name="remote-management-threats"></a>Ameaças de gerenciamento remoto
Os invasores frequentemente tentam obter acesso privilegiado comprometendo as credenciais da conta (por exemplo, por meio de força bruta para obter a senha, phishing e coleta de credenciais) ou enganando os usuários para que executem código prejudicial (por exemplo, de sites prejudiciais com downloads não intencionais ou de anexos de email prejudiciais). Em um ambiente de nuvem gerenciado remotamente, violações de contas podem levar a um risco maior devido ao acesso em qualquer local e a qualquer momento.

Mesmo com controles rígidos em contas de administrador primárias, contas de usuário de nível inferior podem ser usadas para explorar pontos fracos na estratégia de segurança. A falta de treinamento de segurança apropriado também pode levar a falhas devido à divulgação acidental ou à exposição de informações de contas.

Quando uma estação de trabalho de usuário também é usada para tarefas administrativas, ela pode ser comprometida em vários pontos diferentes. Isso pode ocorrer, por exemplo, se o usuário navegar na Web, usar ferramentas de terceiros e de software livre ou abrir um arquivo de documento prejudicial que contenha um cavalo de Troia.

Em geral, a maioria dos ataques direcionados que resultam em violações de dados pode ser rastreada como explorações de navegador, plug-ins (como Flash, PDF e Java) e spear phishing (email) em computadores desktop. Esses computadores podem ter permissões de nível administrativo ou de nível de serviço para acessar servidores ativos ou dispositivos de rede para operações quando usados para desenvolvimento ou gerenciamento de outros ativos.

### <a name="operational-security-fundamentals"></a>Conceitos básicos de segurança operacional
Para que o gerenciamento e as operações sejam mais seguros, você pode minimizar a superfície de ataque do cliente reduzindo o número de pontos de entrada possíveis. Isso pode ser feito por meio de princípios de segurança: "separação de tarefas" e "diferenciação de ambientes".

Isolar funções confidenciais umas das outras para reduzir a probabilidade de um erro em um nível leva a uma violação em outro nível. Exemplos:

* As tarefas administrativas não devem ser combinadas a atividades que possam levar ao comprometimento (por exemplo, o malware no email do administrador que, em seguida, infecta um servidor de infraestrutura).
* Uma estação de trabalho usada para operações de alta confidencialidade não deve ser o mesmo sistema usado para fins de alto risco, como navegar na Internet.

Reduza a superfície de ataque do sistema ao remover o software desnecessário. Exemplo:

* Uma estação de trabalho padrão administrativa, de suporte ou de desenvolvimento não deve exigir a instalação de um cliente de email ou outros aplicativos de produtividade se o principal objetivo do dispositivo é gerenciar serviços de nuvem.

Os sistemas de clientes com acesso de administrador aos componentes de infraestrutura devem estar sujeitos à política mais rígida possível para reduzir os riscos de segurança. Exemplos:

* As políticas de segurança podem incluir configurações de Política de Grupo que negam acesso aberto à Internet do dispositivo e o uso de uma configuração de firewall restritiva.
* Use VPNs com o IPsec (protocolo IPsec), caso seja necessário acesso direto.
* Configure domínios separados do Active Directory de gerenciamento e de desenvolvimento.
* Isole e filtre o tráfego de rede de estações de trabalho de gerenciamento.
* Use software antimalware.
* Implemente a autenticação multifator para reduzir o risco de credenciais roubadas.

Consolidar recursos de acesso e eliminar pontos de extremidade não gerenciados também simplifica as tarefas de gerenciamento.

### <a name="providing-security-for-azure-remote-management"></a>Fornecer segurança para o gerenciamento remoto do Azure
O Azure fornece mecanismos de segurança para ajudar os administradores que gerenciam serviços de nuvem e máquinas virtuais do Azure. Esses mecanismos incluem:

* Autenticação e [controle de acesso baseado em função](../role-based-access-control/role-assignments-portal.md).
* Monitoramento, registro em log e auditoria.
* Certificados e comunicações criptografadas.
* Um portal de gerenciamento da Web.
* Filtragem de pacotes de rede.

Com a configuração de segurança do cliente e a implantação de datacenter de um gateway de gerenciamento, é possível restringir e monitorar o acesso de administrador a dados e aplicativos de nuvem.

> [!NOTE]
> Determinadas recomendações neste artigo podem resultar em maior uso de recursos de computação, rede ou dados e podem aumentar os custos de licença ou assinatura.
>
>

## <a name="hardened-workstation-for-management"></a>Estação de trabalho protegida para gerenciamento
O objetivo da proteção de uma estação de trabalho é eliminar todas as funções, exceto as mais críticas que são necessárias para o funcionamento, reduzindo ao máximo a superfície de ataque potencial. A proteção do sistema inclui minimizar o número de serviços e aplicativos instalados, limitar a execução de aplicativos, restringir o acesso à rede apenas ao que é necessário e sempre manter o sistema atualizado. Além disso, o uso de uma estação de trabalho protegida para o gerenciamento segrega as ferramentas administrativas e as atividades de outras tarefas de usuário final.

Em um ambiente corporativo local, você pode limitar a superfície de ataque da infraestrutura física por meio de redes de gerenciamento dedicadas, salas de servidores com acesso por cartão e estações de trabalho executadas em áreas protegidas da rede. Em um modelo de TI de nuvem ou híbrido, ser cuidadoso quanto aos serviços de gerenciamento seguro pode ser mais complexo, devido à falta de acesso físico aos recursos de TI. A implementação de soluções de proteção requer configuração cuidadosa do software, processos voltados para a segurança e regras abrangentes.

O uso de um espaço de software minimizado com menos privilégios em uma estação de trabalho bloqueada para gerenciamento de nuvem (e para desenvolvimento de aplicativos) pode reduzir o risco de incidentes de segurança padronizando os ambientes de desenvolvimento e gerenciamento remotos. Uma configuração de estação de trabalho protegida pode ajudar a impedir o comprometimento de contas que são usadas para gerenciar recursos de nuvem críticos fechando muitos caminhos comuns usados por malware e explorações. Especificamente, você pode usar o [Windows AppLocker](https://technet.microsoft.com/library/dd759117.aspx) e a tecnologia Hyper-V para controlar e isolar o comportamento do sistema cliente e reduzir as ameaças, incluindo email ou a navegação na Internet.

Em uma estação de trabalho protegida, o administrador executa uma conta de usuário padrão (que bloqueia a execução de nível administrativo), e os aplicativos associados são controlados por uma lista de permissões. Os elementos básicos de uma estação de trabalho protegida são:

* Verificação e patches ativos. Implantar software antimalware, realizar verificações de vulnerabilidade regulares e atualizar todas as estações de trabalho usando a última atualização de segurança em tempo hábil.
* Funcionalidade limitada. Desinstalar todos os aplicativos que não são necessários e desabilitar serviços desnecessários (inicialização).
* Proteção de rede. Usar regras de Firewall do Windows para permitir somente endereços IP, portas e URLs válidos relacionados ao gerenciamento do Azure. Verificar se as conexões remotas de entrada para a estação de trabalho também estão bloqueadas.
* Restrição de execução. Permitir apenas um conjunto de arquivos executáveis predefinidos que são necessários para a execução do gerenciamento (chamado de "negação padrão"). Por padrão, os usuários devem ter a permissão negada para executar qualquer programa, a menos que ele seja explicitamente definido na lista de permissões.
* Menor privilégio. Os usuários da estação de trabalho de gerenciamento não devem ter privilégios administrativos no computador local em si. Assim, não podem alterar a configuração do sistema nem arquivos do sistema, intencionalmente ou não.

Você pode impor todos esses itens usando [GPOs](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-administer-group-policy) (Objetos de Política de Grupo) no AD DS (Active Directory Domain Services) e aplicá-los no domínio de gerenciamento (local) para todas as contas de gerenciamento.

### <a name="managing-services-applications-and-data"></a>Gerenciamento de serviços, aplicativos e dados
A configuração de serviços de nuvem do Azure é executada por meio do portal do Azure ou da SMAPI, na interface de linha de comando do Windows PowerShell ou em um aplicativo personalizado que tira proveito das interfaces RESTful. Os serviços que usam esses mecanismos incluem o Azure AD (Azure Active Directory), o Armazenamento do Azure, os Sites do Azure e a Rede Virtual do Azure, entre outros.

Os aplicativos implantados na Máquina Virtual fornecem suas próprias interfaces e ferramentas de clientes, conforme necessário, como o MMC (Console de Gerenciamento Microsoft), um console de gerenciamento empresarial (como o Microsoft System Center ou o Windows Intune) ou outro aplicativo de gerenciamento (o Microsoft SQL Server Management Studio, por exemplo). Essas ferramentas geralmente residem em uma rede de cliente ou ambiente empresarial. Elas podem depender de protocolos de rede específicos, como o protocolo RDP, que exigem conexões diretas com estado. Algumas delas podem ter interfaces habilitadas para a Web que não devem ser publicadas abertamente nem acessíveis pela Internet.

Você pode restringir o acesso ao gerenciamento de serviços de plataforma e infraestrutura no Azure usando a [autenticação multifator](../active-directory/authentication/multi-factor-authentication.md), os [certificados de gerenciamento X.509](https://blogs.msdn.microsoft.com/azuresecurity/2015/07/13/certificate-management-in-azure-dos-and-donts/) e as regras de firewall. O portal do Azure e a SMAPI exigem o protocolo TLS. No entanto, os serviços e aplicativos que você implanta no Azure exigem que tome medidas de proteção que são apropriadas com base no aplicativo. Esses mecanismos muitas vezes podem ser habilitados mais facilmente por meio de uma configuração de estação de trabalho protegida padronizada.

### <a name="management-gateway"></a>Gateway de gerenciamento
Para centralizar todo o acesso administrativo e simplificar o monitoramento e o registro em log, você pode implantar um servidor de [Gateway de Área de Trabalho Remota](https://technet.microsoft.com/library/dd560672) dedicado na rede local, conectado ao ambiente do Azure.

Um Gateway de Área de Trabalho Remota é um serviço de proxy RDP com base em políticas que impõe requisitos de segurança. Implementar o Gateway de Área de Trabalho Remota junto com o NAP (Proteção de Acesso à Rede) do Windows Server ajuda a garantir que somente os clientes que atendam a critérios de integridade de segurança específicos estabelecidos pelos GPOs (Objetos de Política de Grupo) do AD DS (Serviços de Domínio do Active Directory) possam se conectar. Além disso:

* Provisione um [certificado de gerenciamento do Azure](https://msdn.microsoft.com/library/azure/gg551722.aspx) no Gateway de Área de Trabalho Remota para que ele seja o único host com permissão para acessar o portal do Azure.
* Una o Gateway de Área de Trabalho Remota ao mesmo [domínio de gerenciamento](https://technet.microsoft.com/library/bb727085.aspx) que as estações de trabalho do administrador. Isso é necessário quando você usa uma VPN IPsec de site a site ou o ExpressRoute em um domínio que tem uma relação de confiança unidirecional para com o Azure AD ou se está associando credenciais entre a instância local do AD DS e o Azure AD.
* Configure uma [política de autorização de conexão de cliente](https://technet.microsoft.com/library/cc753324.aspx) para permitir que o Gateway de Área de Trabalho Remota verifique se o nome do computador cliente é válido (ingressado no domínio) e tem permissão para acessar o portal do Azure.
* Use IPsec para a [VPN do Azure](https://azure.microsoft.com/documentation/services/vpn-gateway/) para proteger ainda mais o tráfego de gerenciamento contra interceptação e roubo de tokens ou considere um link da Internet isolado por meio do [Azure ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).
* Habilite a autenticação multifator (via [Autenticação Multifator do Azure](../active-directory/authentication/multi-factor-authentication.md)) ou a autenticação de cartão inteligente para administradores que fazem logon por meio do Gateway de Área de Trabalho Remota.
* Configure [restrições de endereço IP](https://azure.microsoft.com/blog/2013/08/27/confirming-dynamic-ip-address-restrictions-in-windows-azure-web-sites/) de origem ou [Grupos de Segurança de Rede](../virtual-network/security-overview.md) no Azure para minimizar o número de pontos de extremidade de gerenciamento permitidos.

## <a name="security-guidelines"></a>Diretrizes de segurança
Em geral, a proteção das estações de trabalho de administrador para uso com a nuvem é semelhante às práticas usadas para qualquer estação de trabalho local (por exemplo, compilação minimizada e permissões restritivas). Alguns aspectos exclusivos do gerenciamento de nuvem são mais semelhantes ao gerenciamento corporativo remoto ou fora de banda. Eles incluem o uso e a auditoria de credenciais, o acesso remoto com segurança avançada e a detecção e a resposta a ameaças.

### <a name="authentication"></a>Authentication
Você pode usar restrições de logon do Azure para restringir os endereços IP de origem para acessar ferramentas administrativas e solicitações de acesso de auditoria. Para ajudar o Azure a identificar clientes de gerenciamento (estações de trabalho e/ou aplicativos), você pode configurar a SMAPI (por meio de ferramentas desenvolvidas pelo cliente, como cmdlets do Windows PowerShell) e o portal do Azure para exigir que certificados de gerenciamento do cliente sejam instalados, além de certificados SSL. Também recomendamos que o acesso de administrador exija a autenticação multifator.

Alguns aplicativos ou serviços que você implanta no Azure podem ter seus próprios mecanismos de autenticação para acesso do administrador e do usuário final, enquanto outros aproveitam plenamente o Azure AD. Dependendo de você federar credenciais por meio de serviços do AD FS (Serviços de Federação do Active Directory), usar a sincronização de diretório ou manter contas de usuário somente na nuvem, o uso do [Microsoft Identity Manager](https://technet.microsoft.com/library/mt218776.aspx) (parte do Azure AD Premium) o ajuda a gerenciar ciclos de vida de identidades entre os recursos.

### <a name="connectivity"></a>Conectividade
Vários mecanismos estão disponíveis para ajudar a proteger conexões de cliente para as redes virtuais do Azure. Dois desses mecanismos, [S2S](https://channel9.msdn.com/series/Azure-Site-to-Site-VPN) (VPN site a site) e [P2S](../vpn-gateway/vpn-gateway-point-to-site-create.md) (VPN ponto a site), habilitam o uso de IPsec padrão do setor (S2S) ou do [SSTP](https://technet.microsoft.com/magazine/2007.06.cableguy.aspx) (Secure Socket Tunneling Protocol) (P2S) para criptografia e túnel. Ao se conectar ao gerenciamento de serviços do Azure voltados para o público, como o portal do Azure, o Azure requer o protocolo HTTPS.

Uma estação de trabalho protegida autônoma que não se conecta ao Azure por meio de um Gateway de Área de Trabalho Remota deve usar a VPN site a ponto baseada em SSTP para criar a conexão inicial com a Rede Virtual do Azure e, em seguida, estabelecer a conexão RDP com máquinas virtuais individuais por meio do túnel VPN.

### <a name="management-auditing-vs-policy-enforcement"></a>Auditoria de gerenciamento versus imposição de políticas
Normalmente, há duas abordagens para proteger processos de gerenciamento: auditoria e imposição de políticas. A adoção de ambas fornece controles abrangentes, mas talvez não seja possível em todas as situações. Além disso, cada abordagem tem diferentes níveis de risco, custo e esforço associados ao gerenciamento de segurança, especialmente no que diz respeito ao nível de confiança para pessoas e arquiteturas de sistema.

O monitoramento, o registro em log e a auditoria fornecem uma base para acompanhar e entender atividades administrativas, mas nem sempre é viável auditar completamente todas as ações, devido à quantidade de dados gerados. No entanto, a auditoria da eficácia das políticas de gerenciamento é uma prática recomendada.

A imposição de políticas que inclui controles de acesso estritos utiliza mecanismos programáticos que podem controlar as ações do administrador e ajuda a garantir que todas as medidas de proteção possíveis sejam usadas. O registro em log fornece a prova de imposição, além de um registro de quem fez o quê, de onde e quando. O registro em log também o habilita a auditar e verificar informações sobre como os administradores seguem as políticas e fornece evidência de atividades

## <a name="client-configuration"></a>Configuração do cliente
Recomendamos três configurações principais para uma estação de trabalho protegida. Os maiores diferenciais entre elas são o custo, a usabilidade e a acessibilidade, mantendo um perfil de segurança semelhante em todas as opções. A tabela a seguir fornece uma breve análise dos benefícios e riscos de cada uma delas. (Observe que "computador corporativo" se refere a uma configuração de computador desktop padrão que seria implantada para todos os usuários do domínio, independentemente das funções.)

| Configuração | Benefícios | Contras |
| --- | --- | --- |
| Estação de trabalho protegida autônoma |Estação de trabalho rigidamente controlada |custo mais alto para áreas de trabalho dedicadas |
| - | Risco reduzido de explorações de aplicativos |Maior esforço de gerenciamento |
| - | Clara separação de funções | - |
| Computador corporativo como máquina virtual |Menor custo de hardware | - |
| - | Segregação de funções e aplicativos | - |
| Windows To Go com criptografia de unidade de disco BitLocker  |Compatibilidade com a maioria dos computadores |Acompanhamento de ativos |
| - | Economia e portabilidade | - |
| - | Ambiente de gerenciamento isolado |- |

É importante que a estação de trabalho protegida seja o host, não o convidado, sem nada entre o sistema operacional do host e o hardware. Seguir o "princípio de origem limpa" (também conhecido como "origem segura") significa que o host deve ser o mais protegido. Caso contrário, a estação de trabalho protegida (convidado) estará sujeita a ataques no sistema em que está hospedada.

Você pode separar ainda mais as funções administrativas por meio de imagens do sistema dedicadas para cada estação de trabalho protegida, que tenham apenas as ferramentas e as permissões necessárias para gerenciar aplicativos específicos do Azure e de nuvem, com GPOs do AD DS locais específicos para as tarefas necessárias.

Para ambientes de TI sem uma infraestrutura local (por exemplo, sem acesso a uma instância local do AD DS para GPOs porque todos os servidores estão na nuvem), um serviço como o [Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx) pode simplificar a implantação e a manutenção de configurações de estação de trabalho.

### <a name="stand-alone-hardened-workstation-for-management"></a>Estação de trabalho protegida autônoma para gerenciamento
Com uma estação de trabalho autônoma protegida, os administradores têm um computador ou laptop que usam para tarefas administrativas e outro computador ou laptop separado para tarefas não administrativas. Uma estação de trabalho dedicada a gerenciar os serviços do Azure não precisa de outros aplicativos instalados. Além disso, o uso de estações de trabalho que dão suporte a [TPM](https://technet.microsoft.com/library/cc766159) (Trusted Platform Module) ou outra tecnologia de criptografia de nível de hardware semelhante auxilia na autenticação de dispositivos e na prevenção de certos ataques. O TPM também pode dar suporte à proteção de volume completo da unidade do sistema usando a [Criptografia de Unidade de Disco BitLocker](https://technet.microsoft.com/library/cc732774.aspx).

No cenário de estação de trabalho protegida autônoma (mostrado abaixo), a instância local do Firewall do Windows (ou um firewall de cliente não Microsoft) é configurada para bloquear conexões de entrada, como RDP. O administrador pode fazer logon na estação de trabalho protegida e iniciar uma sessão RDP que se conecta ao Azure depois de estabelecer uma conexão VPN com uma Rede Virtual do Azure, mas não pode fazer logon em um computador corporativo e usar RDP para se conectar à própria estação de trabalho protegida.

![][2]

### <a name="corporate-pc-as-virtual-machine"></a>Computador corporativo como máquina virtual
Em casos em que uma estação de trabalho protegida autônoma separada tem custo proibitivo ou é inconveniente, a estação de trabalho protegida pode hospedar uma máquina virtual para realizar tarefas não administrativas.

![][3]

Para evitar vários riscos de segurança que podem ocorrer devido ao uso de uma estação de trabalho para gerenciamento de sistemas e outras tarefas de trabalho diário, você pode implantar uma máquina virtual Hyper-V do Windows para a estação de trabalho protegida. A máquina virtual pode ser usada como o computador corporativo. O ambiente de computador corporativo pode permanecer isolado do host, o que reduz a superfície de ataque e impede a coexistência das atividades diárias do usuário (como email) com tarefas administrativas confidenciais.

A máquina de virtual do computador corporativo é executada em um espaço protegido e fornece aplicativos do usuário. O host permanece como uma "origem limpa" e impõe políticas de rede estritas no sistema operacional raiz (por exemplo, bloqueando o acesso a RDP da máquina virtual).

### <a name="windows-to-go"></a>Windows To Go
Uma alternativa à exigência de uma estação de trabalho protegida autônoma é usar uma unidade [Windows To Go](https://technet.microsoft.com/library/hh831833.aspx), um recurso que dá suporte a uma funcionalidade de inicialização USB do lado do cliente. O Windows To Go habilita os usuários a inicializar um computador compatível para uma imagem do sistema isolada em execução por meio de uma unidade flash USB criptografada. Ele fornece controles adicionais para pontos de extremidade de administração remotos, pois a imagem pode ser totalmente gerenciada por um grupo de TI corporativo, com políticas de segurança estritas, uma compilação mínima do sistema operacional e suporte a TPM.

Na figura a seguir, a imagem portátil é um sistema unido ao domínio que é pré-configurado para se conectar apenas ao Azure, requer a autenticação multifator e bloqueia todo o tráfego que não é de gerenciamento. Se um usuário inicializar o mesmo computador para a imagem corporativa padrão e tentar acessar o Gateway de Área de Trabalho Remota para as ferramentas de gerenciamento do Azure, a sessão será bloqueada. O Windows To Go torna-se o sistema operacional de nível raiz, e não são necessárias camadas adicionais (sistema operacional host, hipervisor, máquina virtual) que possam ser mais vulneráveis a ataques externos.

![][4]

É importante observar que as unidades flash USB são mais facilmente perdidas do que um computador desktop médio. O uso do BitLocker para criptografar todo o volume, juntamente com uma senha forte, reduz a probabilidade de que um invasor use a imagem da unidade para fins prejudicais. Além disso, se a unidade flash USB for perdida, a revogação e a [emissão de um novo certificado de gerenciamento](https://technet.microsoft.com/library/hh831574.aspx), juntamente com uma redefinição de senha rápida, poderão reduzir a exposição. Os logs de auditoria administrativos residem no Azure, não no cliente, reduzindo ainda mais a perda de dados potencial.

## <a name="best-practices"></a>Práticas recomendadas
Considere as diretrizes adicionais a seguir ao gerenciar aplicativos e dados no Azure.

### <a name="dos-and-donts"></a>Recomendações
Não presuma que, como uma estação de trabalho foi bloqueada, outros requisitos de segurança comuns não precisam ser atendidos. O risco potencial é maior devido aos níveis de acesso com privilégios elevados que as contas de administrador geralmente têm. Exemplos de riscos e suas práticas de segurança alternativas são mostrados na tabela a seguir.

| Errado | Certo |
| --- | --- |
| Não envie por email credenciais para acesso de administrador ou outros segredos (por exemplo, certificados de gerenciamento ou SSL) |Mantenha a confidencialidade fornecendo nomes de contas e senhas por voz (mas não os armazenando na caixa postal), execute uma instalação remota de certificados de cliente/servidor (por meio de uma sessão criptografada), baixe de um compartilhamento de rede protegido ou distribua manualmente por meio de mídia removível. |
| - | Gerencie os ciclos de vida de certificado de gerenciamento. |
| Não armazene senhas de contas não criptografadas ou sem hash no armazenamento de aplicativos (como em planilhas, sites do SharePoint ou compartilhamentos de arquivos). |Estabeleça princípios de gerenciamento de segurança e políticas de proteção do sistema os e aplique-os a seu ambiente de desenvolvimento. |
| - | Use regras de anexação de certificados do [Enhanced Mitigation Experience Toolkit 5.5](https://technet.microsoft.com/security/jj653751) para garantir o acesso apropriado a sites SSL/TLS do Azure. |
| Não compartilhe contas e senhas entre administradores nem reutilize senhas em várias contas de usuário ou serviços, particularmente para mídia social ou outras atividades não administrativas. |Crie uma conta da Microsoft dedicada para gerenciar sua assinatura do Azure (uma conta que não seja usada para email pessoal). |
| Não envie arquivos de configuração por email. |Os perfis e arquivos de configuração devem ser instalados de uma fonte confiável (por exemplo, uma unidade flash USB criptografada), não por meio de um mecanismo que possa ser facilmente comprometido, como email. |
| Não use senhas de logon fracas ou simples. |Imponha políticas de senha forte, ciclos de expiração (alteração no primeiro uso), tempos limite de console e bloqueios de conta automáticos. Use um sistema de gerenciamento de senha de cliente com a autenticação multifator para acesso ao cofre de senhas. |
| Não exponha as portas de gerenciamento à Internet. |Bloqueie as portas do Azure e os endereços IP para restringir o acesso de gerenciamento. Para saber mais, confira o white paper [Segurança de rede do Azure](https://download.microsoft.com/download/4/3/9/43902EC9-410E-4875-8800-0788BE146A3D/Windows%20Azure%20Network%20Security%20Whitepaper%20-%20FINAL.docx). |
| - | Use firewalls, VPNs e NAP para todas as conexões de gerenciamento. |

## <a name="azure-operations"></a>Operações do Azure
Na operação do Azure da Microsoft, os engenheiros de operações e a equipe de suporte que acessam os sistemas de produção do Azure usam [computadores de estações de trabalho protegidos com VMs](#stand-alone-hardened-workstation-for-management) provisionados neles para acesso à rede corporativa interna e aos aplicativos (como email, intranet etc.). Todas as estações de trabalho de gerenciamento têm TPMs, a unidade de inicialização do host é criptografada com BitLocker e elas são unidas a uma UO (unidade organizacional) especial no domínio corporativo principal da Microsoft.

A proteção do sistema é imposta pela Política de Grupo, com a atualização de software centralizada. Para análise e auditoria, logs de eventos (por exemplo, segurança e AppLocker) são coletados de estações de trabalho de gerenciamento e salvos em um local central.

Além disso, jump boxes dedicadas na rede da Microsoft que exigem a autenticação de dois fatores são usadas para se conectar à rede de produção do Azure.

## <a name="azure-security-checklist"></a>Lista de verificação de segurança do Azure
Minimizar o número de tarefas que os administradores podem executar em uma estação de trabalho protegida ajuda a minimizar a superfície de ataque no ambiente de desenvolvimento e gerenciamento. Use as seguintes tecnologias para proteger a estação de trabalho protegida:

* Proteção do IE. O navegador Internet Explorer (ou qualquer navegador da Web, de fato) é um dos principais pontos de entrada para código prejudicial devido a suas amplas interações com servidores externos. Examine suas políticas de cliente e imponha a execução no modo protegido, desabilitando complementos e downloads de arquivos e usando a filtragem do [Microsoft SmartScreen](https://technet.microsoft.com/library/jj618329.aspx). Verifique se os avisos de segurança são exibidos. Tire proveito das zonas da Internet e crie uma lista de sites confiáveis para os quais você configurou proteção razoável. Bloqueie todos os outros sites e código no navegador, como ActiveX e Java.
* Usuário standard. A execução como usuário padrão apresenta várias vantagens. A maior delas é que o roubo de credenciais de administrador por meio de malware torna-se mais difícil. Além disso, uma conta de usuário padrão não tem privilégios elevados no sistema operacional raiz, e muitas opções de configuração e APIs são bloqueadas por padrão.
* AppLocker. Você pode usar o [AppLocker](https://technet.microsoft.com/library/ee619725.aspx) para restringir os programas e scripts que os usuários podem executar. Você pode executar o AppLocker no modo de auditoria ou de imposição. Por padrão, o AppLocker tem uma regra de permissão que habilita os usuários que têm um token de administrador a executar todo o código no cliente. Essa regra existe para impedir que os administradores bloqueiem a si mesmos e aplica-se somente a tokens com privilégios elevados. Confira também Integridade do código como parte da [segurança principal](https://technet.microsoft.com/library/dd348705.aspx) do Windows Server.
* Assinatura de código. A assinatura de código de todas as ferramentas e scripts usados pelos administradores fornece um mecanismo gerenciável para implantar políticas de bloqueio de aplicativos. Hashes não são dimensionados com alterações rápidas no código, e caminhos de arquivo não fornecem um alto nível de segurança. Você deve combinar as regras do AppLocker a uma [política de execução](https://technet.microsoft.com/library/ee176961.aspx) do PowerShell que permita que apenas scripts e código assinado específicos sejam [executados](https://technet.microsoft.com/library/hh849812.aspx).
* Política de Grupo. Crie uma política administrativa global que seja aplicada a qualquer estação de trabalho do domínio usada para gerenciamento (e bloqueie o acesso de todas as outras) e às contas de usuário autenticadas nessas estações de trabalho.
* Provisionamento com segurança avançada. Proteja a imagem da estação de trabalho protegida de linha de base para evitar violações. Use medidas de segurança como criptografia e isolamento para armazenar imagens, máquinas virtuais e scripts e restringir o acesso (talvez com o uso de um processo de check-in/check-out auditável).
* Aplicação de patches. Mantenha uma compilação consistente (ou tenha imagens separadas para desenvolvimento, operações e outras tarefas administrativas), verifique rotineiramente se há alterações e malware, mantenha a compilação atualizada e ative os computadores apenas quando eles forem necessários.
* Criptografia. Verifique se as estações de trabalho têm um TPM para habilitar com mais segurança o [EFS](https://technet.microsoft.com/library/cc700811.aspx) (Encrypting File System) e o BitLocker. Se estiver usando o Windows To Go, use somente chaves USB criptografadas junto com o BitLocker.
* Governança. Use GPOs do AD DS para controlar todas as interfaces do Windows dos administradores, como o compartilhamento de arquivos. Inclua as estações de trabalho de gerenciamento em processos de auditoria, monitoramento e registro em log. Acompanhe todo o acesso e uso de administradores e desenvolvedores.

## <a name="summary"></a>Resumo
O uso de uma configuração de estação de trabalho protegida para administrar os serviços de nuvem, as Máquinas Virtuais e os aplicativos do Azure pode ajudá-lo a evitar vários riscos e ameaças que podem ocorrer deviso ao gerenciamento remoto da infraestrutura de TI crítica. O Azure e o Windows fornecem mecanismos que você pode adotar para proteger e controlar a comunicação, a autenticação e o comportamento do cliente.

## <a name="next-steps"></a>Próximas etapas
Os seguintes recursos estão disponíveis para fornecer informações mais gerais sobre o Azure e os serviços relacionados da Microsoft, além de itens específicos mencionados neste documento:

* [Proteção de acesso privilegiado](https://technet.microsoft.com/library/mt631194.aspx) – obtenha os detalhes técnicos para projetar e criar uma estação de trabalho administrativa segura para o gerenciamento do Azure
* [Central de Confiabilidade da Microsoft](https://microsoft.com/en-us/trustcenter/cloudservices/azure) – saiba mais sobre os recursos da plataforma Azure que protegem a malha do Azure e as cargas de trabalho que são executados no Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717.aspx) – o local em que vulnerabilidades de segurança da Microsoft, inclusive problemas do Azure, podem ser relatadas ou enviadas por email a [secure@microsoft.com](mailto:secure@microsoft.com)
* [Blog de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) – leia as últimas notícias da Segurança do Azure

<!--Image references-->
[1]: ./media/azure-security-management/typical-management-network-topology.png
[2]: ./media/azure-security-management/stand-alone-hardened-workstation-topology.png
[3]: ./media/azure-security-management/hardened-workstation-enabled-with-hyper-v.png
[4]: ./media/azure-security-management/hardened-workstation-using-windows-to-go-on-a-usb-flash-drive.png
