---
title: 'Emparelhamento privado do Azure ExpressRoute: configurar o modo de transporte IPsec-hosts do Windows'
description: Como habilitar o modo de transporte IPsec entre as VMs do Windows do Azure e os hosts do Windows local usando GPOs e UOs de emparelhamento privado do ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/07/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: cfcc515787cee2bc90a2100e84337a3c96219d8a
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98017265"
---
# <a name="configure-ipsec-transport-mode-for-expressroute-private-peering"></a>Configurar o modo de transporte IPsec para emparelhamento privado do ExpressRoute

Este artigo ajuda você a criar túneis IPsec no modo de transporte por meio do emparelhamento privado do ExpressRoute. O túnel é criado entre as VMs do Azure que executam o Windows e os hosts Windows locais. As etapas neste artigo para essa configuração usam objetos de política de grupo. Embora seja possível criar essa configuração sem usar UOs (unidades organizacionais) e GPOs (objetos de diretiva de grupo). A combinação de UOs e GPOs ajudará a simplificar o controle de suas políticas de segurança e permite que você amplie rapidamente. As etapas neste artigo pressupõem que você já tem uma configuração de Active Directory e está familiarizado com o uso de UOs e GPOs.

## <a name="about-this-configuration"></a>Sobre esta configuração

A configuração nas etapas a seguir usa uma única rede virtual do Azure (VNet) com emparelhamento privado do ExpressRoute. No entanto, essa configuração pode abranger outras VNets do Azure e redes locais. Este artigo o ajudará a definir uma política de criptografia IPsec que você pode aplicar a um grupo de VMs do Azure ou a hosts locais. Essas VMs do Azure ou hosts locais fazem parte da mesma UO. Você configura a criptografia entre as VMs do Azure (vm1 e vm2) e o host1 local somente para tráfego HTTP com a porta de destino 8080. Diferentes tipos de política de IPsec podem ser criados com base nos seus requisitos.

### <a name="working-with-ous"></a>Como trabalhar com UOs 

A política de segurança associada a uma UO é enviada aos computadores por meio do GPO. Algumas vantagens de usar UOs, em vez de aplicar políticas a um único host, são:

* Associar uma política a uma UO garante que os computadores que pertencem à mesma UO obtenham as mesmas políticas.
* Alterar a política de segurança associada à UO aplicará as alterações a todos os hosts na UO.

### <a name="diagrams"></a>Diagramas

O diagrama a seguir mostra a interconexão e o espaço de endereços IP atribuído. As VMs do Azure e o host local estão em execução no Windows 2016. As VMs do Azure e o host1 local fazem parte do mesmo domínio. As VMs do Azure e os hosts locais podem resolver nomes corretamente usando DNS.

[![1]][1]

Este diagrama mostra os túneis de IPsec em trânsito no emparelhamento privado do ExpressRoute.

[![4]][4]

### <a name="working-with-ipsec-policy"></a>Trabalhar com a política de IPsec

No Windows, a criptografia é associada à política de IPsec. A política IPsec determina qual tráfego IP é protegido e o mecanismo de segurança aplicado aos pacotes IP.
**Políticas de IPsec** são compostas pelos seguintes itens: **Listas de Filtros**, **Ações de Filtro** e **Regras de Segurança**.

Ao configurar a política de IPsec, é importante compreender a terminologia de política de IPsec a seguir:

* **Política IPsec:** uma coleção de regras. Apenas uma política pode estar ativa ("atribuída") em um determinado momento. Cada política pode ter uma ou mais regras, podendo estar todas elas ativas simultaneamente. É possível atribuir apenas uma política de IPsec a um computador por vez. No entanto, na política de IPsec, você pode definir várias ações que podem ser executadas em situações diferentes. Cada conjunto de regras de IPsec está associado a uma lista de filtros que afeta o tipo de tráfego de rede ao qual a regra se aplica.

* **Listas de filtros:** são listas de filtros de pacote de um ou mais filtros. Uma lista pode conter vários filtros. Um filtro define se a comunicação é bloqueada, permitida ou protegida com base nos seguintes critérios: intervalos de endereços IP, protocolos ou até mesmo portas específicas. Cada filtro corresponde a um determinado conjunto de condições. Por exemplo, os pacotes enviados de uma sub-rede específica a um computador específico em uma porta de destino específica. Quando as condições de rede correspondem a um ou mais desses filtros, a lista de filtros é ativada. Cada filtro é definido dentro de uma lista de filtros específica. Filtros não podem ser compartilhados entre as listas de filtro. No entanto, uma lista de filtro fornecidos pode ser incorporada em várias políticas de IPsec. 

* **Ações de filtro:** um método de segurança define um conjunto de algoritmos de segurança, protocolos e chaves que um computador oferece durante as negociações de IKE. Ações de filtro são listas de métodos de segurança, classificados em ordem de preferência.  Quando um computador negocia uma sessão IPsec, ela aceita ou envia propostas com base na configuração de segurança armazenada na lista de ações de filtro.

* **Regras de segurança:** regras que controlam como e quando uma política de IPsec protege a comunicação. Usa **lista de filtros** e **ações de filtro** para criar uma regra de IPsec para criar a conexão IPsec. Cada política pode ter uma ou mais regras, podendo estar todas elas ativas simultaneamente. Cada regra contém uma lista de filtros IP e uma coleção de ações de segurança que ocorrem mediante a correspondência com essa lista de filtros:
  * Ações de Filtro IP
  * Métodos de autenticação
  * Configurações de túnel IP
  * Tipos de conexão

[![5]][5]

## <a name="before-you-begin"></a>Antes de começar

Verifique se os seguintes pré-requisitos foram atendidos:

* Você deve ter uma configuração funcional do Active Directory que pode usar para implementar as configurações de Política de Grupo. Para obter mais informações sobre GPOs, confira [Objetos de Política de Grupo](/previous-versions/windows/desktop/Policy/group-policy-objects).

* Você deve ter um circuito do ExpressRoute ativo.
  * Para obter mais informações sobre como criar um circuito do ExpressRoute, confira [Criar um circuito do ExpressRoute](expressroute-howto-circuit-arm.md). 
  * Verifique se o circuito está habilitado pelo provedor de conectividade. 
  * Verifique se o emparelhamento privado do Azure está configurado para seu circuito. Veja o artigo [Configurar roteamento](expressroute-howto-routing-arm.md) para obter instruções sobre roteamento. 
  * Verifique se você tem uma VNet e um gateway de rede virtual criados e totalmente provisionados. Siga as instruções para [criar um gateway de rede virtual para ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Um gateway de rede virtual para ExpressRoute usa o GatewayType “ExpressRoute”, não VPN.

* O gateway de rede virtual ExpressRoute deve estar conectado ao circuito do ExpressRoute. Para obter mais informações, confira [Conectar uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Verifique se as VMs do Windows do Azure são implantadas para a rede virtual.

* Verifique se há conectividade entre os hosts locais e as VMs do Azure.

* Verifique se as VMs do Windows do Azure e os hosts locais podem usar o DNS para resolver nomes corretamente.

### <a name="workflow"></a>Fluxo de trabalho

1. Crie um GPO e associe-o à UO.
2. Defina uma **Ação de Filtro** do IPsec.
3. Definir uma **Lista de Filtros** do IPsec.
4. Criar uma Política de IPsec com **Regras de Segurança**.
5. Atribua o GPO de IPsec à UO.

### <a name="example-values"></a>Valores de exemplo

* **Nome de Domínio:** ipsectest.com

* **UO:** IPSecOU

* **Computador do Windows local:** host1

* **VMs do Windows do Azure:** vm1, vm2

## <a name="1-create-a-gpo"></a><a name="creategpo"></a>1. criar um GPO

1. Crie um novo GPO vinculado a uma UO abrindo o snap-in de gerenciamento de Política de Grupo. Em seguida, localize a UO à qual o GPO será vinculado. No exemplo, a UO é denominada **IPSecOU**. 

   [![9]][9]
2. No snap-in Gerenciamento de Política de Grupo, selecione a UO e clique com o botão direito do mouse. Na lista suspensa, selecione "**criar um GPO neste domínio e vincule-o aqui...**".

   [![10]][10]
3. Dê ao GPO um nome intuitivo para que você possa localizá-lo facilmente mais tarde. Selecione **OK** para criar e vincular o GPO.

   [![11]][11]

## <a name="2-enable-the-gpo-link"></a><a name="enablelink"></a>2. habilitar o link do GPO

Para aplicar o GPO à UO, o GPO deve não apenas ser vinculado a UO, mas o link deve ser habilitado também.

1. Localize o GPO que você criou, clique com o botão direito do mouse e selecione **Editar** na lista suspensa.
2. Para aplicar o GPO à UO, selecione **Link Habilitado**.

   [![12]][12]

## <a name="3-define-the-ip-filter-action"></a><a name="filteraction"></a>3. definir a ação de filtro IP

1. Na lista suspensa, clique com o botão direito do mouse em **política de segurança IP em Active Directory** e, em seguida, selecione **gerenciar listas de filtros IP e ações de filtro...**.

   [![15]][15]
2. Na guia "**Gerenciar ações de filtro**", selecione **Adicionar**.

   [![16]][16]

3. No **Assistente de ação de filtro de segurança de IP**, selecione **Avançar**.

   [![17]][17]
4. Dê à ação de filtro um nome intuitivo para que você possa encontrá-lo posteriormente. Neste exemplo, a ação de filtro é denominada **myEncryption**. Também é possível adicionar uma descrição. Em seguida, selecione **Avançar**.

   [![18]][18]
5. **Negociar segurança** permite que você defina o comportamento caso o IPsec não possa ser estabelecido com outro computador. Selecione **negociar segurança** e, em seguida, selecione **Avançar**.

   [![19]][19]
6. Na página **comunicando-se com computadores que não dão suporte a IPSec** , selecione não **Permitir comunicação não segura** e, em seguida, selecione **Avançar**.

   [![20]][20]
7. Na página **tráfego e segurança de IP** , selecione **personalizado** e, em seguida, selecione **configurações...**.

   [![21]][21]
8. Na página **Configurações de Método de Segurança Personalizado**, selecione **Integridade de dados e criptografia (ESP): SHA1, 3DES**. Depois, selecione **OK**.

   [![22]][22]
9. Na página **Gerenciar Ações de Filtro**, você pode ver que o filtro **myEncryption** foi adicionado com êxito. Selecione **Fechar**.

   [![23]][23]

## <a name="4-define-an-ip-filter-list"></a><a name="filterlist1"></a>4. definir uma lista de filtros IP

Crie uma lista de filtros que especifica o tráfego HTTP criptografado com a porta de destino 8080.

1. Para qualificar os tipos de tráfego que devem ser criptografados, use uma **lista de filtros IP**. Na guia **gerenciar listas de filtros IP** , selecione **Adicionar** para adicionar uma nova lista de filtros IP.

   [![24]][24]
2. No campo **Nome:**, digite um nome para sua lista de filtros IP. Por exemplo, **azure-onpremises-HTTP8080**. Em seguida, selecione **Adicionar**.

   [![25]][25]
3. Na página **Descrição do filtro IP e propriedade espelhada**, selecione **Espelhado**. A configuração espelhada corresponde a pacotes que vão em ambos os sentidos, o que permite comunicação bidirecional. Em seguida, selecione **Avançar**.

   [![26]][26]
4. Na página **Origem do Tráfego IP**, na lista suspensa **Endereço de origem:**, escolha **Uma Sub-rede ou Endereço IP Específico**. 

   [![27]][27]
5. Especifique o endereço **IP ou a sub-rede** do endereço de origem: do tráfego IP e, em seguida, selecione **Avançar**.

   [![28]][28]
6. Especifique a Sub-rede ou o Endereço IP do **Endereço de destino:**. Em seguida, selecione **Avançar**.

   [![29]][29]
7. Na página **Tipo de Protocolo IP**, selecione **TCP**. Em seguida, selecione **Avançar**.

   [![30]][30]
8. Na página **Porta do Protocolo IP**, selecione **De qualquer porta** e **Para esta porta:**. Digite **8080** na caixa de texto. Essas configurações especificam que apenas o tráfego HTTP na porta 8080 do destino será criptografado. Em seguida, selecione **Avançar**.

   [![31]][31]
9. Exibir a lista de filtro IP.  A configuração da Lista de Filtro IP **azure-onpremises-HTTP8080** dispara a criptografia para todo o tráfego que corresponde aos critérios a seguir:

   * Qualquer endereço de origem em 10.0.1.0/24 (Sub-rede 2 do Azure)
   * Qualquer endereço de destino em 10.2.27.0/25 (sub-rede local)
   * Protocolo TCP
   * Porta de destino 8080

   [![32]][32]

## <a name="5-edit-the-ip-filter-list"></a><a name="filterlist2"></a>5. editar a lista de filtros IP

Para criptografar o mesmo tipo de tráfego do host local para a VM do Azure, você precisa de um segundo filtro IP. Siga as mesmas etapas que você usou para configurar o primeiro filtro IP e criar um novo filtro de IP. As únicas diferenças são as sub-redes de origem e de destino.

1. Para adicionar um novo filtro IP à lista de filtros IP, selecione **Editar**.

   [![33]][33]
2. Na página **lista de filtros IP** , selecione **Adicionar**.

   [![34]][34]
3. Crie um segundo filtro IP usando as configurações no exemplo a seguir:

   [![35]][35]
4. Depois de criar o segundo filtro IP, a lista de filtros IP terá esta aparência:

   [![36]][36]

Se a criptografia for necessária entre uma localização local e uma sub-rede do Azure para proteger um aplicativo. Em vez de modificar a lista de filtros IP existente, você pode adicionar uma nova lista de filtros IP. A associação de duas ou mais listas de filtros IP à mesma diretiva IPsec fornecerá mais flexibilidade. Você pode modificar ou remover uma lista de filtros IP sem afetar as outras listas de filtros IP.

## <a name="6-create-an-ipsec-security-policy"></a><a name="ipsecpolicy"></a>6. criar uma política de segurança IPsec 

Criar uma Política de IPsec com regras de segurança.

1. Selecione **Políticas de IPSecurity no Active Directory** associado à UO. Clique com o botão direito do mouse e selecione **Criar Política de Segurança de IP**.

   [![37]][37]
2. Dê um nome à política de segurança. Por exemplo, **policy-azure-onpremises**. Em seguida, selecione **Avançar**.

   [![38]][38]
3. Selecione **Avançar** sem marcar a caixa de seleção.

   [![39]][39]
4. Verifique se a caixa de seleção **Editar propriedades** está marcada e, em seguida, selecione **concluir**.

   [![40]][40]

## <a name="7-edit-the-ipsec-security-policy"></a><a name="editipsec"></a>7. editar a política de segurança IPsec

Adicione à política de IPsec a **Lista de Filtros IP** e a **Ação de Filtro** que você configurou anteriormente.

1. Na guia **regras** de propriedades de política http, selecione **Adicionar**.

   [![41]][41]
2. Sobre o boas-vindas página, selecione **próxima**.

   [![42]][42]
3. Uma regra fornece a opção de definir o modo de IPsec: modo de túnel ou modo de transporte.

   * No modo de túnel, o pacote original é encapsulado por um conjunto de cabeçalhos de IP. O modo de túnel protege as informações de roteamento internas ao criptografar o cabeçalho IP do pacote original. O modo de túnel é implementado amplamente entre os gateways em cenários de VPN site a site. O modo de túnel é, na maioria dos casos, usado para criptografia de ponta a ponta entre hosts.

   * O modo de transporte criptografa apenas o payload e o trailer ESP; o cabeçalho IP do pacote original não é criptografado. No modo de transporte, o IP de origem e o IP de destino dos pacotes são inalterados.

   Selecione **esta regra não especifica um túnel** e, em seguida, selecione **Avançar**.

   [![43]][43]
4. **Tipo de Rede** define qual conexão de rede se associa à política de segurança. Selecione **todas as conexões de rede** e, em seguida, selecione **Avançar**.

   [![44]][44]
5. Selecione a lista de filtros IP que você criou anteriormente,  **Azure-onlocal-HTTP8080** e, em seguida, selecione **Avançar**.

   [![45]][45]
6. Selecione a Ação de Filtro **myEncryption** existente que você criou anteriormente.

   [![46]][46]
7. O Windows dá suporte a quatro tipos distintos de autenticações: Kerberos, certificados, NTLMv2 e chave pré-compartilhada. Como estamos trabalhando com hosts ingressados no domínio, selecione **Active Directory padrão (protocolo Kerberos V5)** e, em seguida, selecione **Avançar**.

   [![47]][47]
8. A nova política cria a regra de segurança: **azure-onpremises-HTTP8080**. Selecione **OK**.

   [![48]][48]

A política IPsec requer que todas as conexões de HTTP na porta 8080 usem o modo de transporte IPsec de destino. Como HTTP é um protocolo de texto não criptografado, ter a política de segurança habilitada, o garante que os dados sejam criptografados quando transferidos por meio do emparelhamento privado do ExpressRoute. A diretiva IPsec para Active Directory é mais complexa de configurar do que o Firewall do Windows com segurança avançada. No entanto, ele permite mais personalização da conexão IPsec.

## <a name="8-assign-the-ipsec-gpo-to-the-ou"></a><a name="assigngpo"></a>8. atribuir o GPO IPsec à UO

1. Exiba a política. A política de grupo de segurança está definida, mas ainda não foi atribuída.

   [![49]][49]
2. Para atribuir a política de grupo de segurança à UO **IPSecOU**, clique com o botão direito do mouse na política de segurança e escolha **Atribuir**.
   Cada computador que pertence à UO terá a política de grupo de segurança atribuída.

   [![50]][50]

## <a name="check-traffic-encryption"></a><a name="checktraffic"></a>Verificar a criptografia de tráfego

Para conferir a GPO de criptografia aplicada à UO, instale o IIS em todas as VMs do Azure e no host1. Todo IIS é personalizado para responder às solicitações HTTP na porta 8080.
Para verificar a criptografia, você pode instalar um rastreador de rede (como Wireshark) em todos os computadores na UO.
Um script do PowerShell funciona como um cliente HTTP para gerar solicitações HTTP na porta 8080:

```powershell
$url = "http://10.0.1.20:8080"
while ($true) {
try {
[net.httpWebRequest]
$req = [net.webRequest]::create($url)
$req.method = "GET"
$req.ContentType = "application/x-www-form-urlencoded"
$req.TimeOut = 60000

$start = get-date
[net.httpWebResponse] $res = $req.getResponse()
$timetaken = ((get-date) - $start).TotalMilliseconds

Write-Output $res.Content
Write-Output ("{0} {1} {2}" -f (get-date), $res.StatusCode.value__, $timetaken)
$req = $null
$res.Close()
$res = $null
} catch [Exception] {
Write-Output ("{0} {1}" -f (get-date), $_.ToString())
}
$req = $null

# uncomment the line below and change the wait time to add a pause between requests
#Start-Sleep -Seconds 1
}

```
A captura de rede a seguir mostra que os resultados para host1 local com o filtro de exibição ESP correspondem apenas ao tráfego criptografado:

[![51]][51]

Se você executar o script do PowerShell local (cliente HTTP), a captura de rede na VM do Azure mostrará um rastreamento semelhante.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o ExpressRoute, consulte [Perguntas Frequentes sobre ExpressRoute](expressroute-faqs.md).

<!--Image References-->

[1]: ./media/expressroute-howto-ipsec-transport-private-windows/network-diagram.png "modo de transporte do IPsec de Diagrama de rede por meio do ExpressRoute"
[4]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-interesting-traffic.png "Tráfego interessante do IPsec"
[5]: ./media/expressroute-howto-ipsec-transport-private-windows/windows-ipsec.png "Política IPsec do Windows"
[9]: ./media/expressroute-howto-ipsec-transport-private-windows/ou.png "Unidade Organizacional na Política de Grupo"
[10]: ./media/expressroute-howto-ipsec-transport-private-windows/create-gpo-ou.png "criar um GPO associado à UO"
[11]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-name.png "atribuir um nome ao GPO associado à UO"
[12]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-gpo.png "editar o GPO"
[15]: ./media/expressroute-howto-ipsec-transport-private-windows/manage-ip-filter-list-filter-actions.png "Gerenciar Listas de Filtro IP e Ações de Filtro"
[16]: ./media/expressroute-howto-ipsec-transport-private-windows/add-filter-action.png "adicionar Ação de Filtro"
[17]: ./media/expressroute-howto-ipsec-transport-private-windows/action-wizard.png "Assistente de Ação"
[18]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-name.png "nome da Ação de Filtro"
[19]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action.png "Ação de Filtro"
[20]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-no-ipsec.png "especificar que o comportamento é de uma conexão não segura é estabelecida"
[21]: ./media/expressroute-howto-ipsec-transport-private-windows/security-method.png "mecanismo de segurança"
[22]: ./media/expressroute-howto-ipsec-transport-private-windows/custom-security-method.png "Método de segurança personalizado"
[23]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-actions-list.png "lista de ações de filtro"
[24]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-ip-filter.png "Adicionar uma nova lista de filtros IP"
[25]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-http-traffic.png "Adicionar tráfego HTTP ao filtro IP"
[26]: ./media/expressroute-howto-ipsec-transport-private-windows/match-both-direction.png "combinar pacote em ambos os sentidos"
[27]: ./media/expressroute-howto-ipsec-transport-private-windows/source-address.png "seleção de sub-rede de Origem"
[28]: ./media/expressroute-howto-ipsec-transport-private-windows/source-network.png "Rede de Origem"
[29]: ./media/expressroute-howto-ipsec-transport-private-windows/destination-network.png "Rede de Destino"
[30]: ./media/expressroute-howto-ipsec-transport-private-windows/protocol.png "Protocolo"
[31]: ./media/expressroute-howto-ipsec-transport-private-windows/source-port-and-destination-port.png "porta de origem e porta de destino"
[32]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list.png "Lista de filtros"
[33]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-for-http.png "lista de filtros IP com tráfego HTTP"
[34]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-add-second-entry.png "Como adicionar um segundo Filtro IP"
[35]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-second-entry.png "lista de Filtros IP – segunda entrada"
[36]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list-2entries.png "lista de Filtros IP – segunda entrada"
[37]: ./media/expressroute-howto-ipsec-transport-private-windows/create-ip-security-policy.png "criar a política de Segurança de IP"
[38]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-policy-name.png "nome da política de IPsec"
[39]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-wizard.png "Assistente de política de IPsec"
[40]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-security-policy.png "edição da política de IPsec"
[41]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-rule.png "adicionar nova regra de segurança à política de IPsec"
[42]: ./media/expressroute-howto-ipsec-transport-private-windows/create-security-rule.png "criar uma nova regra de segurança"
[43]: ./media/expressroute-howto-ipsec-transport-private-windows/transport-mode.png "Modo de Transporte"
[44]: ./media/expressroute-howto-ipsec-transport-private-windows/network-type.png "Tipo de rede"
[45]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-list.png "seleção da Lista de Filtros IP existente"
[46]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-action.png "seleção de Ação de Filtro existente"
[47]: ./media/expressroute-howto-ipsec-transport-private-windows/authentication-method.png "seleção do método de autenticação"
[48]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-completed.png "fim do processo de criação da política de segurança"
[49]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-not-assigned.png "Política IPsec vinculada ao GPO, mas não atribuída"
[50]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-assigned.png "Política de IPsec atribuída ao GPO"
[51]: ./media/expressroute-howto-ipsec-transport-private-windows/encrypted-traffic.png "Captura do tráfego criptografado de IPsec"