---
title: Data center virtual do Microsoft Azure | Microsoft Docs
description: Aprenda a criar seu data center virtual no Azure
services: networking
author: tracsman
manager: rossort
tags: azure-resource-manager
ms.service: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: jonor
ms.openlocfilehash: 7dcc6b77bde8b8a7b485525105c1a07c53301f8e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="microsoft-azure-virtual-data-center"></a>Data center virtual do Microsoft Azure
**Microsoft Azure**: avance mais rapidamente, economize dinheiro, integre aplicativos e dados locais

## <a name="overview"></a>Visão geral
Migrar aplicativos locais para o Azure, mesmo sem quaisquer alterações significativas (uma abordagem conhecida como "lift and shift"), permite que as organizações beneficiem-se de uma infraestrutura segura e eficiente. No entanto, para aproveitar ao máximo possível a agilidade com a computação em nuvem, as empresas devem evoluir suas arquiteturas para aproveitarem os recursos do Azure. O Microsoft Azure oferece serviços e infraestrutura em larga escala, recursos e confiabilidade de nível empresarial e várias opções de conectividade híbrida. Os clientes podem optar por acessar esses serviços de nuvem pela Internet ou com o Azure ExpressRoute, que fornece conectividade de rede privada. A plataforma Microsoft Azure permite que os clientes estendam facilmente sua infra-estrutura para a nuvem e criem arquiteturas com várias camadas. Além disso, os parceiros da Microsoft oferecem recursos avançados, oferecendo serviços de segurança e soluções de virtualização otimizados para execução no Azure.

Este artigo apresenta uma visão geral de padrões e designs que podem ser usados para resolver as preocupações de segurança, desempenho e escala arquitetura que muitos clientes enfrentam quando pensam em uma transição em massa para a nuvem. Uma visão geral de como ajustar as diferentes funções de TI organizacionais ao gerenciamento e à governança do sistema também é discutida, com ênfase no requisitos de segurança e na otimização de custos.

## <a name="what-is-a-virtual-data-center"></a>O que é um Data Center Virtual?
Antigamente, as soluções de nuvem eram projetadas para hospedar aplicativos únicos relativamente isolados no espectro público. Essa abordagem funcionou bem por alguns anos. No entanto, conforme os benefícios das soluções de nuvem ficaram mais claros e várias cargas trabalho em grande escala eram hospedadas na nuvem, lidar com questões de segurança, confiabilidade, desempenho e custo de implantações em uma ou mais regiões tornou-se vital em todo o ciclo de vida do serviço de nuvem.

O diagrama de implantação em nuvem a seguir ilustra alguns exemplos de falhas de segurança (caixa vermelha) e espaço para otimizar soluções de virtualização de rede entre as cargas de trabalho (caixa amarela).

[![0]][0]

O Virtual Data Center (vDC) nasceu dessa necessidade de ajuste de escala para dar suporte a cargas de trabalho e da necessidade de lidar com os problemas introduzidos ao dar suporte a aplicativos em grande escala na nuvem pública.

Uma vDC não se refere apenas às cargas de trabalho de aplicativos na nuvem, mas também à rede de segurança, ao gerenciamento e à infraestrutura (por exemplo, DNS e Serviços de Diretório). Geralmente também fornece uma conexão privada de volta a uma rede ou data center local. Conforme cada vez mais cargas de trabalho vão para o Azure, é importante pensar na infraestrutura de suporte e nos objetos em que essas cargas de trabalho estão colocadas. Pensar cuidadosamente em como os recursos são estruturados pode evitar a proliferação de centenas de "ilhas de carga de trabalho" que devem ser gerenciadas separadamente com o fluxo de dados, modelos de segurança e desafios de conformidade independentes.

Um Data Center Virtual é essencialmente uma coleção de entidades separadas, porém relacionadas, com infraestrutura, recursos e funções de suporte em comum. Ao exibir suas cargas de trabalho como um vDC integrado, você pode aproveitar o custo reduzido devido a economias de escala, segurança otimizada por meio de centralização de componente e fluxo de dados, além de operações, gerenciamento e auditorias de conformidade mais fáceis.

> [!NOTE]
> É importante compreender que a vDC **NÃO** é um produto separado do Azure, mas a combinação de vários recursos e capacidades para atender às suas necessidades exatas. O vDC é uma maneira de pensar sobre suas cargas de trabalho e o uso do Azure para maximizar os recursos e as habilidades na nuvem. O DC virtual é, portanto, uma abordagem modular sobre como criar serviços de TI no Azure, respeitando funções e responsabilidades organizacionais.

O vDC pode ajudar as empresas a levar cargas de trabalho e aplicativos para o Azure para os seguintes cenários:

-   Hospedagem de várias cargas de trabalho relacionadas
-   Migração de cargas de trabalho de um ambiente local para o Azure
-   Implementação de segurança compartilhada ou centralizada e requisitos de acesso entre cargas de trabalho
-   Como combinar TI centralizada e DevOps apropriadamente para uma grande empresa

A chave para aproveitar as vantagens do vDC é uma topologia centralizada (hub e spokes) com uma combinação de recursos do Azure: [VNet do Azure][VNet], [NSGs][NSG], [Emparelhamento VNet][VNetPeering], [UDR (Rotas Definidas pelo Usuário)][UDR] e Identidade do Azure com [RBAC (Controle de Acesso Baseado em Função)][RBAC].

## <a name="who-needs-a-virtual-data-center"></a>Quem precisa de um Data Center Virtual?
Qualquer cliente do Azure que precise ser movido mais do que algumas cargas de trabalho no Azure pode se beneficiar de pensar sobre como usar recursos comuns. Dependendo da magnitude, até mesmo aplicativos únicos podem se beneficiar de usar os padrões e os componentes empregados para criar um vDC.

Se a sua organização tiver uma equipe/departamento de TI, Rede, Segurança e/ou conformidade centralizado, um vDC poderá ajudar a impor pontos da política, segregação de dever e garantir a uniformidade dos componentes comuns subjacentes enquanto dá às equipes de aplicativo o máximo de liberdade e controle que for apropriado para necessidades delas.

As organizações que estão considerando DevOps podem utilizar os conceitos de vDC para fornecer portas embutidas de recursos do Azure e garantir que tenham controle total dentro desse grupo (seja um grupo de recursos ou de assinaturas em uma assinatura comum), mas os limites de segurança de rede permanecem em conformidade, conforme definido por uma política centralizada em um Grupo de Recursos e VNet de um hub.

## <a name="considerations-on-implementing-a-virtual-data-center"></a>Considerações sobre a implementação de um Data Center Virtual
Ao projetar um vDC, há várias questões importante a considerar:

-   Serviços de identidade e diretório
-   Infraestrutura de segurança
-   Conectividade com a nuvem
-   Conectividade na nuvem

##### <a name="identity-and-directory-service"></a>*Serviço de identidade e de diretório*
Serviços de identidade e diretório são um aspecto fundamental de todos os data centers, sejam locais ou na nuvem. A identidade está relacionada a todos os aspectos de acesso e autorização para os serviços no vDC. Para ajudar a garantir que somente usuários e processos autorizados acessem seus recursos e Conta do Azure, o Azure usa vários tipos de credenciais para autenticação. Eles incluem senhas (para acessar a conta do Azure), chaves de criptografia, assinaturas digitais e certificados. [*MFA* (Autenticação Multifator do Azure)][MFA] é uma camada adicional de segurança para acessar os serviços do Azure. O MFA do Azure oferece autenticação forte com uma variedade de opções fáceis de verificação, como chamada telefônica, mensagem de texto ou notificação por aplicativo móvel, permitindo que os clientes escolham seus métodos preferidos.

Qualquer grande empresa precisa definir um processo de gerenciamento de identidade que descreva o gerenciamento de identidades individuais, autenticação, autorização, funções e privilégios dentro ou entre o vDC. Os objetivos deste processo devem ser aumentar a segurança e a produtividade e diminuir o custo, o tempo de inatividade e as tarefas manuais repetitivas.

Empresas/organizações podem exigir uma combinação exigente de serviços para diferentes LOBs (linha de negócios) e os funcionários geralmente têm diferentes funções quando estão envolvido em projetos diferentes. Uma vDC requer uma boa cooperação entre diferentes equipes, cada uma com definições de função específica, para que os sistemas sejam executados com boa governança. A matriz de responsabilidades, acesso e direitos pode ser extremamente complexa. O gerenciamento de identidade no vDC é implementado por meio do [*AAD* (Azure Active Directory)][AAD] e do RBAC (controle de acesso baseado em função).

Um Serviço de Diretório é uma infraestrutura de informações compartilhadas para localizar, gerenciar, administrar e organizar itens cotidianos e recursos de rede. Esses recursos podem incluir volumes, pastas, arquivos, impressoras, usuários, grupos, dispositivos e outros objetos. Cada recurso na rede é considerado um objeto pelo servidor de diretório. Informações sobre um recurso são armazenadas como uma coleção de atributos associados a tal recurso ou objeto.

Todos os serviços comerciais online da Microsoft dependem do Azure AD (Azure Active Directory) para conexão e outras necessidades de identidade. O Azure Active Directory é uma solução de nuvem de gerenciamento de acesso e identidade abrangente e altamente disponível que combina os serviços principais de diretório, governança avançada de identidades e gerenciamento do acesso de aplicativos. O AAD pode ser integrado ao Active Directory local para habilitar logon único para todos os aplicativos hospedados localmente e baseados em nuvem (local). Os atributos do usuário do Active Directory local podem ser sincronizados automaticamente com o AAD.

Um único administrador global não é necessário para atribuir todas as permissões em um vDC. Em vez disso, cada departamento específico (ou grupo de usuários ou serviços no Serviço de Diretório) pode ter as permissões necessárias para gerenciar seus próprios recursos dentro de um vDC. Estruturar permissões requer balanceamento. Um número excessivo de permissões pode prejudicar a eficiência de desempenho, enquanto permissões de menos ou não exigentes podem aumentar os riscos de segurança. O RBAC (controle de acesso baseado em função) do Azure ajuda a resolver esse problema oferecendo o gerenciamento de acesso refinado para recursos vDC.

##### <a name="security-infrastructure"></a>*Infraestrutura de Segurança*
Infraestrutura de segurança, no contexto de um vDC, está principalmente relacionada à segregação de tráfego no segmento de rede virtual específico do vDC e a como controlar os fluxos de entrada e saída em todo o vDC. O Azure se baseia na arquitetura multilocatário que impede o tráfego não autorizado e não intencional entre implantações, usando isolamento de VNet (rede virtual), ACLs (listas de controle de acesso), balanceadores de carga e filtros IP, juntamente com políticas de fluxo de tráfego. NAT (conversão de endereços de rede) separa o tráfego de rede interno do tráfego externo.

A malha do Azure aloca recursos de infraestrutura a cargas de trabalho de locatário e gerencia comunicações para e de VMs (máquinas virtuais). O hipervisor do Azure impõe a separação de memória e processo entre VMs e encaminha com segurança o tráfego de rede a locatários do sistema operacional convidado.

##### <a name="connectivity-to-the-cloud"></a>*Conectividade com a nuvem*
O vDC precisa de conectividade com redes externas para oferecer serviços a clientes, parceiros e/ou usuários internos. Isso geralmente significa conectividade não apenas com a Internet, mas também com data centers e redes locais.

Os clientes podem criar suas políticas de segurança para controlar o que e como serviços hospedados do vDC específicos são acessíveis de/para a Internet usando Dispositivos de Rede Virtual (com inspeção e filtragem de tráfego) e políticas personalizadas de roteamento e filtragem de rede (Roteamento Definido pelo Usuário e Grupos de Segurança de Rede).

As empresas geralmente precisam conectar vDCs a data centers locais ou outros recursos. A conectividade entre o Azure e redes locais é, portanto, um aspecto essencial ao projetar uma arquitetura eficaz. As empresas têm duas maneiras diferentes de criar uma interconexão entre o vDC e o local no Azure: trânsito pela Internet e/ou por conexões diretas privadas.

Um [**VPN Site a Site do Azure**][VPN] é um serviço de interconexão pela Internet entre redes locais e o vDC, estabelecida por meio de conexões criptografadas seguras (túneis IPsec/IKE). A conexão Site a Site do Azure é flexível e rápida de criar e não requer nenhuma compra adicional, uma vez que todas as conexões são feitas pela Internet.

[**ExpressRoute**][ExR] é um serviço de conectividade do Azure que permite criar conexões privadas entre o vDC e as redes locais. Conexões do ExpressRoute não passam pela Internet pública e oferecem maior segurança, confiabilidade e velocidades mais altas (até 10 Gbps), além de latência consistente. O ExpressRoute é muito útil para vDCs, uma vez que os clientes do ExpressRoute podem aproveitar os benefícios de regras de conformidade associados a conexões privadas.

Implantar conexões do ExpressRoute envolve a interação com um provedor de serviços do ExpressRoute. Para clientes que precisam iniciar rapidamente, é comum usar inicialmente VPN Site a Site para estabelecer a conectividade entre o vDC e recursos locais e então migrar para a conexão do ExpressRoute.

##### <a name="connectivity-within-the-cloud"></a>*Conectividade na nuvem*
[VNets][VNet] e [Emparelhamento VNet][VNetPeering] são os serviços de conectividade de rede básicos em um vDC. Uma VNet garante um limite natural de isolamento para recursos do vDC e o emparelhamento VNet permite a intercomunicação entre diferentes VNets na mesma região do Azure. Controle de tráfego em uma VNet e entre VNets precisa corresponder a um conjunto de regras de segurança especificado por meio de Listas de Controle de Acesso ([Grupo de Segurança de Rede][NSG]), [Soluções de Virtualização de Rede][NVA] e as tabelas de roteamento personalizadas ([UDR][UDR]).

## <a name="virtual-data-center-overview"></a>Visão geral do Data Center Virtual

### <a name="topology"></a>Topologia
O modelo hub e spokes estendeu o Data Center Virtual em uma única região do Azure

[![1]][1]

O hub é a zona central que controla e inspeciona o tráfego de entrada e/ou saída entre diferentes zonas: Internet, local e os spokes. A topologia de hub e spoke dá ao departamento de TI uma maneira eficiente de impor políticas de segurança em um local central, ao mesmo tempo em que reduz a possibilidade de erros de configuração e exposição.

O hub contém os componentes de serviço comuns consumidos pelos spokes. Aqui estão alguns exemplos típicos de serviços centrais comuns:

-   A infraestrutura do Microsoft Active Directory (com o serviço ADFS relacionado) necessária para a autenticação de usuário de terceiros acessando de redes não confiáveis antes de obterem acesso às cargas de trabalho no spoke
-   Um serviço DNS para resolver nomes para a carga de trabalho nos spokes, para acessar recursos locais e na Internet
-   Uma infraestrutura PKI para implementar logon único em cargas de trabalho
-   Controle de fluxo (TCP/UDP) entre os spokes e a Internet
-   Controle de fluxo entre o spoke e o local
-   Se desejar, fluxo de controle entre um spoke e outro

O vDC reduz o custo geral usando a infraestrutura de hub compartilhada entre vários spokes.

A função de cada spoke pode ser hospedar diferentes tipos de cargas de trabalho. Os spokes também podem fornecer uma abordagem modular para implantações repetíveis (por exemplo, desenvolvimento e teste, Teste de Aceitação do Usuário, pré-produção e produção) das mesmas cargas de trabalho. Os spokes também podem ser usados para separar e habilitar diferentes grupos na sua organização (por exemplo, grupos de DevOps). Dentro de um spoke, é possível implantar uma carga de trabalho básica ou cargas de trabalho complexas de várias camadas com controle de tráfego entre as camadas.

##### <a name="subscription-limits-and-multiple-hubs"></a>Limites de assinatura e vários hubs
No Azure, cada componente, seja qual for o tipo, é implantado em uma Assinatura do Azure. O isolamento dos componentes do Azure em diferentes assinaturas do Azure pode atender aos requisitos de diferentes LOBs, como configurar níveis diferenciados de acesso e autorização.

Uma único vDC pode ser escalado verticalmente para um grande número de spokes, embora, assim como acontece com todos os sistemas de TI, há limites de plataformas. A implantação do hub está associada a uma assinatura específica do Azure, que tem restrições e limites (por exemplo, um número máximo de emparelhamentos de VNet – consulte [Assinatura do Azure e limites de serviço, cotas e restrições][Limits] para obter detalhes). Em casos em que os limites possam ser um problema, a arquitetura pode ser escalada verticalmente ainda mais estendendo o modelo de um único hub-spokes para um cluster de hub e spokes. Vários hubs em uma ou mais regiões do Azure podem ser interconectados usando o ExpressRoute ou a VPN site a site.

[![2]][2]

A introdução de vários hubs aumenta o esforço de gerenciamento e o custo do sistema e somente seria justificada pela escalabilidade (exemplos: limites ou redundância do sistema) e replicação regional (exemplos: recuperação de desastre ou desempenho do usuário final). Em cenários que exigem vários hubs, todos os hubs devem buscar oferecer o mesmo conjunto de serviços para facilidade operacional.

##### <a name="interconnection-between-spokes"></a>Interconexão entre spokes
Dentro de um único spoke, é possível implementar cargas de trabalho complexas de várias camadas. Configurações de várias camadas podem ser implementadas usando sub-redes (uma para cada camada) na mesma VNet e filtrando os fluxos usando NSGs.

Por outro lado, um arquiteto talvez queira implantar uma carga de trabalho de várias camadas em várias VNets. Usando emparelhamento VNet, spokes podem se conectar a outros spokes no mesmo hub ou em hubs diferentes. Um exemplo típico desse cenário é o caso em que os servidores de processamento do aplicativo estão em um spoke (VNet), enquanto o banco de dados está implantado em um spoke diferente (VNet). Nesse caso, é fácil interconectar os spokes ao emparelhamento VNet e, assim, evitar trânsito pelo hub. Um exame cuidadoso de arquitetura e segurança deve ser executada para garantir que ignorar o hub não ignore pontos de auditoria ou segurança importantes que podem existir somente no hub.

[![3]][3]

Os spokes também podem ser interconectados a um spoke que atue como um hub. Essa abordagem cria uma hierarquia de dois níveis: o spoke no nível mais alto (nível 0) torna-se o hub dos spokes inferiores (nível 1) da hierarquia. Os spokes do vDC precisam encaminhar o tráfego para o hub central para alcançarem a Internet ou a rede local. Uma arquitetura com dois níveis de hub apresenta roteamento complexo que remove os benefícios de uma relação de hub-spoke simples.

Embora o Azure permita topologias complexas, um dos princípios mais importantes do conceito de vDC é repetição e simplicidade. Para minimizar o esforço de gerenciamento, o design simples de hub-spoke é a arquitetura de referência do vDC recomendada.

### <a name="components"></a>Componentes
Um Data Center Virtual é composto por quatro tipos de componente básicos: **Infraestrutura**, **Redes de Perímetro**, **Cargas de Trabalho** e **Monitoramento**.

Cada tipo de componente consiste em vários recursos e funcionalidades do Azure. Seu vDC é composto por instâncias de vários tipos de componentes e muitas variações do mesmo tipo de componente. Por exemplo, você pode ter muitas instâncias de carga de trabalho diferentes e logicamente separadas que representem diferentes aplicativos. Use esses diferentes tipos e instâncias de componentes para criar, por fim, o vDC.

[![4]][4]

A arquitetura de alto nível anterior de um vDC mostra diferentes tipos de componente usados em diferentes zonas na topologia de hub-spokes. O diagrama mostra os componentes da infraestrutura em várias partes da arquitetura.

Como uma prática recomendada (para um DC ou vDC local), direitos e privilégios de acesso devem ser baseados em grupo. Lidar com grupos, em vez de usuários individuais, ajuda a manter as políticas de acesso de modo consistente entre equipes e a minimizar erros de configuração. Atribuir e remover usuários de e para grupos apropriados ajuda a manter os privilégios de um usuário específico atualizados.

Cada grupo de função deve ter um prefixo exclusivo em seus nomes, tornando mais fácil identificar qual grupo está associado a qual carga de trabalho. Por exemplo, uma carga de trabalho que hospede um serviço de autenticação pode ter grupos chamados *AuthServiceNetOps, AuthServiceSecOps, AuthServiceDevOps e AuthServiceInfraOps.* Da mesma forma, para funções centralizadas ou funções não relacionadas a um serviço específico, poderia ser usado o sufixo "Corp", *CorpNetOps*, por exemplo.

Muitas organizações usam uma variação dos seguintes grupos para fornecer uma divisão importante de funções:

-   O *grupo central de TI (Corp)* tem os direitos de propriedade para controlar os componentes de infraestrutura (como rede e segurança) e, portanto, precisa ter a função de colaborador na assinatura (e ter controle do hub) e direitos de colaborador de rede nos raios. Organizações de grande porte com frequência dividem essas responsabilidades de gerenciamento entre várias equipes, como: um grupo de Operações de Rede (CorpNetOps) (com foco exclusivo na rede) e um grupo de Operações de Segurança (CorpSecOps) (responsável para política de segurança e de firewall). Nesse caso específico, os dois grupos diferentes precisam ser criados para a atribuição dessas funções personalizadas.
-   O *grupo de desenvolvimento e teste (AppDevOps)* tem a responsabilidade de implantar cargas de trabalho (Aplicativos ou Serviços). Esse grupo usa a função de Colaborador de Máquina Virtual para implantações de IaaS e/ou uma ou mais funções de colaborador de PaaS (consulte [Funções internas para Controle de Acesso Baseado em Função do Azure][Roles]). Opcionalmente, a equipe de desenvolvimento e teste pode precisar ter visibilidade de políticas de segurança (NSGs) e políticas de roteamento (UDR) dentro do hub ou de um spoke específico. Portanto, além das funções de colaborador para cargas de trabalho, esse grupo também precisaria da função de Leitor de Rede.
-   O *grupo de operação e manutenção (CorpInfraOps ou AppInfraOps)* tem a responsabilidade de gerenciar cargas de trabalho em produção. Esse grupo deve ser um colaborador de assinatura em cargas de trabalho em qualquer assinatura de produção. Algumas organizações também podem avaliar se elas precisam de um grupo de equipe suporte de escalonamento adicional com a função de colaborador de assinatura em produção e na assinatura do hub central, para corrigir possíveis problemas de configuração no ambiente de produção.

Uma vDC é estruturada de modo que os grupos criados para os grupos de TI centrais que gerenciam o hub tenham grupos correspondentes no nível de carga de trabalho. Além de gerenciar recursos de hub, somente os grupos de TI centrais seriam capazes de controlar o acesso externo e permissões de nível superior na assinatura. No entanto, os grupos de carga de trabalho seriam capazes de controlar recursos e permissões da VNet de modo independente na TI Central.

O vDC precisa ser particionados para hospedar vários projetos de maneira segura em diferentes LOBs (linhas de negócios). Todos os projetos exigem diferentes ambientes isolados (Dev, UAT, produção). Assinaturas separadas do Azure para cada um desses ambientes fornecem isolamento natural.

[![5]][5]

O diagrama anterior mostra a relação entre os projetos, os usuários, os grupos e os ambientes de uma organização em que os componentes do Azure são implantados.

Normalmente, em TI, um ambiente (ou camada) é um sistema em que vários aplicativos são implantados e executados. Grandes empresas usam um ambiente de desenvolvimento (em que as alterações são originalmente feitas e testadas) e um ambiente de produção (que os usuários finais usam). Esses ambientes são separados, geralmente com vários ambientes de preparo entre eles para permitir a implantação em fases (distribuição), testes e reversão em caso de problemas. As arquiteturas de implantação variam significativamente, mas geralmente o processo básico de iniciar em desenvolvimento (DEV) e terminar em produção (PROD) ainda é seguido.

Uma arquitetura comum para esses tipos de ambientes de várias camadas consiste em ambientes de produção DevOps (desenvolvimento e teste), UAT (de preparo) e ambientes de produção. As organizações podem aproveitar locatários do Azure AD únicos ou múltiplos para definir o acesso e os direitos para esses ambientes. O diagrama anterior mostra um caso em que dois locatários do Azure AD diferentes são usados: um para DevOps e UAT e outro exclusivamente para produção.

A presença de diferentes locatários do Azure AD impõe a separação entre ambientes. O mesmo grupo de usuários (por exemplo, TI Central) precisa autenticar usando um URI diferente para acessar um locatário diferente do AD, modificar as funções ou permissões de ambientes de DevOps ou de produção de um projeto. A presença de diferentes autenticações de usuário para acessar diferentes ambientes reduz possíveis interrupções e outros problemas causados por erros humanos.

#### <a name="component-type-infrastructure"></a>Tipo de componente: infraestrutura
Esse tipo de componente é o local em que a maioria da infraestrutura de suporte reside. Também é o ponto em que as equipes centralizadas de TI, Segurança e/ou Conformidade passam a maior parte do tempo.

[![6]][6]

Componentes da infraestrutura fornecem uma interconexão entre os diferentes componentes de um vDC e estão presentes tanto no hub quanto nos spokes. Normalmente, a responsabilidade por gerenciar e manter os componentes da infraestrutura é atribuída à equipe de segurança e/ou TI central.

Uma das principais tarefas da equipe de infraestrutura de TI é assegurar a consistência dos esquemas de endereço IP em toda a empresa. O espaço de endereço IP privado atribuído ao vDC precisa ser consistente e NÃO se sobrepor a endereços IP atribuídos em suas redes locais.

Embora NAT nos roteadores de borda locais ou em ambientes do Azure possa evitar conflitos de endereço IP, ele adiciona complicações aos componentes da sua infraestrutura. Simplicidade de gerenciamento é uma das principais metas do vDC, assim, usar NAT para lidar com preocupações de IP não é uma solução recomendada.

Componentes da infraestrutura contêm a seguinte funcionalidade:

-   [**Serviços de identidade e diretório**][AAD]. Acesso a todos os tipos de recurso no Azure é controlado por uma identidade armazenada em um serviço de diretório. O serviço de diretório armazena não apenas a lista de usuários, mas também os direitos de acesso aos recursos em uma assinatura específica do Azure. Esses serviços podem existir somente em nuvem ou podem ser sincronizados com identidade local armazenada no Active Directory.
-   [**Rede Virtual**][VPN]. Redes Virtuais são um dos principais componentes de um vDC e permitem que você crie um limite de isolamento de tráfego na plataforma do Azure. Uma Rede Virtual é composta por um ou vários segmentos de rede virtual, cada um com um prefixo de rede IP específico (uma sub-rede). A rede Virtual define uma área de perímetro interna em que máquinas virtuais de IaaS e serviços de PaaS podem estabelecer uma comunicação privada. VMs (e serviços de PaaS) em uma rede virtual não podem se comunicar diretamente com VMs (e serviços de PaaS) em uma rede virtual diferente, mesmo se as duas redes virtuais forem criadas pelo mesmo cliente, na mesma assinatura. Isolamento é uma propriedade vital que garante que as VMs e as comunicações do cliente permaneçam privadas em uma rede virtual.
-   [**UDR**][UDR]. O tráfego em uma Rede Virtual é roteado por padrão com base na tabela de roteamento do sistema. Uma Rota de Definição de Usuário é uma tabela de roteamento personalizada que administradores de rede podem associar a uma ou mais sub-redes para substituir o comportamento da tabela de roteamento do sistema e definir um caminho de comunicação em uma rede virtual. A presença de UDRs garante que o tráfego de saída spoke transite por VMs personalizadas específicas e/ou Dispositivos de Rede Virtual e balanceadores de carga presentes no hub e nos spokes.
-   [**NSG**][NSG]. Um Grupo de Segurança de Rede é uma lista de regras de segurança que atuam como filtragem de tráfego em Fontes IP, Destino IP, Protocolos, portas de Origem IP e portas de Destino IP. O NSG pode ser aplicado a uma sub-rede, um cartão de NIC Virtual associado a uma VM do Azure ou ambos. Os NSGs são essenciais para implementar um controle de fluxo correto no hub e nos spokes. O nível de segurança proporcionada pelo NSG é uma função de quais portas você abre e para qual finalidade. Os clientes devem aplicar filtros adicionais por VM com os firewalls baseados em host, como IPtables ou o Firewall do Windows.
-   **DNS**. A resolução de nomes de recursos em VNets de um vDC é fornecida por meio do DNS. O escopo de resolução de nomes do DNS padrão está limitado à VNet. Geralmente, um serviço DNS personalizado precisa ser implantado no hub como parte dos serviços comuns, mas os consumidores principais de serviços DNS residem no spoke. Se necessário, os clientes podem criar uma estrutura hierárquica de DNS com a delegação de zonas DNS para os spokes.
-   [**Assinatura][SubMgmt] e [Gerenciamento de Grupo de Recursos][RGMgmt]**. Uma assinatura define um limite natural para criar vários grupos de recursos no Azure. Recursos em uma assinatura são montados em conjunto em contêineres lógicos denominados Grupos de Recursos. O Grupo de Recursos representa um grupo lógico para organizar os recursos de uma vDC.
-   [**RBAC**][RBAC]. Por meio de RBAC, é possível mapear a função organizacional junto com direitos de acesso a recursos específicos do Azure, permitindo que você restrinja os usuários a somente um certo subconjunto de ações. Com RBAC, você pode conceder acesso ao atribuir a função apropriada a usuários, grupos e aplicativos no escopo relevante. O escopo de uma atribuição de função pode ser uma assinatura do Azure, um grupo de recursos ou um único recurso. RBAC permite a herança de permissões. Uma função atribuída a um escopo pai também concede acesso aos filhos contidos nele. Usando RBAC, você pode separar as tarefas e conceder aos usuários apenas o nível de acesso de que eles precisam para trabalhar. Por exemplo, use RBAC para permitir que um funcionário gerencie máquinas virtuais em uma assinatura, enquanto outro pode gerenciar bancos de dados SQL na mesma assinatura.
-   [**Emparelhamento VNet**][VNetPeering]. O recurso fundamental usado para criar a infraestrutura de um vDC é Emparelhamento VNet, um mecanismo que conecta duas VNets (redes virtuais) na mesma região por meio da rede do data center do Azure.

#### <a name="component-type-perimeter-networks"></a>Tipo de componente: redes de perímetro
Componentes da [rede de perímetro][DMZ] (também conhecidos como uma rede DMZ) permitem que você forneça conectividade de rede com suas redes do data center físicas ou locais, juntamente com qualquer conectividade para e da Internet. Também é o local em que suas equipes de segurança de rede provavelmente passarão a maior parte do tempo.

Os pacotes de entrada devem fluir por dispositivos de segurança no hub, como firewall, IDS, IPS e afins, antes de alcançar os servidores back-end nos spokes. Pacotes limitados à Internet das cargas de trabalho também devem fluir pelos dispositivos de segurança na rede de perímetro para fins de imposição de política, inspeção e auditoria antes de saírem da rede.

Os componentes de rede de perímetro fornecem os seguintes recursos:

-   [Redes Virtuais][VNet], [UDR][UDR], [NSG][NSG]
-   [Solução de Virtualização de Rede][NVA]
-   [Load Balancer][ALB]
-   [Gateway de Aplicativo][AppGW] / [WAF][WAF]
-   [IPs Públicos][PIP]

Normalmente, as equipes de segurança e TI centrais têm a responsabilidade de definir requisitos e operações das redes de perímetro.

[![7]][7]

O diagrama anterior mostra a imposição de dois perímetros com acesso à Internet e uma rede local, ambos residentes no hub. Em um único hub, a rede de perímetro para a Internet pode ser escalada verticalmente para dar suporte a grandes números de LOBs, usando vários farms de WAFs (Firewalls de Aplicativo Web) e/ou firewalls.

[**Redes Virtuais**][VNet] O hub geralmente é criado em uma VNet com várias sub-redes para hospedar os diferentes tipos de serviços que filtram e inspecionam o tráfego de ou para a Internet por meio de NVAs, WAFs e Gateways de Aplicativo do Azure.

[**UDR**][UDR] Usando UDR, os clientes podem implantar firewall, IDS/IPS e outras soluções de virtualização, além de rotear o tráfego de rede por meio dessas soluções de segurança para imposição de política, auditoria e inspeção de limite de segurança. UDRs podem ser criados tanto no hub quanto nos spokes para assegurar que o tráfego passe pelas VMs personalizadas específicas, Soluções de Virtualização de Rede e balanceadores de carga usados pelo vDC. Para assegurar que o tráfego gerado de VMs residentes no spoke transite para as soluções de virtualização corretas, um UDR precisa ser definido nas sub-redes do spoke configurando o endereço IP de front-end do balanceador de carga interno como o próximo salto. O balanceador de carga interno distribui o tráfego interno para as soluções de virtualização (pool de back-end do balanceador de carga).

[![8]][8]

[**Soluções de Virtualização de Rede**][NVA] No hub, a rede de perímetro com acesso à Internet normalmente é gerenciado por meio de um farm de firewalls e/ou WAFs (Firewalls de Aplicativo Web).

LOBs diferentes geralmente usam muitos aplicativos Web e esses aplicativos tendem a sofrer de diversas vulnerabilidades e explorações em potencial. Os Firewalls de Aplicativos Web são uma categoria especial de produto usada para detectar ataques contra aplicativos Web (HTTP/HTTPS) em mais profundidade que um firewall genérico. Em comparação à tecnologia de firewall tradicional, WAFs têm um conjunto de recursos específicos para proteger os servidores Web internos contra ameaças.

Um farm de firewall é o grupo de firewalls que trabalha em conjunto sob a mesma administração comum, com um conjunto de regras de segurança para proteger as cargas de trabalho hospedadas em spokes e controlar o acesso a redes locais. Um farm de firewall tem software menos especializado comparado a um WAF, mas tem um escopo de aplicação mais amplo para filtrar e inspecionar qualquer tipo de tráfego de entrada e saída. Farms de firewall normalmente são implementados no Azure por meio de NVAs (Soluções de Virtualização de Rede), que estão disponíveis no Azure marketplace.

É recomendável usar um conjunto de NVAs para tráfego originado na Internet e outro para o tráfego originado localmente. Usar apenas um conjunto de NVAs para ambos é um risco à segurança, uma vez que ele não oferece nenhuma segurança de perímetro entre os dois conjuntos de tráfego de rede. Usar NVAs separadas reduz a complexidade de verificar as regras de segurança e deixa claro quais regras correspondem a quais solicitações de rede de entrada.

A maioria das grandes empresas gerencia vários domínios. Um DNS do Azure pode ser usado para hospedar os registros DNS para um domínio específico. Como exemplo, o VIP (endereço IP virtual) do balanceador externo de carga do Azure (ou WAFs) pode ser registrado no registro A de um registro de DNS do Azure.

[**Azure Load Balancer**][ALB] O Azure Load Balancer oferece um serviço de Camada 4 (TCP, UDP) alta disponibilidade, que pode distribuir o tráfego de entrada entre instâncias de serviço definidas em um conjunto com balanceamento de carga. O tráfego enviado ao balanceador de carga de pontos de extremidade de front-end (pontos de extremidade IP públicos ou pontos de extremidade IP privados) pode ser redistribuído com ou sem conversão de endereços para um pool de endereços IP de back-end (exemplos são Soluções de Virtualização de Rede ou VMs).

O Azure Load Balancer também pode investigar a integridade de várias instâncias de servidor e quando uma investigação falha em responder, o balanceador de carga para de enviar tráfego para a instância não íntegra. Em um vDC, temos a presença de um balanceador externo de carga no hub (por exemplo, balancear o tráfego para NVAs) e nos spokes (para realizar tarefas como balanceamento de tráfego entre diferentes VMs de um aplicativo de várias camada).

[**Gateway de Aplicativo**][AppGW] O Gateway de Aplicativo do Microsoft Azure é uma solução de virtualização dedicada que fornece o ADC (controlador de entrega de aplicativos) como um serviço, oferecendo vários recursos de balanceamento de carga de camada 7 para o seu aplicativo. Ele permite que você otimize a produtividade do Web farm descarregando a terminação SSL com uso intensivo de CPU para o Gateway de Aplicativo. Ele também fornece outros recursos de roteamento de camada 7, incluindo distribuição round robin do tráfego de entrada, afinidade de sessão, roteamento com base no caminho de URL e a capacidade de hospedar vários sites por trás de um único Gateway de Aplicativo baseado em cookie. Um WAF (firewall do aplicativo Web) também é fornecido como parte da SKU do WAF do gateway de aplicativo. Essa SKU oferece proteção para aplicativos Web contra explorações e vulnerabilidades comuns da Web. O Gateway de Aplicativo pode ser configurado como um gateway voltado para a Internet, um gateway apenas interno ou uma combinação de ambos. 

[**IPs públicos**][PIP] Alguns recursos do Azure permitem associar pontos de extremidade de serviço a um endereço IP público que permite que seu recurso seja acessado pela Internet. Esse ponto de extremidade usa NAT (Conversão de Endereços de Rede) para rotear o tráfego para a porta e endereço internos na rede virtual do Azure. Esse caminho é a principal rota para que o tráfego externo passe para dentro da rede virtual. Os endereços IP Públicos podem ser configurados pelo usuário para determinar qual tráfego é passado para dentro e como e onde ele é convertido para a rede virtual.

#### <a name="component-type-monitoring"></a>Tipo de componente: monitoramento
Componentes de monitoramento oferecem visibilidade e alertas de todos os outros tipos de componentes. Todas as equipes devem ter acesso ao monitoramento para os componentes e serviços aos quais elas têm acesso. Se você tiver um equipes de operações ou suporte técnico centralizada, ela precisará ter acesso integrado aos dados fornecidos por esses componentes.

O Azure oferece diferentes tipos de serviços de registro em log e monitoramento para controlar o comportamento de recursos hospedados do Azure. Governança e controle de cargas de trabalho no Azure baseiam-se não apenas na coleta de dados de log, mas também na capacidade de disparar ações com base em eventos relatados específicos.

Há dois tipos principais de logs no Azure:

-   [**Logs de Atividade**][ActLog] (conhecidos também como "Log Operacional") fornecem informações sobre as operações que foram executadas em recursos na assinatura do Azure. Esses logs relatam os eventos de plano de controle para suas assinaturas. Todos os recursos do Azure geram logs de auditoria.

-   [**Logs de Diagnóstico do Azure**][DiagLog] são logs gerados por um recurso que fornecem dados avançados e frequentes sobre a operação desse recurso. O conteúdo desses logs varia de acordo com o tipo de recurso.

[![9]][9]

Em um vDC, é extremamente importante controlar os logs de NSGs, particularmente estas informações:

-   [**Logs de Eventos**][NSGLog]: fornecem informações sobre quais regras de NSG são aplicadas às VMs e funções de instância com base no endereço MAC.
-   [**Logs do Contador**][NSGLog]: controlam quantas vezes cada regra NSG foi executada para negar ou permitir o tráfego.

Todos os logs podem ser armazenados nas Contas de Armazenamento do Azure para fins de auditoria, análise estática ou backup. Quando os logs são armazenados em uma conta de armazenamento do Azure, os clientes podem usar diferentes tipos de estruturas para recuperar, preparar, analisar e visualizar esses dados para relatar o status e a integridade dos recursos de nuvem.

Grandes empresas já devem ter adquirido uma estrutura padrão para monitorar sistemas locais e podem estender essa estrutura para integrar logs gerados por implantações de nuvem. Para organizações que desejam manter todo o registro em log na nuvem, o [OMS (Microsoft Operations Management Suite)][OMS] é uma ótima opção. Como o OMS é implementado como um serviço baseado em nuvem, é possível colocá-lo em funcionamento com investimentos mínimos em serviços de infraestrutura. O OMS também pode integrar-se a componentes do System Center, como System Center Operations Manager, para estender seus investimentos atuais em gerenciamento para a nuvem.

A Log Analytics do OMS é um componente da estrutura do OMS para ajudar a coletar, correlacionar, pesquisar e agir quanto a dados de desempenho e log gerados por sistemas operacionais, aplicativos e componentes de infraestrutura de nuvem. Ele dá aos clientes informações operacionais em tempo real usando pesquisa integrada e painéis personalizados para analisar todos os registros em todas as suas cargas de trabalho em um vDC.

#### <a name="component-type-workloads"></a>Tipo de componente: cargas de trabalho
Componentes de carga de trabalho são o local em que seus aplicativos reais e serviços residem. Também é o ponto em que as equipes de desenvolvimento de aplicativo passam a maior parte do tempo.

As possibilidades de carga de trabalho são realmente infinitas. A seguir estão apenas alguns dos tipos de carga de trabalho possíveis:

**Aplicativos de LOB Internos**

Aplicativos de linha de negócios são aplicativos de computador críticos para a operação contínua de uma empresa. Aplicativos de LOB têm algumas características em comum:

-   **Interativos**. Aplicativos de LOB são interativos por natureza: os dados são inseridos e o resultado/relatórios são retornados.
-   **Conduzidos por dados**. Aplicativos de LOB fazem uso intenso de dados, com acesso frequente aos bancos de dados ou outro armazenamento.
-   **Integrados**. Aplicativos de LOB oferecem integração a outros sistemas dentro ou fora da organização.

**Sites voltados para o cliente (voltados para a Internet ou internos)** A maioria dos aplicativos que interage com a Internet é composta por sites. O Azure oferece a capacidade de executar um site em uma VM IaaS ou de um site [Aplicativos Web do Azure][WebApps] (PaaS). Os Aplicativos Web do Azure dão suporte à integração com VNets que permitem a implantação dos Aplicativos Web no spoke de uma vDC. Com a integração VNET, você não precisa expor um ponto de extremidade da Internet para seus aplicativos, mas pode usar o endereço roteável não da Internet privado dos recursos da sua VNet privada em vez disso.

**Big Data/Análise de Dados** Quando os dados precisam ser escalados verticalmente para um volume muito grande, talvez os bancos de dados não sejam escalados verticalmente de modo adequado. A tecnologia Hadoop oferece um sistema para executar consultas distribuídas em paralelo em um grande número de nós. Os clientes têm a opção de executar cargas de trabalho de dados em VMs de IaaS ou PaaS ([HDInsight][HDI]). O HDInsight dá suporte à implantação em uma VNet baseada em local, podendo ser implantado em um cluster de um spoke do vDC.

**Eventos e Mensagens**
[Hubs de Eventos do Azure][EventHubs] são um serviço de ingestão de telemetria em hiperescala que coleta, transforma e armazena milhões de eventos. Como uma plataforma de streaming distribuída, oferece baixa latência e tempo de retenção configurável, permitindo a ingestão de grandes quantidades de telemetria no Azure e a leitura de dados de vários aplicativos. Com Hubs de Evento, um único fluxo pode dar suporte a pipelines tanto em tempo real quanto em lote.

Um serviço de mensagem em nuvem altamente confiável entre aplicativos e serviços pode ser implementado por meio do [Barramento de Serviço do Azure][ServiceBus] que oferece sistema de mensagens agenciado assíncrono entre cliente e servidor, junto com recursos de publicar/assinar e mensagens PEPS (primeiro a entrar, primeiro a sair).

[![10]][10]

### <a name="multiple-vdc"></a>Vários vDCs
Até agora, este artigo concentrou-se em um único vDC, descrevendo os componentes e a arquitetura básicos que contribuem para uma vDC resiliente. Recursos do Azure, como Azure Load Balancer, NVAs, conjuntos de disponibilidade, conjuntos de dimensionamento, junto a outros mecanismos, contribuem para um sistema que permite que você crie níveis sólidos de SLA em seus serviços de produção.

No entanto, um único vDC é hospedado em uma única região e está vulnerável a interrupções importantes que podem afetar toda uma região. Os clientes que desejam obter SLAs altos precisam proteger os serviços por meio de implantações do mesmo projeto em dois (ou mais) vDCs, colocados em regiões diferentes.

Além de questões de SLA, há vários cenários comuns em que a implantação de vários vDCs faz sentido:

-   Presença regional/global
-   Recuperação de desastre
-   Mecanismo para desviar o tráfego entre o DC

#### <a name="regionalglobal-presence"></a>Presença regional/global
Data Centers do Azure estão presentes em várias regiões no mundo todo. Ao selecionar vários data centers do Azure, os clientes precisam considerar dois fatores relacionados: distâncias geográficas e latência. Os clientes precisam avaliar a distância geográfica entre os vDCs e a distância entre o vDC e os usuários finais para oferecer a melhor experiência de usuário.

A Região do Azure que hospeda os vDCs também precisa estar em conformidade com os requisitos normativos estabelecidos por qualquer jurisdição legal sob a qual sua organização opere.

#### <a name="disaster-recovery"></a>Recuperação de desastre
A implementação de um plano de recuperação de desastre está fortemente relacionada ao tipo de carga de trabalho em questão e à capacidade de sincronizar o estado da carga de trabalho entre diferentes vDCs. Idealmente, a maioria dos clientes deseja sincronizar dados de aplicativo entre implantações em execução em dois vDCs diferentes para implementar um mecanismo de failover rápido. A maioria dos aplicativos é sensível à latência e isso pode causar possíveis tempos limites e atrasos na sincronização de dados.

Sincronização ou monitoramento de pulsação de aplicativos em diferentes vDCs exige a comunicação entre eles. Dois vDCs em regiões diferentes podem ser conectados por meio de:

-   Emparelhamento privado do ExpressRoute quando hubs de vDC estão conectados ao mesmo circuito do ExpressRoute
-   vários circuitos do ExpressRoute conectados por meio do backbone corporativo e sua malha vDC conectada aos circuitos do ExpressRoute
-   Conexões de VPN Site a Site entre seus hubs o vDC em cada Região do Azure

Geralmente, a conexão do ExpressRoute é o mecanismo preferido devido à maior largura de banda e à latência consistente ao transitar pelo backbone da Microsoft.

Não há nenhuma receita mágica para validar um aplicativo distribuído entre dois (ou mais) vDCs diferentes localizados em regiões diferentes. Os clientes devem executar testes de qualificação de rede para verificar a latência e a largura de banda das conexões e definir se replicação síncrona ou assíncrona de dados é apropriada e qual pode ser o RTO (objetivo de tempo de recuperação) para suas cargas de trabalho.

#### <a name="mechanism-to-divert-traffic-between-dc"></a>Mecanismo para desviar o tráfego entre o DC
Uma técnica eficaz para desviar o tráfego que entra em um DC para outro é baseada em DNS. O [Gerenciador de Tráfego do Azure][TM] usa o mecanismo de DNS (sistema de nomes de domínio) para direcionar o tráfego do usuário final para o ponto de extremidade público mais apropriado em um vDC específico. Por meio de testes, o Gerenciador de Tráfego verifica periodicamente a integridade do serviço de pontos de extremidade públicos em diferentes vDCs e, em caso de falha desses pontos de extremidade, faz automaticamente o roteamento para o vDC secundário.

O Gerenciador de Tráfego funciona em pontos de extremidade públicos do Azure e pode ser usado, por exemplo, para controlar/desviar o tráfego para VMs do Azure e os aplicativos Web no vDC apropriado. O Gerenciador de Tráfego é resiliente mesmo em caso de uma falha de toda a região do Azure e pode controlar a distribuição do tráfego do usuário para pontos de extremidade de serviço em diferentes vDCs com base em vários critérios (por exemplo, falha de um serviço em um vDC específico ou selecionando o vDC com a menor latência de rede para o cliente).

### <a name="conclusion"></a>Conclusão
O Data Center Virtual é uma abordagem à migração do data center para a nuvem que usa uma combinação de recursos e funcionalidades para criar uma arquitetura escalonável no Azure que maximiza o uso de recursos de nuvem, reduzindo os custos e simplificando a governança do sistema. O conceito de vDC baseia-se em uma topologia hub-spokes, fornecendo serviços compartilhados comuns no hub e permitindo aplicativos/cargas de trabalho específicos nos spokes. Um vDC corresponde à estrutura de funções da empresa, em que departamentos diferentes (TI Central, DevOps, operação e manutenção) trabalham em conjunto, cada um com uma lista específica de funções e tarefas. Uma vDC cumpre os requisitos para uma migração de "Lift-and-Shift", mas também oferece muitas vantagens em implantações de nuvem nativas.

## <a name="references"></a>Referências
Os seguintes recursos foram discutidos neste documento. Clique nos links para sabe mais.

| | | |
|-|-|-|
|Recursos de rede|Balanceamento de Carga|Conectividade|
|[Redes Virtuais do Azure][VNet]</br>[Grupos de segurança de rede][NSG]</br>[Logs do NSG][NSGLog]</br>[Roteamento Definido pelo Usuário][UDR]</br>[Soluções de Virtualização de Rede][NVA]</br>[Endereços IP Públicos][PIP]|[Azure Load Balancer (L3) ][ALB]</br>[Gateway de Aplicativo (L7) ][AppGW]</br>[Firewall do Aplicativo Web][WAF]</br>[Gerenciador de Tráfego do Azure][TM] |[Emparelhamento VNet][VNetPeering]</br>[Rede Privada Virtual][VPN]</br>[ExpressRoute][ExR]
|Identidade</br>|Monitoramento</br>|Práticas Recomendadas</br>|
|[Azure Active Directory][AAD]</br>[Autenticação Multifator][MFA]</br>[Controles de Acesso Baseados em Função][RBAC]</br>[Funções Padrão do AAD][Roles] |[Logs de Atividade][ActLog]</br>[Logs de Diagnóstico][DiagLog]</br>[Microsoft Operations Management Suite][OMS]</br> |[Práticas Recomendadas de Redes de Perímetro][DMZ]</br>[Gerenciamento de Assinaturas][SubMgmt]</br>[Gerenciamento de Grupo de Recursos][RGMgmt]</br>[Limites de Assinatura do Azure][Limits] |
|Outros serviços do Azure|
|[Aplicativos Web do Azure][WebApps]</br>[HDInsights (Hadoop) ][HDI]</br>[Hubs de Eventos][EventHubs]</br>[Barramento de Serviço][ServiceBus]|



## <a name="next-steps"></a>Próximas etapas
 - Explore [Emparelhamento VNet][VNetPeering], a tecnologia de base para os designs de hub e spoke do vDC
 - Implemente o [AAD][AAD] para começar a usar a exploração de [RBAC][RBAC]
 - Desenvolva um modelo de gerenciamento de Assinatura e Recurso e um modelo de RBAC para atender à estrutura, aos requisitos e às políticas da sua organização. A atividade mais importante é o planejamento. Tanto quanto possível, planeje reorganizações, fusões, novas linhas de produto etc.

<!--Image References-->
[0]: ./media/networking-virtual-datacenter/redundant-equipment.png "Exemplos de sobreposição de componente" 
[1]: ./media/networking-virtual-datacenter/vdc-high-level.png "Exemplo de alto nível de vDC de hub e spoke"
[2]: ./media/networking-virtual-datacenter/hub-spokes-cluster.png "Cluster de hubs e spokes"
[3]: ./media/networking-virtual-datacenter/spoke-to-spoke.png "Spoke-to-spoke"
[4]: ./media/networking-virtual-datacenter/vdc-block-level-diagram.png "Diagrama em nível de bloco do vDC"
[5]: ./media/networking-virtual-datacenter/users-groups-subsciptions.png "Usuários, grupos, assinaturas e projetos"
[6]: ./media/networking-virtual-datacenter/infrastructure-high-level.png "Diagrama de alto nível de infraestrutura"
[7]: ./media/networking-virtual-datacenter/highlevel-perimeter-networks.png "Diagrama de alto nível de infraestrutura"
[8]: ./media/networking-virtual-datacenter/vnet-peering-perimeter-neworks.png "Redes de perímetro e de emparelhamento VNet"
[9]: ./media/networking-virtual-datacenter/high-level-diagram-monitoring.png "Diagrama de alto nível para Monitoramento"
[10]: ./media/networking-virtual-datacenter/high-level-workloads.png "Diagrama de alto nível para Carga de Trabalho"

<!--Link References-->
[Limits]: https://docs.microsoft.com/azure/azure-subscription-service-limits
[Roles]: https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles
[VNet]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview
[NSG]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg 
[VNetPeering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview 
[UDR]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview 
[RBAC]: https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is
[MFA]: https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication
[AAD]: https://docs.microsoft.com/azure/active-directory/active-directory-whatis
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways 
[ExR]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction 
[NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[SubMgmt]: https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-subscription-governance 
[RGMgmt]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview
[DMZ]: https://docs.microsoft.com/azure/best-practices-network-security
[ALB]: https://docs.microsoft.com/azure/load-balancer/load-balancer-overview
[PIP]: https://docs.microsoft.com/azure/virtual-network/resource-groups-networking#public-ip-address
[AppGW]: https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction
[WAF]: https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview
[ActLog]: https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs 
[DiagLog]: https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs
[NSGLog]: https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log
[OMS]: https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview
[WebApps]: https://docs.microsoft.com/azure/app-service/
[HDI]: https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-introduction
[EventHubs]: https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs 
[ServiceBus]: https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-overview
[TM]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview
