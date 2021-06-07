---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: cec44bbabdb7d528c30a8d3396b819f2eb3c5386
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95999411"
---
O recurso é fácil de configurar, mas isso não significa que sua experiência estará livre de problemas. Se você encontrar problemas para acessar o ponto de extremidade desejado, há alguns utilitários que podem ser usados para testar a conectividade no console do aplicativo. Há dois consoles que você pode usar. Um é o console do Kudu e o outro é o console do na portal do Azure. Para acessar o console do kudu do seu aplicativo, acesse **ferramentas**  >  **kudu**. Você também pode acessar o console do Kudo em [SiteName]. SCM. azurewebsites. net. Depois que o site for carregado, vá para a guia **console de depuração** . Para acessar o console do portal do Azure hospedado do seu aplicativo, vá para o   >  **console** de ferramentas.

#### <a name="tools"></a>Ferramentas
Em aplicativos nativos do Windows, as ferramentas **ping**, **nslookup** e **tracert** não funcionarão no console do devido a restrições de segurança (elas funcionam em [contêineres personalizados do Windows](../articles/app-service/quickstart-custom-container.md)). Para preencher o void, duas ferramentas separadas são adicionadas. Para testar a funcionalidade do DNS, adicionamos uma ferramenta chamada **nameresolver.exe**. A sintaxe do é:

```console
nameresolver.exe hostname [optional: DNS Server]
```

Você pode usar a nameresolver para verificar os nomes de host de que seu aplicativo depende. Dessa forma, você pode testar se alguma coisa está configurada incorretamente com seu DNS ou talvez não tenha acesso ao seu servidor DNS. Você pode ver o servidor DNS que seu aplicativo usa no console examinando as variáveis de ambiente WEBSITE_DNS_SERVER e WEBSITE_DNS_ALT_SERVER.

> [!NOTE]
> nameresolver.exe atualmente não funciona em contêineres personalizados do Windows.
>

Você pode usar a próxima ferramenta para testar a conectividade TCP com uma combinação de host e porta. Essa ferramenta é chamada **tcpping** e a sintaxe é:

```console
tcpping.exe hostname [optional: port]
```

O utilitário **tcpping** informa se você pode alcançar um host e uma porta específicos. Ele pode mostrar o sucesso somente se houver um aplicativo escutando na combinação de host e porta, e houver acesso à rede do seu aplicativo para o host e a porta especificados.

#### <a name="debug-access-to-virtual-network-hosted-resources"></a>Depurar o acesso a recursos hospedados na rede virtual
Várias coisas podem impedir que seu aplicativo atinja um host e uma porta específicos. Na maioria das vezes, é uma destas coisas:

* **Há um firewall no caminho.** Se você tiver um firewall no caminho, atingirá o tempo limite de TCP. O tempo limite de TCP é 21 segundos neste caso. Use a ferramenta **tcpping** para testar a conectividade. Os tempos limite de TCP podem ser causados por muitas coisas além dos firewalls, mas começam lá.
* **O DNS não está acessível.** O tempo limite do DNS é de 3 segundos por servidor DNS. Se você tiver dois servidores DNS, o tempo limite será de seis segundos. Use nameresolver para ver se o DNS está funcionando. Não é possível usar o Nslookup, pois isso não usa o DNS com o qual a rede virtual está configurada. Se estiver inacessível, você pode ter um firewall ou NSG bloqueando o acesso ao DNS ou pode estar inoperante.

Se esses itens não responderem a seus problemas, veja primeiro algo como:

**Integração VNET regional**
* Seu destino é um endereço não RFC1918 e você não tem WEBSITE_VNET_ROUTE_ALL definido como 1?
* Há um NSG bloqueando a saída de sua sub-rede de integração?
* Se você estiver entrando no Azure ExpressRoute ou em uma VPN, seu gateway local será configurado para rotear o tráfego de volta para o Azure? Se você puder acessar pontos de extremidade em sua rede virtual, mas não no local, verifique suas rotas.
* Você tem permissões suficientes para definir a delegação na sub-rede de integração? Durante a configuração de integração VNet regional, sua sub-rede de integração é delegada para Microsoft. Web/serverFarms. A interface do usuário de integração VNet delega a sub-rede para Microsoft. Web/serverFarms automaticamente. Se sua conta não tiver permissões de rede suficientes para definir a delegação, você precisará de alguém que possa definir atributos em sua sub-rede de integração para delegar a sub-rede. Para delegar manualmente a sub-rede de integração, vá para a interface do usuário da sub-rede da rede virtual do Azure e defina a delegação para Microsoft. Web/serverFarms.

**Integração de VNet necessária ao gateway**
* É o intervalo de endereços de ponto a site nos intervalos RFC 1918 (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255)?
* O gateway é mostrado como estando ativo no portal? Se o gateway estiver inativo, ative-o.
* Os certificados são mostrados como estando em sincronia ou você suspeita de que a configuração de rede foi alterada?  Se os certificados estiverem fora de sincronia ou se você suspeitar que uma alteração foi feita em sua configuração de rede virtual que não foi sincronizada com seus ASPs, selecione **sincronizar rede**.
* Se você estiver entrando em uma VPN, o gateway local será configurado para rotear o tráfego de backup para o Azure? Se você puder acessar pontos de extremidade em sua rede virtual, mas não no local, verifique suas rotas.
* Você está tentando usar um gateway de coexistência que dá suporte a ponto a site e ExpressRoute? Não há suporte para gateways de coexistência com integração VNet.

A depuração de problemas de rede é um desafio porque você não pode ver o que está bloqueando o acesso a uma combinação de hosts: porta específica. Algumas causas incluem:

* Você tem um firewall em seu host que impede o acesso à porta do aplicativo do intervalo de IP ponto a site. A cruzamento de sub-redes geralmente requer acesso público.
* Seu host de destino está inoperante.
* Seu aplicativo está inoperante.
* Você tinha o IP ou o nome de host incorreto.
* Seu aplicativo está ouvindo em uma porta diferente da que você esperava. Você pode corresponder a sua ID de processo com a porta de escuta usando "netstat -aon" no host do ponto de extremidade.
* Os grupos de segurança de rede são configurados de forma que impeçam o acesso ao host do aplicativo e à porta do intervalo de IP ponto a site.

Você não sabe qual endereço seu aplicativo realmente usa. Pode ser qualquer endereço na sub-rede de integração ou intervalo de endereços de ponto a site, portanto, você precisa permitir o acesso de todo o intervalo de endereços.

As etapas de depuração adicionais incluem:

* Conecte-se a uma VM em sua rede virtual e tente acessar a porta do host de recursos: a partir daí. Para testar o acesso TCP, use o comando do PowerShell **test-netconnection**. A sintaxe do é:

```powershell
test-netconnection hostname [optional: -Port]
```

* Traga um aplicativo em uma VM e teste o acesso ao host e à porta do console a partir do seu aplicativo usando o **tcpping**.

#### <a name="on-premises-resources"></a>Recursos locais ####

Se seu aplicativo não puder acessar um recurso local, verifique se você pode acessar o recurso de sua rede virtual. Use o comando do PowerShell **test-netconnection** para verificar se há acesso TCP. Se sua VM não conseguir acessar o recurso local, sua conexão VPN ou ExpressRoute poderá não estar configurada corretamente.

Se sua VM hospedada na rede virtual puder acessar seu sistema local, mas seu aplicativo não puder, a causa provavelmente será um dos seguintes motivos:

* Suas rotas não estão configuradas com os intervalos de endereços de sua sub-rede ou de ponto a site no gateway local.
* Os grupos de segurança de rede estão bloqueando o acesso ao intervalo de IP ponto a site.
* Seus firewalls locais estão bloqueando o tráfego do intervalo de IP ponto a site.
* Você está tentando acessar um endereço não RFC 1918 usando o recurso de integração VNet regional.
