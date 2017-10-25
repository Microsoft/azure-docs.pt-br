---
title: 'Azure AD Connect: Topologias com suporte | Microsoft Docs'
description: "Este tópico detalha as topologias com e sem suporte para o Azure AD Connect"
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 1034c000-59f2-4fc8-8137-2416fa5e4bfe
ms.service: active-directory
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: d7af3a531bc1bcced1a3c48403a6f60afea0f82f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="topologies-for-azure-ad-connect"></a>Topologias para o Azure AD Connect
Este artigo descreve várias topologias locais e do Azure Active Directory (Azure AD) que usam a sincronização do Azure AD Connect como solução de integração principal. Este artigo inclui configurações com e sem suporte.

Aqui está a legenda para imagens neste artigo:

| Descrição | Símbolo |
| --- | --- |
| Floresta do Active Directory local |![Floresta do Active Directory local](./media/active-directory-aadconnect-topologies/LegendAD1.png) |
| Active Directory local com importação filtrada |![Active Directory com importação filtrada](./media/active-directory-aadconnect-topologies/LegendAD2.png) |
| Servidor do Azure AD Connect Sync |![Servidor do Azure AD Connect Sync](./media/active-directory-aadconnect-topologies/LegendSync1.png) |
| “Modo de preparo” do servidor de sincronização do Azure AD Connect |![“Modo de preparo” do servidor de sincronização do Azure AD Connect](./media/active-directory-aadconnect-topologies/LegendSync2.png) |
| GALSync com o Forefront Identity Manager (FIM) 2010 ou o Microsoft Identity Manager (MIM) 2016 |![GALSync com o FIM 2010 ou MIM 2016](./media/active-directory-aadconnect-topologies/LegendSync3.png) |
| Servidor do Azure AD Connect Sync detalhado |![Servidor do Azure AD Connect Sync detalhado](./media/active-directory-aadconnect-topologies/LegendSync4.png) |
| AD do Azure |![Azure Active Directory](./media/active-directory-aadconnect-topologies/LegendAAD.png) |
| Cenário sem suporte |![Cenário sem suporte](./media/active-directory-aadconnect-topologies/LegendUnsupported.png) |

## <a name="single-forest-single-azure-ad-tenant"></a>Floresta única, locatário único do Azure AD
![Topologia de uma única floresta e um único locatário](./media/active-directory-aadconnect-topologies/SingleForestSingleDirectory.png)

A topologia mais comum é uma floresta única local, com um ou vários domínios e um único locatário do Azure AD. Para a autenticação do Azure AD, é usada a sincronização de senha. A instalação expressa do Azure AD Connect dá suporte apenas a esta topologia.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Uma única floresta, vários servidores de sincronização para um locatário do Azure AD
![Topologia sem suporte, filtrada para uma floresta única](./media/active-directory-aadconnect-topologies/SingleForestFilteredUnsupported.png)

Não há suporte para vários servidores de sincronização do Azure AD Connect conectados ao mesmo locatário do Azure AD, exceto para um [servidor de preparo](#staging-server). Não haverá suporte mesmo se os servidores estiverem configurados para sincronizar com um conjunto de objetos mutuamente exclusivo. Talvez você tenha considerado esta topologia caso não possa acessar todos os domínios na floresta de um único servidor, ou se quiser distribuir a carga entre vários servidores.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Várias florestas, locatário único do Azure AD
![Topologia de várias florestas e um único locatário](./media/active-directory-aadconnect-topologies/MultiForestSingleDirectory.png)

Muitas organizações têm ambientes com várias florestas do Active Directory local. Há vários motivos para ter mais de uma floresta do Active Directory local. Exemplos típicos são designs com florestas de conta-recurso e o resultado de uma fusão ou aquisição.

Quando você tem várias florestas, todas elas devem ser acessíveis por um único servidor de sincronização do Azure AD Connect. Você não precisa adicionar o servidor a um domínio. Se necessário, para acessar todas as florestas, você pode colocar o servidor em uma rede de perímetro (também conhecida como DMZ, zona desmilitarizada e sub-rede selecionada).

O assistente de instalação do Azure AD Connect oferece várias opções para consolidar os usuários representados em várias florestas. O objetivo é que um usuário seja representado somente uma vez no Azure AD. Há algumas topologias comuns que você pode configurar o caminho de instalação personalizada do assistente de instalação. Na página **Identificando exclusivamente seus usuários**, selecione a opção correspondente que representa sua topologia. A consolidação está configurada somente para os usuários. Grupos duplicados não são consolidados com a configuração padrão.

As topologias comuns são discutidas nas seções sobre: [separar topologias](#multiple-forests-separate-topologies), [malha completa](#multiple-forests-full-mesh-with-optional-galsync) e [a topologia de recurso da conta](#multiple-forests-account-resource-forest).

A configuração padrão do Azure AD Connect sync supõe:

* Os usuários têm apenas uma conta habilitada e a floresta em que essa conta se encontra é usada para autenticar o usuário. Essa suposição serve para a sincronização de senha e para a federação. UserPrincipalName e sourceAnchor/immutableID vêm desta floresta.
* Cada usuário tem apenas uma caixa de correio.
* A floresta que hospeda a caixa de correio de um usuário tem a melhor qualidade de dados para atributos visíveis na GAL (Lista de Endereços Global) do Exchange. Se não houver nenhuma caixa de correio para o usuário, qualquer floresta pode ser usada para contribuir com esses valores de atributos.
* Caso tenha uma caixa de correio vinculada, há uma conta em outra floresta usada para entrada.

Se seu ambiente não corresponder a essas suposições, acontecerá o seguinte:

* Se você tiver mais de uma conta ativa ou mais de uma caixa de correio, o mecanismo de sincronização escolherá uma e ignorará as outras.
* Uma caixa de correio vinculada sem nenhuma outra conta ativa não é exportada para o Azure AD. A conta de usuário não é representada como um membro de nenhum grupo. Uma caixa de correio vinculada no DirSync é sempre representado como uma caixa de correio normal. Essa alteração é intencionalmente um comportamento diferente para dar melhor suporte a cenários de várias florestas.

Você pode encontrar mais detalhes em [Noções básicas sobre a configuração padrão](active-directory-aadconnectsync-understanding-default-configuration.md).

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Várias florestas, vários servidores de sincronização para um locatário do Azure AD
![Topologia sem suporte para vários servidores de sincronização e de várias florestas](./media/active-directory-aadconnect-topologies/MultiForestMultiSyncUnsupported.png)

Não há suporte para a conexão de mais de um servidor de sincronização do Azure AD Connect a um único locatário do Azure AD. A exceção é o uso de um [servidor de preparo](#staging-server).

### <a name="multiple-forests-separate-topologies"></a>Várias florestas, topologias separadas
![Opção para representar os usuários apenas uma vez em todos os diretórios](./media/active-directory-aadconnect-topologies/MultiForestUsersOnce.png)

![Representação de várias florestas e topologias separadas](./media/active-directory-aadconnect-topologies/MultiForestSeperateTopologies.png)

Nesse ambiente, todas as florestas locais são tratadas como entidades separadas. Não há usuários presentes em outras florestas. Cada floresta tem sua própria organização do Exchange e não há nenhuma GALSync entre as florestas. Esta topologia deve ser a situação após uma fusão/aquisição ou em uma organização em que cada unidade de negócios opera de forma independente. Essas florestas serão da mesma empresa no Azure AD e aparecerão com uma GAL unificada. Na figura acima, cada objeto em cada floresta é representado uma vez no metaverso e agregado no locatário de destino do Azure AD.

### <a name="multiple-forests-match-users"></a>Várias florestas: usuários correspondentes
O que é comum a todos esses cenários é que esses grupos de distribuição e segurança podem conter uma mescla de usuários, contatos e FSPs (Entidades de Segurança Externas). As FSPs são usadas no AD DS (Active Directory Domain Services) para representar os membros de outras florestas em um grupo de segurança. Todas as FSPs serão resolvidas para o objeto real no Azure AD.

### <a name="multiple-forests-full-mesh-with-optional-galsync"></a>Várias florestas: malha completa com GALSync opcional
![Opção para usar o atributo de email para correspondência quando existem identidades de usuários em vários diretórios](./media/active-directory-aadconnect-topologies/MultiForestUsersMail.png)

![Topologia de malha completa para várias florestas](./media/active-directory-aadconnect-topologies/MultiForestFullMesh.png)

Uma topologia de malha completa permite que os usuários e recursos estejam localizados em qualquer floresta. Normalmente, há relações de confiança bidirecionais entre as florestas.

Se o Exchange estiver presente em mais de uma floresta, poderá haver uma solução GALSync local (opcional). Cada usuário é representado como um contato em todas as outras florestas. GALSync geralmente é implementado por meio do FIM 2010 ou MIM 2016. O Azure AD Connect não pode ser usado para GALSync local.

Nesse cenário, os objetos de identidade são ingressados usando o atributo de email. Um usuário com uma caixa de correio em uma floresta é ingressado aos contatos em outras florestas.

### <a name="multiple-forests-account-resource-forest"></a>Várias florestas: floresta de recursos de conta
![Opção para usar os atributos ObjectSID e msExchMasterAccountSID para correspondência quando existem identidades em vários diretórios](./media/active-directory-aadconnect-topologies/MultiForestUsersObjectSID.png)

![Topologia de floresta de conta-recurso para várias florestas](./media/active-directory-aadconnect-topologies/MultiForestAccountResource.png)

Em uma topologia de floresta de conta-recurso, você tem uma ou mais florestas de *conta* com contas de usuário ativas. Você também tem uma ou mais florestas de *recursos* com contas desabilitadas.

Nesse cenário, uma (ou mais) floresta de recursos confia em todas as florestas de conta. Essa floresta de recurso normalmente tem um esquema do Active Directory estendido com o Exchange e o Lync. Todos os serviços do Exchange e do Lync, bem como outros serviços compartilhados, estão localizados nessa floresta. Os usuários têm uma conta de usuário desabilitada nesta floresta e a caixa de correio está vinculada à floresta da conta.

## <a name="office-365-and-topology-considerations"></a>Office 365 e considerações de topologia
Algumas cargas de trabalho do Office 365 têm certas restrições em topologias com suporte:

| Carga de trabalho | Restrições |
--------- | ---------
| Exchange Online | Se houver mais de uma organização local do Exchange (ou seja, o Exchange for implantado em mais de uma floresta), você deve usar o Exchange 2013 SP1 ou posterior. Para obter mais informações, confira [Implantações híbridas com várias florestas do Active Directory](https://technet.microsoft.com/library/jj873754.aspx). |
| Skype for Business | Ao usar várias florestas locais, somente a topologia de floresta conta-recurso terá suporte. Para obter mais informações, confira [Requisitos ambientais para Skype for Business Server 2015](https://technet.microsoft.com/library/dn933910.aspx). |


## <a name="staging-server"></a>Servidor de preparo
![Servidor de preparo em uma topologia](./media/active-directory-aadconnect-topologies/MultiForestStaging.png)

O Azure AD Connect dá suporte à instalação em um segundo servidor no *modo de preparo*. Um servidor nesse modo lê dados de todos os diretórios conectados, mas não grava nada em diretórios conectados. Ele usa o ciclo de sincronização normal e, portanto, tem uma cópia atualizada dos dados de identidade.

Em um desastre em que o servidor primário falha, você pode fazer failover para o servidor de preparo. Você pode fazer isso no Assistente do Azure AD Connect. Esse segundo servidor pode estar localizado em um datacenter diferente, pois nenhuma infraestrutura é compartilhada com o servidor primário. É necessário copiar manualmente qualquer alteração de configuração feita no servidor principal para o segundo servidor.

Você pode usar um servidor de preparo para testar uma nova configuração personalizada e o efeito sobre seus dados. Você pode visualizar as alterações e ajustar a configuração. Quando estiver satisfeito com a nova configuração, transforme o servidor de preparo no servidor ativo e defina o antigo servidor ativo para modo de preparo.

Esse método também pode ser usado para substituir o servidor de sincronização ativo. Prepare o novo servidor e configure-o no modo de preparo. Verifique se ele está em boas condições, desabilite o modo de preparo (tornando-o ativo) e desligue o servidor ativo no momento.

É possível ter mais de um servidor de preparo quando você quiser ter vários backups em datacenters diferentes.

## <a name="multiple-azure-ad-tenants"></a>Vários locatários do Azure AD
Recomendamos ter um único locatário no Azure AD para uma organização.
Antes de planejar usar vários locatários do AD do Azure, confira o artigo [Gerenciamento de unidades administrativas no Azure AD](../active-directory-administrative-units-management.md). Ele aborda cenários comuns em que você pode usar um único locatário.

![Topologia de várias florestas e vários locatários](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectory.png)

Há uma relação de 1:1 entre um servidor de sincronização do Azure AD Connect e um locatário do Azure AD. Para cada locatário do Azure AD, você precisará de uma instalação de servidor do Azure AD Connect Sync. As instâncias de locatário do Azure AD são isoladas por design. Ou seja, os usuários em um locatário não podem ver os usuários no outro locatário. Se você quiser que essa separação, essa é uma configuração com suporte. Caso contrário, você deve usar o único modelo de locatário do Azure AD.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Cada objeto uma única vez em um locatário do Azure AD
![Topologia filtrada para uma floresta única](./media/active-directory-aadconnect-topologies/SingleForestFiltered.png)

Nessa topologia, um servidor do Azure AD Connect sync está conectado a cada locatário do Azure AD. Os servidores de sincronização do Azure AD Connect devem ser configurados para filtragem para que cada um deles tenha um conjunto de objetos mutuamente exclusivos para operação. Você pode, por exemplo, definir o escopo de cada servidor para um determinado domínio ou unidade organizacional.

Um domínio DNS só pode ser registrado em um único locatário do Azure AD. Os UPNs dos usuários na instância local do Active Directory devem usar também os namespaces separados. Por exemplo, na figura acima, três sufixos de UPN separado são registrados na instância local do Active Directory: contoso.com, fabrikam.com e wingtiptoys.com. Os usuários em cada domínio local do Active Directory usam um namespace diferente.

Não há GALSync entre as instâncias de locatário do Azure AD. O catálogo de endereços no Exchange Online e no Skype for Business apenas mostra usuários no mesmo locatário.

Essa topologia tem as seguintes restrições aos cenários que, caso contrário, têm suporte:

* Apenas um dos locatários do Azure AD pode habilitar um Exchange híbrido com a instância local do Active Directory.
* Os dispositivos Windows 10 podem ser associados a um locatário do Azure AD.
* A opção de SSO (logon único) para autenticação de passagem e sincronização de senha só pode ser usada com um locatário do Azure AD.

O requisito para um conjunto de objetos mutuamente exclusivo também se aplica ao write-back. Alguns recursos de write-back não têm suporte com esta topologia pois eles supõem uma configuração única local. Esses recursos incluem:

* Write-back de grupo com configuração padrão.
* Write-back de dispositivo.

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Cada objeto várias vezes em um locatário do Azure AD
![Topologia sem suporte para uma única floresta e vários locatários](./media/active-directory-aadconnect-topologies/SingleForestMultiDirectoryUnsupported.png) ![Topologia sem suporte para uma única floresta e vários conectores](./media/active-directory-aadconnect-topologies/SingleForestMultiConnectorsUnsupported.png)

Estas tarefas não têm suporte:

* Sincronização do mesmo usuário para vários locatários do Azure AD.
* Faça uma alteração de configuração para fazer os usuários de um locatário do Azure AD aparecerem como contatos em outro locatário do Azure AD.
* Modifique a sincronização do Azure AD Connect para se conectar a vários locatários do Azure AD.

### <a name="galsync-by-using-writeback"></a>GALSync com o uso de write-back
![Topologia sem suporte para várias florestas e vários diretórios com GALSync concentrando-se no Azure AD](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![Topologia sem suporte para várias florestas e vários diretórios com GALSync concentrando-se no Active Directory local](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Os locatários do Azure AD são isolados por design. Estas tarefas não têm suporte:

* Altere a configuração da sincronização do Azure AD Connect para ler dados de outro locatário do Azure AD.
* Exporte usuários como contatos para outra instância local do Active Directory usando a sincronização do Azure AD Connect.

### <a name="galsync-with-on-premises-sync-server"></a>GALSync com o servidor de sincronização local
![GALSync em uma topologia para várias florestas e vários diretórios](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync.png)

Você pode usar o FIM 2010 ou MIM 2016 local para sincronizar usuários (via GALSync) entre duas organizações do Exchange. Os usuários em uma organização aparecem como usuários/contatos externos na outra organização. Essas instâncias locais diferentes do Active Directory poderão então ser sincronizadas para seus próprios locatários do Azure AD.

## <a name="next-steps"></a>Próximas etapas
Para saber como instalar o Azure AD Connect para esses cenários, veja [Instalação personalizada do Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

Saiba mais sobre a configuração de [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Saiba mais sobre [como integrar suas identidades locais ao Azure Active Directory](active-directory-aadconnect.md).
