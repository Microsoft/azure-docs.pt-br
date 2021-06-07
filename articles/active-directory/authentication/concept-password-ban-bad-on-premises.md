---
title: Proteção de senha do Azure AD-Azure Active Directory
description: Proibir senhas fracas em ambientes de Active Directory Domain Services locais usando a proteção de senha do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f5df1cb158821fb0cd85d90f9ba3b79d80adf45
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96743914"
---
# <a name="enforce-on-premises-azure-ad-password-protection-for-active-directory-domain-services"></a>Impor a proteção de senha do Azure AD local para Active Directory Domain Services

A proteção por senha do Azure AD detecta e bloqueia senhas fracas conhecidas e suas variantes e também pode bloquear outros termos fracos que são específicos para sua organização. A implantação local da proteção de senha do Azure AD usa as mesmas listas de senhas excluídas globais e personalizadas que são armazenadas no Azure AD e faz as mesmas verificações para as alterações de senha no local, uma vez que o Azure AD faz alterações baseadas em nuvem. Essas verificações são executadas durante as alterações de senha e os eventos de redefinição de senha em controladores de domínio Active Directory Domain Services (AD DS) locais.

## <a name="design-principles"></a>Princípios de design

A proteção de senha do Azure AD é projetada com os seguintes princípios em mente:

* Controladores de domínio (DCs) nunca precisam se comunicar diretamente com a Internet.
* Nenhuma nova porta de rede é aberta nos DCs.
* Nenhuma alteração de esquema de AD DS é necessária. O software usa o *contêiner* de AD DS existente e os objetos de esquema do *serviceConnectionPoint* .
* Nenhum nível funcional de domínio ou floresta de AD DS mínimo (DFL/FFL) é necessário.
* O software não cria ou exige contas nos domínios de AD DS que ele protege.
* As senhas de texto não criptografado do usuário nunca deixam o controlador de domínio, seja durante as operações de validação de senha ou a qualquer outro momento.
* O software não depende de outros recursos do Azure AD. Por exemplo, a sincronização de hash de senha (PHS) do Azure AD não está relacionada ou necessária para a proteção de senha do Azure AD.
* A implantação incremental tem suporte, no entanto, a política de senha só é imposta quando o agente de controlador de domínio (agente DC) está instalado.

## <a name="incremental-deployment"></a>Implantação incremental

A proteção de senha do Azure AD dá suporte à implantação incremental entre os DCs em um domínio AD DS. É importante entender o que isso realmente significa e quais são as compensações.

O software de agente DC de proteção de senha do Azure AD só pode validar senhas quando ela está instalada em um controlador de domínio e apenas para alterações de senha enviadas para esse DC. Não é possível controlar quais DCs são escolhidos por computadores cliente Windows para processar alterações de senha de usuário. Para garantir o comportamento consistente e a imposição de segurança de proteção de senha do Azure AD universal, o software do agente de DC deve ser instalado em todos os DCs em um domínio.

Muitas organizações desejam testar cuidadosamente a proteção de senha do Azure AD em um subconjunto de controladores de domínio antes de uma implantação completa. Para dar suporte a esse cenário, a proteção de senha do Azure AD dá suporte à implantação parcial. O software do agente de DC em um determinado DC valida ativamente as senhas mesmo quando outros DCs no domínio não têm o software do agente de DC instalado. Implantações parciais desse tipo não são seguras e não são recomendadas além de para fins de teste.

## <a name="architectural-diagram"></a>Diagrama de arquitetura

É importante entender os conceitos subjacentes de design e função antes de implantar a proteção de senha do Azure AD em um ambiente de AD DS local. O diagrama a seguir mostra como os componentes da Proteção de Senha do Azure AD funcionam em conjunto:

![Como os componentes de Proteção de Senha do Azure AD trabalham em conjunto](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* O serviço de proxy de proteção de senha do Azure AD é executado em qualquer computador ingressado no domínio na floresta de AD DS atual. A finalidade principal do serviço é encaminhar solicitações de download da política de senha de DCs para o Azure AD e, em seguida, retornar as respostas do Azure AD para o controlador de domínio.
* A DLL de filtro de senha do agente de DC recebe solicitações de validação de senha de usuário do sistema operacional. O filtro os encaminha para o serviço de agente de DC que está sendo executado localmente no controlador de domínio.
* O serviço de agente de controlador de domínio da proteção de senha do Azure AD recebe solicitações de validação de senha da DLL de filtro de senha do agente de DC. O serviço de agente de controlador de domínio o processa usando a política de senha atual (disponível localmente) e retorna o resultado de *Pass* ou *Fail*.

## <a name="how-azure-ad-password-protection-works"></a>Como funciona a proteção de senha do Azure AD

Os componentes locais de proteção de senha do Azure AD funcionam da seguinte maneira:

1. Cada instância de serviço de proxy de proteção de senha do Azure AD se anuncia aos controladores de domínio na floresta criando um objeto *serviceConnectionPoint* no Active Directory.

    Cada serviço de agente de DC para proteção de senha do Azure AD também cria um objeto *serviceConnectionPoint* no Active Directory. Esse objeto é usado principalmente para relatórios e diagnósticos.

1. O serviço de agente de controlador de domínio é responsável por iniciar o download de uma nova política de senha do Azure AD. A primeira etapa é localizar um serviço de proxy de proteção por senha do Azure AD consultando a floresta em busca de objetos *serviceConnectionPoint* de proxy.

1. Quando um serviço de proxy disponível é encontrado, o agente de DC envia uma solicitação de download de política de senha para o serviço de proxy. O serviço de proxy, por sua vez, envia a solicitação ao Azure AD e, em seguida, retorna a resposta ao serviço de agente de controlador de domínio.

1. Depois que o serviço de agente de controlador de domínio recebe uma nova política de senha do Azure AD, o serviço armazena a política em uma pasta dedicada na raiz de seu compartilhamento de pasta *SYSVOL* de domínio. O serviço de agente de controlador de domínio também monitora essa pasta caso as políticas mais recentes repliquem em de outros serviços de agente de DC no domínio.

1. O serviço de agente de controlador de domínio sempre solicita uma nova política na inicialização do serviço. Depois que o serviço de agente de controlador de domínio é iniciado, ele verifica a duração da política disponível localmente atual por hora. Se a política for anterior a uma hora, o agente de DC solicitará uma nova política do Azure AD por meio do serviço de proxy, conforme descrito anteriormente. Se a política atual não tiver mais de uma hora, o agente de DC continuará a usar essa política.

1. Quando os eventos de alteração de senha são recebidos por um DC, a política armazenada em cache é usada para determinar se a nova senha é aceita ou rejeitada.

### <a name="key-considerations-and-features"></a>Principais considerações e recursos

* Sempre que uma política de senha de proteção de senha do Azure AD é baixada, essa política é específica para um locatário. Em outras palavras, as políticas de senha sempre são uma combinação da lista global banida de senhas da Microsoft e da lista personalizada de senha proibida por locatário.
* O agente de DC se comunica com o serviço de proxy via RPC sobre TCP. O serviço de proxy escuta essas chamadas em uma porta RPC dinâmica ou estática, dependendo da configuração.
* O agente de DC nunca escuta em uma porta disponível de rede.
* O serviço de proxy nunca chama o serviço de agente de controlador de domínio.
* O serviço de proxy não tem estado. Ele nunca armazena em cache as políticas ou qualquer outro Estado baixado do Azure.
* O serviço de agente de controlador de domínio sempre usa a política de senha mais recente disponível localmente para avaliar a senha de um usuário. Se nenhuma política de senha estiver disponível no controlador de domínio local, a senha será aceita automaticamente. Quando isso acontece, uma mensagem de evento é registrada para avisar o administrador.
* A proteção por senha do Azure AD não é um mecanismo de aplicativo de política em tempo real. Pode haver um atraso entre o momento em que uma alteração de configuração de política de senha é feita no Azure AD e quando essa alteração atinge e é imposta em todos os DCs.
* A proteção de senha do Azure AD atua como um suplemento para as políticas de senha de AD DS existentes, não com uma substituição. Isso inclui qualquer outra DLL de filtro de senha de terceiros que possa ser instalada. AD DS sempre exige que todos os componentes de validação de senha concordem antes de aceitar uma senha.

## <a name="forest--tenant-binding-for-azure-ad-password-protection"></a>Associação de floresta/locatário para proteção de senha do Azure AD

A implantação da proteção de senha do Azure AD em uma floresta AD DS requer o registro dessa floresta com o Azure AD. Cada serviço de proxy implantado também deve ser registrado com o Azure AD. Esses registros de floresta e proxy são associados a um locatário específico do Azure AD, que é identificado implicitamente pelas credenciais que são usadas durante o registro.

A floresta AD DS e todos os serviços de proxy implantados em uma floresta devem ser registrados com o mesmo locatário. Não há suporte para ter uma floresta AD DS ou quaisquer serviços de proxy na floresta que estão sendo registrados em locatários diferentes do Azure AD. Os sintomas de tal implantação mal configurada incluem a incapacidade de baixar políticas de senha.

> [!NOTE]
> Os clientes que têm vários locatários do Azure AD devem, portanto, escolher um locatário distinto para registrar cada floresta para fins de proteção de senha do Azure AD.

## <a name="download"></a>Baixar

Os dois instaladores de agente necessários para a proteção de senha do Azure AD estão disponíveis no [centro de download da Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="next-steps"></a>Próximas etapas

Para começar a usar a proteção de senha do Azure AD local, conclua as seguintes instruções:

> [!div class="nextstepaction"]
> [Implantar a proteção de senha do Azure AD local](howto-password-ban-bad-on-premises-deploy.md)
