---
title: 'Azure AD Connect: Topologias com suporte | Microsoft Docs'
description: Este tópico detalha as topologias com e sem suporte para o Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 1034c000-59f2-4fc8-8137-2416fa5e4bfe
ms.service: active-directory
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.topic: conceptual
ms.date: 11/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d3f8e9441064a5d2d1372e3f177534b8dfefb93
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92359825"
---
# <a name="topologies-for-azure-ad-connect"></a>Topologias para o Azure AD Connect
Este artigo descreve várias topologias locais e do Azure Active Directory (Azure AD) que usam a sincronização do Azure AD Connect como solução de integração principal. Este artigo inclui configurações com e sem suporte.


Aqui está a legenda para imagens neste artigo:

| Descrição | Símbolo |
| --- | --- |
| Floresta do Active Directory local |![Floresta do Active Directory local](./media/plan-connect-topologies/legendad1.png) |
| Active Directory local com importação filtrada |![Active Directory com importação filtrada](./media/plan-connect-topologies/legendad2.png) |
| Servidor do Azure AD Connect Sync |![Servidor do Azure AD Connect Sync](./media/plan-connect-topologies/legendsync1.png) |
| “Modo de preparo” do servidor de sincronização do Azure AD Connect |![“Modo de preparo” do servidor de sincronização do Azure AD Connect](./media/plan-connect-topologies/legendsync2.png) |
| GALSync com o Forefront Identity Manager (FIM) 2010 ou o Microsoft Identity Manager (MIM) 2016 |![GALSync com o FIM 2010 ou MIM 2016](./media/plan-connect-topologies/legendsync3.png) |
| Servidor do Azure AD Connect Sync detalhado |![Servidor do Azure AD Connect Sync detalhado](./media/plan-connect-topologies/legendsync4.png) |
| Azure AD |![Azure Active Directory](./media/plan-connect-topologies/legendaad.png) |
| Cenário sem suporte |![Cenário sem suporte](./media/plan-connect-topologies/legendunsupported.png) |


> [!IMPORTANT]
> A Microsoft não oferece suporte à modificação ou à operação da sincronização do Azure AD Connect fora das configurações ou ações formalmente documentadas. Qualquer uma dessas configurações ou ações pode resultar em um estado inconsistente ou sem suporte de Azure AD Connect sincronização. Como resultado, a Microsoft não pode fornecer suporte técnico para essas implantações.


## <a name="single-forest-single-azure-ad-tenant"></a>Floresta única, locatário único do Azure AD
![Topologia de uma única floresta e um único locatário](./media/plan-connect-topologies/singleforestsingledirectory.png)

A topologia mais comum é uma floresta única local, com um ou vários domínios e um único locatário do Azure AD. Para a autenticação do Azure AD, é usada a sincronização de hash de senha. A instalação expressa do Azure AD Connect dá suporte apenas a esta topologia.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Uma única floresta, vários servidores de sincronização para um locatário do Azure AD
![Topologia sem suporte, filtrada para uma floresta única](./media/plan-connect-topologies/singleforestfilteredunsupported.png)

Não há suporte para vários servidores de sincronização do Azure AD Connect conectados ao mesmo locatário do Azure AD, exceto para um [servidor de preparo](#staging-server). Não haverá suporte mesmo se os servidores estiverem configurados para sincronizar com um conjunto de objetos mutuamente exclusivo. Talvez você tenha considerado esta topologia caso não possa acessar todos os domínios na floresta de um único servidor, ou se quiser distribuir a carga entre vários servidores.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Várias florestas, locatário único do Azure AD
![Topologia de várias florestas e um único locatário](./media/plan-connect-topologies/multiforestsingledirectory.png)

Muitas organizações têm ambientes com várias florestas do Active Directory local. Há vários motivos para ter mais de uma floresta do Active Directory local. Exemplos típicos são designs com florestas de conta-recurso e o resultado de uma fusão ou aquisição.

Quando você tem várias florestas, todas elas devem ser acessíveis por um único servidor de sincronização do Azure AD Connect. O servidor deve ser unido a um domínio. Se necessário, para acessar todas as florestas, você pode colocar o servidor em uma rede de perímetro (também conhecida como DMZ, zona desmilitarizada e sub-rede selecionada).

O assistente de instalação do Azure AD Connect oferece várias opções para consolidar os usuários representados em várias florestas. O objetivo é que um usuário seja representado somente uma vez no Azure AD. Há algumas topologias comuns que você pode configurar o caminho de instalação personalizada do assistente de instalação. Na página **Identificando exclusivamente seus usuários**, selecione a opção correspondente que representa sua topologia. A consolidação está configurada somente para os usuários. Grupos duplicados não são consolidados com a configuração padrão.

As topologias comuns são discutidas nas seções sobre topologias separadas, [malha completa](#multiple-forests-full-mesh-with-optional-galsync)e [a topologia de recurso de conta](#multiple-forests-account-resource-forest).

A configuração padrão do Azure AD Connect sync supõe:

* Os usuários têm apenas uma conta habilitada e a floresta em que essa conta se encontra é usada para autenticar o usuário. Esse pressuposto é para sincronização de hash de senha, autenticação de passagem e federação. UserPrincipalName e sourceAnchor/immutableID vêm desta floresta.
* Cada usuário tem apenas uma caixa de correio.
* A floresta que hospeda a caixa de correio de um usuário tem a melhor qualidade de dados para atributos visíveis na GAL (Lista de Endereços Global) do Exchange. Se não houver nenhuma caixa de correio para o usuário, qualquer floresta pode ser usada para contribuir com esses valores de atributos.
* Caso tenha uma caixa de correio vinculada, há uma conta em outra floresta usada para entrada.

Se seu ambiente não corresponder a essas suposições, acontecerá o seguinte:

* Se você tiver mais de uma conta ativa ou mais de uma caixa de correio, o mecanismo de sincronização escolherá uma e ignorará as outras.
* Uma caixa de correio vinculada sem nenhuma outra conta ativa não é exportada para o Azure AD. A conta de usuário não é representada como um membro de nenhum grupo. Uma caixa de correio vinculada no DirSync é sempre representado como uma caixa de correio normal. Essa alteração é intencionalmente um comportamento diferente para dar melhor suporte a cenários de várias florestas.

Você pode encontrar mais detalhes em [Noções básicas sobre a configuração padrão](concept-azure-ad-connect-sync-default-configuration.md).

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Várias florestas, vários servidores de sincronização para um locatário do Azure AD
![Topologia sem suporte para vários servidores de sincronização e de várias florestas](./media/plan-connect-topologies/multiforestmultisyncunsupported.png)

Não há suporte para a conexão de mais de um servidor de sincronização do Azure AD Connect a um único locatário do Azure AD. A exceção é o uso de um [servidor de preparo](#staging-server).

Essa topologia difere da que está abaixo em que **vários servidores de sincronização** conectados a um único locatário do Azure Active Directory não são suportados.

### <a name="multiple-forests-single-sync-server-users-are-represented-in-only-one-directory"></a>Múltiplas florestas, servidor de sincronização única, os usuários são representados em apenas um diretório
![Opção para representar os usuários apenas uma vez em todos os diretórios](./media/plan-connect-topologies/multiforestusersonce.png)

![Representação de várias florestas e topologias separadas](./media/plan-connect-topologies/multiforestseparatetopologies.png)

Nesse ambiente, todas as florestas locais são tratadas como entidades separadas. Não há usuários presentes em outras florestas. Cada floresta tem sua própria organização do Exchange e não há nenhuma GALSync entre as florestas. Esta topologia deve ser a situação após uma fusão/aquisição ou em uma organização em que cada unidade de negócios opera de forma independente. Essas florestas serão da mesma empresa no Azure AD e aparecerão com uma GAL unificada. Na figura acima, cada objeto em cada floresta é representado uma vez no metaverso e agregado no locatário de destino do Azure AD.

### <a name="multiple-forests-match-users"></a>Várias florestas: usuários correspondentes
O que é comum a todos esses cenários é que esses grupos de distribuição e segurança podem conter uma mescla de usuários, contatos e FSPs (Entidades de Segurança Externas). As FSPs são usadas no AD DS (Active Directory Domain Services) para representar os membros de outras florestas em um grupo de segurança. Todas as FSPs serão resolvidas para o objeto real no Azure AD.

### <a name="multiple-forests-full-mesh-with-optional-galsync"></a>Várias florestas: malha completa com GALSync opcional
![Opção para usar o atributo de email para correspondência quando existem identidades de usuários em vários diretórios](./media/plan-connect-topologies/multiforestusersmail.png)

![Topologia de malha completa para várias florestas](./media/plan-connect-topologies/multiforestfullmesh.png)

Uma topologia de malha completa permite que os usuários e recursos estejam localizados em qualquer floresta. Normalmente, há relações de confiança bidirecionais entre as florestas.

Se o Exchange estiver presente em mais de uma floresta, poderá haver uma solução GALSync local (opcional). Cada usuário é representado como um contato em todas as outras florestas. GALSync geralmente é implementado por meio do FIM 2010 ou MIM 2016. O Azure AD Connect não pode ser usado para GALSync local.

Nesse cenário, os objetos de identidade são ingressados usando o atributo de email. Um usuário com uma caixa de correio em uma floresta é ingressado aos contatos em outras florestas.

### <a name="multiple-forests-account-resource-forest"></a>Várias florestas: floresta de recursos de conta
![Opção para usar os atributos ObjectSID e msExchMasterAccountSID para correspondência quando existem identidades em vários diretórios](./media/plan-connect-topologies/multiforestusersobjectsid.png)

![Topologia de floresta de conta-recurso para várias florestas](./media/plan-connect-topologies/multiforestaccountresource.png)

Em uma topologia de floresta de recursos de conta, você tem uma ou mais florestas de *conta* com contas de usuário ativas. Você também tem uma ou mais florestas de *recursos* com contas desabilitadas.

Nesse cenário, uma (ou mais) floresta de recursos confia em todas as florestas de conta. Essa floresta de recurso normalmente tem um esquema do Active Directory estendido com o Exchange e o Lync. Todos os serviços do Exchange e do Lync, bem como outros serviços compartilhados, estão localizados nessa floresta. Os usuários têm uma conta de usuário desabilitada nesta floresta e a caixa de correio está vinculada à floresta da conta.

## <a name="microsoft-365-and-topology-considerations"></a>Considerações sobre Microsoft 365 e topologia
Algumas cargas de trabalho Microsoft 365 têm determinadas restrições em topologias com suporte:

| Carga de trabalho | Restrições |
| --------- | --------- |
| Exchange Online | Para obter mais informações sobre topologias híbridas com suporte pelo Exchange Online, consulte [Implantações híbridas com várias florestas do Active Directory](/Exchange/hybrid-deployment/hybrid-with-multiple-forests). |
| Skype for Business | Ao usar várias florestas locais, somente a topologia de floresta conta-recurso terá suporte. Para obter mais informações, confira [Requisitos ambientais para Skype for Business Server 2015](/skypeforbusiness/plan-your-deployment/requirements-for-your-environment/environmental-requirements). |

Se você for uma organização maior, considere usar o recurso de [Microsoft 365 PreferredDataLocation](how-to-connect-sync-feature-preferreddatalocation.md) . Ele permite que você defina em qual região do datacenter estão localizados os recursos do usuário.

## <a name="staging-server"></a>Servidor de preparo
![Servidor de preparo em uma topologia](./media/plan-connect-topologies/multiforeststaging.png)

O Azure AD Connect dá suporte a um segundo servidor em *modo de preparo*. Um servidor nesse modo lê dados de todos os diretórios conectados, mas não grava nada em diretórios conectados. Ele usa o ciclo de sincronização normal e, portanto, tem uma cópia atualizada dos dados de identidade.

Em um desastre em que o servidor primário falha, você pode fazer failover para o servidor de preparo. Você pode fazer isso no Assistente do Azure AD Connect. Esse segundo servidor pode estar localizado em um datacenter diferente, pois nenhuma infraestrutura é compartilhada com o servidor primário. É necessário copiar manualmente qualquer alteração de configuração feita no servidor principal para o segundo servidor.

Você pode usar um servidor de preparo para testar uma nova configuração personalizada e o efeito sobre seus dados. Você pode visualizar as alterações e ajustar a configuração. Quando estiver satisfeito com a nova configuração, transforme o servidor de preparo no servidor ativo e defina o antigo servidor ativo para modo de preparo.

Esse método também pode ser usado para substituir o servidor de sincronização ativo. Prepare o novo servidor e configure-o no modo de preparo. Verifique se ele está em boas condições, desabilite o modo de preparo (tornando-o ativo) e desligue o servidor ativo no momento.

É possível ter mais de um servidor de preparo quando você quiser ter vários backups em datacenters diferentes.

## <a name="multiple-azure-ad-tenants"></a>Vários locatários do Azure AD
Recomendamos ter um único locatário no Azure AD para uma organização.
Antes de planejar usar vários locatários do AD do Azure, confira o artigo [Gerenciamento de unidades administrativas no Azure AD](../roles/administrative-units.md). Ele aborda cenários comuns em que você pode usar um único locatário.

![Topologia de várias florestas e vários locatários](./media/plan-connect-topologies/multiforestmultidirectory.png)

Há uma relação de 1:1 entre um servidor de sincronização do Azure AD Connect e um locatário do Azure AD. Para cada locatário do Azure AD, você precisará de uma instalação de servidor do Azure AD Connect Sync. As instâncias de locatário do Azure AD são isoladas por design. Ou seja, os usuários em um locatário não podem ver os usuários no outro locatário. Se você quiser que essa separação, essa é uma configuração com suporte. Caso contrário, você deve usar o único modelo de locatário do Azure AD.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Cada objeto uma única vez em um locatário do Azure AD
![Topologia filtrada para uma floresta única](./media/plan-connect-topologies/singleforestfiltered.png)

Nessa topologia, um servidor do Azure AD Connect sync está conectado a cada locatário do Azure AD. Os servidores de sincronização do Azure AD Connect devem ser configurados para filtragem para que cada um deles tenha um conjunto de objetos mutuamente exclusivos para operação. Você pode, por exemplo, definir o escopo de cada servidor para um determinado domínio ou unidade organizacional.

Um domínio DNS só pode ser registrado em um único locatário do Azure AD. Os UPNs dos usuários na instância local do Active Directory devem usar também os namespaces separados. Por exemplo, na figura acima, três sufixos de UPN separado são registrados na instância local do Active Directory: contoso.com, fabrikam.com e wingtiptoys.com. Os usuários em cada domínio local do Active Directory usam um namespace diferente.

>[!NOTE]
>A GalSync (Sincronização de Lista de Endereços Global) não é feita automaticamente nesta topologia e requer uma implementação MIM personalizada adicional para garantir que cada locatário tenha uma GAL (Lista de Endereços Global) completa no Exchange Online e Skype for Business Online.


Essa topologia tem as seguintes restrições aos cenários que, caso contrário, têm suporte:

* Um máximo de 5 Azure Active Directory locatários pode ter o Exchange híbrido com a instância de Active Directory local. Esse cenário é descrito em [atualização do assistente de configuração híbrida de setembro de 2020](https://techcommunity.microsoft.com/t5/exchange-team-blog/september-2020-hybrid-configuration-wizard-update/ba-p/1687698).
* O assistente de configuração híbrida do Exchange Server em execução deve ser 2016 CU18 ou 2019 CU7 ou posterior.
* Cada instância de Azure AD Connect deve estar em execução em um computador ingressado no domínio.
* Azure AD Connect deve ser configurado usando a opção de filtragem de domínio/UO para filtrar usuários de seu diretório local. O uso dessa opção garante que os usuários apareçam apenas em um único locatário do Exchange Online.
* Os dispositivos Windows 10 podem ser associados a um locatário do Azure AD.
* A opção de SSO (logon único) para autenticação de passagem e sincronização de hash de senha só pode ser usada com um locatário do Azure AD.

O requisito para um conjunto de objetos mutuamente exclusivo também se aplica ao write-back. Alguns recursos de write-back não têm suporte com esta topologia pois eles supõem uma configuração única local. Esses recursos incluem:

* Write-back de grupo com configuração padrão.
* Write-back de dispositivo.

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Cada objeto várias vezes em um locatário do Azure AD
![Topologia sem suporte para uma única floresta e vários locatários](./media/plan-connect-topologies/singleforestmultidirectoryunsupported.png) ![Topologia sem suporte para uma única floresta e vários conectores](./media/plan-connect-topologies/singleforestmulticonnectorsunsupported.png)

Estas tarefas não têm suporte:

* Sincronização do mesmo usuário para vários locatários do Azure AD.
* Faça uma alteração de configuração para fazer os usuários de um locatário do Azure AD aparecerem como contatos em outro locatário do Azure AD.
* Modifique a sincronização do Azure AD Connect para se conectar a vários locatários do Azure AD.

### <a name="galsync-by-using-writeback"></a>GALSync com o uso de write-back
![Topologia sem suporte para várias florestas e vários diretórios com GALSync concentrando-se no Azure AD](./media/plan-connect-topologies/multiforestmultidirectorygalsync1unsupported.png) ![Topologia sem suporte para várias florestas e vários diretórios com GALSync concentrando-se no Active Directory local](./media/plan-connect-topologies/multiforestmultidirectorygalsync2unsupported.png)

Os locatários do Azure AD são isolados por design. Estas tarefas não têm suporte:

* Altere a configuração da sincronização do Azure AD Connect para ler dados de outro locatário do Azure AD.
* Exporte usuários como contatos para outra instância local do Active Directory usando a sincronização do Azure AD Connect.

### <a name="galsync-with-on-premises-sync-server"></a>GALSync com o servidor de sincronização local
![GALSync em uma topologia para várias florestas e vários diretórios](./media/plan-connect-topologies/multiforestmultidirectorygalsync.png)

Você pode usar o FIM 2010 ou MIM 2016 local para sincronizar usuários (via GALSync) entre duas organizações do Exchange. Os usuários em uma organização aparecem como usuários/contatos externos na outra organização. Essas instâncias locais diferentes do Active Directory poderão então ser sincronizadas para seus próprios locatários do Azure AD.

### <a name="using-unauthorized-clients-to-access-the-azure-ad-connect-backend"></a>Usando clientes não autorizados para acessar o back-end de Azure AD Connect
![Usando clientes não autorizados para acessar o back-end de Azure AD Connect](./media/plan-connect-topologies/other-client-unsupported.png)

O servidor Azure Active Directory Connect se comunica com Azure Active Directory por meio do back-end Azure Active Directory Connect. O único software que pode ser usado para se comunicar com esse back-end é Azure Active Directory Connect. Não há suporte para comunicação com o back-end Azure Active Directory Connect usando qualquer outro software ou método. 

## <a name="next-steps"></a>Próximas etapas
Para saber como instalar o Azure AD Connect para esses cenários, veja [Instalação personalizada do Azure AD Connect](how-to-connect-install-custom.md).

Saiba mais sobre a configuração de [sincronização do Azure AD Connect](how-to-connect-sync-whatis.md) .

Saiba mais sobre como [integrar suas identidades locais com o Azure Active Directory](whatis-hybrid-identity.md).
