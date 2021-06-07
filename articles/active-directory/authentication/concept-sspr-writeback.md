---
title: Write-back de senha local com redefinição de senha self-service – Azure Active Directory
description: Saiba como os eventos de alteração ou redefinição de senha no Azure Active Directory podem ser submetidos a write-back em um ambiente de diretório local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53f416a23dbb47660097c41ada09c8c135434bcb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96743642"
---
# <a name="how-does-self-service-password-reset-writeback-work-in-azure-active-directory"></a>Como funciona a redefinição de senha self-service no Azure Active Directory?

A redefinição de senha self-service (SSPR) no Azure Active Directory (Azure AD) permite que os usuários redefinam as senhas na nuvem, mas a maioria das empresas também tem um ambiente local de Active Directory Domain Services (AD DS) onde estão os seus usuários. O write-back de senha é um recurso habilitado com [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) que permite que as alterações de senha na nuvem possam efetuar write-back para um diretório local existente em tempo real. Nessa configuração, à medida que os usuários alteram ou redefinem suas senhas usando a SSPR na nuvem, as senhas atualizadas também são gravadas no ambiente local de AD DS

> [!IMPORTANT]
> Este artigo conceitual explica como administrador como o Write-back de redefinição de senha de autoatendimento funciona. Se você for um usuário final já registrado para redefinição de senha por autoatendimento e precisar voltar à sua conta, vá para https://aka.ms/sspr.
>
> Se sua equipe de TI não tiver habilitado a capacidade de redefinir sua própria senha, entre em contato com sua assistência técnica para obter mais assistência.

Há suporte de write-back de senha em ambientes que usam os seguintes modelos de identidade híbrida:

* [Sincronização de hash de senha](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Autenticação de passagem](../hybrid/how-to-connect-pta.md)
* [Serviços de Federação do Active Directory (AD FS)](../hybrid/how-to-connect-fed-management.md)

O write-back de senha fornece os seguintes recursos:

* **Imposição de políticas de senha do Active Directory Domain Services (AD DS) local**: quando um usuário redefine a senha, é verificado se essa senha atende à política do AD DS local antes de confirmá-la nesse diretório. Essa revisão inclui a verificação do histórico, da complexidade, do tempo, dos filtros de senha e de qualquer outra restrição de senha que você tenha definido no AD DS.
* **Comentários de atraso zero**:  O write-back de senha é uma operação síncrona. Os usuários serão notificados imediatamente se suas senhas não atenderem à política ou se não puderem ser redefinidas nem alteradas por qualquer motivo.
* **Dá suporte a alterações de senha do painel de acesso e Microsoft 365**: quando os usuários federados ou sincronizados com hash de senha são alterados para alterar suas senhas expiradas ou não expiradas, essas senhas são gravadas novamente no AD DS.
* **Com suporte a write-back de senha quando um administrador as redefine no Portal do Azure**: sempre que um administrador redefine uma senha de usuário no [portal do Azure](https://portal.azure.com), se esse usuário for federado ou sincronizado com hash de senha, o write-back de senha é efetuado no local. Essa funcionalidade não é compatível atualmente com o Portal de Administração do Office.
* **Não exige nenhuma regra de firewall de entrada**: o write-back de senha usa uma retransmissão do Barramento de Serviço do Azure como um canal de comunicação subjacente. Toda a comunicação é de saída pela porta 443.

> [!NOTE]
> Contas de administrador existentes em grupos protegidos no AD local podem ser usadas com o write-back de senha. Os administradores podem alterar sua senha na nuvem, mas não podem usar a redefinição de senha para redefinir uma senha esquecida. Para obter mais informações sobre grupos protegidos, consulte [contas e grupos protegidos no AD DS](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory).

Para começar a usar o write-back da SSPR, conclua o seguinte tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Habilitar a redefinição de senha self-service (SSPR)](./tutorial-enable-sspr-writeback.md)

## <a name="how-password-writeback-works"></a>Como funciona o write-back de senha

Quando um usuário federado ou sincronizado com hash de senha tenta redefinir ou alterar a senha na nuvem, ocorrem as seguintes ações:

1. Uma verificação é realizada para ver qual tipo de senha o usuário tem. Se a senha do usuário for gerenciada no local:
   * Uma verificação será executada para ver se o serviço de write-back está ativo e em execução. Se estiver, o usuário poderá prosseguir.
   * Se o serviço de write-back estiver inativo, o usuário será informado de que a senha não poderá ser redefinida agora.
1. Em seguida, o usuário passa pelas entradas de autenticação pertinentes e alcança a página de **redefinição de senha**.
1. O usuário seleciona uma nova senha e a confirma.
1. Quando o usuário seleciona **Enviar**, a senha de texto não criptografado é criptografada com uma chave simétrica criada durante o processo de configuração de write-backback.
1. A senha criptografada é incluída em uma carga que é enviada por um canal HTTPS para a retransmissão de barramento de serviço específica do locatário (que é configurada para você durante o processo de configuração de write-back). Esta retransmissão é protegida por uma senha gerada aleatoriamente que somente a sua instalação local sabe.
1. Depois que a mensagem chega ao barramento de serviço, o ponto de extremidade de redefinição de senha é automaticamente reativado e vê que há uma solicitação de redefinição pendente.
1. O serviço procurará o usuário em questão usando o atributo de âncora de nuvem. Para essa pesquisa funcionar, as seguintes condições devem ser atendidas:

   * O objeto de usuário deve existir no espaço do conector de AD DS.
   * O objeto de usuário deve estar vinculado ao objeto de MV (metaverso) correspondente.
   * O objeto de usuário deve estar vinculado ao objeto do Azure AD Connector correspondente.
   * O link do objeto do conector de AD DS para o MV deve ter a regra de sincronização `Microsoft.InfromADUserAccountEnabled.xxx` no link.

   Quando a chamada chega da nuvem, o mecanismo de sincronização usa o atributo **cloudAnchor** para pesquisar o objeto de espaço do conector do Azure AD. Em seguida, ele segue o link de volta para o objeto MV e, em seguida, segue o link de volta para o objeto AD DS. Como pode haver vários objetos AD DS (várias florestas) para o mesmo usuário, o mecanismo de sincronização depende do `Microsoft.InfromADUserAccountEnabled.xxx` link para escolher o correto.

1. Depois que a conta de usuário for encontrada, uma tentativa de redefinir a senha diretamente na floresta de AD DS apropriada será feita.
1. Se a operação de definição de senha for realizada com êxito, o usuário será informado de que a senha foi alterada.

   > [!NOTE]
   > Se o hash de senha do usuário for sincronizado com o Azure AD usando a sincronização de hash de senha, haverá uma chance de a política de senha local ser mais fraca que a política de senha na nuvem. Nesse caso, a política local é imposta. Essa política garante que a política local seja imposta na nuvem, independentemente de você usar federação ou sincronização de hash de senha para fornecer logon único.

1. Se a operação de definição de senha falhar, um erro solicitará que o usuário tente novamente. A operação poderá falhar devido aos seguintes motivos:
    * O serviço foi desativado.
    * A senha selecionada não atendeu às políticas da organização.
    * Não é possível localizar o usuário no ambiente de AD DS local.

   As mensagens de erro fornecem diretrizes aos usuários para que possam tentar resolver sem intervenção do administrador.

## <a name="password-writeback-security"></a>Segurança de write-back de senha

O write-back de senha é um serviço altamente seguro. Para garantir que as informações sejam protegidas, um modelo de segurança em quatro camadas é habilitado, conforme a seguir:

* **Retransmissão do barramento de serviço específica ao locatário**
   * Quando você configura o serviço, é configurada uma retransmissão de barramento de serviço específica de locatário protegida por uma senha forte gerada aleatoriamente à qual a Microsoft nunca tem acesso.
* **Chave de criptografia de senha criptograficamente forte e bloqueada**
   * Após a criação da retransmissão do barramento de serviço, é criada uma chave simétrica forte utilizada para criptografar a senha à medida que ela é transmitida. Essa chave existe apenas no repositório secreto de sua empresa na nuvem, que é amplamente bloqueado e auditado, assim como qualquer outra senha no diretório.
* **Segurança de camada de transporte (TLS) padrão do setor**
   1. Quando ocorre uma redefinição de senha ou operação de alteração na nuvem, a senha de texto sem formatação é criptografada com a chave pública.
   1. A senha criptografada é colocada em uma mensagem HTTPS que é enviada por um canal criptografado usando certificados TLS/SSL da Microsoft para a retransmissão do barramento de serviço.
   1. Depois que a mensagem chega ao barramento de serviço, o agente local é ativado e se autentica no barramento de serviço usando a senha forte gerada anteriormente.
   1. O agente local recebe a mensagem criptografada e a descriptografa usando a chave privada.
   1. O agente local tenta definir a senha por meio da API SetPassword do AD DS. Esta etapa é o que permite a imposição de sua AD DS política de senha local (como a complexidade, a idade, o histórico e os filtros) na nuvem.
* **Políticas de expiração de mensagem**
   * Caso a mensagem fique no barramento de serviço devido ao serviço local estar desativado, ela atingirá o tempo limite e será removida após alguns minutos. O tempo limite e a remoção da mensagem aumenta ainda mais a segurança.

### <a name="password-writeback-encryption-details"></a>Detalhes de criptografia de write-back de senha

Depois que um usuário envia uma redefinição de senha, a solicitação de redefinição passa por várias etapas de criptografia antes de chegar no seu ambiente local. Essas etapas de criptografia garantem a máxima segurança e confiabilidade do serviço. Elas são descritas da seguinte maneira:

1. **Criptografia de senha com chave RSA de 2048 bits**: depois que um usuário envia uma senha para write back local, a senha enviada em si é criptografada com uma chave RSA de 2048 bits.
1. **Criptografia em nível de pacote com AES-GCM**: todo o pacote (senha + metadados necessários) é criptografado usando AES-GCM. Essa criptografia impede que qualquer pessoa com acesso direto ao canal do barramento de serviço subjacente exiba ou viole o conteúdo.
1. **Toda a comunicação ocorre por TLS/SSL**: toda a comunicação com o barramento de serviço ocorre em um canal SSL/TLS. Essa criptografia protege o conteúdo de terceiros não autorizados.
1. **Substituição automática de chave a cada seis meses**: todas as chaves são transferidas a cada seis meses ou sempre que o Write-back de senha é desabilitado e, em seguida, habilitado novamente no Azure ad Connect, para garantir a segurança máxima do serviço e a segurança.

### <a name="password-writeback-bandwidth-usage"></a>Uso de largura de banda de write-back de senha

O write-back de senha é um serviço de largura de banda baixa que envia solicitações de volta para o agente local somente nas seguintes circunstâncias:

* Duas mensagens são enviadas quando o recurso for habilitado ou desabilitado por meio do Azure AD Connect.
* Uma mensagem é enviada a cada cinco minutos como uma pulsação de serviço enquanto o serviço está em execução.
* Duas mensagens são enviadas sempre que uma nova senha é enviada:
   * A primeira mensagem é uma solicitação para executar a operação.
   * A segunda mensagem contém o resultado da operação e é enviada nas seguintes circunstâncias:
      * Sempre que uma nova senha é enviada durante uma redefinição de senha de autoatendimento do usuário.
      * Sempre que uma nova senha é enviada durante uma operação de alteração de senha do usuário.
      * Sempre que uma nova senha é enviada durante uma redefinição de senha do usuário iniciada pelo administrador (somente nos portais de administração do Azure).

#### <a name="message-size-and-bandwidth-considerations"></a>Considerações sobre a largura de banda e o tamanho da mensagem

O tamanho de cada mensagem descrita anteriormente normalmente é inferior a 1 KB. Mesmo sob cargas extremas, o próprio serviço de write-back de senha consome alguns quilobits por segundo de largura de banda. Como cada mensagem é enviada em tempo real, somente quando for solicitado por uma operação de atualização de senha, e como o tamanho da mensagem é bem pequeno, o uso da largura de banda da capacidade de write-back será efetivamente muito pequeno para ter qualquer impacto real mensurável.

## <a name="supported-writeback-operations"></a>Operações de write-back com suporte

É feito o write-back das senhas em todas as seguintes situações:

* **Operações do usuário final com suporte**
   * Qualquer operação de alteração de senha voluntária de autoatendimento do usuário final.
   * Qualquer operação para forçar o autoatendimento de alteração de senha do usuário final, por exemplo, expiração de senha.
   * Qualquer redefinição de senha de autoatendimento do usuário final originada do [portal de redefinição de senha](https://passwordreset.microsoftonline.com).

* **Operações do administrador com suporte**
   * Qualquer operação de alteração de senha voluntária de autoatendimento do administrador.
   * Qualquer operação para forçar o autoatendimento de alteração de senha do administrador, por exemplo, expiração de senha.
   * Qualquer redefinição de senha de autoatendimento do administrador originada do [portal de redefinição de senha](https://passwordreset.microsoftonline.com).
   * Qualquer redefinição de senha do usuário final iniciada pelo administrador no [portal do Azure](https://portal.azure.com).
   * Qualquer redefinição de senha do usuário final iniciada pelo administrador na [API do Microsoft Graph beta](/graph/api/passwordauthenticationmethod-resetpassword?tabs=http&view=graph-rest-beta).

## <a name="unsupported-writeback-operations"></a>Operações de write-back sem suporte

Não é feito o write-back das senhas em nenhuma das seguintes situações:

* **Operações do usuário final sem suporte**
   * Qualquer usuário final que redefine sua própria senha usando o PowerShell versão 1, versão 2 ou a API do Microsoft Graph.
* **Operações do administrador sem suporte**
   * Qualquer redefinição de senha do usuário final iniciada pelo administrador do PowerShell versão 1, versão 2 ou da API do Microsoft Graph (a [API do Microsoft Graph beta](/graph/api/passwordauthenticationmethod-resetpassword?tabs=http&view=graph-rest-beta) é suportada).
   * Qualquer redefinição de senha do usuário final iniciada pelo administrador no [centro de administração do Microsoft 365](https://admin.microsoft.com).
   * Nenhum administrador pode usar a ferramenta de redefinição de senha para redefinir sua própria senha para write-back de senha.

> [!WARNING]
> O uso da caixa de seleção “O usuário deve alterar a senha no próximo logon” em ferramentas administrativas do AD DS local como Usuários e Computadores do Active Directory ou o Centro de Administração do Active Directory tem suporte como uma versão prévia do recurso do Azure AD Connect. Para obter mais informações, consulte [Implementar a sincronização de senha com a sincronização do Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Próximas etapas

Para começar a usar o write-back da SSPR, conclua o seguinte tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Habilitar a redefinição de senha self-service (SSPR)](./tutorial-enable-sspr-writeback.md)