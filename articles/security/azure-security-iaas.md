---
title: "Práticas recomendadas de segurança para as cargas de trabalho IaaS no Azure | Microsoft Docs"
description: " A migração das cargas de trabalho para o Azure IaaS traz oportunidades para reavaliar nosso designs "
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2017
ms.author: barclayn
ms.openlocfilehash: f93211d289553b7a8afbe8c17fa4847f3d4585a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Práticas recomendadas de segurança para as cargas de trabalho IaaS no Azure

Quando você começou a pensar sobre como mover as cargas de trabalho para o Azure IaaS (infraestrutura como um serviço), provavelmente chegou à conclusão de que algumas questões são familiares. Talvez você já tenha experiência em proteger os ambientes virtuais. Ao mudar para o Azure IaaS, você poderá aplicar seu conhecimento em proteger os ambientes virtuais e usar um novo conjunto de opções para ajudar a proteger seus ativos.

Vamos começar dizendo que não devemos esperar trazer os recursos locais individualmente para o Azure. Os novos desafios e as novas opções trazem uma oportunidade para reavaliar os designs, ferramentas e processos existentes.

Sua responsabilidade pela segurança é baseada no tipo de serviço de nuvem. A tabela a seguir resume a proporção de responsabilidade assumida pela Microsoft e por você:


![Áreas de Responsabilidade](./media/azure-security-iaas/sec-cloudstack-new.png)


Abordaremos algumas das opções disponíveis no Azure que podem ajudá-lo a atender aos requisitos de segurança de sua organização. Tenha em mente que os requisitos de segurança podem variar para tipos diferentes de cargas de trabalho. Nenhuma dessas práticas recomendadas pode, por si só, proteger seus sistemas. Como tudo em segurança, você precisa escolher as opções apropriadas e ver como as soluções podem complementar-se, preenchendo as lacunas.

## <a name="use-privileged-access-workstations"></a>Usar Estações de Trabalho com Acesso Privilegiado

As organizações geralmente são vítimas de ataques cibernéticos por causa dos administradores que executam ações enquanto usam contas com direitos elevados. Geralmente, isso não é feito com más intenções, mas porque a configuração existente e os processos permitem fazê-lo. A maioria desses usuários entende o risco dessas ações na teoria, mas ainda optam por executá-las.

Ações como verificação de email e navegação na Internet parecem seguras. Porém, elas podem expor contas com privilégios elevados a serem comprometidas por atores mal-intencionados. Atividades de navegação, emails especialmente criados ou outras técnicas podem ser usados para obter acesso à sua empresa. É altamente recomendável o uso de SAWs (estações de trabalho de gerenciamento seguro) para realizar todas as tarefas de administração do Azure. As SAWs são uma maneira de reduzir a exposição a comprometimentos acidentais.

As Estações de Trabalho com Acesso Privilegiado (PAWs) fornecem um sistema operacional dedicado para as tarefas confidenciais protegido contra ataques da Internet e vetores de ameaça. Separar essas contas e tarefas confidenciais de dispositivos e estações de trabalho de uso diário proporciona proteção forte. Essa separação limita o impacto de ataques de phishing, vulnerabilidades de SO e aplicativos, vários ataques de representação e ataques de roubo de credenciais. (registro de pressionamento de teclas, Pass-the-Hash e Pass-the-Ticket)

A abordagem PAW é uma extensão da prática recomendada e bem estabelecida de usar uma conta administrativa individualmente atribuída. A conta administrativa é separada de uma conta de usuário padrão. Uma PAW fornece uma estação de trabalho confiável para essas contas confidenciais.

Para obter mais informações e orientações de implementação, confira [Estações de trabalho de acesso privilegiado](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations).

## <a name="use-multi-factor-authentication"></a>Usar Autenticação Multifator

No passado, o perímetro da rede era usado para controlar o acesso aos dados corporativos. Em um mundo de nuvem e dispositivos móveis, a identidade é o plano de controle: você pode usá-la para controlar o acesso aos serviços IaaS de qualquer dispositivo. Você também usá-la para obter visibilidade e percepção de onde e como os dados estão sendo usados. Proteger a identidade digital dos usuários do Azure é a base para proteger suas assinaturas contra o roubo de identidade e outros crimes cibernéticos.

Uma das etapas mais vantajosas que podem ser tomadas para proteger uma conta é habilitar a autenticação de dois fatores. A autenticação de dois fatores é uma maneira de autenticar usando algo além de uma senha. Isso ajuda a minimizar o risco do acesso por uma pessoa que consegue obter a senha de outra pessoa.

A [Autenticação Multifator do Azure](../multi-factor-authentication/multi-factor-authentication.md) ajuda a proteger o acesso a dados e aplicativos enquanto atende à demanda dos usuários para um processo de logon simples. Ele fornece autenticação forte por meio de uma variedade de opções de fácil verificação: chamada telefônica, mensagem de texto ou notificação de aplicativo móvel. Os usuários escolhem o método preferido.

A maneira mais fácil de usar a Autenticação Multifator do Azure é o aplicativo móvel do Microsoft Authenticator, que pode ser usado em dispositivos móveis que executam o Windows, IOS e Android. Com a versão mais recente do Windows 10 e a integração do Active Directory local com o Azure AD (Azure Active Directory), o [Windows Hello para Empresas](../active-directory/active-directory-azureadjoin-passport-deployment.md) pode ser usado para fazer um logon único e perfeito nos recursos do Azure. Neste caso, o dispositivo do Windows 10 é usado como o segundo fator para a autenticação.

No caso de contas que gerenciam sua assinatura do Azure e de contas que podem entrar em máquinas virtuais, o uso da autenticação multifator fornece um nível muito maior de segurança do que o uso de apenas uma senha. Outras formas de autenticação de dois fatores podem funcionar muito bem, mas implantá-las pode ser complicado se ainda não estiverem em produção.

A captura de tela a seguir mostra algumas das opções disponíveis para a Autenticação Multifator do Azure:

![Opções da Autenticação Multifator](./media/azure-security-iaas/mfa-options.png)

## <a name="limit-and-constrain-administrative-access"></a>Limitar e restringir o acesso administrativo

Proteger as contas que podem gerenciar sua assinatura do Azure é extremamente importante. O comprometimento de alguma dessas contas nega o valor de todas as outras etapas que você pode tomar para garantir a confidencialidade e a integridade dos dados. Como ilustrado recentemente por [Edward Snowden](https://en.wikipedia.org/wiki/Edward_Snowden), ataques internos representam uma enorme ameaça à segurança geral de qualquer organização.

Avalie os direitos administrativos que devem ser concedidos a cada pessoa usando critérios semelhantes a estes:

- Eles estão realizando tarefas que exigem privilégios administrativos?
- Com que frequência as tarefas são realizadas?
- Existe um motivo específico para as tarefas não poderem ser realizadas por outro administrador em seu nome?

Documente todas as outras abordagens alternativas conhecidas para conceder o privilégio e por que cada uma não é aceitável.

O uso de uma administração apropriada impede a existência desnecessária de contas com direitos elevados nos períodos em que esses direitos não são necessários. Contas que têm direitos elevados por tempo limitado para que os administradores possam fazer seus trabalhos. Em seguida, esses direitos são removidos no final de um turno ou quando uma tarefa é concluída.

Você pode usar o [Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md) para gerenciar, monitorar e controlar o acesso em sua organização. Ele ajuda você a permanecer ciente das ações que as pessoas realizam em sua organização. Ele também oferece administração just-in-time para o AD do Azure, introduzindo o conceito de administradores qualificados. Estas são as pessoas que têm contas com o potencial de receberem os direitos de administrador. Esses tipos de usuários podem passar por um processo de ativação e receber direitos de administrador por um tempo limitado.


## <a name="use-devtest-labs"></a>Usar DevTest Labs

Usar o Azure para laboratórios e ambientes de desenvolvimento permite que as organizações tenham agilidade no desenvolvimento e teste, acabando com os atrasos introduzidos pela aquisição do hardware. Infelizmente, a falta de familiaridade com o Azure ou um desejo de ajudar a agilizar sua adoção pode levar o administrador a ser excessivamente permissivo com a atribuição de direitos. Esse risco pode expor a organização a ataques internos sem querer. Alguns usuários podem receber muito mais acesso do que deveriam.

O serviço [Azure DevTest Labs](../devtest-lab/devtest-lab-overview.md) utiliza o [RBAC](../active-directory/role-based-access-control-what-is.md) (Controle de Acesso Baseado em Função do Azure). Com o RBAC, você pode separar as tarefas dentro de sua equipe em funções que concedem somente o nível de acesso necessário para os usuários fazerem seus trabalhos. O RBAC vem com funções predefinidas (proprietário, usuário do laboratório e colaborador). Você pode até usar essas funções para atribuir direitos a parceiros externos e simplificar muito a colaboração.

Como o DevTest Labs usa o RBAC, é possível criar outras [funções personalizadas](../devtest-lab/devtest-lab-grant-user-permissions-to-specific-lab-policies.md). O DevTest Labs não apenas simplifica o gerenciamento de permissões, mas também o processo de provisionar os ambientes. Ele também ajuda a lidar com outros desafios típicos de equipes que trabalham em ambientes de desenvolvimento e teste. Ele requer certa preparação, mas a longo prazo facilitará as coisas para sua equipe.

Os principais recursos do Azure DevTest Labs incluem:

- Controle administrativo sobre as opções disponíveis para os usuários. O administrador pode gerenciar centralmente aspectos como tamanhos da VM permitidos, número máximo de VMs e quando as VMs são iniciadas e finalizadas.
- Automação da criação do ambiente de laboratório.
- Controle de custos.
- Distribuição simplificada das VMs para o trabalho colaborativo temporário.
- Autoatendimento que permite aos usuários provisionar seus laboratórios usando modelos.
- Gerenciando e limitando o consumo.

![Usando o DevTest Labs para criar um laboratório](./media/azure-security-iaas/devtestlabs.png)

Não há custos adicionais associados ao uso do DevTest Labs. A criação de laboratórios, políticas, modelos e artefatos é gratuita. Você paga apenas pelos recursos do Azure usados em seus laboratórios, como máquinas virtuais, contas de armazenamento e redes virtuais.



## <a name="control-and-limit-endpoint-access"></a>Controlar e limitar o acesso do ponto de extremidade

Hospedar laboratórios ou sistemas de produção no Azure significa que os sistemas precisam ser acessíveis pela Internet. Por padrão, uma nova máquina virtual do Windows tem a porta RDP acessível pela Internet e uma máquina virtual Linux tem a porta SSH aberta. Medidas para 'limitar os pontos de extremidade expostos' são necessárias a fim de minimizar o risco do acesso não autorizado.

As tecnologias no Azure podem ajudá-lo a limitar o acesso a esses pontos de extremidade administrativos. No Azure, você pode usar os [NSGs](../virtual-network/virtual-networks-nsg.md) (grupos de segurança de rede). Quando você usa o Azure Resource Manager para a implantação, os NSGs limitam o acesso de todas as redes para apenas os pontos de extremidade de gerenciamento (RDP ou SSH). Quando você pensar em NSGs, considere as ACLs do roteador. Você pode usá-las para controlar totalmente a comunicação de rede entre os vários segmentos das redes do Azure. Isso é semelhante a criar redes em redes de perímetro ou outras redes isoladas. Elas não inspecionam o tráfego, mas ajudam na segmentação da rede.


No Azure, você pode configurar um [VPN site a site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) da sua rede local. Um VPN site a site estende sua rede local para a nuvem. Isso lhe dá outra oportunidade de usar NSGs, pois você também pode modificar os NSGs para não permitir o acesso de outro lugar que não seja a rede local. Em seguida, você pode exigir que a administração seja feita primeiro pela conexão com a rede do Azure por meio de VPN.

A opção VPN site a site pode ser muito atraente nos casos em que você hospeda os sistemas de produção que são muito integrados com seus recursos locais no Azure.

Como alternativa, você pode usar a opção [ponto a site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md) nas situações em que você deseja gerenciar os sistemas que não precisam acessar os recursos locais. Esses sistemas podem ser isolados em sua própria rede virtual do Azure. Os administradores podem usar VPN para o ambiente hospedado pelo Azure de suas estações de trabalho administrativas.

>[!NOTE]
>Você pode usar a opção VPN para reconfigurar as ACLs nos NSGs a fim de impedir o acesso aos pontos de extremidade de gerenciamento pela Internet.

Outra opção que vale a pena considerar é uma implantação do [Gateway da Área de Trabalho Remota](../multi-factor-authentication/multi-factor-authentication-get-started-server-rdg.md). Você pode usar essa implantação para se conectar com segurança a servidores de área de trabalho remota via HTTPS ao mesmo tempo em que aplica controles mais detalhados a essas conexões.

Os recursos que você acessaria incluem:

- Opções do administrador para limitar as conexões às solicitações de sistemas específicos.
- Autenticação com cartão inteligente ou autenticação multifator do Azure.
- Controle sobre quais sistemas alguém pode conectar por meio do gateway.
- Controle sobre o redirecionamento do dispositivo e do disco.

## <a name="use-a-key-management-solution"></a>Uso de uma solução de gerenciamento de chaves

O gerenciamento de chaves seguro é fundamental para proteger os dados na nuvem. Com o [Cofre de Chaves do Azure](../key-vault/key-vault-whatis.md), você pode armazenar com segurança as chaves de criptografia e pequenos segredos, como senhas nos módulos de segurança do hardware (HSMs). Para garantia extra, você pode importar ou gerar chaves em HSMs.

A Microsoft processará suas chaves em HSMs FIPS 140-2 Nível 2 validados (hardware e firmware). Execute o monitoramento e a auditoria do uso das chaves com o registro em log do Azure: redirecione logs no Azure ou em seu sistema SIEM (gerenciamento de eventos e de informações de segurança) para obter mais análises e detecções de ameaças.

Qualquer pessoa com uma assinatura do Azure pode criar e usar os cofres de chaves. Embora o Key Vault beneficie os desenvolvedores e administradores de segurança, ele pode ser implementado e gerenciado pelo administrador responsável por gerenciar os serviços do Azure em uma organização.


## <a name="encrypt-virtual-disks-and-disk-storage"></a>Criptografar discos virtuais e o armazenamento de discos

O [Azure Disk Encryption](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0) endereça a ameaça do roubo de dados ou a exposição contra o acesso não autorizado feito movendo um disco. O disco pode ser anexado a outro sistema como uma forma de ignorar os outros controles de segurança. A criptografia de disco usa o [BitLocker](https://technet.microsoft.com/library/hh831713) no Windows e o DM-Crypt no Linux para criptografar o sistema operacional e as unidades de dados. O Azure Disk Encryption se integra com o Key Vault para controlar e gerenciar as chaves de criptografia. Ele está disponível para VMs padrão e VMs com armazenamento premium.

Para saber mais, confira [Azure Disk Encryption em VMs IaaS com Windows e Linux](azure-security-disk-encryption.md).

A [Criptografia do Serviço de Armazenamento do Azure](../storage/common/storage-service-encryption.md) ajuda a proteger seus dados em repouso. Ela é habilitada no nível da conta de armazenamento. Ela criptografa os dados conforme eles são gravados em nossos data centers e eles são descriptografados automaticamente quando você a acessa. Ele oferece suporte para os seguintes cenários:

- Criptografia de blobs de bloco, blobs de acréscimo e blobs de páginas
- Criptografia de VHDs arquivados e modelos colocados no Azure localmente
- Criptografia de sistema operacional subjacente e discos de dados para VMs de IaaS criadas com seus VHDs

Antes de prosseguir com a Criptografia de Armazenamento do Azure, você deve estar ciente de duas limitações:

- não está disponível nas contas de armazenamento clássicas.
- só criptografa os dados gravados após a criptografia estar habilitada.

## <a name="use-a-centralized-security-management-system"></a>Usar um sistema de gerenciamento de segurança centralizado

Seus servidores precisam ser monitorados para a aplicação de patch, configuração, eventos e atividades que podem ser considerados preocupações com a segurança. Para lidar com essas preocupações, você pode usar a [Central de Segurança](https://azure.microsoft.com/services/security-center/) e a [Conformidade e Segurança do Operations Management Suite](https://azure.microsoft.com/services/security-center/). Essas duas opções vão além da configuração no sistema operacional. Eles também fornecem monitoramento da configuração da infraestrutura subjacente, como configuração de rede e o uso da solução de virtualização.

## <a name="manage-operating-systems"></a>Gerenciar sistemas operacionais

Em uma implantação IaaS, você ainda é responsável pelo gerenciamento dos sistemas que implanta, assim como por qualquer outro servidor ou estação de trabalho em seu ambiente. Patches, proteção, atribuições de direitos e qualquer outra atividade relacionadas à manutenção do sistema ainda são sua responsabilidade. Para os sistemas que são muito integrados com seus recursos locais, você pode querer usar as mesmas ferramentas e procedimentos que está usando no local para coisas como antivírus, antimalware, aplicação de patches e backup.

### <a name="harden-systems"></a>Proteger sistemas
Todas as máquinas virtuais no Azure IaaS devem ser protegidas para que apenas exponham os pontos de extremidade dos serviços necessários para os aplicativos instalados. Para as máquinas virtuais do Windows, siga as recomendações que a Microsoft publica como linhas de base para a solução [Security Compliance Manager](https://technet.microsoft.com/solutionaccelerators/cc835245.aspx).

O Security Compliance Manager é uma ferramenta gratuita. Você pode usá-lo para configurar e gerenciar seus desktops, data center tradicional e nuvem pública e privada rapidamente usando a política de grupo e o System Center Configuration Manager.

O Security Compliance Manager fornece políticas prontas para implantar e pacotes de configuração de DCM testados. Essas linhas de base são baseadas em recomendações das [Diretrizes de segurança da Microsoft](https://technet.microsoft.com/en-us/library/cc184906.aspx) e práticas recomendadas do setor. Elas ajudam a gerenciar os descompassos de configuração, atender aos requisitos de conformidade e reduzir as ameaças de segurança.

Você pode usar o Security Compliance Manager para importar a configuração atual dos computadores usando dois métodos diferentes. Primeiro, você pode importar as políticas de grupo baseadas no Active Directory. Você também pode importar a configuração de um computador de referência "mestre dourado" usando a [ferramenta LocalGPO](https://blogs.technet.microsoft.com/secguide/2016/01/21/lgpo-exe-local-group-policy-object-utility-v1-0/) para fazer backup da política de grupo local. Você pode importar a política de grupo local para o Security Compliance Manager.

Compare seus padrões de práticas recomendadas do setor, personalize-os e crie novas políticas e pacotes de configuração de Gerenciamento de Configurações Desejadas. Linhas de base foram publicadas para todos os sistemas operacionais suportados, incluindo o Windows 10 Atualização de Aniversário e o Windows Server 2016.


### <a name="install-and-manage-antimalware"></a>Instalar e gerenciar o antimalware

Para ambientes hospedados separadamente do seu ambiente de produção, você pode usar uma extensão de antimalware para ajudar a proteger máquinas virtuais e serviços de nuvem. Ela se integra à [Central de segurança do Azure](../security-center/security-center-intro.md).


O [Microsoft Antimalware](azure-security-antimalware.md) inclui recursos como a proteção em tempo real, verificação agendada, remediação de malware, atualizações de assinatura, atualizações do mecanismo, exemplos de relatório, coleta de eventos de exclusão e [suporte PowerShell](https://msdn.microsoft.com/library/dn771715.aspx).

![Antimalware do Azure](./media/azure-security-iaas/azantimalware.png)

### <a name="install-the-latest-security-updates"></a>Instalar as últimas atualizações de segurança
Algumas das primeiras cargas de trabalho que os clientes mudam para o Azure são laboratórios e sistemas voltados para fora. Se as máquinas virtuais hospedadas hospedam aplicativos ou serviços que precisam ser acessados pela Internet, preste atenção nos patches. Patch além do sistema operacional. As vulnerabilidades sem patch dos aplicativos de terceiros também podem levar a problemas que podem ser evitados com um bom gerenciamento de patches.

### <a name="deploy-and-test-a-backup-solution"></a>Implantar e testar uma solução de backup

Assim como as atualizações de segurança, um backup precisa ser tratado da mesma maneira como você lida com qualquer outra operação. Isso é verdadeiro para os sistemas que fazem parte de seu ambiente de produção que se estende para a nuvem. Os sistemas de desenvolvimento e teste devem seguir as estratégias de backup que ofereçam recursos de restauração semelhantes aos que os usuários já utilizam com base em sua experiência com os ambientes locais.

As cargas de trabalho de produção movidas para o Azure devem se integrar a soluções de backup existentes quando possível. Você também pode usar o [Backup do Azure](../backup/backup-azure-arm-vms.md) para ajudar a solucionar suas necessidades de backup.


## <a name="monitor"></a>Monitoramento

A [Central de Segurança](../security-center/security-center-intro.md) fornece uma avaliação contínua do estado de segurança de seus recursos do Azure para identificar as possíveis vulnerabilidades de segurança. Uma lista de recomendações orienta você no processo de configuração dos controles necessários.

Os exemplos incluem:

- Provisionamento de antimalware para ajudar a identificar e remover software mal-intencionado.
- Como configurar grupos de segurança de rede e as regras para controlar o tráfego para máquinas virtuais.
- O provisionamento de firewalls do aplicativo Web para ajudar a proteger contra ataques direcionados aos seus aplicativos Web.
- Implantando atualizações de sistema ausentes.
- Abordando configurações do sistema operacional que não coincidem com as linhas de base recomendadas.

A imagem a seguir mostra algumas das opções que você pode habilitar na Central de Segurança.

![Políticas da Central de Segurança do Azure](./media/azure-security-iaas/security-center-policies.png)

O [Operations Management Suite](../operations-management-suite/operations-management-suite-overview.md) é a solução de gerenciamento de TI baseada em nuvem da Microsoft que ajuda a gerenciar e proteger sua infraestrutura de nuvem e local. Como o Operations Management Suite é implementado como um serviço baseado em nuvem, ele pode ser implantado rapidamente e com um investimento mínimo em recursos de infraestrutura.

Os novos recursos são entregues automaticamente, evitando os custos contínuos com manutenção e atualização. O Operations Management Suite também se integra com o System Center Operations Manager. Ele tem componentes diferentes para ajudá-lo a gerenciar melhor as cargas de trabalho do Azure, incluindo um módulo de [Segurança e Conformidade](../operations-management-suite/oms-security-getting-started.md).

Você pode usar os recursos de segurança e conformidade no Operations Management Suite para exibir informações sobre seus recursos. As informações estão organizadas em quatro categorias principais:

- **Domínios de segurança**: explorar mais registros de segurança ao longo do tempo. Acessar a avaliação de malware, atualizar a avaliação, informações de segurança de rede, informações de identidade e acesso e computadores com eventos de segurança. Aproveite o acesso rápido ao painel da Central de Segurança do Azure.
- **Problemas Importantes**: identifique rapidamente o número de problemas ativos e sua severidade.
- **Detecções** (visualização): identifique os padrões de ataque visualizando os alertas de segurança à medida que eles ocorrem em relação aos recursos.
- **Inteligência contra ameaças**: identifique os padrões de ataque visualizando o número total de servidores com tráfego IP de saída mal-intencionado, tipo de ameaça mal-intencionada e um mapa que mostra a origem desses IPs.
- **Consultas comuns de segurança**: veja uma lista das consultas de segurança mais comuns que você pode usar para monitorar seu ambiente. Quando você clica em uma dessas consultas, ela abre a folha **Pesquisa** e mostra os resultados da consulta.

A captura de tela a seguir mostra um exemplo das informações que podem ser exibidas pelo Operations Management Suite.

![Linhas de base de segurança do Operations Management Suite](./media/azure-security-iaas/oms-security-baseline.png)

## <a name="next-steps"></a>Próximas etapas

* [Blog da equipe de segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/)
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx)
* [Padrões e práticas recomendadas de segurança do Azure](security-best-practices-and-patterns.md)
