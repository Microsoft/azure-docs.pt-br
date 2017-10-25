---
title: "Funcionalidades técnicas de segurança do Azure | Microsoft Docs"
description: "Aprenda sobre os serviços de computação baseados em nuvem que incluem uma ampla seleção de instâncias e serviços de computação, os quais podem ser escalados verticalmente de forma automática para atender às necessidades de seu aplicativo ou empresa."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/26/2017
ms.author: TomSh
ms.openlocfilehash: a32693a274c2615a515838306d72dbe64a5d1763
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-technical-capabilities"></a>Funcionalidades técnicas de segurança do Azure

Para ajudar os clientes atuais e potenciais do Azure a entender e usar as várias funcionalidades relacionadas à segurança disponíveis na Plataforma do Azure e em torno dela, a Microsoft desenvolveu uma série de white papers, visões gerais de segurança, práticas recomendadas e listas de verificação. Os tópicos variam em termos de abrangência e profundidade e são atualizados periodicamente. Este documento faz parte dessa série, conforme resumido na seção Resumo a seguir. Mais informações sobre esta série sobre a Segurança do Azure podem ser encontradas em (URL).

## <a name="azure-platform"></a>Plataforma do Azure

O [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) é uma plataforma de nuvem composta de serviços de infraestrutura e de aplicativos, com análise avançada e serviços de dados integrados, além de serviços e ferramentas de desenvolvedor, hospedados nos data centers da nuvem pública da Microsoft. Os clientes usam o Azure para muitos cenários e recursos diferentes, desde computação básica, rede e armazenamento, até serviços de aplicativos Web e móveis, passando por cenários de nuvem completos, como no caso da Internet das Coisas. Esses cenários podem ser usados com tecnologias de software livre e implantados como nuvem híbrida ou hospedados dentro do data center de um cliente. O Azure fornece a tecnologia de nuvem na forma de blocos de construção para ajudar as empresas a economizar custos, inovar rapidamente e gerenciar sistemas de maneira proativa. Ao migrar ativos de TI para um provedor de nuvem ou basear seus ativos nesse provedor, você está confiando na capacidade dessa organização de proteger seus aplicativos e dados com os serviços e os controles que ela oferece para gerenciar a segurança de seus ativos baseados em nuvem.

O Microsoft Azure é o único provedor de computação em nuvem que oferece uma plataforma de aplicativo segura e consistente, com infraestrutura como um serviço, para que as equipes trabalhem com diferentes níveis de habilidades na nuvem e diferentes níveis de complexidade de projeto, com análises e serviços de dados integrados que descobrem inteligência de dados onde ela estiver, seja em estruturas abertas, ferramentas e plataformas da Microsoft ou de terceiros, oferecendo a opção de integrar a nuvem com recursos locais, bem como de implantar os serviços de nuvem do Azure em data centers locais. Como parte da Nuvem Confiável da Microsoft, os clientes confiam no Azure para fornecer o mais elevado nível de segurança, confiabilidade, conformidade e privacidade do setor, bem como uma ampla rede de pessoas, parceiros e processos para dar suporte às organizações na nuvem.

Com o Microsoft Azure, você pode:

- Acelerar a inovação na nuvem.

- Impulsionar aplicativos e decisões de negócios com insights.

- Criar livremente e implantar em qualquer lugar.

- Proteger seus negócios.

## <a name="scope"></a>Escopo

O ponto focal deste white paper são os recursos e funcionalidades de segurança que dão suporte aos componentes núcleo do Microsoft Azure, que são o [Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-introduction), os [Bancos de Dados SQL do Microsoft Azure](https://docs.microsoft.com/azure/sql-database/), o [modelo de máquina virtual do Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/    ) e as ferramentas e a infraestrutura que os gerenciam. Este white paper se concentra nas funcionalidades técnicas do Microsoft Azure que estão disponíveis para habilitar você, como cliente, a cumprir sua função de proteger a segurança e a privacidade de seus dados.

A importância de entender esse modelo de responsabilidade compartilhada é essencial para os clientes que estão migrando para a nuvem. Os provedores de nuvem oferecem vantagens consideráveis em termos de esforços de segurança e conformidade, mas essas vantagens não desobrigam o cliente de proteger seus usuários, aplicativos e ofertas de serviço.

Para soluções de IaaS, o cliente é responsável ou tem responsabilidade compartilhada, por proteger e gerenciar o sistema operacional, a configuração de rede, os aplicativos, a identidade, os clientes e os dados.  Soluções de PaaS se baseiam em implantações de IaaS e o cliente ainda é responsável ou tem a responsabilidade compartilhada de proteger e gerenciar aplicativos, identidades, clientes e dados. Não obstante, no caso de soluções de SaaS, o cliente continua sendo responsável. Ele precisa garantir que os dados sejam classificados corretamente e ele compartilha a responsabilidade de gerenciar seus usuários e dispositivos de ponto de extremidade.

Este documento não aborda em detalhes nenhum dos componentes relacionados da plataforma Microsoft Azure, como os Sites do Azure, o Azure Active Directory, o HDInsight, os Serviços de Mídia e outros serviços que estão dispostos em camadas sobre os componentes núcleo. Embora um nível mínimo de informações gerais seja fornecido, presume-se que os leitores estejam familiarizados com os conceitos básicos do Azure, conforme descritos em outras referências fornecidas pela Microsoft e incluídas em links neste white paper.


## <a name="available-security-technical-capabilities-to-fulfil-user-customer-responsibility---big-picture"></a>Funcionalidades técnicas de segurança disponíveis para que o usuário (cliente) cumpra sua responsabilidade – Um panorama

O Microsoft Azure fornece serviços que podem ajudar os clientes a atenderem suas necessidades de segurança, privacidade e conformidade. A figura a seguir ajuda a explicar vários serviços do Azure disponíveis para os usuários construírem uma infraestrutura de aplicativo segura e em conformidade, com base em padrões do setor.

![Funcionalidades técnicas de segurança disponíveis – Um panorama](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig1.png)

## <a name="manage-and-control-identity-and-user-access-protect"></a>Gerenciar e controlar a identidade e o acesso do usuário (proteger)

O Azure ajuda a proteger informações pessoais e de negócios, permitindo que você gerencie credenciais e identidades do usuário e controle o acesso.

### <a name="azure-active-directory"></a>Azure Active Directory

As soluções de gerenciamento de acesso e identidade da Microsoft ajudam a TI a proteger o acesso a aplicativos e recursos no datacenter corporativo e na nuvem, permitindo níveis de validação adicionais, como Autenticação Multifator e políticas de acesso condicional. O monitoramento de atividade suspeita por meio de alertas, auditoria e relatórios de segurança avançados ajuda a reduzir potenciais problemas de segurança. [Azure Active Directory Premium](https://docs.microsoft.com/azure/active-directory/active-directory-editions) fornece logon único para milhares de aplicativos (SaaS) de nuvem e acesso a aplicativos Web executados de forma local.

Os benefícios de segurança do Azure AD (Active Directory) incluem a capacidade de:

- Criar e gerenciar uma identidade única para cada usuário em sua empresa híbrida, mantendo usuários, grupos e dispositivos em sincronia.

- Fornecer acesso de logon único para seus aplicativos, incluindo milhares de aplicativos de SaaS pré-integrados.

- Habilitar a segurança de acesso do aplicativo por meio da imposição da Autenticação Multifator baseada em regras para aplicativos locais e na nuvem.

- Provisionar o acesso remoto seguro a aplicativos Web locais por meio do Proxy de Aplicativo Azure AD.

O [portal do Azure Active Directory](http://aad.portal.azure.com/) está disponível como parte do portal do Azure. Neste painel, você pode ter uma visão geral do estado de sua organização e pode aprofundar-se facilmente para gerenciar o diretório, os usuários ou o acesso a aplicativos.

![Azure Active Directory](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig2.png)

A seguir, temos as funcionalidades centrais de gerenciamento de identidade do Azure:

- Logon único

- Autenticação multifator

- Relatórios baseados em aprendizado de máquina, alertas e monitoramento de segurança

- Gerenciamento de acesso e identidade do consumidor

- Registro de dispositivos

- Privileged Identity Management

- Identity Protection

#### <a name="single-sign-on"></a>Logon único

[SSO (logon único)](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) significa poder acessar todos os aplicativos e recursos de que você precisa para fazer negócios, conectando-se apenas uma vez usando uma única conta de usuário. Depois de conectado, você pode acessar todos os aplicativos necessários sem precisar ser autenticado (por exemplo, digitar uma senha) uma segunda vez.

Muitas organizações contam com aplicativos de SaaS (software como serviço), como o Office 365, o Box e o Salesforce, para aumentar a produtividade do usuário final. Historicamente, a equipe de TI precisava criar e atualizar individualmente as contas de usuário em cada aplicativo SaaS e os usuários precisavam lembrar uma senha para cada aplicativo SaaS.

O [Azure AD estende o Active Directory local para a nuvem](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis), permitindo que os usuários usem suas contas organizacionais primárias não apenas para se conectar a recursos corporativos e dispositivos ingressados no domínio, mas também a todos os aplicativos Web e de SaaS necessários para seu trabalho.

Os usuários não precisam gerenciar vários conjuntos de nomes de usuário e senhas, o acesso a aplicativos pode ser provisionado ou desconfigurado automaticamente com base em grupos organizacionais e seu status como funcionário. O [Azure AD introduz controles de governança de acesso e segurança](https://docs.microsoft.com/azure/active-directory/active-directory-sso-integrate-saas-apps) que permitem que você gerencie centralmente o acesso dos usuários a aplicativos de SaaS.

#### <a name="multi-factor-authentication"></a>Autenticação multifator

A [MFA (Autenticação Multifator) do Azure](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) é um método de autenticação que exige o uso de mais de um método de verificação e adiciona uma segunda camada de segurança crítica às entradas e transações dos usuários. A [MFA ajuda a proteger](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works) o acesso a dados e aplicativos, ao mesmo tempo em que atende à demanda dos usuários por um processo de entrada simples. Ela fornece autenticação forte por meio de uma variedade de opções de verificação – chamada telefônica, mensagem de texto, notificação de aplicativo móvel ou código de verificação e tokens OAuth de terceiros.

#### <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Relatórios baseados em aprendizado de máquina, alertas e monitoramento de segurança

Monitoramento e alertas de segurança e relatórios baseados no aprendizado de máquina que identificam padrões de acesso inconsistentes podem ajudá-lo a proteger seus negócios. Você pode usar os relatórios de uso e de acesso do Active Directory do Azure para obter visibilidade quanto à integridade e a segurança do diretório da sua organização. Com essas informações, um administrador de diretório pode determinar melhor onde possíveis riscos de segurança podem estar, de modo que pode fazer planos adequados para mitigar esses riscos.

No portal clássico do Azure ou no [portal do Azure Active Directory](http://aad.portal.azure.com/), os [relatórios](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) são categorizados das seguintes maneiras:

- Relatórios de anomalias: contêm eventos de entrada que nós identificamos como anômalos. Nossa meta é que você fique ciente dessas atividades e possa decidir se um evento é suspeito ou não.

- Relatórios de aplicativos integrados: fornecem um panorama de como os aplicativos em nuvem estão sendo usados na sua organização. O Active Directory do Azure oferece integração com milhares de aplicativos em nuvem.

- Relatórios de erros: indicam erros que podem ocorrer ao provisionar contas para aplicativos externos.

- Relatórios específicos do usuário: exibem dados de atividade de entrada/dispositivo de um usuário específico.

- Logs de atividades – contêm um registro de todos os eventos auditados nas últimas 24 horas, nos últimos 7 dias ou 30 dias, bem como alterações de atividades do grupo e atividades de registro e redefinição de senha.

#### <a name="consumer-identity-and-access-management"></a>Gerenciamento de acesso e identidade do consumidor

O [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) é um serviço de gerenciamento de identidade global e altamente disponível para aplicativos voltados para o consumidor que pode ser dimensionado para centenas de milhões de identidades. Ele pode ser integrado a plataformas móveis e da Web. Seus clientes podem fazer logon em todos os seus aplicativos por meio de experiências personalizáveis usando suas contas sociais existentes ou criando novas credenciais.

No passado, desenvolvedores de aplicativos que desejavam [inscrever e conectar consumidores](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview) a seus aplicativos teriam escrito seu próprio código. E eles teriam usado sistemas ou bancos de dados locais para armazenar nomes de usuário e senhas. O Azure Active Directory B2C oferece à sua organização uma maneira melhor de integrar o gerenciamento de identidade do consumidor a aplicativos com a ajuda de uma plataforma segura baseada em padrões e um grande conjunto de políticas extensíveis.

Quando você usa o Azure Active Directory B2C, os consumidores poderão se inscrever nos seus aplicativos usando suas contas sociais existentes (Facebook, Google, Amazon, LinkedIn) ou criando novas credenciais (endereço de email e senha ou o nome de usuário e a senha).

Registro de dispositivos

O [Registro de Dispositivos do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-device-registration-overview) é a base para cenários de [acesso condicional](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-device-registration-overview) baseado em dispositivo. Quando um dispositivo é registrado, o Registro de Dispositivo do Azure Active Directory fornece ao dispositivo uma identidade, que é usada para autenticar o dispositivo quando o usuário faz logon. O dispositivo autenticado e os atributos desse dispositivo podem, em seguida, ser usados para impor políticas de acesso condicional para aplicativos locais e hospedados em nuvem.

Quando combinados com uma solução de [MDM (gerenciamento de dispositivo móvel)](https://www.microsoft.com/itshowcase/Article/Content/588/Mobile-device-management-at-Microsoft), como o Intune, os atributos do dispositivo no Azure Active Directory são atualizados com informações adicionais sobre o dispositivo. Isso permite que você crie regras de acesso condicional que imponham que o acesso dos dispositivos atendam aos padrões de segurança e conformidade.

#### <a name="privileged-identity-management"></a>Privileged Identity Management

O [Privileged Identity Management do Azure AD (Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) permite gerenciar, controlar e monitorar suas identidades privilegiadas e acessar recursos no Azure AD, bem como em outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune.

Às vezes, os usuários precisam executar operações privilegiadas em recursos do Azure ou Office 365 ou outros aplicativos SaaS. Isso geralmente significa que as organizações precisam conceder acesso privilegiado permanente no Azure AD. Esse é um risco de segurança cada vez maior para recursos hospedados em nuvem porque as organizações não podem monitorar de maneira suficiente o que esses usuários estão fazendo com seus privilégios de administrador. Além disso, se uma conta de usuário com acesso privilegiado for comprometida, essa falha poderá afetar a segurança geral da nuvem. O gerenciamento de identidades com privilégios do AD do Azure ajuda a resolver esse risco.

O Gerenciamento de identidades com privilégios do AD do Azure:

- Ver quais usuários são administradores do Azure AD

- Habilitar o acesso administrativo “just in time” para Microsoft Online Services como Office 365 e Intune

- Obter relatórios sobre o histórico de acesso de administrador e as alterações nas atribuições de administrador

- Receber alertas sobre o acesso a uma função com privilégios

#### <a name="identity-protection"></a>Identity Protection

O [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) é um serviço de segurança que fornece uma visão consolidada dos eventos de risco e das possíveis vulnerabilidades que afetam as identidades da sua organização. O Identity Protection usa funcionalidades existentes de detecção de anomalias do Azure Active Directory (disponíveis por meio dos Relatórios de Atividade Anômala do Azure AD) e apresenta novos tipos de evento de risco que podem detectar anomalias em tempo real.

## <a name="secured-resource-access-in-azure"></a>Acesso a recursos protegidos no Azure

O controle de acesso no Azure parte de uma perspectiva de cobrança. O proprietário de uma conta do Azure, acessada pela visitação ao [Centro de Contas do Azure](https://account.windowsazure.com/subscriptions), é o Administrador da Conta (AA). As assinaturas são um contêiner para cobrança, mas também atuam como um limite de segurança: cada assinatura tem um Administrador de Serviços (SA) que pode adicionar, remover e modificar recursos do Azure nessa assinatura usando o [Portal clássico do Azure](https://manage.windowsazure.com/). O SA padrão de uma nova assinatura é o AA, mas o AA pode alterar o SA no Centro de contas do Azure.

![Acesso a recursos protegidos no Azure](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig3.png)

As assinaturas também têm uma associação com um diretório. O diretório define um conjunto de usuários. Eles podem ser usuários corporativos ou de estudantes que criaram o diretório ou podem ser usuários externos (ou seja, Contas da Microsoft). As assinaturas são acessíveis por um subconjunto desses usuários do diretório que foram atribuídos como um serviço SA (Administrador) ou CA (Coadministrador); a única exceção é que, por motivos de herança, Contas da Microsoft (anteriormente chamadas de Windows Live ID) podem ser atribuídas como SA ou CA sem estarem presentes no diretório.

As empresas direcionadas a segurança devem focar em fornecer aos funcionários as permissões exatas necessárias. Muitas permissões podem expor uma conta a ataques. Permissões insuficientes fazem com que os funcionários não consigam trabalhar com eficiência. O [RBAC (controle de acesso baseado em função) do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is) ajuda a resolver esse problema oferecendo gerenciamento de acesso refinado para o Azure.

![Acesso a recursos protegidos ](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig4.png)

Com o RBAC, você pode separar as tarefas dentro de sua equipe e conceder somente a quantidade de acesso que os usuários precisam para realizar seus trabalhos. Em vez de apresentar todos irrestrito permissões em sua assinatura do Azure ou recursos, você pode permitir apenas determinadas ações. Por exemplo, use o RBAC para permitir que um funcionário gerencie máquinas virtuais em uma assinatura, enquanto outro pode gerenciar bancos de dados SQL dentro da mesma assinatura.

![Acesso a recursos protegidos no Azure (RBAC)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig5.png)

## <a name="azure-data-security-and-encryption-protect"></a>Criptografia e segurança de dados no Azure (proteger)

Uma das chaves de proteção de dados na nuvem é responsável por possíveis estados em que os dados podem ocorrer e quais controles estão disponíveis para esse estado. Com relação às práticas recomendadas de criptografia e segurança de dados no Azure, as recomendações são feitas para os seguintes estados de dados.

- Em repouso: isso inclui todos os objetos de armazenamento, contêineres e tipos de informações que existem estaticamente em mídia física, seja ela magnética ou disco óptico.

- Em trânsito: quando dados estão sendo transferidos entre componentes, locais ou programas, como através da rede, através de um barramento de serviço (do local para a nuvem e vice-versa, incluindo conexões híbridas, como o ExpressRoute) ou durante um processo de entrada/saída, ele é considerado como estando em trânsito.

### <a name="encryption--rest"></a>Criptografia em repouso

Para obter a criptografia em repouso, cada um dos seguintes:

Dá suporte a pelo menos um dos modelos de criptografia recomendados detalhados na tabela a seguir para criptografar dados.

| Modelos de criptografia |  |  |  |
| ----------------  | ----------------- | ----------------- | --------------- |
| Criptografia de servidor | Criptografia de servidor | Criptografia de servidor | Criptografia de cliente
| Criptografia do lado do servidor usando chaves de serviço gerenciadas | Criptografia do lado do servidor usando chaves gerenciadas pelo cliente no Azure Key Vault | Criptografia do lado do servidor usando chaves gerenciadas pelo cliente no local |
| • Provedores de recursos do Azure executam as operações de criptografia e descriptografia <br> • A Microsoft gerencia as chaves <br>• Funcionalidade completa na nuvem | • Provedores de recursos do Azure executam as operações de criptografia e descriptografia<br>• O cliente controla as chaves por meio do Azure Key Vault<br>• Funcionalidade completa na nuvem | • Provedores de recursos do Azure executam as operações de criptografia e descriptografia <br>• O cliente controla as chaves no local <br> • Funcionalidade completa na nuvem| • Os serviços do Azure não podem ver dados descriptografados <br>• Os clientes mantêm as chaves localmente (ou em outros repositórios seguros). As chaves não estão disponíveis para os serviços do Azure <br>• Funcionalidade reduzida na nuvem|

### <a name="enabling-encryption-at-rest"></a>Habilitando a criptografia em repouso

**Identifique todos os locais em que você armazena dados**

A meta da Criptografia em repouso é criptografar todos os dados. Isso elimina a possibilidade de perder dados importantes ou todos os locais persistentes. Enumere todos os dados armazenados pelo aplicativo. 

> [!Note] 
> Não apenas "dados de aplicativos" ou "PII", mas todos os dados relacionados ao aplicativo, incluindo metadados da conta (mapeamentos de assinatura, informações de contrato, PII).

Considere quais repositórios você está usando para armazenar dados. Por exemplo:

- Armazenamento externo (por exemplo, SQL Azure, DocumentDB, HDInsights, Data Lake etc.)

- Armazenamento temporário (qualquer cache local que inclui dados de locatário)

- Cache na memória (pode ser inserido no arquivo de paginação)

### <a name="leverage-the-existing-encryption-at-rest-support-in-azure"></a>Aproveite o suporte para criptografia em repouso existente no Azure

Para cada repositório que você usar, aproveite o suporte para criptografia em repouso existente.

- Armazenamento do Azure: consulte [Criptografia do Serviço de Armazenamento do Azure para dados em repouso](https://docs.microsoft.com/azure/storage/storage-service-encryption),

- SQL Azure: consulte [TDE (Transparent Data Encryption), SQL Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx)

- Armazenamento em disco local ou em VM ([Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption))

Para armazenamento em disco local ou em VM, use o Azure Disk Encryption quando houver suporte:

IaaS

Serviços com VMs de IaaS (Windows ou Linux) devem usar o [Azure Disk Encryption](https://microsoft.sharepoint.com/teams/AzureSecurityCompliance/Security/SitePages/Azure%20Disk%20Encryption.aspx) para criptografar volumes que contêm dados do cliente.

PaaS v2

Serviços que estão em execução na PaaS v2 usando o Service Fabric podem usar o Azure Disk Encryption para VMSS (conjunto de dimensionamento de máquinas virtuais) para criptografar suas VMs da PaaS v2.

PaaS v1

No momento, não há suporte para o Azure Disk Encryption na PaaS v1. Portanto, você precisa usar a criptografia no nível do aplicativo para criptografar dados em repouso persistentes.  Isso inclui, sem limitações, dados de aplicativos, arquivos temporários, logs e despejos de memória.

A maioria dos serviços deve tentar utilizar a criptografia do provedor de recursos de armazenamento. Alguns serviços precisam fazer criptografia explícita, por exemplo, qualquer material com chave persistente (certificados, chaves raiz/mestre) deve ser armazenado no Key Vault.

Se houver suporte para criptografia no lado do serviço com chaves gerenciadas pelo cliente, é necessário que haja uma maneira de o cliente nos passar essas chaves. A maneira recomendada e com suporte para fazer isso é a integração com o AKV (Azure Key Vault). Nesse caso, os clientes podem adicionar e gerenciar suas chaves no Azure Key Vault. O cliente pode aprender a usar o AKV em [Introdução ao Azure Key Vault](http://go.microsoft.com/fwlink/?linkid=521402).

Para fazer a integração com o Azure Key Vault, você precisaria adicionar código para solicitar uma chave do AKV quando fosse necessário para a descriptografia.

- Consulte [Azure Key Vault – passo a passo](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step/) para obter informações sobre como fazer a integração com o AKV.

Se der suporte a chaves gerenciadas pelo cliente, você precisará fornecer uma experiência do usuário para o cliente especificar qual Key Vault (ou URI do Key Vault) será usado.

Como a criptografia em repouso envolve a criptografia de dados de locatário, infraestrutura e host, a perda das chaves devido a falhas no sistema ou a atividades mal-intencionadas poderia levar à perda de todos os dados criptografados. Portanto, é essencial que sua solução de criptografia em repouso tenha um amplo histórico de recuperação de desastre resiliente a falhas de sistema e a atividades mal-intencionadas.

Normalmente, serviços que implementam criptografia em repouso ainda são suscetíveis a cenários em que dados ou chaves de criptografia são deixados sem criptografia na unidade host (por exemplo, no arquivo de paginação do sistema operacional host). Sendo assim, os serviços devem garantir que o volume host de seus serviços seja criptografado. Para facilitar isso, a equipe de computação habilitou a implantação da criptografia de host, que usa NKP e extensões do [Bitlocker](https://technet.microsoft.com/library/dn306081.aspx) para o agente e o serviço de DCM criptografarem o volume de host.

A maioria dos serviços são implementados em VMs padrão do Azure. Esses serviços devem receber a [Criptografia de host](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) automaticamente quando a computação habilitá-la. Para serviços executados em clusters gerenciados por computação, a criptografia de host será habilitada automaticamente conforme o Windows Server 2016 for distribuído.

### <a name="encryption-in-transit"></a>Criptografia em trânsito

A proteção dos dados em trânsito deve ser parte essencial de sua estratégia de proteção de dados. Já que os dados se movem entre vários locais, a recomendação geral é que você sempre use protocolos SSL/TLS para trocar dados entre diferentes locais. Em alguns casos, convém isolar todo o canal de comunicação entre suas infraestruturas locais e na nuvem usando uma VPN (rede virtual privada).

Para dados que se movem entre sua infraestrutura local e o Azure, você deve considerar proteções adequadas, como HTTPS ou VPN.

Para organizações que precisam proteger o acesso de várias estações de trabalho localizadas localmente ao Azure, use [VPN site a site do Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-site-to-site-create).

Para organizações que precisam proteger o acesso de uma estação de trabalho localizada localmente local ao Azure, use [VPN Ponto a Site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-point-to-site-create).

Conjuntos de dados maiores podem ser movidos em um link WAN de alta velocidade dedicado, como o [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Se você optar por usar o ExpressRoute, também poderá criptografar os dados no nível do aplicativo usando [SSL/TLS](https://support.microsoft.com/kb/257591) ou outros protocolos para proteção adicional.

Se você estiver interagindo com o Armazenamento do Azure pelo Portal do Azure, todas as transações ocorrerão via HTTPS. A [API REST de armazenamento](https://msdn.microsoft.com/library/azure/dd179355.aspx) sobre HTTPS também pode ser usada para interagir com o [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) e com o [Banco de Dados SQL](https://azure.microsoft.com/services/sql-database/).

Organizações que não protegem dados em trânsito são mais suscetíveis a [ataques man-in-the-middle](https://technet.microsoft.com/library/gg195821.aspx), [eavesdropping](https://technet.microsoft.com/library/gg195641.aspx) e sequestro de sessão. Esses ataques podem ser a primeira etapa na obtenção de acesso a dados confidenciais.

Você pode aprender mais sobre a opção de VPN do Azure lendo o artigo [Planejamento e design para o Gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

### <a name="enforce-file-level-data-encryption"></a>Impor criptografia de dados no nível do arquivo

O [Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) usa políticas de criptografia, identidade e autorização para ajudar a proteger seus arquivos e emails. O Azure RMS funciona em vários dispositivos, como telefones, tablets e PCs, protegendo-os dentro e fora da sua organização. Esse recurso é possível porque o Azure RMS adiciona um nível de proteção que permanece com os dados, mesmo quando eles saem dos limites da organização.

Quando você usa o Azure RMS para proteger seus arquivos, está usando criptografia padrão do setor com suporte total de [FIPS 140-2](http://csrc.nist.gov/groups/STM/cmvp/standards.html). Ao utilizar o Azure RMS para proteção de dados, você tem a garantia de que a proteção permanece com o arquivo, mesmo quando ele é copiado para um armazenamento que não está sob o controle do departamento de TI, como um serviço de armazenamento de nuvem. O mesmo ocorre com arquivos compartilhados por email: o arquivo é protegido como um anexo a uma mensagem de email, com instruções de como abrir o anexo protegido.
Ao planejar a adoção do Azure RMS, recomendamos o seguinte:

- Instale o [aplicativo RMS sharing](https://technet.microsoft.com/library/dn339006.aspx). Esse aplicativo se integra a aplicativos do Office, instalando um suplemento do Office para que os usuários possam proteger seus arquivos de forma direta e fácil.

- Configurar aplicativos e serviços para dar suporte ao Azure RMS

- Crie [modelos personalizados](https://technet.microsoft.com/library/dn642472.aspx) que reflitam as necessidades dos negócios. Por exemplo: um modelo de dados secretos que deve ser aplicado a todos os emails secretos relacionados.

As organizações que não priorizam a [classificação de dados](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) e a proteção de arquivos podem estar mais vulneráveis à perda de dados. Sem a devida proteção de arquivos, as organizações não conseguirão obter percepções sobre os negócios, monitorar o abuso e impedir o acesso mal-intencionado a arquivos.

> [!Note]
> Você pode aprender mais sobre o Azure RMS lendo o artigo [Introdução ao Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx).

## <a name="secure-your-application-protect"></a>Proteger seu aplicativo (proteção)
Enquanto o Azure é responsável por proteger a infraestrutura e a plataforma em que seu aplicativo é executado, é sua responsabilidade proteger o aplicativo em si. Em outras palavras, você precisa desenvolver, implantar e gerenciar o código e o conteúdo do aplicativo de maneira segura. Caso contrário, o código ou conteúdo do aplicativo pode ficar vulnerável a ameaças.

### <a name="web-application-firewall-waf"></a>Firewall do aplicativo Web (WAF)
O [WAF (firewall do aplicativo Web)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) é um recurso do [Gateway de Aplicativo](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) que fornece proteção centralizada de seus aplicativos Web contra vulnerabilidades e explorações comuns.

O firewall de aplicativo Web tem base em regras dos [conjuntos de regras principais do OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 ou 2.2.9. Os aplicativos Web cada vez mais são alvos de ataques mal-intencionados que exploram vulnerabilidades conhecidas comuns. Os ataques de injeção de SQL, os ataques de scripts entre sites, entre outros, são comuns entre essas explorações. Pode ser difícil impedir esses ataques no código do aplicativo e pode exigir manutenção rigorosa, aplicação de patches e monitoramento em várias camadas da topologia do aplicativo. Um firewall de aplicativo Web centralizado ajuda a simplificar bastante o gerenciamento de segurança e oferece mais garantia ao administrador do aplicativo contra ameaças ou invasões. Uma solução WAF também pode reagir a uma ameaça de segurança mais rapidamente ao aplicar um patch contra uma vulnerabilidade conhecida em um local central do que a proteção de cada um dos aplicativos Web individuais. Os gateways de aplicativos existentes podem ser facilmente convertidos em um gateway de aplicativo com firewall de aplicativo Web.

Algumas das vulnerabilidades da Web comuns contra as quais o firewall do aplicativo Web protege incluem:

- Proteção contra injeção de SQL

- Proteção contra scripts entre sites

- Proteção Contra Ataques Comuns da Web, como a injeção de comandos, as solicitações HTTP indesejadas, a divisão de resposta HTTP e o ataque de inclusão de arquivo remoto

- Proteção contra violações de protocolo HTTP

- Proteção contra anomalias de protocolo HTTP, como ausência de host de agente do usuário e de cabeçalhos de aceitação

- Prevenção contra bots, rastreadores e scanners

- Detecção de problemas de configuração de aplicativo comuns (ou seja, Apache, IIS etc.)

> [!Note]
> Para obter uma lista mais detalhada das regras e suas proteções, confira os [Conjuntos de regras principais](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview#core-rule-sets):

O Azure fornece vários recursos fáceis de usar para ajudar a proteger o tráfego de entrada e de saída do seu aplicativo. O Azure também ajuda os clientes a proteger o código de seu aplicativo fornecendo funcionalidade externa para verificar se há vulnerabilidades em seu aplicativo Web.

- [Configurar a autenticação do Active Directory do Azure para seu aplicativo](https://azure.microsoft.com/blog/azure-websites-authentication-authorization/)


- [Proteger o tráfego para seu aplicativo, habilitando o protocolo TLS (TLS/SSL) - HTTPS](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl)

    - [Forçar todo o tráfego recebido por conexão HTTPS](http://microsoftazurewebsitescheatsheet.info/)

  - [Habilitar HSTS (Segurança de Transporte Estrito)](http://microsoftazurewebsitescheatsheet.info/#enable-http-strict-transport-security-hsts)


- [Restringir o acesso ao seu aplicativo pelo endereço IP do cliente](http://microsoftazurewebsitescheatsheet.info/#filtering-traffic-by-ip)

- [Restringir o acesso ao seu aplicativo pelo comportamento do cliente - frequência de solicitação e simultaneidade](http://microsoftazurewebsitescheatsheet.info/#dynamic-ip-restrictions)

- [Examinar o código do aplicativo Web em busca de vulnerabilidades usando a Tinfoil Security Scanning](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)

- [Configurar a autenticação mútua TLS para exigir que certificados de cliente se conectem ao seu aplicativo Web](https://docs.microsoft.com/azure/app-service/app-service-web-configure-tls-mutual-auth)

- [Configurar um certificado de cliente para usar em seu aplicativo para se conectar com segurança a recursos externos](https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/)

- [Remover cabeçalhos de servidor padrão para evitar que ferramentas coloquem impressão digital em seu aplicativo](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

- [Conectar seu aplicativo com segurança com recursos em uma rede privada usando VPN Ponto a Site](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)

- [Conectar seu aplicativo com segurança com recursos em uma rede privada usando Conexões Híbridas](https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections)

O Serviço de Aplicativo do Azure usa a mesma solução Antimalware usada pelos Serviços de Nuvem do Azure e pelas máquinas virtuais. Para saber mais sobre isso, confira nossa [documentação antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware).

## <a name="secure-your-network-protect"></a>Proteger sua rede (proteção)
O Microsoft Azure inclui uma infraestrutura de rede robusta para dar suporte a seus requisitos de conectividade de aplicativo e de serviço. A conectividade de rede é possível entre os recursos localizados no Azure, entre os recursos locais e aqueles hospedados no Azure e de origem e destino à Internet e ao Azure.

A [infraestrutura de rede do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines) permite que você conecte com segurança os recursos do Azure uns com os outros usando [redes virtuais (VNets)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Uma VNet é uma representação da sua própria rede na nuvem. Uma VNet é um isolamento lógico da rede de nuvem do Azure dedicada à sua assinatura. Você pode conectar VNets às suas redes locais.

![Proteger sua rede (proteção)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig6.png)

Caso você precise de um controle de acesso no nível de rede básico (baseado no endereço IP e nos protocolos TCP ou UDP), é possível usar os [Grupos de Segurança de Rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg). Um NSG (Grupo de Segurança de Rede) é um firewall básico de filtragem de pacotes com estado e permite o controle do acesso baseado em uma sequência de [5 tuplas](https://www.techopedia.com/definition/28190/5-tuple).

A rede do Azure dá suporte à capacidade de personalizar o comportamento de roteamento do tráfego de rede nas Redes Virtuais do Azure. É possível fazer isso configurando [Rotas Definidas pelo Usuário](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) no Azure.

[túnel forçado](https://www.petri.com/azure-forced-tunneling) é um mecanismo que pode ser usado para garantir que seus serviços não tenham permissão para iniciar uma conexão com dispositivos na Internet.

O Azure dá suporte a conectividade do link WAN dedicado para sua rede local e a uma Rede Virtual do Azure com o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction). O link entre o Azure e seu site utiliza uma conexão dedicada que não passa pela Internet pública. Se seu aplicativo Azure estiver sendo executado em vários datacenters, você poderá usar o [Gerenciador de Tráfego do Azure](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) para rotear solicitações de usuários de maneira inteligente entre as instâncias do aplicativo. Você também pode rotear o tráfego para serviços que não estejam sendo executados no Azure, desde que possam ser acessados pela Internet.

## <a name="virtual-machine-security-protect"></a>Segurança de máquina virtual (proteger)

As [máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/) permitem que você implante uma ampla gama de soluções de computação de forma ágil. Com suporte para o Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP e Serviços BizTalk do Azure, você pode implantar qualquer carga de trabalho e qualquer linguagem em praticamente qualquer sistema operacional.

Com o Azure, você pode usar [software antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) de fornecedores de segurança como Microsoft, Symantec, Trend Micro e Kaspersky para proteger suas máquinas virtuais contra arquivos mal-intencionados, adware e outras ameaças.

O Microsoft Antimalware para Serviços de Nuvem e Máquinas Virtuais do Azure é uma funcionalidade de proteção em tempo real que ajuda a identificar e remover vírus, spyware e outros softwares mal-intencionados. O Microsoft Antimalware fornece alertas configuráveis quando um software mal-intencionado ou indesejado conhecido tenta se instalar ou executar nos sistemas do Azure.

O [Backup do Azure](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) é uma solução escalonável que protege os dados de seu aplicativo sem nenhum investimento de capital e com custos operacionais mínimos. Erros de aplicativo podem corromper seus dados e erros humanos podem introduzir bugs em seus aplicativos. Com o Backup do Azure, suas máquinas virtuais executando Windows e Linux estão protegidas.

O [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) ajuda a orquestrar a replicação, failover e recuperação dos aplicativos e cargas de trabalho para que eles estejam disponíveis a partir de um local secundário, caso o local principal fique inativo.

## <a name="ensure-compliance-cloud-services-due-diligence-checklist-protect"></a>Garantia de conformidade: lista de verificação de inspeção detalhada dos serviços de nuvem (proteger)

A Microsoft desenvolveu a [Lista de verificação de inspeção detalhada dos serviços de nuvem](https://aka.ms/cloudchecklist.download) para ajudar as organizações a realizarem inspeções detalhadas conforme elas consideram migrar para a nuvem. Ela fornece uma estrutura para organizações de qualquer tamanho e tipo — empresas privadas e organizações do setor público, incluindo organizações governamentais de todos os níveis e organizações sem fins lucrativos — identificarem seus próprios objetivos e requisitos de desempenho, serviço, gerenciamento de dados e governança. Isso permite comparar ofertas de diferentes provedores de serviços de nuvem, levando à formação de uma base para um contrato de serviço de nuvem.

A lista de verificação fornece uma estrutura alinhada cláusula a cláusula com um novo padrão internacional para contratos de serviço de nuvem, o ISO/IEC 19086. Esse padrão oferece um conjunto unificado de considerações para as organizações a fim de ajudá-las a tomar decisões sobre a adoção da nuvem e a criar uma base comum para comparar ofertas de serviços de nuvem.

A lista de verificação promove uma movimentação para a nuvem cuidadosamente examinada, proporcionando uma orientação estruturada e uma abordagem consistente e reproduzível para escolher um provedor de serviços de nuvem.

A adoção da nuvem não é simplesmente uma decisão tecnológica. Uma vez que os requisitos da lista de verificação abordam todos os aspectos de uma organização, eles servem para reunir todos os principais tomadores de decisão internos — o CIO e o CISO, bem como profissionais dos setores jurídico, de gerenciamento de riscos, de aquisições e de conformidade. Isso aumenta a eficiência do processo de tomada de decisão e baseia as decisões em um raciocínio coerente, reduzindo assim a probabilidade de problemas imprevistos com a adoção.

Além disso, a lista de verificação:

- Expõe os principais tópicos de discussão para os tomadores de decisões no início do processo de adoção da nuvem.

- Dá suporte a discussões de negócios detalhadas sobre normas e sobre os objetivos da organização em termos de privacidade, PII (informações de identificação pessoal) e segurança de dados.

- Ajuda as organizações a identificar problemas potenciais que podem afetar um projeto de nuvem.

- Fornece um conjunto consistente de perguntas com os mesmos termos, definições, métricas e resultados para cada provedor, de forma a simplificar o processo de comparação de ofertas de diferentes provedores de serviços de nuvem.

## <a name="azure-infrastructure-and-application-security-validation-detect"></a>Validação de segurança de aplicativo e infraestrutura do Azure (detectar)

A [Segurança Operacional do Azure](https://docs.microsoft.com/azure/security/azure-operational-security) refere-se a serviços, controles e recursos disponíveis aos usuários para proteger seus dados, aplicativos e outros recursos no Microsoft Azure.

![Validação de segurança (detectar)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig7.png)

A Segurança Operacional do Azure baseia-se em uma estrutura que incorpora o conhecimento adquirido por meio de várias funcionalidades que são exclusivas à Microsoft, incluindo o Microsoft Security Development Lifecycle (SDL), o programa Microsoft Security Response Center e uma conscientização profunda quanto ao cenário de ameaças à segurança cibernética.

### <a name="microsoft-operations-management-suiteoms"></a>Microsoft OMS (Operations Management Suite)

O [Microsoft OMS (Operations Management Suite)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) é a solução de gerenciamento de TI para a nuvem híbrida. Seja usado sozinho ou para estender sua implantação existente do System Center, o OMS oferece a máxima flexibilidade e controle para o gerenciamento baseado em nuvem da infraestrutura.

![Microsoft OMS (Operations Management Suite)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig8.png)

Com o OMS, você pode gerenciar qualquer instância em qualquer nuvem, incluindo local, Azure, AWS, Windows Server, Linux, VMware e OpenStack, com um custo menor do que as soluções dos concorrentes. Criado para o mundo que prioriza a nuvem, o OMS oferece uma nova abordagem para gerenciar sua empresa, que é a maneira mais rápida e mais econômica de atender aos novos desafios de negócios e acomodar novas cargas de trabalho, novos aplicativos e ambientes de nuvem.

### <a name="log-analytics"></a>Log Analytics

O [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) fornece serviços de monitoramento para o OMS coletando dados de recursos gerenciados em um repositório central. Esses dados podem incluir eventos, dados de desempenho ou dados personalizados fornecidos pela API. Depois de coletados, os dados ficam disponíveis para alertas, análise e exportação.

![Log Analytics](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig9.png)

Esse método permite consolidar dados de várias fontes para que você possa combinar dados de serviços do Azure com o ambiente local existente. Ele também separa a coleta dos dados da ação executada neles para que todas as ações fiquem disponíveis para todos os tipos de dados.

### <a name="azure-security-center"></a>Central de Segurança do Azure

A [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) ajuda você a impedir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Azure. Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas do Azure, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

A Central de Segurança analisa o estado de segurança de seus recursos do Azure para identificar possíveis vulnerabilidades na segurança. Uma lista de recomendações orienta você no processo de configuração dos controles necessários.

Os exemplos incluem:

- Provisionamento de antimalware para ajudar a identificar e remover software mal-intencionado

- Configurando os grupos de segurança de rede e as regras para controlar o tráfego para máquinas virtuais

- Provisionamento de firewalls do aplicativo Web para ajudar a proteger contra ataques direcionados aos seus aplicativos Web

- Como implantar atualizações de sistema ausentes

- Endereçamento de configurações do sistema operacional que não coincidem com as linhas de base recomendadas

A Central de segurança automaticamente coleta, analisa e integra os dados de registro de seus recursos do Azure, da rede e das soluções de parceiros como programas antimalware e firewalls. Quando forem detectadas ameaças, é criado um alerta de segurança. Os exemplos abrangem a detecção de:

- As máquinas virtuais comprometidas se comunicam com os endereços IP mal-intencionados conhecidos

- Malware avançado detectado usando o relatório de erros do Windows

- Ataques por força bruta contra máquinas virtuais

- Alertas de segurança dos firewalls e programas antimalware integrados

### <a name="azure-monitor"></a>Azure Monitor

O [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) fornece ponteiros para informações sobre tipos específicos de recursos. Ele oferece visualização, consulta, roteamento, alertas, escala automática e automação nos dados por meio da infraestrutura do Azure (Logs de Atividades) e de cada recurso individual do Azure (Logs de Diagnóstico).

Os aplicativos em nuvem são complexos com muitas partes móveis. O monitoramento fornece dados para garantir que seu aplicativo permaneça ativo e em execução em um estado íntegro. Ele também ajuda a afastar os problemas potenciais ou solucionar problemas antigos.

![Azure Monitor](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig10.png) Além disso, você pode usar os dados de monitoramento para obter mais informações sobre seu aplicativo. Esse conhecimento pode ajudá-lo a melhorar o desempenho ou a capacidade de manutenção do aplicativo ou automatizar ações que normalmente exigiriam intervenção manual.

A auditoria da segurança de sua rede é fundamental para detectar vulnerabilidades de rede e garantir a conformidade com o modelo de governança regulatória e segurança de TI. Com a exibição Grupo de Segurança, você pode recuperar o Grupo de Segurança de Rede configurado e as regras de segurança, bem como as regras de segurança efetivas. Com a lista de regras aplicadas, é possível determinar as portas que estão abertas e avaliar a vulnerabilidade da rede.

### <a name="network-watcher"></a>Observador de Rede

O [Observador de Rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher) é um serviço regional que permite monitorar e diagnosticar as condições em um nível de rede em, para e do Azure. As ferramentas de diagnóstico e visualização da rede disponíveis com o Observador de Rede ajudam a entender, diagnosticar e ter informações para sua rede no Azure. Esse serviço inclui a captura de pacotes, próximo salto, verificação do fluxo de IP, exibição do grupo de segurança e logs de fluxo de NSG. O monitoramento no nível do cenário fornece uma exibição completa dos recursos de rede em contraste com o monitoramento de recursos de rede individual.

### <a name="storage-analytics"></a>Análise de armazenamento

A [Análise de Armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) pode armazenar métricas que incluem estatísticas de transação agregadas e dados de capacidade sobre solicitações em um serviço de armazenamento. As transações são relatadas no nível de operação da API, bem como no nível de serviço de armazenamento, e a capacidade é relatada no nível de serviço de armazenamento. Os dados de métricas podem ser usados para analisar o uso do serviço de armazenamento, diagnosticar problemas com solicitações feitas no serviço de armazenamento e melhorar o desempenho de aplicativos que usam um serviço.

### <a name="application-insights"></a>Application insights

O [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) é um serviço APM (Gerenciamento de Desempenho de Aplicativos) extensível para desenvolvedores da Web em várias plataformas. Use-o para monitorar seu aplicativo Web online. Ele detectará anomalias de desempenho automaticamente. Ele inclui ferramentas de análise avançadas para ajudar você a diagnosticar problemas e entender o que os usuários fazem com seu aplicativo. Ele foi projetado para ajudar você a aprimorar continuamente o desempenho e a usabilidade do seu aplicativo. Ele funciona com aplicativos em uma ampla variedade de plataformas incluindo .NET e J2EE, hospedados localmente ou na nuvem. Ele é integrado ao seu processo de DevOps e tem pontos de conexão para várias ferramentas de desenvolvimento.

Ele monitora:

- **Taxas de solicitação, tempos de resposta e taxas de falha** - descubra quais páginas estão mais populares, em que momentos do dia, e onde os usuários estão. Confira as páginas que têm melhor desempenho. Se as taxas de falha e os tempos de resposta ficam altos quando há mais solicitações, possivelmente você tem um problema de alocação de recursos.

- **Taxas de dependência, tempos de resposta e taxas de falha** - descubra se os serviços externos estão atrasando você.

- **Exceções** – analise as estatísticas agregadas ou escolha instâncias específicas e faça uma busca detalhada no rastreamento de pilha e nas solicitações relacionadas. A maioria das exceções de navegador e servidor são relatadas.

- **Exibições de página e o desempenho de carregamento** - relatados por navegadores dos usuários.

- **Chamadas AJAX de páginas da Web** – taxas, tempos de resposta e taxas de falha.

- **Contagens de sessão e usuários.**

- **Contadores de desempenho** de suas máquinas de servidor Linux ou Windows server, como CPU, memória e uso da rede.

- **Diagnósticos de host** do Docker ou do Azure.

- **Logs de rastreamento de diagnóstico** do seu aplicativo - para que você possa correlacionar eventos de rastreamento com solicitações.

- **Métricas e eventos personalizados** que você escreve no código de cliente ou servidor, a fim de acompanhar eventos de negócios como itens vendidos ou jogos vencidos.
A infraestrutura do seu aplicativo geralmente é composta de vários componentes; talvez uma máquina virtual, uma conta de armazenamento e uma rede virtual, ou aplicativo Web, banco de dados, servidor de banco de dados e serviços de terceiros. Tais componentes não são vistos como entidades separadas, em vez disso, eles são mostrados como partes relacionadas e interdependentes de uma única entidade. Você deseja implantar, gerenciar e monitorá-los como um grupo. O [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) permite trabalhar com os recursos da sua solução como um grupo.

Você pode implantar, atualizar ou excluir todos os recursos da sua solução em uma única operação coordenada. Usar um modelo para a implantação e esse modelo pode ser útil para ambientes diferentes, como teste, preparação e produção. O Gerenciador de Recursos fornece recursos de segurança, auditoria e marcação para ajudá-lo a gerenciar seus recursos após a implantação.

**Os benefícios de usar o Resource Manager**

O Gerenciador de Recursos fornece vários benefícios:

- Você pode implantar, gerenciar e monitorar todos os recursos da sua solução como um grupo em vez de tratá-los individualmente.

- Você pode implantar a solução repetidamente em todo seu ciclo de vida de desenvolvimento e com a confiança de que seus recursos serão implantados em um estado consistente.

- Você pode gerenciar sua infraestrutura por meio de modelos declarativos em vez de scripts.

- Você pode definir as dependências entre os recursos para que eles sejam implantados na ordem correta.

- Você pode aplicar o controle de acesso a todos os serviços no grupo de recursos, pois o RBAC (Controle de Acesso Baseado em Função) é integrado nativamente à plataforma de gerenciamento.

- Você pode aplicar marcas aos recursos para organizar de modo lógico todos os recursos em sua assinatura.

- Você pode esclarecer a cobrança da sua organização exibindo os custos para um grupo de recursos que compartilha a mesma marcação.

> [!Note]
> O Gerenciador de Recursos fornece uma nova maneira de implantar e gerenciar suas soluções. Se você tiver usado o modelo de implantação anterior e quiser saber mais sobre as alterações, confira [Noções básicas sobre a implantação do Resource Manager e a implantação clássica](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a segurança lendo alguns dos tópicos detalhados sobre segurança:

- [Auditoria e log](https://www.microsoft.com/en-us/trustcenter/security/auditingandlogging)

- [Crime cibernético](https://www.microsoft.com/en-us/trustcenter/security/cybercrime)

- [Design e segurança operacional](https://www.microsoft.com/en-us/trustcenter/security/designopsecurity)

- [Criptografia](https://www.microsoft.com/en-us/trustcenter/security/encryption)

- [Gerenciamento de identidade e de acesso](https://www.microsoft.com/en-us/trustcenter/security/identity)

- [Segurança de rede](https://www.microsoft.com/en-us/trustcenter/security/networksecurity)

- [Gerenciamento de ameaças](https://www.microsoft.com/en-us/trustcenter/security/threatmanagement)
