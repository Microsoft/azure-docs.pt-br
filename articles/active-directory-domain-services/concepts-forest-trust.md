---
title: Como as relações de confiança funcionam para Azure AD Domain Services | Microsoft Docs
description: Saiba mais sobre como a relação de confiança de floresta funciona com Azure AD Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 5c72ab7d085de558ee95f3c602ccc6be6160b322
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96620198"
---
# <a name="how-trust-relationships-work-for-resource-forests-in-azure-active-directory-domain-services"></a>Como as relações de confiança funcionam para florestas de recursos no Azure Active Directory Domain Services

O Active Directory Domain Services (AD DS) fornece segurança em vários domínios ou florestas por meio de relações de confiança de domínio e floresta. Antes que a autenticação possa ocorrer em relações de confiança, o Windows deve primeiro verificar se o domínio que está sendo solicitado por um usuário, computador ou serviço tem uma relação de confiança com o domínio da conta solicitante.

Para verificar essa relação de confiança, o sistema de segurança do Windows computa um caminho de confiança entre o controlador de domínio (DC) para o servidor que recebe a solicitação e um DC no domínio da conta solicitante.

Os mecanismos de controle de acesso fornecidos pelo AD DS e o modelo de segurança distribuída do Windows fornecem um ambiente para a operação de relações de confiança de domínio e floresta. Para que essas relações de confiança funcionem corretamente, cada recurso ou computador deve ter um caminho de confiança direto para um DC no domínio no qual ele está localizado.

O caminho de confiança é implementado pelo serviço de logon de rede usando uma conexão de RPC (chamada de procedimento remoto) autenticada para a autoridade de domínio confiável. Um canal protegido também se estende a outros domínios de AD DS por meio de relações de confiança entre domínios. Esse canal protegido é usado para obter e verificar informações de segurança, incluindo identificadores de segurança (SIDs) para usuários e grupos.

Para obter uma visão geral de como as relações de confiança se aplicam ao AD DS do Azure, consulte [conceitos e recursos da floresta de recursos][create-forest-trust].

Para começar a usar as relações de confiança no Azure AD DS, [crie um domínio gerenciado que usa relações de confiança de floresta][tutorial-create-advanced].

## <a name="trust-relationship-flows"></a>Fluxos de relações de confiança

O fluxo de comunicações protegidas por relações de confiança determina a elasticidade de uma relação de confiança. A maneira como você cria ou configura uma relação de confiança determina até que distância a comunicação se estende dentro ou entre florestas.

O fluxo de comunicação sobre relações de confiança é determinado pela direção da relação de confiança. As relações de confiança podem ser unidirecionais ou bidirecionais e podem ser transitivas ou não transitivas.

O diagrama a seguir mostra que todos os domínios na *árvore 1* e *árvore 2* têm relações de confiança transitivas por padrão. Como resultado, os usuários na *árvore 1* podem acessar recursos em domínios na *árvore 2* e os usuários na *árvore 1* podem acessar recursos na *árvore 2*, quando as permissões apropriadas são atribuídas no recurso.

![Diagrama de relações de confiança entre duas florestas](./media/concepts-forest-trust/trust-relationships.png)

### <a name="one-way-and-two-way-trusts"></a>Relações de confiança unidirecional e bidirecional

As relações de confiança permitem que o acesso aos recursos possa ser unidirecional ou bidirecional.

Uma relação de confiança unidirecional é um caminho de autenticação unidirecional criado entre dois domínios. Em uma relação de confiança unidirecional entre *o domínio a e o* *domínio B*, os usuários no *domínio a* podem acessar recursos no *domínio B*. No entanto, os usuários no *domínio B* não podem acessar recursos no *domínio A*.

Algumas relações de confiança unidirecionais podem ser não transitivas ou transitivas, dependendo do tipo de relação de confiança que está sendo criado.

Em uma relação de confiança bidirecional, *o domínio a* confia no *domínio b* e o *domínio b* confia no *domínio a*. Essa configuração significa que as solicitações de autenticação podem ser passadas entre os dois domínios em ambas as direções. Algumas relações bidirecionais podem ser não transitivas ou transitivas, dependendo do tipo de relação de confiança que está sendo criado.

Todas as relações de confiança de domínio em uma floresta AD DS são relações de confiança transitivas bidirecionais. Quando um novo domínio filho é criado, uma relação de confiança transitiva bidirecional é automaticamente criada entre o novo domínio filho e o domínio pai.

### <a name="transitive-and-non-transitive-trusts"></a>Relações de confiança transitivas e não transitivas

A transitividade determina se uma confiança pode ser estendida fora dos dois domínios com os quais foi formada.

* Uma relação de confiança transitiva pode ser usada para estender relações de confiança com outros domínios.
* Uma relação de confiança não transitiva pode ser usada para negar relações de confiança com outros domínios.

Sempre que você cria um novo domínio em uma floresta, uma relação de confiança transitiva bidirecional é criada automaticamente entre o novo domínio e seu domínio pai. Se os domínios filho forem adicionados ao novo domínio, o caminho de confiança fluirá para cima na hierarquia de domínio, estendendo o caminho de confiança inicial criado entre o novo domínio e seu domínio pai. As relações de confiança transitivas seguem em direção ao topo de uma árvore de domínio formada, criando esse tipo de relação entre todos os domínios da árvore.

As solicitações de autenticação seguem esses caminhos de confiança, portanto, as contas de qualquer domínio na floresta podem ser autenticadas por qualquer outro domínio na floresta. Com um único processo de logon, as contas com as permissões adequadas podem acessar recursos em qualquer domínio na floresta.

## <a name="forest-trusts"></a>Relações de confiança de floresta

As relações de confiança de floresta ajudam a gerenciar uma infraestrutura de AD DS segmentada e dar suporte ao acesso a recursos e a outros objetos em várias florestas. As relações de confiança de floresta são úteis para provedores de serviços, empresas que passam por fusões ou aquisições, extranets de negócios colaborativas e empresas que buscam uma solução para autonomia administrativa.

Usando relações de confiança de floresta, você pode vincular duas florestas diferentes para formar uma relação de confiança transitiva unidirecional ou bidirecional. Uma relação de confiança de floresta permite que os Administradores conectem duas florestas de AD DS com um único relacionamento de confiança para fornecer uma experiência de autenticação e autorização direta em todas as florestas.

Uma relação de confiança de floresta só pode ser criada entre um domínio raiz de floresta em uma floresta e um domínio raiz de floresta em outra floresta. As relações de confiança de floresta só podem ser criadas entre duas florestas e não podem ser estendidas implicitamente para uma terceira floresta. Esse comportamento significa que, se uma relação de confiança de floresta for criada entre a *floresta 1* e a *floresta 2*, e outra relação de confiança de floresta for criada entre a floresta *2* e a *floresta 3*, a *floresta 1* não terá uma confiança implícita com a *floresta 3*.

O diagrama a seguir mostra duas relações de confiança de floresta separadas entre três AD DS florestas em uma única organização.

![Diagrama de relações de confiança de floresta em uma única organização](./media/concepts-forest-trust/forest-trusts-diagram.png)

Esta configuração de exemplo fornece o seguinte acesso:

* Os usuários na *floresta 2* podem acessar recursos em qualquer domínio na *floresta 1* ou *floresta 3*
* Os usuários na *floresta 3* podem acessar recursos em qualquer domínio na *floresta 2*
* Os usuários na *floresta 1* podem acessar recursos em qualquer domínio na *floresta 2*

Essa configuração não permite que os usuários na *floresta 1* acessem recursos na *floresta 3* ou vice-versa. Para permitir que os usuários na *floresta 1* e na *floresta 3* compartilhem recursos, uma relação de confiança transitiva bidirecional deve ser criada entre as duas florestas.

Se uma relação de confiança de floresta unidirecional for criada entre duas florestas, os membros da floresta confiável poderão utilizar os recursos localizados na floresta confiante. Entretanto, a relação de confiança opera em apenas uma direção.

Por exemplo, quando uma relação de confiança de floresta unidirecional é criada entre a *floresta 1* (a floresta confiável) e a *floresta 2* (a floresta confiante):

* Os membros da *floresta 1* podem acessar os recursos localizados na *floresta 2*.
* Os membros da *floresta 2* não podem acessar recursos localizados na *floresta 1* usando a mesma relação de confiança.

> [!IMPORTANT]
> Azure AD Domain Services floresta de recursos só dá suporte a uma relação de confiança de floresta unidirecional para Active Directory local.

### <a name="forest-trust-requirements"></a>Requisitos de confiança de floresta

Antes de criar uma relação de confiança de floresta, você precisa verificar se tem a infraestrutura de DNS (sistema de nomes de domínio) correta em vigor. As relações de confiança de floresta só podem ser criadas quando uma das seguintes configurações de DNS está disponível:

* Um único servidor DNS raiz é o servidor DNS raiz para ambos os namespaces DNS da floresta-a zona raiz contém delegações para cada um dos namespaces DNS e as dicas de raiz de todos os servidores DNS incluem o servidor DNS raiz.
* Quando não há nenhum servidor DNS raiz compartilhado e os servidores DNS raiz em cada namespace DNS da floresta usam encaminhadores condicionais DNS para cada namespace DNS para rotear consultas para nomes no outro namespace.

    > [!IMPORTANT]
    > Azure AD Domain Services floresta de recursos deve usar essa configuração de DNS. A hospedagem de um namespace DNS diferente do namespace DNS da floresta de recursos não é um recurso do Azure AD Domain Services. Os encaminhadores condicionais são a configuração correta.

* Quando não há nenhum servidor DNS raiz compartilhado e os servidores DNS raiz em cada namespace DNS de floresta são usados, as zonas secundárias de DNS são configuradas em cada namespace DNS para rotear consultas para nomes no outro namespace.

Para criar uma relação de confiança de floresta, você deve ser membro do grupo Admins. do domínio (no domínio raiz da floresta) ou do grupo Administradores de empresa em Active Directory. Cada relação de confiança é atribuída a uma senha que os administradores em ambas as florestas devem conhecer. Os membros de administradores corporativos em ambas as florestas podem criar as relações de confiança em ambas as florestas de uma só vez e, nesse cenário, uma senha criptograficamente aleatória é automaticamente gerada e gravada para ambas as florestas.

A relação de confiança de floresta de saída para Azure AD Domain Services é criada no portal do Azure. Você não cria manualmente a relação de confiança com o próprio domínio gerenciado. A relação de confiança de floresta de entrada deve ser configurada por um usuário com os privilégios indicados anteriormente no Active Directory local.

## <a name="trust-processes-and-interactions"></a>Processos e interações de confiança

Muitas transações entre domínios e entre florestas dependem de relações de confiança de domínio ou floresta para concluir várias tarefas. Esta seção descreve os processos e as interações que ocorrem à medida que os recursos são acessados entre relações de confiança e referências de autenticação são avaliadas.

### <a name="overview-of-authentication-referral-processing"></a>Visão geral do processamento de referência de autenticação

Quando uma solicitação de autenticação é referenciada em um domínio, o controlador de domínio nesse domínio deve determinar se existe uma relação de confiança com o domínio do qual a solicitação vem. A direção da relação de confiança e se a confiança é transitiva ou não-transitiva também deve ser determinada antes de autenticar o usuário para acessar recursos no domínio. O processo de autenticação que ocorre entre domínios confiáveis varia de acordo com o protocolo de autenticação em uso. Os protocolos Kerberos V5 e NTLM processam referências de autenticação para um domínio de forma diferente

### <a name="kerberos-v5-referral-processing"></a>Processamento de referência Kerberos V5

O protocolo de autenticação Kerberos V5 depende do serviço de logon de rede em controladores de domínio para informações de autenticação e autorização de cliente. O protocolo Kerberos se conecta a um centro de distribuição de chaves online (KDC) e ao armazenamento de conta do Active Directory para tíquetes de sessão.

O protocolo Kerberos também usa relações de confiança para os serviços de concessão de tíquete entre Realms (TGS) e para validar o PACs (certificados de atributo de privilégio) em um canal protegido. O protocolo Kerberos executa a autenticação entre territórios somente com territórios Kerberos do sistema operacional da marca não Windows, como um realm do MIT Kerberos e não precisa interagir com o serviço de logon de rede.

Se o cliente usar o Kerberos V5 para autenticação, ele solicitará um tíquete para o servidor no domínio de destino de um controlador de domínio em seu domínio de conta. O KDC Kerberos atua como um intermediário confiável entre o cliente e o servidor e fornece uma chave de sessão que permite que as duas partes se autentiquem. Se o domínio de destino for diferente do domínio atual, o KDC seguirá um processo lógico para determinar se uma solicitação de autenticação pode ser referida:

1. O domínio atual é confiável diretamente pelo domínio do servidor que está sendo solicitado?
    * Em caso afirmativo, envie ao cliente uma referência para o domínio solicitado.
    * Se não, vá para a próxima etapa.

2. Existe uma relação de confiança transitiva entre o domínio atual e o próximo domínio no caminho de confiança?
    * Em caso afirmativo, envie ao cliente uma referência para o próximo domínio no caminho de confiança.
    * Se não, envie ao cliente uma mensagem de entrada negada.

### <a name="ntlm-referral-processing"></a>Processamento de referência NTLM

O protocolo de autenticação NTLM depende do serviço de logon de rede em controladores de domínio para informações de autenticação e autorização do cliente. Esse protocolo autentica clientes que não usam a autenticação Kerberos. O NTLM usa relações de confiança para passar solicitações de autenticação entre domínios.

Se o cliente usar NTLM para autenticação, a solicitação inicial de autenticação vai diretamente do cliente para o servidor de recursos no domínio de destino. Esse servidor cria um desafio para o qual o cliente responde. Em seguida, o servidor envia a resposta do usuário a um controlador de domínio em seu domínio de conta de computador. Esse controlador de domínio verifica a conta de usuário em relação ao banco de dados de contas de segurança.

Se a conta não existir no banco de dados, o controlador de domínio determinará se a autenticação de passagem deve ser executada, encaminhar a solicitação ou negar a solicitação usando a seguinte lógica:

1. O domínio atual tem uma relação de confiança direta com o domínio do usuário?
    * Em caso afirmativo, o controlador de domínio envia as credenciais do cliente para um controlador de domínio no domínio do usuário para autenticação de passagem.
    * Se não, vá para a próxima etapa.

2. O domínio atual tem uma relação de confiança transitiva com o domínio do usuário?
    * Em caso afirmativo, passe a solicitação de autenticação para o próximo domínio no caminho de confiança. Esse controlador de domínio repete o processo verificando as credenciais do usuário em seu próprio banco de dados de contas de segurança.
    * Se não, envie ao cliente uma mensagem de logon negado.

### <a name="kerberos-based-processing-of-authentication-requests-over-forest-trusts"></a>Processamento baseado em Kerberos de solicitações de autenticação sobre relações de confiança de floresta

Quando duas florestas são conectadas por uma relação de confiança de floresta, as solicitações de autenticação feitas usando os protocolos Kerberos V5 ou NTLM podem ser roteadas entre as florestas para fornecer acesso aos recursos em ambas as florestas.

Quando uma relação de confiança de floresta é estabelecida pela primeira vez, cada floresta coleta todos os namespaces confiáveis em sua floresta de parceiros e armazena as informações em um [objeto de domínio confiável](#trusted-domain-object). Os namespaces confiáveis incluem nomes de árvore de domínio, sufixos de nome principal de usuário (UPN), sufixos de SPN (nome da entidade de serviço) e namespaces de SID (ID de segurança) usados na outra floresta. Os objetos TDO são replicados para o catálogo global.

Antes que os protocolos de autenticação possam seguir o caminho de confiança da floresta, o nome da entidade de serviço (SPN) do computador de recursos deve ser resolvido para um local na outra floresta. Um SPN pode ser um dos seguintes nomes:

* O nome DNS de um host.
* O nome DNS de um domínio.
* O nome distinto de um objeto de ponto de conexão de serviço.

Quando uma estação de trabalho em uma floresta tenta acessar dados em um computador de recurso em outra floresta, o processo de autenticação Kerberos entra em contato com o controlador de domínio para obter um tíquete de serviço para o SPN do computador de recursos. Depois que o controlador de domínio consulta o catálogo global e determina que o SPN não está na mesma floresta que o controlador de domínio, o controlador de domínio envia uma referência para seu domínio pai de volta para a estação de trabalho. Nesse ponto, a estação de trabalho consulta o domínio pai do tíquete de serviço e continua a seguir a cadeia de referência até atingir o domínio em que o recurso está localizado.

O diagrama e as etapas a seguir fornecem uma descrição detalhada do processo de autenticação Kerberos usado quando os computadores que executam o Windows tentam acessar recursos de um computador localizado em outra floresta.

![Diagrama do processo Kerberos em uma relação de confiança de floresta](media/concepts-forest-trust/kerberos-over-forest-trust-process-diagram.png)

1. O *Usuário1* entra na *EstaçãoDeTrabalho1* usando credenciais do domínio *Europe.tailspintoys.com* . Em seguida, o usuário tenta acessar um recurso compartilhado no *FileServer1* localizado na floresta *usa.wingtiptoys.com* .

2. A *EstaçãoDeTrabalho1* entra em contato com o KDC do Kerberos em um controlador de domínio em seu domínio, *ChildDC1* e solicita um tíquete de serviço para o SPN do *FileServer1* .

3. O *ChildDC1* não encontra o SPN em seu banco de dados de domínio e consulta o catálogo global para ver se algum domínio na floresta *TAILSPINTOYS.com* contém esse SPN. Como um catálogo global é limitado a sua própria floresta, o SPN não é encontrado.

    Em seguida, o catálogo global verifica seu banco de dados para obter informações sobre as relações de confiança de floresta estabelecidas com sua floresta. Se encontrado, ele compara os sufixos de nome listados no objeto de domínio confiável de confiança de floresta (TDO) para o sufixo do SPN de destino para encontrar uma correspondência. Quando uma correspondência é encontrada, o catálogo global fornece uma dica de roteamento de volta ao *ChildDC1*.

    Dicas de roteamento ajudam a direcionar solicitações de autenticação para a floresta de destino. As dicas são usadas apenas quando todos os canais de autenticação tradicionais, como o controlador de domínio local e o catálogo global, falham ao localizar um SPN.

4. *ChildDC1* envia uma referência para seu domínio pai de volta para a *EstaçãoDeTrabalho1*.

5. A *EstaçãoDeTrabalho1* entra em contato com um controlador de domínio em *ForestRootDC1* (seu domínio pai) para obter uma referência a um controlador de domínio (*ForestRootDC2*) no domínio raiz da floresta *wingtiptoys.com* .

6. A *EstaçãoDeTrabalho1* contata *ForestRootDC2* na floresta *wingtiptoys.com* para um tíquete de serviço para o serviço solicitado.

7. O *ForestRootDC2* contata seu catálogo global para localizar o SPN e o catálogo global encontra uma correspondência para o SPN e o envia de volta para o *ForestRootDC2*.

8. Em seguida, *ForestRootDC2* envia a referência para *usa.wingtiptoys.com* de volta para a *EstaçãoDeTrabalho1*.

9. A *EstaçãoDeTrabalho1* entra em contato com o KDC no *ChildDC2* e negocia o tíquete do *Usuário1* para obter acesso ao *FileServer1*.

10. Depois que a *EstaçãoDeTrabalho1* tiver um tíquete de serviço, ele enviará o tíquete de serviço para *FileServer1*, que lerá as credenciais de segurança de *Usuário1* e construirá um token de acesso de acordo.

## <a name="trusted-domain-object"></a>Objeto de domínio confiável

Cada relação de confiança de domínio ou floresta em uma organização é representada por um TDO (objeto de domínio confiável) armazenado no contêiner do *sistema* dentro de seu domínio.

### <a name="tdo-contents"></a>Conteúdo do TDO

As informações contidas em um TDO variam dependendo se um TDO foi criado por uma confiança de domínio ou por uma relação de confiança de floresta.

Quando uma relação de confiança de domínio é criada, atributos como o nome de domínio DNS, o SID de domínio, o tipo de confiança, a transitividade de confiança e o nome de domínio recíproco são representados no TDO. Confiança de floresta TDOs armazenar atributos adicionais para identificar todos os namespaces confiáveis da floresta do parceiro. Esses atributos incluem nomes de árvore de domínio, sufixos de nome principal de usuário (UPN), sufixos SPN (nome da entidade de serviço) e namespaces de SID (ID de segurança).

Como as relações de confiança são armazenadas em Active Directory como TDOs, todos os domínios em uma floresta têm conhecimento das relações de confiança que estão em vigor em toda a floresta. Da mesma forma, quando duas ou mais florestas são unidas por meio de relações de confiança de floresta, os domínios raiz da floresta em cada floresta têm conhecimento das relações de confiança que estão em vigor em todos os domínios em florestas confiáveis.

### <a name="tdo-password-changes"></a>Alterações de senha do TDO

Ambos os domínios em uma relação de confiança compartilham uma senha, que é armazenada no objeto TDO no Active Directory. Como parte do processo de manutenção da conta, a cada 30 dias, o controlador de domínio confiante altera a senha armazenada no TDO. Como todas as relações de confiança bidirecionais são, na verdade, confianças de 2 1 em direções opostas, o processo ocorre duas vezes para relações de confiança bidirecionais.

Uma relação de confiança tem confiança e um lado confiável. No lado confiável, qualquer controlador de domínio gravável pode ser usado para o processo. No lado da confiança, o emulador de PDC executa a alteração de senha.

Para alterar uma senha, os controladores de domínio concluem o seguinte processo:

1. O emulador do controlador de domínio primário (PDC) no domínio confiante cria uma nova senha. Um controlador de domínio no domínio confiável nunca inicia a alteração de senha. Ele é sempre iniciado pelo emulador de PDC do domínio confiante.

2. O emulador de PDC no domínio confiante define o campo *oldPassword* do objeto tdo para o campo *newPassword* atual.

3. O emulador de PDC no domínio confiante define o campo *newPassword* do objeto tdo como a nova senha. Manter uma cópia da senha anterior torna possível reverter para a senha antiga se o controlador de domínio no domínio confiável falhar ao receber a alteração ou se a alteração não for replicada antes que seja feita uma solicitação que use a nova senha de confiança.

4. O emulador de PDC no domínio confiante faz uma chamada remota para um controlador de domínio no domínio confiável solicitando que ele defina a senha na conta de confiança como a nova senha.

5. O controlador de domínio no domínio confiável altera a senha de confiança para a nova senha.

6. Em cada lado da relação de confiança, as atualizações são replicadas para os outros controladores de domínio no domínio. No domínio confiante, a alteração dispara uma replicação urgente do objeto de domínio confiável.

A senha agora é alterada em ambos os controladores de domínio. A replicação normal distribui os objetos TDO para os outros controladores de domínio no domínio. No entanto, é possível que o controlador de domínio no domínio confiante altere a senha sem Atualizar com êxito um controlador de domínio no domínio confiável. Esse cenário pode ocorrer porque um canal protegido, que é necessário para processar a alteração de senha, não pôde ser estabelecido. Também é possível que o controlador de domínio no domínio confiável possa estar indisponível em algum momento durante o processo e pode não receber a senha atualizada.

Para lidar com situações em que a alteração de senha não é comunicada com êxito, o controlador de domínio no domínio confiante nunca altera a nova senha, a menos que tenha sido autenticado com êxito (configure um canal protegido) usando a nova senha. Esse comportamento é o motivo pelo qual as senhas antigas e novas são mantidas no objeto TDO do domínio confiante.

Uma alteração de senha não é finalizada até que a autenticação usando a senha seja realizada com sucesso. A senha armazenada antiga pode ser usada no canal protegido até que o controlador de domínio no domínio confiável receba a nova senha, habilitando assim o serviço ininterrupto.

Se a autenticação que usa a nova senha falhar porque a senha é inválida, o controlador de domínio confiante tentará autenticar usando a senha antiga. Se ele for autenticado com êxito com a senha antiga, ele retoma o processo de alteração de senha dentro de 15 minutos.

As atualizações de senha de confiança precisam ser replicadas para os controladores de domínio de ambos os lados da relação de confiança dentro de 30 dias. Se a senha de confiança for alterada após 30 dias e um controlador de domínio tiver apenas a senha N-2, ele não poderá usar a relação de confiança do lado de confiança e não poderá criar um canal seguro no lado confiável.

## <a name="network-ports-used-by-trusts"></a>Portas de rede usadas por relações de confiança

Como as relações de confiança devem ser implantadas em vários limites de rede, elas podem ter que abranger um ou mais firewalls. Quando esse for o caso, você pode encapsular o tráfego de confiança em um firewall ou abrir portas específicas no firewall para permitir que o tráfego passe.

> [!IMPORTANT]
> Active Directory Domain Services não dá suporte à restrição Active Directory tráfego RPC para portas específicas.

Leia a seção **Windows Server 2008 e versões posteriores** do artigo suporte da Microsoft [como configurar um firewall para Active Directory domínios e relações de confiança](https://support.microsoft.com/help/179442/how-to-configure-a-firewall-for-domains-and-trusts) para saber mais sobre as portas necessárias para uma relação de confiança de floresta.

## <a name="supporting-services-and-tools"></a>Serviços e ferramentas de suporte

Para dar suporte a relações de confiança e autenticação, são usados alguns recursos adicionais e ferramentas de gerenciamento.

### <a name="net-logon"></a>Logon de Rede

O serviço de logon de rede mantém um canal protegido de um computador baseado no Windows para um DC. Ele também é usado nos seguintes processos relacionados à confiança:

* Configuração e gerenciamento de confiança-o logon de rede ajuda a manter senhas de confiança, coleta informações de confiança e verifica relações de confiança interagindo com o processo LSA e o TDO.

    Para relações de confiança de floresta, as informações de confiança incluem o registro *FTInfo*(informações de confiança de floresta), que inclui o conjunto de namespaces que uma floresta confiável alega gerenciar, anotada com um campo que indica se cada declaração é confiável pela floresta confiante.

* Autenticação – fornece as credenciais do usuário em um canal protegido para um controlador de domínio e retorna os SIDs de domínio e os direitos de usuário para o usuário.

* Local do controlador de domínio – ajuda na localização ou localização de controladores de domínio em um domínio ou entre domínios.

* Validação de passagem – as credenciais de usuários em outros domínios são processadas pelo logon de rede. Quando um domínio confiante precisa verificar a identidade de um usuário, ele passa as credenciais do usuário por meio do logon de rede para o domínio confiável para verificação.

* Verificação de PAC (certificado de atributo de privilégio) – quando um servidor que usa o protocolo Kerberos para autenticação precisa verificar a PAC em um tíquete de serviço, ele envia a PAC pelo canal seguro para seu controlador de domínio para verificação.

### <a name="local-security-authority"></a>Autoridade de Segurança Local

A autoridade de segurança local (LSA) é um subsistema protegido que mantém informações sobre todos os aspectos da segurança local em um sistema. Coletivamente conhecido como diretiva de segurança local, a LSA fornece vários serviços para conversão entre nomes e identificadores.

O subsistema de segurança LSA fornece serviços no modo kernel e no modo de usuário para validar o acesso a objetos, verificar os privilégios do usuário e gerar mensagens de auditoria. O LSA é responsável por verificar a validade de todos os tíquetes de sessão apresentados pelos serviços em domínios confiáveis ou não confiáveis.

### <a name="management-tools"></a>Ferramentas de gerenciamento

Os administradores podem usar *Active Directory domínios e relações de confiança*, *netdom* e *nltest* para expor, criar, remover ou modificar relações de confiança.

* *Active Directory domínios e relações de confiança* é o MMC (console de gerenciamento Microsoft) que é usado para administrar relações de confiança de domínio, níveis funcionais de domínio e floresta e sufixos de nome principal de usuário.
* As ferramentas de linha de comando *netdom* e *nltest* podem ser usadas para localizar, exibir, criar e gerenciar relações de confiança. Essas ferramentas se comunicam diretamente com a autoridade LSA em um controlador de domínio.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as florestas de recursos, consulte [como as relações de confiança de floresta funcionam no Azure AD DS?][concepts-trust]

Para começar a criar um domínio gerenciado com uma floresta de recursos, consulte [criar e configurar um domínio gerenciado do Azure AD DS][tutorial-create-advanced]. Em seguida, você pode [criar uma relação de confiança de floresta de saída para um domínio local][create-forest-trust].

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
