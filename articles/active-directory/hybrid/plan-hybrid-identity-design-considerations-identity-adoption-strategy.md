---
title: Design de identidade híbrida - estratégia de adoção do Azure | Microsoft Docs
description: Com o controle de acesso condicional, o Azure AD verifica as condições específicas que você escolhe ao autenticar o usuário e antes de permitir o acesso ao aplicativo.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: b92fa5a9-c04c-4692-b495-ff64d023792c
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f52e46ff9cab7d3d150af9fd7b4f1c432bec74b
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836182"
---
# <a name="define-a-hybrid-identity-adoption-strategy"></a>Definir uma estratégia de adoção de identidade híbrida
Nesta tarefa, você define uma estratégia de adoção para sua solução de identidade híbrida para atender aos requisitos de negócios que abordamos nos tópicos:

* [Determinar as necessidades de negócios](plan-hybrid-identity-design-considerations-business-needs.md)
* [Determinar os requisitos de sincronização de diretório](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)
* [Determinar os requisitos de autenticação multifator](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>Definir uma estratégia para as necessidades de negócios
A primeira tarefa aborda como determinar as necessidades de negócios das organizações.  Isso é muito amplo e pode não atender aos objetivos, se você não atuar com precisão.  Mantenha a simplicidade no início, mas lembre-se sempre de planejar um design adequado para facilitar e ajustar as alterações no futuro.  Independentemente de ser um design simples ou extremamente complexo, Azure Active Directory é a plataforma de identidade da Microsoft que dá suporte a Microsoft 365, Microsoft Online Services e aplicativos com reconhecimento de nuvem.

## <a name="define-an-integration-strategy"></a>Definir uma estratégia de integração
A Microsoft tem três cenários básicos de integração: as identidades de nuvem, as identidades sincronizadas e as identidades federadas.  Planeje a adoção de uma dessas estratégias de integração.  A escolha da estratégia pode variar e as decisões na escolha envolvem: determinar o tipo de experiência de usuário que você pretende fornecer, saber se você já tem uma infraestrutura existente no local e determinar qual é a mais eficiente.  

![cenários de integração](./media/plan-hybrid-identity-design-considerations/integration-scenarios.png)

Os cenários descritos acima são:

* **Identidades de nuvem**: são identidades existentes apenas na nuvem.  No caso do AD do Azure, elas são armazenadas especialmente nesse serviço.
* **Identidades sincronizadas**: são identidades existentes no local e na nuvem.  Com o Azure AD Connect, os usuários são criados ou associados a contas existentes do AD do Azure.  A senha do usuário é sincronizada do ambiente local para a nuvem, ao que chamamos de hash de senha.  A única ressalva para o uso da identidade sincronizada é que, quando um usuário estiver desabilitado no ambiente local, poderá demorar até três horas para que o status da conta seja exibido no Azure Active Directory.  Isso se deve ao intervalo de tempo de sincronização.
* **Identidades federadas**: são identidades existentes no local e na nuvem.  Com o Azure AD Connect, os usuários são criados ou associados a contas existentes do AD do Azure.  

> [!NOTE]
> Para obter mais informações sobre as opções de sincronização, leia [integrando suas identidades locais com o Azure Active Directory](whatis-hybrid-identity.md).
> 
> 

A tabela a seguir ajuda a determinar as vantagens e desvantagens de cada uma das estratégias a seguir:

| Estratégia | Vantagens | Desvantagens |
| --- | --- | --- |
| **Identidades de nuvem** |Mais fácil de gerenciar para as organizações de pequeno porte. <br> Nada a instalar localmente. Nenhum hardware adicional necessário<br>Desativado facilmente se o usuário deixar a empresa |Os usuários deverão se conectar ao acessar cargas de trabalho na nuvem <br> As senhas podem ser as mesmas para as identidades locais ou de nuvem |
| **Sincronizado** |A senha local autentica ambos os diretórios locais e na nuvem <br>Mais fácil de gerenciar para organizações de pequeno, médio ou grande porte <br>Os usuários podem usar SSO (Logon único) para alguns recursos <br> Método preferido da Microsoft para sincronização <br> Mais fácil de gerenciar |Alguns clientes podem ser relutados para sincronizar seus diretórios com a nuvem devido às políticas específicas da empresa |
| **Federado** |Os usuários podem fazer SSO  <br>Se um usuário for encerrado ou sair, a conta poderá ser imediatamente desabilitada e o acesso revogado,<br> Com suporte para cenários avançados que não podem ser realizados com sincronização |Mais etapas para definir e configurar <br> Maior manutenção <br> Pode exigir hardware adicional para a infra-estrutura do STS <br> Pode exigir hardware adicional para instalar o servidor de federação. Um software adicional será necessário se o AD FS for utilizado <br> Requer configuração ampla para SSO <br> Ponto de falha crítico se o servidor de federação estiver desativado, os usuários não conseguirão autenticar |

### <a name="client-experience"></a>Experiência do cliente
A estratégia adotada define a experiência de entrada do usuário.  A tabela a seguir dá informações sobre a experiência de entrada do usuário.  Nem todos os provedores de identidade federados oferecem suporte a SSO em todos os cenários.

**Aplicativos de rede privada ou ingressados no domínio**:

| Aplicativo | Identidade sincronizada | Identidade federada |
| --- | --- | --- |
| Navegadores da Web |Autenticação baseada em formulários |Às vezes requer logon único para fornecer a ID da organização |
| Outlook |Solicita credenciais |Solicita credenciais |
| Skype for Business (Lync) |Solicita credenciais |logon único para o Lync, solicitação de credenciais para o Exchange |
| OneDrive for Business |Solicita credenciais |logon único |
| Assinatura do Office Pro Plus |Solicita credenciais |logon único |

**Fontes externas ou não confiáveis**:

| Aplicativo | Identidade sincronizada | Identidade federada |
| --- | --- | --- |
| Navegadores da Web |Autenticação baseada em formulários |Autenticação baseada em formulários |
| Outlook, Skype for Business (Lync), OneDrive for Business, assinatura do Office |Solicita credenciais |Solicita credenciais |
| Exchange ActiveSync |Solicita credenciais |logon único para o Lync, solicitação de credenciais para o Exchange |
| Aplicativos móveis |Solicita credenciais |Solicita credenciais |

Se você determinou a partir da tarefa 1 que tem um IdP de terceiro ou que utilizará um para fornecer federação com o Azure Active Directory, você precisa estar ciente dos seguintes recursos com suporte:

* Qualquer provedor SAML 2.0 que seja compatível com o perfil SP-Lite pode dar suporte à autenticação no Azure Active Directory e nos aplicativos associados
* Com suporte de autenticação passiva, o que facilita a autenticação para OWA, SPO, etc.
* Clientes do Exchange Online podem ser suportados por meio do ECP (perfil de cliente aprimorado) do SAML 2.0

Conheça também os recursos que não estão disponíveis:

* Sem o suporte a WS-Trust/Federação, todos os outros clientes ativos ficam inoperantes
  * Isso se aplica aos clientes do Lync, do OneDrive, às assinaturas do Office e do Office Mobile anteriores ao Office 2016
* A transição do Office para o processo de autenticação passiva lhes permitem fornecer suporte exclusivamente a provedores de identidade SAML 2.0, mas o suporte é fornecido individualmente aos clientes

> [!NOTE]
> Para obter a lista mais atualizada, leia o artigo [Lista de compatibilidade de federação do Azure AD](how-to-connect-fed-compatibility.md).
> 
> 

## <a name="define-synchronization-strategy"></a>Definir uma estratégia de sincronização
Nesta tarefa, você vai definir a topologia e as ferramentas que vai usar para sincronizar os dados locais da organização na nuvem.  Fornecemos informações detalhadas sobre o uso do Azure AD Connect para resolver as questões abordadas acima, uma vez que a maioria das organizações usa o Active Directory.  Para os ambientes que não usam o Active Directory, temos informações sobre o uso do FIM 2010 R2 e do MIM 2016 para auxiliar no planejamento dessa estratégia.  As versões futuras do Azure AD Connect, no entanto, serão compatíveis com os diretórios LDAP, de modo que essas informações podem ser úteis, dependendo da sua linha do tempo.

### <a name="synchronization-tools"></a>Ferramentas de sincronização
Usamos diversas ferramentas de sincronização para vários cenários ao longo dos anos.  Atualmente, o Azure AD Connect é a ferramenta de eleição para todos os cenários compatíveis.  O AAD Sync e o DirSync continuam disponíveis e podem fazer parte do seu ambiente imediatamente. 

> [!NOTE]
> Para obter informações recentes sobre os recursos com suporte de cada ferramenta, leia o artigo [Comparação de ferramentas de integração de diretório](plan-hybrid-identity-design-considerations-tools-comparison.md) .  
> 
> 

### <a name="supported-topologies"></a>Topologias com suporte
Escolha a topologia que vai usar quando definir a estratégia de sincronização. Dependendo das informações indicadas na etapa 2, determine a topologia de utilização mais adequada. A floresta única, topologia exclusiva do AD do Azure, é a mais comum e consiste de uma floresta única do Active Directory e de uma instância única do AD do Azure.  Ela será usada na maioria dos cenários, além de ser a topologia esperada para o uso da instalação expressa do Azure AD Connect Express, como ilustrado na imagem a seguir.

![](./media/plan-hybrid-identity-design-considerations/single-forest.png)Cenário de floresta única de topologias com suporte é comum que organizações grandes e até mesmo pequenas tenham várias florestas, como mostra a Figura 5.

> [!NOTE]
> Para saber mais sobre as diversas topologias locais e as topologias do AD do Azure com serviço de sincronização do Azure AD Connect, leia o artigo [Topologias do Azure AD Connect](plan-connect-topologies.md).
> 
> 

![topologia de várias florestas](./media/plan-hybrid-identity-design-considerations/multi-forest.png) 

Cenário de topologia de várias florestas

Se esse for o caso, considere a topologia de várias florestas do Azure AD, caso os itens a seguir sejam atendidos:

* Os usuários têm apenas 1 identidade para todas as florestas. A seção abaixo, exclusiva para identificação de usuários, descreve isso com mais detalhes.
* O usuário se autentica na floresta em que sua identidade está localizada.
* O UPN e a âncora de origem (ID imutável) serão gerados nessa floresta.
* Todas as florestas são acessíveis pelo Azure AD Connect. Isso significa que o serviço não precisa ser ingressado no domínio e pode ser colocado em um DMZ, se isso facilitar.
* Os usuários têm apenas uma caixa de correio.
* A floresta que hospeda a caixa de correio do usuário tem a melhor qualidade de dados para os atributos visíveis na Lista de Endereços Global (GAL) do Exchange.
* Quando não há nenhuma caixa de correio do usuário, qualquer floresta pode ser usada para contribuir com esses valores
* Caso tenha uma caixa de correio vinculada, haverá outra conta em outra floresta usada para iniciar sessão.

> [!NOTE]
> Os objetos existentes no local e na nuvem são "conectados" através de um identificador exclusivo. No contexto de sincronização de diretório, este identificador exclusivo é conhecido como SourceAnchor. No contexto de logon único, ele é conhecido como ImmutableId. [Conceitos de design do Azure AD Connect](plan-connect-design-concepts.md#sourceanchor) .
> 
> 

Caso a situação descrita anteriormente não se aplique e se você tiver mais de uma conta ativa ou mais de uma caixa de correio, o Azure AD Connect vai escolher uma delas e ignorar as outras.  Se você tiver vinculado caixas de correio, mas não vincular nenhuma outra conta, essas contas não serão exportadas para o AD do Azure e esse usuário não será membro de nenhum grupo.  Com esse processo, diferente da atuação anterior com o DirSync, temos um melhor suporte para os cenários de várias florestas. Confira um cenário de várias florestas na imagem abaixo.

![vários locatários do Azure AD](./media/plan-hybrid-identity-design-considerations/multiforest-multipleAzureAD.png) 

**Cenário de várias florestas do AD do Azure**

Recomendamos ter apenas um único diretório no AD do Azure de uma organização, embora haja suporte para uma relação de um para um entre um servidor de sincronização do Azure AD Connect e um diretório do AD do Azure.  Para cada instância do Azure Active Directory, você precisará de uma instalação do Azure AD Connect.  Além disso, o AD do Azure é isolado pelo design e os usuários de uma instância do AD do Azure não podem ver os usuários de outra instância.

Temos suporte e você pode se conectar a uma instância local do Active Directory para vários diretórios do AD do Azure, conforme mostrado na figura abaixo:

![filtragem de floresta única](./media/plan-hybrid-identity-design-considerations/single-forest-flitering.png) 

**Cenário de filtragem de floresta única**

Para realizar esse processo, as seguintes situações devem ser atendidas:

* Os servidores de sincronização do Azure AD Connect devem ser configurados para filtragem para que cada um deles tenha um conjunto de objetos mutuamente exclusivos.  Para fazer isso, por exemplo, defina o escopo de cada servidor para um determinado domínio ou UO.
* Um domínio DNS só pode ser registrado em um único diretório do AD do Azure para que os UPNs dos usuários no AD local tenham de usar namespaces separados
* Os usuários de uma determinada instância do AD do Azure só podem ver os usuários de sua própria instância.  Eles não poderão ver os usuários de outras instâncias
* Apenas um dos diretórios do AD do Azure pode habilitar o Exchange híbrido com o AD local
* A exclusividade mútua também se aplica a write-back.  Isso faz com que alguns recursos de write-back não sejam compatíveis com esta topologia, pois eles supõem a existência de uma única configuração local.  Isso inclui:
  * Agrupar write-back com configuração padrão
  * Write-back de dispositivo

Os itens a seguir não têm suporte e não devem ser escolhidos como implementação:

* Não há suporte para a conexão de vários servidores de sincronização do Azure AD Connect ao mesmo diretório do AD do Azure, mesmo que eles sejam configurados para sincronizar conjuntos de objetos mutuamente exclusivos.
* Ele não tem suporte para sincronização do mesmo usuário para vários diretórios do AD do Azure. 
* Ele também não dá suporte para alterar uma configuração para fazer os usuários de um AD do Azure aparecerem como contatos em outro diretório do AD do Azure. 
* Também não há suporte para modificar o Azure AD Connect Sync para se conectar a vários diretórios do AD do Azure.
* Diretórios do AD do Azure são isolados por padrão. Não há suporte para alterações da configuração do Azure AD Connect Sync para ler dados de outro diretório do AD do Azure como uma tentativa de criar uma GAL comum e unificada entre os diretórios. Também não há suporte para a exportação de usuários como contatos para outro AD local usando o Azure AD Connect Sync.

> [!NOTE]
> Se a sua organização restringe os computadores da rede a se conectarem à Internet, este artigo listará os pontos de extremidade (intervalos de endereços FQDNs, IPv4 e IPv6) que você deve incluir nas listas de permissões de saída e na zona de sites confiáveis do Internet Explorer de computadores cliente para garantir que seus computadores possam usar o Microsoft 365 com êxito. Para saber mais, leia o artigo [Intervalos de endereços IP e URLs do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US).
> 
> 

## <a name="define-multi-factor-authentication-strategy"></a>Definir uma estratégia de autenticação multifator
Nesta tarefa, você define a estratégia de autenticação multifator que vai usar.  A autenticação multifator do Azure AD vem em duas versões diferentes.  Uma delas é baseada na nuvem e a outra é local usando o servidor Azure MFA.  Com base na avaliação que você fez anteriormente, determine a solução mais adequada para sua estratégia.  Use a tabela abaixo para determinar a opção de design que melhor atende aos requisitos de segurança da empresa:

Opções de design de vários fatores:

| Ativo a ser protegido | MFA na nuvem | MFA local |
| --- | --- | --- |
| Aplicativos da Microsoft |sim |sim |
| Aplicativos SaaS da Galeria de Aplicativos |sim |sim |
| Aplicativos IIS publicados por meio da Proxy de aplicativo do Azure AD |sim |sim |
| Aplicativos do IIS não publicados através do Proxy de Aplicativo do AD do Azure |no |sim |
| Acesso remoto, como VPN e RDG |no |sim |

Mesmo que defina uma solução para sua estratégia, você deve usar a avaliação anterior sobre a localização dos usuários.  Isso pode levar a uma mudança de solução.  Use a tabela abaixo para lhe ajudar a determinar os seguintes itens:

| Local do usuário | Opção de design preferida |
| --- | --- |
| Azure Active Directory |Multi-FactorAuthentication na nuvem |
| Azure AD e AD local usando federação com AD FS |Ambos |
| AD do Azure e AD local usando o Azure AD Connect sem sincronização de senha |Ambos |
| O AD do Azure e o AD local usando o Azure AD Connect com sincronização de senha |Ambos |
| AD local |Servidor de Autenticação Multifator |

> [!NOTE]
> Você deve garantir que a opção de design de autenticação multifator selecionada seja compatível com os recursos necessários para o seu design.  Para saber mais, leia [Escolha a solução de segurança multifator mais adequada](../authentication/concept-mfa-howitworks.md).
> 

## <a name="multi-factor-auth-provider"></a>Provedor de Multi-Factor Authentication
A autenticação multifator está disponível por padrão para administradores globais que tenham um locatário do Azure Active Directory. No entanto, se desejar estender este recurso para todos os usuários ou se pretende permitir que os administradores globais aproveitem os recursos, como o portal de gerenciamento, saudações personalizadas e relatórios, você deve comprar e configurar o provedor de Autenticação Multifator.

> [!NOTE]
> Você deve garantir que a opção de design de autenticação multifator selecionada seja compatível com os recursos necessários para o seu design. 
> 
> 

## <a name="next-steps"></a>Próximas etapas
[Determinar os requisitos para proteção de dados](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>Confira também
[Visão geral sobre as considerações de design](plan-hybrid-identity-design-considerations-overview.md)

