---
title: Conceitos de floresta de recursos do Azure AD Domain Services | Microsoft Docs
description: Saiba o que é uma floresta de recursos no Azure Active Directory Domain Services e como elas beneficiam a sua organização em um ambiente híbrido com opções de autenticação de usuário limitadas ou preocupações de segurança.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 79cf408bcb9060c247b97e6a81204c5a5517d384
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619994"
---
# <a name="resource-forest-concepts-and-features-for-azure-active-directory-domain-services"></a>Conceitos e recursos da floresta de recursos do Azure Active Directory Domain Services

O Azure Active Directory Domain Services (Azure AD DS) fornece uma experiência de entrada para aplicativos de linha de negócios herdados e locais. Os usuários, grupos e hashes de senha de usuários locais e na nuvem são sincronizados com o domínio gerenciado do Azure AD DS. Esses hashes de senha sincronizados são o que fornece aos usuários um único conjunto de credenciais que eles podem usar para o AD DS local, Microsoft 365 e Azure Active Directory.

Embora seja seguro e forneça benefícios de segurança adicionais, algumas organizações não podem sincronizar os hashes de senhas de usuário para o Azure AD ou o Azure AD DS. Os usuários em uma organização podem não saber senha deles porque usam apenas a autenticação de cartão inteligente. Essas limitações impedem que algumas organizações usem o Azure AD DS para realizar lift-and-shift em aplicativos clássicos locais para o Azure.

Para atender a essas necessidades e restrições, você pode criar um domínio gerenciado que usa uma floresta de recursos. Este artigo conceitual explica o que são as florestas e como elas confiam em outros recursos para fornecer um método de autenticação seguro.

## <a name="what-are-forests"></a>O que são florestas?

Uma *floresta* é um constructo lógico usado pelo AD DS (Active Directory Domain Services) para agrupar um ou mais *domínios*. Em seguida, os domínios armazenam objetos para usuários ou grupos e fornecem serviços de autenticação.

Em um domínio gerenciado AD DS do Azure, a floresta contém apenas um domínio. As florestas do AD DS locais geralmente contêm muitos domínios. Em grandes organizações, especialmente depois de fusões e aquisições, você poderá terminar com várias florestas locais que contêm vários domínios.

Por padrão, um domínio gerenciado é criado como uma floresta de *usuário* . Esse tipo de floresta sincroniza todos os objetos do Azure AD, incluindo qualquer conta de usuário criada em um ambiente do AD DS local. As contas de usuário podem se autenticar diretamente no domínio gerenciado, como para entrar em uma VM ingressada no domínio. Uma floresta de usuário funciona quando os hashes de senha podem ser sincronizados e os usuários não estão usando métodos de conexão exclusivos, como a autenticação de cartão inteligente.

Em uma floresta de *recursos* de domínio gerenciado, os usuários se autenticam em uma *relação de confiança* de floresta unidirecional de suas AD DS locais. Com essa abordagem, os objetos de usuário e os hashes de senha não são sincronizados com o domínio gerenciado. Os objetos de usuário e as credenciais existem somente no AD DS local. Essa abordagem permite que as empresas hospedem recursos e plataformas de aplicativo no Azure que dependem da autenticação clássica, tais como LDAPS, Kerberos ou NTLM, mas todas as preocupações ou problemas de autenticação são removidos.

As florestas de recursos também fornecem a capacidade de realizar lift-and-shift nos seus aplicativos, um componente por vez. Muitos aplicativos locais herdados têm vários níveis, geralmente usando um servidor Web ou front-end e muitos componentes relacionados ao banco de dados. Esses níveis dificultam a realização de lift-and-shift do aplicativo inteiro para a nuvem em uma etapa. Com as florestas de recursos, você pode elevar o seu aplicativo na nuvem em uma abordagem em fases, que facilita mover o seu aplicativo para o Azure.

## <a name="what-are-trusts"></a>O que são relações de confiança?

As organizações que têm mais de um domínio geralmente precisam que os usuários acessem recursos compartilhados em um domínio diferente. O acesso a esses recursos compartilhados requer que os usuários em um domínio sejam autenticados para outro domínio. Para fornecer essas funcionalidades de autenticação e autorização entre clientes e servidores em domínios diferentes, precisa haver uma *relação de confiança* entre os dois domínios.

Com as relações de confiança do domínio, os mecanismos de autenticação de cada domínio confiam nas autenticações provenientes do outro domínio. As relações de confiança ajudam a fornecer acesso controlado a recursos compartilhados em um domínio de recursos (o domínio *confiante*), verificando se as solicitações de autenticação recebidas são provenientes de uma autoridade confiável (o domínio *confiável*). As relações de confiança atuam como pontes que permitem que somente solicitações de autenticação validadas transitem entre domínios.

A maneira como uma relação de confiança passa solicitações de autenticação depende de como ela está configurada. As relações de confiança podem ser configuradas de uma das seguintes maneiras:

* **Unidirecional** – Fornece acesso do domínio confiável aos recursos no domínio confiante.
* **Bidirecional** – Fornece acesso de cada domínio aos recursos no outro domínio.

As relações de confiança também são configuradas para lidar com relações de confiança adicionais de uma das seguintes maneiras:

* **Não transitiva** – A relação de confiança existe somente entre os dois domínios de parceiro de confiança.
* **Transitiva** – A relação de confiança se estende automaticamente para todos os outros domínios nos quais qualquer um dos parceiros confia.

Em alguns casos, as relações de confiança são automaticamente estabelecidas quando os domínios são criados. Em outras ocasiões, você precisa escolher um tipo de relação de confiança e estabelecer explicitamente as relações apropriadas. Os tipos específicos de confianças usados e a estrutura dessas relações de confiança dependem de como o diretório de AD DS é organizado e se diferentes versões do Windows coexistem na rede.

## <a name="trusts-between-two-forests"></a>Relações de confiança entre duas florestas

Você pode estender as relações de confiança de domínio em uma única floresta para outra floresta criando manualmente uma relação de confiança da floresta unidirecional ou bidirecional. Uma relação de confiança da floresta é transitiva e existe apenas entre dois domínios raiz de floresta.

* Uma relação de confiança da floresta unidirecional permite que todos os usuários em uma floresta confiem em todos os domínios da outra floresta.
* Uma relação de confiança da floresta bidirecional forma uma relação de confiança transitiva entre cada domínio em ambas as florestas.

A transitividade das relações de confiança da floresta é limitada aos dois parceiros de floresta. A relação de confiança da floresta não se estende a florestas adicionais que qualquer um dos parceiros confia.

![Diagrama da relação de confiança de floresta do Azure AD DS com o AD DS local](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

Você pode criar diferentes configurações de confiança de domínio e floresta dependendo da estrutura de AD DS da organização. O Azure AD DS dá suporte apenas a uma relação de confiança da floresta unidirecional. Nessa configuração, os recursos no domínio gerenciado podem confiar em todos os domínios em uma floresta local.

## <a name="supporting-technology-for-trusts"></a>Tecnologia de suporte para relações de confiança

As relações de confiança usam vários serviços e recursos, como o DNS, para localizar controladores de domínio em florestas parceiras. As relações de confiança também dependem de protocolos de autenticação NTLM e Kerberos e de autorização baseada em Windows e mecanismos de controle de acesso para ajudar a fornecer uma infraestrutura de comunicações segura entre AD DS domínios e florestas. Os seguintes serviços e recursos ajudam a dar suporte a relações de confiança bem-sucedidas.

### <a name="dns"></a>DNS

O AD DS precisa de DNS para nomenclatura e localização do DC (controlador de domínios). O suporte a seguir do DNS é fornecido para que o AD DS funcione com êxito:

* Um serviço de resolução de nomes que permite que os hosts de rede e os serviços localizem os DCs.
* Uma estrutura de nomenclatura que permite que uma empresa reflita a estrutura organizacional dela nos nomes dos domínios de serviço de diretório.

Um namespace de domínio DNS geralmente é implantado de modo que ele espelhe o namespace de domínio do AD DS. Se houver um namespace DNS existente antes da implantação de AD DS, o namespace DNS será normalmente particionado para AD DS, e um subdomínio e delegação de DNS para a raiz da floresta AD DS será criado. Nomes de domínio DNS adicionais são então adicionados para cada AD DS domínio filho.

O DNS também é usado para dar suporte ao local de DCs do AD DS. As zonas DNS são preenchidas com registros de recursos DNS que permitem que os serviços e hosts de rede localizem AD DS DCs.

### <a name="applications-and-net-logon"></a>Aplicativos e Logon de Rede

Os aplicativos e o serviço Logon de Rede são componentes do modelo de canal de segurança distribuída do Windows. Os aplicativos integrados ao Windows Server e AD DS usam protocolos de autenticação para se comunicarem com o serviço de logon de rede para que um caminho protegido possa ser estabelecido em relação à qual a autenticação pode ocorrer.

### <a name="authentication-protocols"></a>Protocolos de autenticação

AD DS DCs autenticam usuários e aplicativos usando um dos seguintes protocolos:

* **Protocolo de autenticação Kerberos versão 5**
    * O protocolo Kerberos versão 5 é o protocolo de autenticação padrão usado por computadores locais que executam o Windows e dão suporte a sistemas operacionais de terceiros. Esse protocolo é especificado no RFC 1510 e é totalmente integrado com AD DS, protocolo SMB, HTTP e RPC (chamada de procedimento remoto), bem como os aplicativos cliente e servidor que usam esses protocolos.
    * Quando o protocolo Kerberos é usado, o servidor não precisa entrar em contato com o DC. Em vez disso, o cliente recebe um tíquete de um servidor solicitando-o de um DC no domínio da conta do servidor. Em seguida, o servidor valida o tíquete sem consultar nenhuma outra autoridade.
    * Se um computador envolvido em uma transação não der suporte ao protocolo Kerberos versão 5, o protocolo NTLM será usado.

* **Protocolo de autenticação NTLM**
    * O protocolo NTLM é um protocolo de autenticação de rede clássico usado por sistemas operacionais mais antigos. Por motivos de compatibilidade, ele é usado por AD DS domínios para processar solicitações de autenticação de rede provenientes de aplicativos criados para clientes e servidores baseados no Windows anteriores e sistemas operacionais de terceiros.
    * Quando o protocolo NTLM é usado entre um cliente e um servidor, o servidor precisa contatar um serviço de autenticação de domínio em um DC para verificar as credenciais do cliente. O servidor autentica o cliente encaminhando as credenciais do cliente para um DC no domínio da conta do cliente.
    * Quando dois domínios AD DS ou florestas são conectados por uma relação de confiança, as solicitações de autenticação feitas usando esses protocolos podem ser roteadas para fornecer acesso aos recursos em ambas as florestas.

## <a name="authorization-and-access-control"></a>Autenticação e controle de acessos

As tecnologias de autorização e confiança funcionam em conjunto para fornecer uma infraestrutura de comunicações protegida entre AD DS domínios ou florestas. A autorização determina qual é o nível de acesso do usuário aos recursos em um domínio. As relações de confiança facilitam a autorização entre domínios de usuários fornecendo um caminho para autenticar usuários em outros domínios para que as solicitações deles para recursos compartilhados nesses domínios possam ser autorizadas.

Quando uma solicitação de autenticação feita em um domínio confiante é validada pelo domínio confiável, ela é passada para o recurso de destino. Em seguida, o recurso de destino determina se deve autorizar a solicitação específica feita pelo usuário, serviço ou computador no domínio confiável com base na configuração de controle de acesso.

As relações de confiança fornecem esse mecanismo para validar solicitações de autenticação que são passadas para um domínio confiante. Os mecanismos de controle de acesso no computador de recursos determinam o nível final de acesso permitido ao solicitante no domínio confiável.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre relações de confiança, consulte [Como as relações de confiança da floresta funcionam no Azure AD DS?][concepts-trust]

Para começar a criar um domínio gerenciado com uma floresta de recursos, consulte [criar e configurar um domínio gerenciado do Azure AD DS][tutorial-create-advanced]. Em seguida, você pode [criar uma relação de confiança de floresta de saída para um domínio local][create-forest-trust].

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
