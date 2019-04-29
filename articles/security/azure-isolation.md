---
title: Isolamento na nuvem pública do Azure | Microsoft Docs
description: Aprenda sobre os serviços de computação baseados em nuvem que incluem uma ampla seleção de instâncias e serviços de computação, os quais podem ser escalados verticalmente de forma automática para atender às necessidades de seu aplicativo ou empresa.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: f5d1c66cb049ab9ec52db619d55a4bb3e485e4b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60588317"
---
# <a name="isolation-in-the-azure-public-cloud"></a>Isolamento na nuvem pública do Azure
##  <a name="introduction"></a>Introdução
### <a name="overview"></a>Visão geral
Para ajudar os clientes atuais e potenciais do Azure a entender e usar as várias funcionalidades relacionadas à segurança disponíveis na Plataforma Azure e em suas proximidades, a Microsoft desenvolveu uma série de white papers, visões gerais de segurança, práticas recomendadas e listas de verificação.
Os tópicos variam em termos de abrangência e profundidade e são atualizados periodicamente. Este documento faz parte de uma série, conforme resumido na próxima seção Resumo.

### <a name="azure-platform"></a>Plataforma Azure
O Azure é uma plataforma de serviço de nuvem aberta e flexível que dá suporte a mais ampla seleção de sistemas operacionais, linguagens de programação, estruturas, ferramentas, bancos de dados e dispositivos. Por exemplo, você pode:
- Executar contêineres do Linux com a integração com o Docker;
- Compilar aplicativos com JavaScript, Python, .NET, PHP, Java e Node.js; e
- Crie back-ends para dispositivos iOS, Android e Windows.

O Microsoft Azure dá suporte às mesmas tecnologias com as quais milhões de desenvolvedores e profissionais de TI já contam e confiam.

Ao se basear ou migrar ativos de TI para um provedor de serviços de nuvem pública, você está confiando na capacidade dessa organização de proteger seus aplicativos e dados com os serviços e os controles que ela oferece para gerenciar a segurança de seus ativos baseados em nuvem.

A infraestrutura do Azure foi projetada desde a instalação até os aplicativos para hospedar simultaneamente milhões de clientes e fornecer uma base confiável com a qual as empresas podem atender às suas necessidades de segurança. Além disso, o Azure oferece uma ampla variedade de opções de segurança configuráveis e a capacidade de controlá-las, para que você possa personalizar a segurança de forma a atender aos requisitos específicos de suas implantações. Este documento ajuda você a atender a esses requisitos.

### <a name="abstract"></a>Resumo

O Microsoft Azure permite que você execute aplicativos e VMs (máquinas virtuais) em uma infraestrutura física compartilhada. Uma das principais motivações econômicas para a execução de aplicativos em um ambiente de nuvem é a capacidade de distribuir o custo de recursos compartilhados entre vários clientes. Essa prática de multilocação aprimora a eficiência por meio da multiplexação de recursos entre diferentes clientes por custos baixos. Infelizmente, ela também apresenta o risco de compartilhar servidores físicos e outros recursos de infraestrutura a fim de executar seus aplicativos confidenciais e VMs que possam pertencer a um usuário aleatório e possivelmente malicioso.

Este artigo descreve como o Microsoft Azure fornece isolamento contra usuários mal-intencionados e bem-intencionados, e serve como um guia para arquitetar soluções de nuvem, oferecendo várias opções de isolamento para arquitetos. Este white paper se concentra na tecnologia da Plataforma Azure e nos controles de segurança voltados para o cliente e não tenta solucionar questões de SLAs, modelos de preço e considerações de prática de DevOps.

## <a name="tenant-level-isolation"></a>Isolamento no nível do locatário
Um dos principais benefícios da computação em nuvem é o conceito de uma infraestrutura comum e compartilhada simultaneamente entre vários clientes, resultando em economia de escala. Esse conceito é chamado de multilocação. A Microsoft trabalha continuamente para garantir que a arquitetura de multilocação do Microsoft Cloud Azure ofereça suporte a segurança, confidencialidade, privacidade, integridade e padrões de disponibilidade.

No local de trabalho habilitado pela nuvem, um locatário pode ser definido como um cliente ou uma organização que possui e gerencia uma instância específica desse serviço em nuvem. Com a plataforma de identidade fornecida pelo Microsoft Azure, um locatário é simplesmente uma instância dedicada do Azure Active Directory (Azure AD) que sua organização recebe e detém quando se inscreve em um serviço de nuvem da Microsoft.

Cada diretório do Azure AD é distinto e separado de outros diretórios do Azure AD. Assim como um prédio de escritórios corporativos é um ativo seguro específico somente a sua organização, um diretório do Azure AD também foi projetado para ser um ativo seguro para ser usado somente por sua organização. A arquitetura do Azure AD isola os dados as informações de identidade do cliente, evitando que sejam misturados a outros. Isso significa que os usuários e administradores de um diretório do Azure AD não podem acessar acidentalmente ou maliciosamente dados em outro diretório.

### <a name="azure-tenancy"></a>Locação do Azure
A locação do Azure (Assinatura do Azure) refere-se a uma relação de "cliente/cobrança" e um único [locatário](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) no [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis). O isolamento no nível do locatário no Microsoft Azure é obtido usando o Azure Active Directory e [controles com base em função](https://docs.microsoft.com/azure/role-based-access-control/overview) oferecidos por ele. Cada assinatura do Azure está associada com um diretório do Azure Active Directory (AD).

Usuários, grupos e aplicativos do diretório podem gerenciar recursos na assinatura do Azure. Você pode atribuir esses direitos de acesso usando o Portal do Azure, ferramentas de linha de comando do Azure e APIs de Gerenciamento do Azure. Um locatário do Azure AD é logicamente isolado usando limites de segurança, para que nenhum cliente possa acessar ou comprometer colocatários, de forma maliciosa ou acidental. O Azure AD é executado em servidores "bare metal" isolados em um segmento de rede segregado, onde a filtragem de pacotes no nível do host e o Firewall do Windows bloqueiam o tráfego e conexões indesejadas.

- O acesso a dados no AD do Azure requer autenticação de usuário por meio de um STS (serviço de token de segurança). As informações sobre a existência do usuário, o estado habilitado e a função são usadas pelo sistema de autorização para determinar se o acesso solicitado ao locatário de destino está autorizado para esse usuário nessa sessão.

![Locação do Azure](./media/azure-isolation/azure-isolation-fig1.png)


- Os locatários são contêineres discretos, e não há nenhuma relação entre eles.

- Não há acesso entre os locatários, a menos que o administrador de locatários o conceda por meio de federação ou do provisionamento de contas de usuários de outros locatários.

- O acesso físico aos servidores que compõem o serviço Azure AD, e acesso direto aos sistemas de back-end do Azure AD, é restrito.

- Os usuários do Azure AD não têm acesso a ativos físicos ou locais e, portanto, não é possível ignorar as verificações lógicas da política de RBAC mencionadas a seguir.

Para as necessidades de diagnóstico e manutenção, um modelo operacional que emprega um sistema de elevação de privilégio just-in-time é exigido e usado. O Azure AD Privileged Identity Management (PIM) introduz o conceito de um administrador elegível. [Administradores elegíveis](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) devem ser usuários que precisam de acesso privilegiado às vezes, mas não todos os dias. A função fica inativa até que o usuário precise de acesso, então ele conclui um processo de ativação e torna-se um administrador ativo por um tempo predeterminado.

![Gerenciamento de identidades com privilégios do AD do Azure](./media/azure-isolation/azure-isolation-fig2.png)

O Azure Active Directory hospeda cada locatário em seu próprio contêiner protegido, com políticas e permissões para e dentro do contêiner que é de propriedade e gerenciamento exclusivo do locatário.

O conceito de contêineres de locatário está profundamente arraigado no serviço de diretório em todas as camadas, desde portais até o armazenamento persistente.

Mesmo quando os metadados de vários locatários do Azure Active Directory são armazenados no mesmo disco físico, não há nenhuma relação entre os contêineres além do que é definido pelo serviço de diretório, que por sua vez é determinado pelo administrador do locatário.

### <a name="azure-role-based-access-control-rbac"></a>RBAC (Controle de Acesso Baseado em Função) do Azure
[O RBAC (Controle de acesso baseado em função) do Azure](https://docs.microsoft.com/azure/role-based-access-control/overview) ajuda você a compartilhar vários componentes disponíveis dentro de uma assinatura do Azure fornecendo gerenciamento de acesso refinado para o Azure. O RBAC do Azure permite a separação de tarefas dentro de sua organização e a concessão de acesso com base no que os usuários precisam para realizar seus trabalhos. Em vez de dar a todos permissões irrestritas na assinatura ou recursos do Azure, você pode permitir apenas certas ações.

O RBAC do Azure tem três funções básicas que se aplicam a todos os tipos de recurso:

- **proprietário** tem acesso total a todos os recursos, inclusive o direito de delegar acesso a outros usuários.

- **colaborador** pode criar e gerenciar todos os tipos de recursos do Azure, mas não pode conceder acesso a outras pessoas.

- **leitor** pode exibir os recursos existentes do Azure.

![Controle de acesso baseado em função do Azure](./media/azure-isolation/azure-isolation-fig3.png)

As demais funções RBAC no Azure permitem o gerenciamento de recursos específicos do Azure. Por exemplo, a função Colaborador de Máquina Virtual permite que o usuário crie e gerencie máquinas virtuais. Ela não concede acesso à Rede Virtual ou à sub-rede do Azure com a qual a máquina virtual se conecta.

[Funções internas de RBAC](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) lista as funções disponíveis no Azure. Ela especifica as operações e o escopo que cada função interna concede aos usuários. Se você pretende definir suas próprias funções para ter ainda mais controle, confira como criar [Funções personalizadas no RBAC do Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).

Entre os outros recursos para o Azure Active Directory estão:
- O Azure AD habilita o SSO para aplicativos SaaS, independentemente de onde estejam hospedados. Alguns aplicativos são federados com o AD do Azure e outros usam SSO com senha. Os aplicativos federados também podem dar suporte ao provisionamento do usuário e ao [armazenamento de senha no cofre](https://www.techopedia.com/definition/31415/password-vault).

- O acesso aos dados no [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) é controlado por meio de autenticação. Cada conta de armazenamento tem uma chave primária ([chave de conta de armazenamento](https://docs.microsoft.com/azure/storage/storage-create-storage-account) ou SAK) e uma chave secreta secundária (a assinatura de acesso compartilhado ou SAS).

- O Azure AD fornece identidade como um serviço de federação usando [Serviços de Federação do Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-azure-adfs), sincronização e replicação com diretórios locais.

- A [Autenticação Multifator do Azure](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) é um serviço de autenticação multifator que exige que os usuários verifiquem seus acessos usando um aplicativo móvel, uma chamada telefônica ou uma mensagem de texto. Ela pode ser usada com o Azure AD para proteger os recursos locais com o servidor de Autenticação Multifator do Azure e com diretórios e aplicativos personalizados que usam o SDK.

- Os [Azure AD Domain Services](https://azure.microsoft.com/services/active-directory-ds/) permitem adicionar máquinas virtuais do Azure a um domínio do Active Directory sem a necessidade de implantar controladores de domínio. Você pode acessar essas máquinas virtuais usando suas credenciais corporativas do Active Directory e administrar as máquinas virtuais associadas ao domínio usando uma Política de Grupo para impor linhas de base de segurança em todas as suas máquinas virtuais do Azure.

- O [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) fornece um serviço de gerenciamento de identidade global, altamente disponível para aplicativos voltados para o consumidor, que pode ser dimensionado para centenas de milhões de identidades. Ele pode ser integrado a plataformas móveis e da Web. Seus clientes podem fazer logon em todos os seus aplicativos por meio de experiências personalizáveis usando suas contas sociais existentes ou criando credenciais.

### <a name="isolation-from-microsoft-administrators--data-deletion"></a>Isolamento dos administradores da Microsoft e exclusão de dados
A Microsoft toma providências fortes para proteger seus dados contra o acesso ou uso inadequado por pessoas não autorizadas. Esses controles e processos operacionais têm base nos [Termos de Serviços Online](https://aka.ms/Online-Services-Terms), que oferecem compromissos contratuais que regulam o acesso aos seus dados.

-   Os engenheiros da Microsoft não tem acesso padrão aos seus dados na nuvem. Em vez disso, eles recebem acesso sob supervisão de gerenciamento, apenas quando é necessário. Esse acesso é cuidadosamente controlado e registrado em log, sendo revogado assim que não é mais necessário.

-   A Microsoft pode contratar outras empresas para fornecer serviços limitados em seu nome. Os subcontratados podem acessar os dados do cliente apenas para fornecer os serviços para os quais os contratamos, e estão proibidos de usá-los para qualquer outra finalidade. Além disso, eles estão obrigados por contrato a manter a confidencialidade das informações de nossos clientes.

Os serviços comerciais com certificações auditadas como ISO/IEC 27001 são verificados regularmente pela Microsoft e por empresas de auditoria reconhecidas, que realizam amostras de auditorias para atestar que o acesso ocorre apenas para fins comerciais legítimos. Você pode acessar os dados de seus clientes a qualquer momento e por qualquer motivo.

Se você excluir quaisquer dados, o Microsoft Azure excluirá os dados, incluindo qualquer cópia em cache ou de backup. Para serviços em escopo, essa exclusão ocorrerá em até 90 dias após o término do período de retenção. (Os serviços no escopo são definidos na seção Termos do processamento de dados de nossos [Termos de Serviços Online](https://aka.ms/Online-Services-Terms).)

Se uma unidade de disco usada para armazenamento sofrer uma falha de hardware, ela será [apagada ou destruída](https://microsoft.com/trustcenter/privacy/you-own-your-data) com segurança antes de a Microsoft retorná-la para o fabricante para reparo ou substituição. Os dados no disco são substituídos para garantir que não possam ser recuperados por qualquer meio.

## <a name="compute-isolation"></a>Isolamento de computação
O Microsoft Azure fornece vários serviços de computação baseados em nuvem que incluem uma ampla seleção de instâncias e serviços de computação, os quais podem ser escalados verticalmente de forma automática para atender às necessidades de seu aplicativo ou empresa. Essas instâncias e serviços de computação oferecem isolamento em vários níveis a fim de proteger os dados sem sacrificar a flexibilidade de configuração exigida pelos clientes.

### <a name="isolated-virtual-machine-sizes"></a>Tamanhos de máquinas virtuais isoladas
A Computação do Azure oferece tamanhos de máquina virtual Isolada, para um tipo de hardware específico e dedicada a um único cliente.  Esses tamanhos de máquina virtual são mais adequados para cargas de trabalho que exigem um alto grau de isolamento de outros clientes, para cargas de trabalho que envolvem elementos como requisitos normativos e de conformidade.  Os clientes também podem optar por subdividir ainda mais os recursos dessas máquinas virtuais Isoladas usando [o Suporte do Azure para máquinas virtuais aninhadas](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).

Utilizar um tamanho isolado garante que sua máquina virtual será apenas sendo executada na instância de servidor específico.  As ofertas atuais da máquina virtual isolada incluem:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2

Você pode aprender mais sobre cada tamanho isolado disponível [aqui](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory).

### <a name="hyper-v--root-os-isolation-between-root-vm--guest-vms"></a>Isolamento de sistema operacional raiz e Hyper-V entre a VM raiz e as VMs convidadas
A plataforma de computação do Azure tem base na virtualização da máquina — ou seja, todo o código do cliente é executado em uma máquina virtual Hyper-V. Em cada nó do Azure (ou ponto de extremidade de rede), há um Hipervisor executado diretamente sobre o hardware e divide um nó em um número variável de VMs (máquinas virtuais) convidadas.


![Isolamento de sistema operacional raiz e Hyper-V entre a VM raiz e as VMs convidadas](./media/azure-isolation/azure-isolation-fig4.jpg)


Cada nó também tem uma VM raiz especial, que executa o sistema operacional host. Um limite crítico é o isolamento da VM raiz das VMs convidadas, e entre as VMs convidadas, gerenciado pelo hipervisor e pelo sistema operacional raiz. O emparelhamento do hipervisor/sistema operacional raiz desfruta da experiência de décadas em segurança de sistema operacional da Microsoft, e do aprendizado mais recente com o Hyper-V da Microsoft, para oferecer um isolamento mais forte de VMs convidadas.

A plataforma do Azure usa um ambiente virtualizado. As instâncias do usuário funcionam como máquinas virtuais autônomas que não têm acesso a um servidor de host físico.

O hipervisor do Azure funciona como um microkernel e envia todas as solicitações de acesso de hardware das máquinas virtuais convidadas para o host para processamento usando uma interface de memória compartilhada chamada VMBus. Isso impede que os usuários obtenham acesso bruto de leitura/gravação/execução no sistema e minimiza o risco de compartilhamento de recursos do sistema.

### <a name="advanced-vm-placement-algorithm--protection-from-side-channel-attacks"></a>Algoritmo avançado de posicionamento da VM e proteção contra ataques de canal lateral
Qualquer ataque entre VMs envolve duas etapas: colocar uma VM controlada pelo adversário no mesmo host que uma das VMs vítima e, em seguida, violar o limite de isolamento para roubar informações confidenciais da vítima ou afetar seu desempenho por ganância ou vandalismo. O Microsoft Azure fornece proteção nas duas etapas usando um algoritmo avançado de posicionamento de VM e proteção contra todos os ataques de canal lateral conhecidos, incluindo VMs vizinhas barulhentas.

### <a name="the-azure-fabric-controller"></a>O Controlador de malha do Azure
O Controlador de Malha do Azure é responsável por alocar recursos de infraestrutura para cargas de trabalho de locatário e gerencia a comunicação unidirecional do host para máquinas virtuais. O algoritmo de posicionamento da VM do controlador de malha do Azure é altamente sofisticado e quase impossível de prever como nível de host físico.

![O Controlador de malha do Azure](./media/azure-isolation/azure-isolation-fig5.png)

O hipervisor do Azure impõe a memória e a separação de processos entre máquinas virtuais e roteia com segurança o tráfego de rede para locatários do sistema operacional convidado. Isso elimina a possibilidade de ataques de canal lateral no nível da VM.

No Azure, a VM raiz é especial: ela executa um sistema operacional protegido chamado de sistema operacional raiz, que hospeda um agente de malha (FA). Os FAs são usados, por sua vez, para gerenciar os agentes convidados (GA) nos sistemas operacionais convidados em VMs do cliente. Os FAs também gerenciam nós de armazenamento.

A coleção de hipervisor do Azure, SO raiz/FA e VMs de cliente/GAs forma um nó de computação. Os FAs são gerenciados por um controlador de malha (FC), que existe fora dos nós de computação e de armazenamento (clusters de computação e de armazenamento são gerenciados por FCs separados). Se um cliente atualiza o arquivo de configuração de seu aplicativo enquanto ele está em execução, o FC se comunica com o FA, que então contata os GAs, que notificam o aplicativo sobre a alteração na configuração. No caso de falha de hardware, o FC localizará automaticamente o hardware disponível e reiniciará a VM no local.

![Controlador de malha do Azure](./media/azure-isolation/azure-isolation-fig6.jpg)

A comunicação de um controlador de malha com um agente é unidirecional. O agente implementa um serviço protegido por SSL que só responde às solicitações do controlador. Ele não pode iniciar conexões com o controlador ou com outros nós internos privilegiados. O FC trata todas as respostas como se fossem não confiáveis.


![Controlador de malha](./media/azure-isolation/azure-isolation-fig7.png)

O isolamento é estendido da VM raiz das VMs convidadas, e entre as VMs convidadas. Os nós de computação também são isolados dos nós de armazenamento para aumentar a proteção.


O hipervisor e o SO host fornecem filtros de pacote de rede para ajudar a garantir que as máquinas virtuais não confiáveis não possam gerar tráfego falsificado ou receber tráfego não endereçado a elas, direcionar tráfego para pontos de extremidade de infraestrutura protegidos ou enviar/receber transmissão de tráfego inadequado.


### <a name="additional-rules-configured-by-fabric-controller-agent-to-isolate-vm"></a>Regras adicionais configuradas pelo Agente do Controlador de Malha para isolar a VM
Por padrão, todo o tráfego é bloqueado quando uma máquina virtual é criada. Então, o agente de controlador de malha configura o filtro de pacote para adicionar regras e exceções e permitir o tráfego autorizado.

Há duas categorias de regras que são programadas:

-   **Regras de configuração do computador ou de infraestrutura:** por padrão, toda a comunicação é bloqueada. Há exceções para permitir que uma máquina virtual envie e receba tráfego DHCP e DNS. As máquinas virtuais também podem enviar tráfego para a Internet "pública" e para outras máquinas virtuais na mesma Rede Virtual do Azure e no servidor de ativação do sistema operacional. A lista de destinos permitidos das máquinas virtuais não inclui sub-redes de roteador do Azure, gerenciamento do Azure e outras propriedades da Microsoft.

-   **Arquivo de configuração de função:** define as Listas de Controle de Acesso (ACLs) de entrada com base no modelo de serviço do locatário.

### <a name="vlan-isolation"></a>Isolamento de VLAN
Há três VLANs em cada cluster:

![Isolamento de VLAN](./media/azure-isolation/azure-isolation-fig8.jpg)


-   A VLAN principal – interconecta nós de clientes não confiáveis

-   A VLAN de FC – contém FCs confiáveis e sistemas de suporte

-   A VLAN do dispositivo – contém a rede confiável e outros dispositivos de infraestrutura

A comunicação é permitida da VLAN de FC para a VLAN principal, mas não pode ser iniciada da VLAN principal para a VLAN de FC. A comunicação também é bloqueada da VLAN principal para a VLAN do dispositivo. Isso garante que mesmo se um nó que executa o código do cliente for comprometido, ele não poderá atacar nós no FC ou nas VLANs do dispositivo.

## <a name="storage-isolation"></a>Isolamento de armazenamento
### <a name="logical-isolation-between-compute-and-storage"></a>Isolamento lógico entre computação e armazenamento
Como parte de seu design fundamental, o Microsoft Azure separa a computação baseada em VM do armazenamento. Essa separação permite que a computação e o armazenamento sejam escalados de maneira independente, facilitando o fornecimento do isolamento e da multilocação.

Portanto, o Armazenamento do Azure é executado em um hardware separado sem conectividade de rede com a Computação do Azure, exceto logicamente. [Isso](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf) significa que, quando um disco virtual é criado, o espaço em disco não é alocado totalmente. Em vez disso, uma tabela é criada mapeando endereços no disco virtual para áreas no disco físico, e essa tabela é inicialmente vazia. **Na primeira vez que um cliente grava dados no disco virtual, o espaço no disco físico é alocado e um ponteiro até ele é posicionado na tabela.**
### <a name="isolation-using-storage-access-control"></a>Isolamento usando o Controle de acesso de armazenamento
**O Controle de acesso no Armazenamento do Azure** tem um modelo de controle de acesso simples. Cada assinatura do Azure pode criar uma ou mais Contas de armazenamento. Cada Conta de armazenamento tem uma única chave secreta usada para controlar o acesso a todos os dados na Conta de armazenamento.

![Isolamento usando o Controle de acesso de armazenamento](./media/azure-isolation/azure-isolation-fig9.png)

**O acesso aos dados do Armazenamento do Azure (incluindo tabelas)** pode ser controlado por meio de um token [SAS (Assinatura de Acesso Compartilhado)](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1), que concede acesso de escopo. A SAS é criada por meio de um modelo de consulta (URL), assinado com a [SAK (chave de conta de armazenamento)](https://msdn.microsoft.com/library/azure/ee460785.aspx). Essa [URL assinada](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) pode ser concedida a outro processo (ou seja, delegada), que pode preencher os detalhes da consulta e fazer a solicitação do serviço de armazenamento. Uma SAS permite a concessão de acesso com base em tempo para clientes sem revelar a chave secreta da conta de armazenamento.

A SAS significa que podemos conceder a um cliente permissões limitadas para objetos em nossa conta de armazenamento por determinado período de tempo e com um conjunto específico de permissões. Podemos conceder essas permissões limitadas sem precisar compartilhar as chaves de acesso da conta.

### <a name="ip-level-storage-isolation"></a>Isolamento de armazenamento no nível do IP
Você pode habilitar o firewall e definir um intervalo de endereços IP para seus clientes confiáveis. Com um intervalo de endereços IP, somente os clientes que possuem um endereço IP no intervalo definido podem se conectar ao [Armazenamento do Azure](https://docs.microsoft.com/azure/storage/storage-security-guide).

Os dados de armazenamento IP podem ser protegidos contra usuários não autorizados por meio de um mecanismo de rede usado para alocar um túnel dedicado de tráfego para o armazenamento IP.

### <a name="encryption"></a>Criptografia
O Azure oferece os seguintes tipos de criptografia para proteger os dados:
-   Criptografia em trânsito

-   Criptografia em repouso

#### <a name="encryption-in-transit"></a>Criptografia em trânsito
A criptografia em trânsito é um mecanismo de proteção de dados quando eles são transmitidos entre redes. Com o Armazenamento do Azure, você pode proteger dados usando:

-   [Criptografia de nível de transporte](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-in-transit), como HTTPS ao transferir dados dentro ou fora do Armazenamento do Azure.

-   [Criptografia na transmissão](../storage/common/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), como a criptografia SMB 3.0 para compartilhamentos de Arquivo do Azure.

-   [Criptografia do cliente](https://docs.microsoft.com/azure/storage/storage-security-guide#using-client-side-encryption-to-secure-data-that-you-send-to-storage), que permite criptografar os dados antes que eles sejam transferidos para o armazenamento e descriptografá-los após serem transferidos para fora do armazenamento.

#### <a name="encryption-at-rest"></a>Criptografia em repouso
Para muitas organizações, a [criptografia de dados em repouso](https://docs.microsoft.com/azure/security/azure-isolation) é uma etapa obrigatória no sentido de garantir a soberania, a privacidade e a conformidade dos dados. Há três recursos do Azure que fornecem criptografia de dados que estão “em repouso”:

-   [Criptografia do Serviço de Armazenamento](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-at-rest) permite solicitar que o serviço de armazenamento criptografe automaticamente os dados ao gravá-los no Armazenamento do Azure.

-   [Client-side Encryption](https://docs.microsoft.com/azure/storage/storage-security-guide#client-side-encryption) também fornece o recurso de criptografia em repouso.

-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) permite criptografar os discos do sistema operacional e os discos de dados usados por uma máquina virtual IaaS.

#### <a name="azure-disk-encryption"></a>Azure Disk Encryption
O [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) para VMs (Máquinas Virtuais) o ajuda a atender aos requisitos de conformidade e segurança organizacionais criptografando os discos de VM (incluindo discos de dados e de inicialização) com chaves e políticas controladas no [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

A solução de Criptografia de Disco para Windows é baseada na [Criptografia de Unidade de Disco BitLocker da Microsoft](https://technet.microsoft.com/library/cc732774.aspx) e a solução para Linux é baseada no [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

A solução dá suporte aos seguintes cenários para VMs IaaS quando habilitados no Microsoft Azure:
-   Integração com o Cofre da Chave do Azure

-   VMs da camada Standard: VMs IaaS das séries A, D, DS, G, GS e assim por diante

-   Como habilitar a criptografia em VMs IaaS Windows e Linux

-   Como desabilitar a criptografia em unidades do sistema operacional e de dados para VMs IaaS do Windows

-   Como desabilitar a criptografia em unidades de dados para VMs IaaS do Linux

-   Ativando a criptografia em VMs IaaS que estão executando o sistema operacional cliente Windows

-   Como habilitar a criptografia em volumes com caminhos de montagem

-   Habilitação da criptografia em VMs do Linux que estão configuradas com distribuição de discos (RAID) usando o [mdadm](https://en.wikipedia.org/wiki/Mdadm)

-   Como habilitar a criptografia em VMs do Linux usando [LVM (Gerenciador de Volume Lógico)](https://msdn.microsoft.com/library/windows/desktop/bb540532) para discos de dados

-   Ativando a criptografia em VMs do Windows que são configurados usando espaços de armazenamento

-   Há suporte para todas as regiões públicas do Azure

A solução não dá suporte aos seguintes cenários, recursos e tecnologia na versão:

-   VMs IaaS da camada Básica

-   Como desabilitar a criptografia em unidades do sistema operacional para VMs IaaS do Linux

-   VMs de IaaS que são criadas usando o método de criação de VM clássico

-   Integração com o Serviço de Gerenciamento de Chaves no local

-   Arquivos do Azure (sistema de arquivos compartilhados), NFS (Network File System), volumes dinâmicos e VMs do Windows configuradas com Sistemas RAID baseados em software

## <a name="sql-azure-database-isolation"></a>Isolamento do Banco de Dados SQL Azure
O Banco de Dados SQL é um serviço de banco de dados relacional de nuvem da Microsoft com base no mecanismo líder de mercado do Microsoft SQL Server e é capaz de lidar com cargas de trabalho críticas. O Banco de Dados SQL oferece isolamento previsível de dados no nível da conta, com base na geografia/região e na rede, tudo com administração quase zero.

### <a name="sql-azure-application-model"></a>Modelo de aplicativo do SQL Azure

[O Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started) é um serviço de banco de dados relacional baseado em nuvem compilado com tecnologias do SQL Server. Ele fornece um serviço de banco de dados multilocatário altamente disponível e escalonável hospedado pela Microsoft na nuvem.

Da perspectiva do aplicativo, o SQL Azure fornece a seguinte hierarquia: cada nível tem de um a vários níveis de independência abaixo.

![Modelo de aplicativo do SQL Azure](./media/azure-isolation/azure-isolation-fig10.png)

A conta e a assinatura são conceitos de plataforma do Microsoft Azure para associar a cobrança e o gerenciamento.

Servidores e bancos de dados lógicos são conceitos específicos do SQL Azure e são gerenciados usando o SQL Azure, pelas interfaces do OData e do TSQL ou por meio do Portal do SQL Azure, que é integrado ao Portal do Azure.

Os servidores do SQL Azure não são instâncias físicas ou de VM, em vez disso, são coleções de bancos de dados que compartilham políticas de gerenciamento e segurança, armazenadas no chamado banco de dados de "mestre lógico".

![SQL Azure](./media/azure-isolation/azure-isolation-fig11.png)

Os bancos de dados mestres lógicos incluem:

-   Logons do SQL usados para se conectar ao servidor

-   Regras de firewall

Não há garantia de que as informações relacionadas à cobrança e ao uso para Bancos de Dados SQL do Azure do mesmo servidor lógico estejam na mesma instância física em cluster do SQL Azure, em vez disso, os aplicativos devem fornecer o nome do banco de dados de destino durante a conexão.

Da perspectiva do cliente, um servidor lógico é criado em uma região geográfica enquanto a criação real do servidor ocorre em um dos clusters na região.

### <a name="isolation-through-network-topology"></a>Isolamento por meio da topologia de rede

Quando um servidor lógico é criado e seu nome DNS é registrado, o nome DNS aponta para o chamado endereço do "Gateway VIP" no data center específico onde o servidor foi posicionado.

Por trás do VIP (Endereço IP virtual), temos uma coleção de serviços de gateway sem estado. Em geral, os gateways são envolvidos quando há a necessidade de coordenação entre várias fontes de dados (banco de dados mestre, banco de dados do usuário etc.). Os serviços de gateway implementam o seguinte:
-   **Proxy de conexão de TDS.** Isso inclui localizar o banco de dados de usuário no cluster de back-end, implementar a sequência de logon e encaminhar pacotes de TDS para o back-end e de volta.

-   **Gerenciamento do banco de dados.** Isso inclui a implementação de uma coleção de fluxos de trabalho para executar operações de banco de dados CREATE/ALTER/DROP. As operações de banco de dados podem ser invocadas por meio do rastreamento de pacotes de TDS ou por APIs do OData explícitas.

-   Operações de logon/usuário CREATE/ALTER/DROP

-   Operações de gerenciamento de servidor lógico por meio da API do OData

![Isolamento por meio da topologia de rede](./media/azure-isolation/azure-isolation-fig12.png)

A camada por trás dos gateways é chamada de "back-end". É nela onde todos os dados são armazenados em um modo altamente disponível. Cada parte dos dados deve pertencer a uma "partição" ou "unidade de failover", com pelo menos três réplicas cada um. As réplicas são armazenadas e replicadas pelo mecanismo do SQL Server e gerenciadas por um sistema de failover, muitas vezes chamado de "malha".

Em geral, o sistema de back-end não se comunica, de saída, com outros sistemas como uma precaução de segurança. Isso é reservado aos sistemas na camada front-end (gateway). As máquinas da camada do gateway têm privilégios limitados nos computadores back-end para minimizar a superfície de ataque como um mecanismo de defesa avançada.

### <a name="isolation-by-machine-function-and-access"></a>Isolamento por função e acesso do computador
O SQL Azure é composto por serviços em execução nas funções de máquina diferentes. O SQL Azure é dividido em Banco de dados de nuvem "back-end" e ambientes "front-end" (Gateway/Gerenciamento), com o princípio geral de tráfego somente entrando no back-end e não saindo. O ambiente front-end pode se comunicar com o mundo exterior de outros serviços e, em geral, tem apenas permissões limitadas no back-end (suficiente para chamar os pontos de entrada que ele precisa chamar).

## <a name="networking-isolation"></a>Isolamento de rede
A implantação do Azure têm vários níveis de isolamento de rede. O diagrama a seguir mostra várias camadas de isolamento de rede que o Azure fornece aos clientes. Essas camadas são tanto nativas na plataforma Azure em si quanto recursos definidos pelo cliente. Proveniente da Internet, o DDoS do Azure fornece isolamento contra ataques em grande escala contra o Azure. A próxima camada de isolamento é de endereços IP públicos definidos pelo cliente (pontos de extremidade), que são usados para determinar qual tráfego pode passar pelo serviço de nuvem para a rede virtual. O isolamento de rede virtual Nativa do Azure garante o isolamento completo de todas as outras redes e garante que o tráfego flua somente através de métodos e caminhos configurados pelo usuário. Esses caminhos e métodos são a próxima camada em que NSGs, UDR e soluções de virtualização de rede podem ser usados para criar limites de isolamento para proteger as implantações de aplicativo na rede protegida.

![Isolamento de rede](./media/azure-isolation/azure-isolation-fig13.png)

**Isolamento de tráfego:** uma [rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) é o limite de isolamento de tráfego na plataforma Azure. As VMs (máquinas virtuais) em uma rede virtual não podem comunicar-se diretamente com VMs em uma rede virtual diferente, mesmo que ambas as redes virtuais sejam criadas pelo mesmo cliente. Isolamento é uma propriedade vital que garante que as VMs e as comunicações do cliente permaneçam privadas em uma rede virtual.

[Subrede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) oferece uma camada adicional de isolamento na rede virtual com base no intervalo de IPs. Os endereços IP na rede virtual. Você pode dividir uma rede virtual em várias sub-redes para organização e segurança. As VMs e as instâncias de função de PaaS implantadas em sub-redes (iguais ou diferentes) em uma Rede Virtual podem se comunicar entre si sem nenhuma configuração adicional. Você também pode configurar os [NSGs (Grupos de segurança de rede)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) para permitir ou negar o tráfego de rede para uma instância de VM com base em regras configuradas na lista de controle de acesso (ACL) do NSG. Os NSGs podem ser associados a sub-redes ou instâncias de VM individuais dentro dessa sub-rede. Quando um NSG é associado a uma sub-rede, as regras de ACL se aplicam a todas as instâncias de VM na sub-rede.

## <a name="next-steps"></a>Próximas etapas

- [Opções de isolamento de rede para computadores em redes virtuais do Windows Azure](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/)

Isso inclui o cenário clássico de front-end e back-end no qual as máquinas em uma rede, ou sub-rede, de back-end específica podem permitir que apenas determinados clientes ou outros computadores se conectem a um determinado ponto de extremidade com base em uma lista de permissão de endereços IP.

- [Isolamento de computação](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf)

O Microsoft Azure fornece vários serviços de computação baseados em nuvem que incluem uma ampla seleção de instâncias e serviços de computação, os quais podem ser escalados verticalmente de forma automática para atender às necessidades de seu aplicativo ou empresa.

- [Isolamento de armazenamento](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf)

O Microsoft Azure separa a computação baseada em VM do cliente do armazenamento. Essa separação permite que a computação e o armazenamento sejam escalados de maneira independente, facilitando o fornecimento do isolamento e da multilocação. Portanto, o Armazenamento do Azure é executado em um hardware separado sem conectividade de rede com a Computação do Azure, exceto logicamente. Todas as solicitações são executadas via HTTP ou HTTPS com base na escolha do cliente.

