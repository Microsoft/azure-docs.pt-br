---
title: Diagnosticar problemas de configuração de links privados no Azure Key Vault
description: Resolver problemas comuns de links privados com Key Vault e aprofundar-se na configuração
author: msfcolombo
ms.author: fcolombo
ms.date: 09/30/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 03abe4e4e098d46060e33ba114872905e54a443f
ms.sourcegitcommit: e5f9126c1b04ffe55a2e0eb04b043e2c9e895e48
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96317054"
---
# <a name="diagnose-private-links-configuration-issues-on-azure-key-vault"></a>Diagnosticar problemas de configuração de links privados no Azure Key Vault

## <a name="introduction"></a>Introdução

Este artigo ajuda os usuários a diagnosticar e corrigir problemas envolvendo Key Vault e o recurso de links particulares. Este guia ajuda sobre aspectos de configuração, como obter links privados trabalhando pela primeira vez ou para corrigir uma situação em que os links privados pararam de funcionar devido a alguma alteração.

Se você for novo neste recurso, consulte [integrar o Key Vault com o link privado do Azure](private-link-service.md).

### <a name="problems-covered-by-this-article"></a>Problemas abordados neste artigo

- As consultas DNS ainda retornam um endereço IP público para o cofre de chaves, em vez de um endereço IP privado que você esperaria usando o recurso de links particulares.
- Todas as solicitações feitas por um determinado cliente que está usando o link privado estão falhando com tempos limite ou erros de rede, e o problema não é intermitente.
- O cofre de chaves tem um endereço IP privado, mas as solicitações ainda recebem `403` resposta com o `ForbiddenByFirewall` código de erro interno.
- Você está usando links privados, mas o cofre de chaves ainda aceita solicitações da Internet pública.
- O cofre de chaves tem dois pontos de extremidade privados. As solicitações que usam um estão funcionando bem, mas as solicitações que usam o outro estão falhando.
- Você tem outra assinatura, cofre de chaves ou rede virtual que está usando links privados. Você deseja fazer uma nova implantação semelhante, mas não pode obter links privados para trabalhar lá.

### <a name="problems-not-covered-by-this-article"></a>Problemas não cobertos por este artigo

- Há um problema de conectividade intermitente. Em um determinado cliente, você vê algumas solicitações funcionando e algumas não funcionam. *Problemas intermitentes normalmente não são causados por um problema na configuração de links particulares; Eles são um sinal de sobrecarga de rede ou de cliente.*
- Você está usando um produto do Azure que dá suporte a BYOK (Bring Your Own Key), CMK (chaves gerenciadas pelo cliente) ou acesso a segredos armazenados no cofre de chaves. Quando você habilita o firewall nas configurações do Key Vault, esse produto não pode acessar o cofre de chaves. *Examine a documentação específica do produto. Verifique se ele declara explicitamente o suporte para cofres de chaves com o firewall habilitado. Contate o suporte para esse produto específico, se necessário.*

### <a name="how-to-read-this-article"></a>Como ler este artigo

Se você for novo em links privados ou se estiver avaliando uma implantação complexa, é recomendável ler o artigo inteiro. Caso contrário, sinta-se à vontade para escolher a seção que faz mais sentido para o problema que você está enfrentando.

Vamos começar!

## <a name="1-confirm-that-you-own-the-client-connection"></a>1. Confirme que você possui a conexão do cliente

### <a name="confirm-that-your-client-runs-at-the-virtual-network"></a>Confirmar que o cliente é executado na rede virtual

Este guia destina-se a ajudá-lo a corrigir conexões com o Key Vault provenientes do código do aplicativo. Os exemplos são aplicativos e scripts que são executados em máquinas virtuais do Azure, clusters de Service Fabric do Azure, serviço Azure App, serviço kubernetes do Azure (AKS) e outros semelhantes. Este guia também se aplica a acessos executados na interface do usuário do portal do Azure Web-base, em que o navegador acessa diretamente o cofre de chaves.

Por definição de links privados, o aplicativo, o script ou o portal deve estar em execução no computador, cluster ou ambiente conectado à rede virtual em que o [recurso de ponto de extremidade particular](../../private-link/private-endpoint-overview.md) foi implantado.

Se o aplicativo, o script ou o portal estiver sendo executado em uma rede conectada pela Internet arbitrária, este guia não será aplicável e provavelmente os links privados não poderão ser usados. Essa limitação também se aplica a comandos executados no Azure Cloud Shell, porque eles são executados em um computador remoto do Azure fornecido sob demanda em vez do navegador do usuário.

### <a name="if-you-use-a-managed-solution-refer-to-specific-documentation"></a>Se você usar uma solução gerenciada, consulte a documentação específica

Este guia não se aplica a soluções gerenciadas pela Microsoft, em que o cofre de chaves é acessado por um produto do Azure que existe independentemente da rede virtual do cliente. Exemplos desses cenários são o armazenamento do Azure ou o SQL do Azure configurado para criptografia em repouso, Hub de eventos do Azure Criptografando dados com chaves fornecidas pelo cliente, Azure Data Factory acessar credenciais de serviço armazenadas no Key Vault, Azure Pipelines recuperar segredos do Key Vault e outros cenários semelhantes. Nesses casos, *você deve verificar se o produto oferece suporte a cofres de chaves com o firewall habilitado*. Esse suporte é normalmente executado com o recurso de [serviços confiáveis](overview-vnet-service-endpoints.md#trusted-services) do Key Vault firewall. No entanto, muitos produtos não são incluídos na lista de serviços confiáveis, por vários motivos. Nesse caso, alcance o suporte específico do produto.

Um pequeno número de produtos do Azure dá suporte ao conceito de *injeção de vnet*. Em termos simples, o produto adiciona um dispositivo de rede à rede virtual do cliente, permitindo que ele envie solicitações como se fosse implantado na rede virtual. Um exemplo notável é [Azure Databricks](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject). Produtos como esse podem fazer solicitações para o cofre de chaves usando os links privados, e este guia de solução de problemas pode ajudar.

## <a name="2-confirm-that-the-connection-is-approved-and-succeeded"></a>2. Confirme se a conexão foi aprovada e foi bem-sucedida

As etapas a seguir validam que a conexão de ponto de extremidade privada foi aprovada e bem-sucedida:

1. Abra o portal do Azure e abra o recurso do cofre de chaves.
2. No menu à esquerda, selecione **rede**.
3. Clique na guia **conexões de ponto de extremidade privado** . Isso mostrará todas as conexões de ponto de extremidade particulares e seus respectivos Estados. Se não houver conexões ou se a conexão de sua rede virtual estiver ausente, você precisará criar um novo ponto de extremidade privado. Isso será abordado mais tarde.
4. Ainda em **conexões de ponto de extremidade privado**, encontre a que você está diagnosticando e confirme se "estado da conexão" foi **aprovado** e se o "estado de provisionamento" foi **bem-sucedido**.
    - Se a conexão estiver no estado "pendente", talvez você possa apenas aprová-la.
    - Se a conexão "rejeitada", "falha", "erro", "desconectado" ou outro Estado, isso não é efetivo, você precisa criar um novo recurso de ponto de extremidade privado.

É uma boa ideia excluir conexões ineficazes a fim de manter as coisas limpas.

## <a name="3-confirm-that-the-key-vault-firewall-is-properly-configured"></a>3. Confirme se o Firewall do cofre de chaves está configurado corretamente

>[!IMPORTANT]
> Alterar as configurações de firewall pode remover o acesso de clientes legítimos que ainda não estão usando links privados. Verifique se você está ciente das implicações de cada alteração na configuração do firewall.

Uma noção importante é que o recurso de links privados só *fornece* acesso ao cofre de chaves em uma rede virtual fechada para evitar dados vazamento. Ele não *Remove* nenhum acesso existente. Para bloquear efetivamente os acessos da Internet pública, você deve habilitar o Firewall do cofre de chaves explicitamente:

1. Abra o portal do Azure e abra o recurso do cofre de chaves.
2. No menu à esquerda, selecione **rede**.
3. Verifique se a guia **firewalls e redes virtuais** está selecionada na parte superior.
4. Verifique se a opção **ponto de extremidade privado e redes selecionadas** está selecionada. Se você encontrar **todas as redes** , selecione, que explica por que os clientes externos ainda podem acessar o cofre de chaves.

As instruções a seguir também se aplicam às configurações de firewall:

- O recurso de links particulares não exige que nenhuma "rede virtual" seja especificada nas configurações de firewall do cofre de chaves. Todas as solicitações que usam o endereço IP privado do cofre de chaves (consulte a próxima seção) devem funcionar, mesmo que nenhuma rede virtual seja especificada nas configurações de firewall do Key Vault.
- O recurso de links particulares não requer a especificação de qualquer endereço IP nas configurações de firewall do cofre de chaves. Novamente, todas as solicitações que usam o endereço IP privado do cofre de chaves devem funcionar, mesmo que nenhum endereço IP tenha sido especificado nas configurações do firewall.

Se você estiver usando links privados, as únicas motivações para especificar a rede virtual ou o endereço IP no firewall do Key Vault serão:

- Você tem um sistema híbrido em que alguns clientes usam links privados, alguns pontos de extremidade de serviço usam, alguns usam o endereço IP público.
- Você está fazendo a transição para links privados. Nesse caso, depois de confirmar que todos os clientes estão usando links privados, você deve remover redes virtuais e endereços IP das configurações de firewall do cofre de chaves.

## <a name="4-make-sure-the-key-vault-has-a-private-ip-address"></a>4. Verifique se o cofre de chaves tem um endereço IP privado

### <a name="difference-between-private-and-public-ip-addresses"></a>Diferença entre endereços IP públicos e privados

Um endereço IP privado sempre tem um dos seguintes formatos:

- 10. x. x. x: exemplos: `10.1.2.3` , `10.56.34.12` .
- 172.16. x. x para 172.32. x. x: exemplos: `172.20.1.1` , `172.31.67.89` .
- 192.168. x. x: exemplos: `192.168.0.1` , `192.168.100.7`

Determinados endereços IP e intervalos são reservados:

- 224. x. x. x: multicast
- 255.255.255.255: difusão
- 127. x. x. x: loopback
- 169.254. x. x: link local
- 168.63.129.16: DNS interno

Todos os outros endereços IP são públicos.

Quando você navega no portal ou executa um comando que mostra o endereço IP, certifique-se de que você pode identificar se esse endereço IP é privado, público ou reservado. Para que os links privados funcionem, o nome do host do cofre de chaves deve resolver para um endereço IP privado que pertence ao espaço de endereço de rede virtual.

### <a name="find-the-key-vault-private-ip-address-in-the-virtual-network"></a>Localizar o endereço IP privado do cofre de chaves na rede virtual

Você precisará diagnosticar a resolução do nome de host e, para que você deve saber o endereço IP privado exato do seu cofre de chaves com links privados habilitados. Para encontrar esse endereço, siga este procedimento:

1. Abra o portal do Azure e abra o recurso do cofre de chaves.
2. No menu à esquerda, selecione **rede**.
3. Clique na guia **conexões de ponto de extremidade privado** . Isso mostrará todas as conexões de ponto de extremidade particulares e seus respectivos Estados.
4. Localize o que você está diagnosticando e confirme se o "estado da conexão" foi **aprovado** e se o estado de provisionamento foi **bem-sucedido**. Se você não estiver vendo isso, volte para as seções anteriores deste documento.
5. Quando você encontrar o item correto, clique no link na coluna **ponto de extremidade privado** . Isso abrirá o recurso de ponto de extremidade privado.
6. A página Visão geral pode mostrar uma seção chamada **configurações personalizadas de DNS**. Confirme se há apenas uma entrada que corresponda ao nome de host do cofre de chaves. Essa entrada mostra o endereço IP privado do cofre de chaves.
7. Você também pode clicar no link na **interface de rede** e confirmar se o endereço IP privado é o mesmo exibido na etapa anterior. A interface de rede é um dispositivo virtual que representa o cofre de chaves.

O endereço IP é aquele que as VMs e outros dispositivos *em execução na mesma rede virtual* usarão para se conectar ao cofre de chaves. Anote o endereço IP ou mantenha a guia do navegador aberta e não a toque enquanto fizer investigações adicionais.

>[!NOTE]
> Se o cofre de chaves tiver vários pontos de extremidade privados, ele terá vários endereços IP privados. Isso só será útil se você tiver várias redes virtuais acessando o mesmo cofre de chaves, cada um por meio de seu próprio ponto de extremidade privado (o ponto de extremidade privado pertence a uma única rede virtual). Certifique-se de diagnosticar o problema para a rede virtual correta e selecione a conexão de ponto de extremidade particular correta no procedimento acima. Além disso, **não** crie vários pontos de extremidade privados para o mesmo Key Vault na mesma rede virtual. Isso não é necessário e é uma fonte de confusão.

## <a name="5-validate-the-dns-resolution"></a>5. validar a resolução de DNS

A resolução DNS é o processo de converter o nome do host do cofre de chaves (exemplo: `fabrikam.vault.azure.net` ) em um endereço IP (exemplo: `10.1.2.3` ). As subseções a seguir mostram os resultados esperados da resolução DNS em cada cenário.

### <a name="key-vaults-without-private-link"></a>Cofres de chaves sem link privado

Esta seção destina-se a fins de aprendizado. Quando o cofre de chaves não tem nenhuma conexão de ponto de extremidade privada no estado aprovado, resolver o nome do host fornece um resultado semelhante a este:

Windows:

```console
C:\> nslookup fabrikam.vault.azure.net
```

```output
Non-authoritative answer:
Address:  52.168.109.101
Aliases:  fabrikam.vault.azure.net
          data-prod-eus.vaultcore.azure.net
          data-prod-eus-region.vaultcore.azure.net
```

Linux:

```console
joe@MyUbuntu:~$ host fabrikam.vault.azure.net
```

```output
fabrikam.vault.azure.net is an alias for data-prod-eus.vaultcore.azure.net.
data-prod-eus.vaultcore.azure.net is an alias for data-prod-eus-region.vaultcore.azure.net.
data-prod-eus-region.vaultcore.azure.net has address 52.168.109.101
```

Você pode ver que o nome é resolvido para um endereço IP público e não há nenhum `privatelink` alias. O alias é explicado mais tarde, não se preocupe agora.

O resultado acima é esperado, independentemente da máquina estar conectada à rede virtual ou ser um computador arbitrário com uma conexão com a Internet. Isso acontece porque o cofre de chaves não tem nenhuma conexão de ponto de extremidade privada no estado aprovado e, portanto, não há necessidade de que o cofre de chaves ofereça suporte a links privados.

### <a name="key-vault-with-private-link-resolving-from-arbitrary-internet-machine"></a>Key Vault com o link privado resolvendo do computador da Internet arbitrário

Quando o cofre de chaves tem uma ou mais conexões de ponto de extremidade privado no estado aprovado e você resolve o nome do host de um computador arbitrário conectado à Internet (um computador que *não está* conectado à rede virtual onde reside o ponto de extremidade privado), você deve encontrar isso:

Windows:

```console
C:\> nslookup fabrikam.vault.azure.net
```

```output
Non-authoritative answer:
Address:  52.168.109.101
Aliases:  fabrikam.vault.azure.net
          fabrikam.privatelink.vaultcore.azure.net
          data-prod-eus.vaultcore.azure.net
          data-prod-eus-region.vaultcore.azure.net
```

Linux:

```console
joe@MyUbuntu:~$ host fabrikam.vault.azure.net
```

```output
fabrikam.vault.azure.net is an alias for fabrikam.privatelink.vaultcore.azure.net.
fabrikam.privatelink.vaultcore.azure.net is an alias for data-prod-eus.vaultcore.azure.net.
data-prod-eus.vaultcore.azure.net is an alias for data-prod-eus-region.vaultcore.azure.net.
data-prod-eus-region.vaultcore.azure.net has address 52.168.109.101
```

A diferença notável do cenário anterior é que há um novo alias com o valor `{vaultname}.privatelink.vaultcore.azure.net` . Isso significa que o plano de dados do cofre de chaves está pronto para aceitar solicitações de links privados.

Isso não significa que as solicitações executadas de computadores *fora* da rede virtual (como a que você acabou de usar) usarão links privados. Você pode ver isso do fato de que o nome do host ainda é resolvido para um endereço IP público. Somente *os computadores conectados à rede virtual* podem usar links privados. Mais informações sobre isso serão seguidas.

Se você não vir o `privatelink` alias, significa que o cofre de chaves tem zero conexões de ponto de extremidade privadas em `Approved` estado. Volte para [esta seção](#2-confirm-that-the-connection-is-approved-and-succeeded) antes de tentar novamente.

### <a name="key-vault-with-private-link-resolving-from-virtual-network"></a>Cofre de chaves com resolução de link privado da rede virtual

Quando o cofre de chaves tem uma ou mais conexões de ponto de extremidade privado no estado aprovado e você resolve o nome do host de um computador conectado à rede virtual em que o ponto de extremidade privado foi criado, essa é a resposta esperada:

Windows:

```console
C:\> nslookup fabrikam.vault.azure.net
```

```output
Non-authoritative answer:
Address:  10.1.2.3
Aliases:  fabrikam.vault.azure.net
          fabrikam.privatelink.vaultcore.azure.net
```

Linux:

```console
joe@MyUbuntu:~$ host fabrikam.vault.azure.net
```

```output
fabrikam.vault.azure.net is an alias for fabrikam.privatelink.vaultcore.azure.net.
fabrikam.privatelink.vaultcore.azure.net has address 10.1.2.3
```

Há duas diferenças notáveis. Primeiro, o nome é resolvido para um endereço IP privado. Esse deve ser o endereço IP que encontramos na [seção correspondente](#find-the-key-vault-private-ip-address-in-the-virtual-network) deste artigo. Em segundo lugar, não há nenhum outro alias após `privatelink` aquele. Isso acontece porque os servidores DNS da rede virtual *interceptam* a cadeia de aliases e retornam o endereço IP privado diretamente do nome `fabrikam.privatelink.vaultcore.azure.net` . Essa entrada é, na verdade, um `A` registro em uma zona DNS privado. Mais informações sobre isso serão seguidas.

>[!NOTE]
> O resultado acima ocorre apenas em uma máquina virtual conectada à rede virtual na qual o ponto de extremidade privado foi criado. Se você não tiver uma VM implantada na rede virtual que contém o ponto de extremidade privado, implante uma e conecte-se remotamente a ela, execute o `nslookup` comando (Windows) ou o `host` comando (Linux) acima.

Se você executar esses comandos em uma máquina virtual conectada à rede virtual na qual o ponto de extremidade privado foi criado e eles **não** estiverem mostrando o endereço IP privado do cofre de chaves, a próxima seção poderá ajudar a corrigir o problema.

## <a name="6-validate-the-private-dns-zone"></a>6. validar a zona de DNS privado

Se a resolução de DNS não estiver funcionando conforme descrito na seção anterior, pode haver um problema com sua zona de DNS privado e esta seção pode ajudar. Se a resolução de DNS mostrar o endereço IP privado do Key Vault correto, sua zona de DNS privado provavelmente estará correta. Você pode ignorar essa seção inteira.

### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>Confirme se o recurso de zona de DNS privado necessário existe

Sua assinatura do Azure deve ter um recurso de [zona DNS privado](../../dns/private-dns-privatednszone.md) com esse nome exato:

`privatelink.vaultcore.azure.net`

Você pode verificar a presença desse recurso acessando a página de assinatura no portal e selecionando "recursos" no menu à esquerda. O nome do recurso deve ser `privatelink.vaultcore.azure.net` , e o tipo de recurso deve ser **DNS privado zona**.

Normalmente, esse recurso é criado automaticamente quando você cria um ponto de extremidade privado usando um procedimento comum. Mas há casos em que esse recurso não é criado automaticamente e você precisa fazê-lo manualmente. Esse recurso pode ter sido excluído acidentalmente também.

Se você não tiver esse recurso, crie um novo DNS privado recurso de zona em sua assinatura. Lembre-se de que o nome deve ser exatamente `privatelink.vaultcore.azure.net` , sem espaços ou pontos adicionais. Se você especificar o nome errado, a resolução de nome explicada neste artigo não funcionará. Para obter mais informações sobre como criar esse recurso, consulte [criar uma zona DNS privada do Azure usando o portal do Azure](../../dns/private-dns-getstarted-portal.md). Se você seguir essa página, poderá ignorar a criação de rede virtual porque, neste ponto, você já deve ter uma. Você também pode ignorar procedimentos de validação com máquinas virtuais.

### <a name="confirm-that-the-private-dns-zone-is-linked-to-the-virtual-network"></a>Confirme se a zona de DNS privado está vinculada à rede virtual

Não é suficiente ter uma zona de DNS privado. Ele também deve ser vinculado à rede virtual que contém o ponto de extremidade privado. Se a zona de DNS privado não estiver vinculada à rede virtual correta, qualquer resolução de DNS dessa rede virtual ignorará a zona de DNS privado.

Abra o recurso de zona DNS privado e clique na opção **links de rede virtual** no menu à esquerda. Isso mostrará uma lista de links, cada um com o nome de um Entrada na Rede virtual sua assinatura. A rede virtual que contém o recurso de ponto de extremidade privado deve estar listada aqui. Se não estiver, adicione-a. Para obter etapas detalhadas, consulte [vincular a rede virtual](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network). Você pode deixar a seleção de "Habilitar registro automático" desmarcada – esse recurso não está relacionado a links privados.

Depois que a zona de DNS privado estiver vinculada à rede virtual, as solicitações DNS originadas da rede virtual procurarão nomes na zona DNS privado. Isso é necessário para a resolução de endereço correta executada em máquinas virtuais conectadas à rede virtual em que o ponto de extremidade privado foi criado.

>[!NOTE]
> Se você acabou de salvar o link, pode levar algum tempo para que isso entre em vigor, mesmo depois que o portal diz que a operação foi concluída. Talvez você também precise reinicializar a VM que está usando para testar a resolução DNS.

### <a name="confirm-that-the-private-dns-zone-contains-the-right-a-record"></a>Confirme se a zona de DNS privado contém o registro a correto

Usando o portal, abra a zona de DNS privado com o nome `privatelink.vaultcore.azure.net` . A página Visão geral mostra todos os registros. Por padrão, haverá um registro com nome `@` e tipo, o que `SOA` significa início de autoridade. Não toque.

Para que a resolução de nome do cofre de chaves funcione, deve haver um `A` registro com o nome do cofre simples sem sufixo ou pontos. Por exemplo, se o nome do host for `fabrikam.vault.azure.net` , deve haver um `A` registro com o nome `fabrikam` , sem nenhum sufixo ou ponto.

Além disso, o valor do `A` registro (o endereço IP) deve ser [o endereço IP privado do cofre de chaves](#find-the-key-vault-private-ip-address-in-the-virtual-network). Se você encontrar o `A` registro, mas ele contiver o endereço IP errado, você deverá remover o endereço IP errado e adicionar um novo. É recomendável que você remova todo o `A` registro e adicione um novo.

>[!NOTE]
> Sempre que você remover ou modificar um `A` registro, o computador ainda poderá ser resolvido para o endereço IP antigo, pois o valor TTL (vida útil) talvez ainda não tenha expirado. É recomendável que você sempre especifique um valor de TTL que não seja menor que 60 segundos (um minuto) e não maior que 600 segundos (10 minutos). Se você especificar um valor muito grande, os clientes poderão levar muito tempo para se recuperar de interrupções.

### <a name="dns-resolution-for-more-than-one-virtual-network"></a>Resolução DNS para mais de uma rede virtual

Se houver várias redes virtuais e cada uma tiver seu próprio recurso de ponto de extremidade privado referenciando o mesmo cofre de chaves, o nome do host do cofre de chaves precisará ser resolvido para um endereço IP privado diferente, dependendo da rede. Isso significa que várias zonas de DNS privado também são necessárias, cada uma vinculada a uma rede virtual diferente e o uso de um endereço IP diferente no `A` registro.

Em cenários mais avançados, as redes virtuais podem ter o emparelhamento habilitado. Nesse caso, apenas uma rede virtual precisa do recurso de ponto de extremidade privado, embora ambos precisem ser vinculados ao recurso de zona de DNS privado. Esse cenário não é coberto diretamente por este documento.

### <a name="understand-that-you-have-control-over-dns-resolution"></a>Entenda que você tem controle sobre a resolução de DNS

Conforme explicado na [seção anterior](#key-vault-with-private-link-resolving-from-arbitrary-internet-machine), um cofre de chaves com links privados tem o alias `{vaultname}.privatelink.vaultcore.azure.net` em seu registro *público* . O servidor DNS usado pela rede virtual usa o registro público, mas verifica cada alias para um registro *privado* e, se um for encontrado, ele interromperá os seguintes aliases definidos no registro público.

Essa lógica significa que, se a rede virtual estiver vinculada a uma zona de DNS privado com nome `privatelink.vaultcore.azure.net` e o registro de DNS público para o cofre de chaves tiver o alias `fabrikam.privatelink.vaultcore.azure.net` (Observe que o sufixo do nome do host do cofre de chaves corresponde exatamente ao nome da zona de DNS privado), a consulta DNS procurará um `A` registro com o nome `fabrikam` *na zona* de DNS privado. Se o `A` registro for encontrado, seu endereço IP será retornado na consulta DNS e nenhuma outra pesquisa será executada no registro de DNS público.

Como você pode ver, a resolução de nomes está sob seu controle. Os motivos para esse design são:

- Você pode ter um cenário complexo que envolve servidores DNS personalizados e integração com redes locais. Nesse caso, você precisa controlar como os nomes são convertidos em endereços IP.
- Talvez seja necessário acessar um cofre de chaves sem links privados. Nesse caso, resolver o nome do host da rede virtual deve retornar o endereço IP público e isso ocorre porque os cofres de chaves sem links privados não têm o `privatelink` alias no registro de nome.

## <a name="7-validate-that-requests-to-key-vault-use-private-link"></a>7. validar que as solicitações para o cofre de chaves usam o link privado

### <a name="query-the-healthstatus-endpoint-of-the-key-vault"></a>Consultar o `/healthstatus` ponto de extremidade do cofre de chaves

O cofre de chaves fornece o `/healthstatus` ponto de extremidade, que pode ser usado para diagnóstico. Os cabeçalhos de resposta incluem o endereço IP de origem, como visto pelo serviço de cofre de chaves. Você pode chamar esse ponto de extremidade com o seguinte comando (**Lembre-se de usar o nome de host do cofre de chaves**):

Windows (PowerShell):

```powershell
PS C:\> $(Invoke-WebRequest -UseBasicParsing -Uri https://fabrikam.vault.azure.net/healthstatus).Headers
```

```output
Key                           Value
---                           -----
Pragma                        no-cache
x-ms-request-id               3729ddde-eb6d-4060-af2b-aac08661d2ec
x-ms-keyvault-service-version 1.2.27.0
x-ms-keyvault-network-info    addr=10.4.5.6;act_addr_fam=InterNetworkV6;
Strict-Transport-Security     max-age=31536000;includeSubDomains
Content-Length                4
Cache-Control                 no-cache
Content-Type                  application/json; charset=utf-8
```

Linux ou uma versão recente do Windows 10 que inclui `curl` :

```console
joe@MyUbuntu:~$ curl -i https://fabrikam.vault.azure.net/healthstatus
```

```output
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: application/json; charset=utf-8
x-ms-request-id: 6c090c46-0a1c-48ab-b740-3442ce17e75e
x-ms-keyvault-service-version: 1.2.27.0
x-ms-keyvault-network-info: addr=10.4.5.6;act_addr_fam=InterNetworkV6;
Strict-Transport-Security: max-age=31536000;includeSubDomains
Content-Length: 4
```

Se você não estiver obtendo uma saída semelhante a essa, ou se receber um erro de rede, significa que o cofre de chaves não está acessível por meio do nome de host especificado ( `fabrikam.vault.azure.net` no exemplo). O nome do host não está resolvendo o endereço IP correto ou você tem um problema de conectividade na camada de transporte. Pode ser causado por problemas de roteamento, quedas de pacote e outros motivos. Você precisa investigar mais.

A resposta deve incluir o cabeçalho `x-ms-keyvault-network-info` :

```console
x-ms-keyvault-network-info: addr=10.4.5.6;act_addr_fam=InterNetworkV6;
```

O `addr` campo no `x-ms-keyvault-network-info` cabeçalho mostra o endereço IP da origem da solicitação. Esse endereço IP pode ser um dos seguintes:

- O endereço IP privado do computador que está fazendo a solicitação. Isso indica que a solicitação está usando links privados ou pontos de extremidade de serviço. Esse é o resultado esperado para links privados.
- Algum outro endereço IP privado, *não* da máquina que faz a solicitação. Isso indica que algum roteamento personalizado é eficaz. Ele ainda indica que a solicitação está usando links privados ou pontos de extremidade de serviço.
- Algum endereço IP público. Isso indica que a solicitação está sendo roteada para a Internet por meio de um dispositivo de gateway (NAT). Isso indica que a solicitação não está usando links privados e algum problema precisa ser corrigido. Os motivos comuns para isso são 1) o ponto de extremidade privado não está em estado aprovado e com êxito; e 2) o nome do host não está resolvendo para o endereço IP privado do Key Vault. Este artigo inclui ações de solução de problemas para ambos os casos.

>[!NOTE]
> Se a solicitação para `/healthstatus` funcionar, mas o `x-ms-keyvault-network-info` cabeçalho estiver ausente, o ponto de extremidade provavelmente não será servido pelo cofre de chaves. Como os comandos acima também validam o certificado HTTPS, o cabeçalho ausente pode ser um sinal de violação.

### <a name="query-the-key-vault-ip-address-directly"></a>Consultar diretamente o endereço IP do cofre de chaves

>[!IMPORTANT]
> O acesso ao cofre de chaves sem validação de certificado HTTPS é perigoso e só pode ser usado para fins de aprendizado. O código de produção nunca deve acessar o cofre de chaves sem essa validação do lado do cliente. Mesmo que você esteja apenas Diagnosticando problemas, você pode estar sujeito a tentativas de violação que não serão reveladas se você desabilitar frequentemente a validação de certificado HTTPS em suas solicitações para o cofre de chaves.

Se você instalou uma versão recente do PowerShell, pode usar `-SkipCertificateCheck` para ignorar as verificações de certificado HTTPS, então você pode direcionar o [endereço IP do cofre de chaves](#find-the-key-vault-private-ip-address-in-the-virtual-network) diretamente:

```powershell
PS C:\> $(Invoke-WebRequest -SkipCertificateCheck -Uri https://10.1.2.3/healthstatus).Headers
```

Se você estiver usando o `curl` , poderá fazer o mesmo com o `-k` argumento:

```console
joe@MyUbuntu:~$ curl -i -k https://10.1.2.3/healthstatus
```

As respostas devem ser as mesmas da seção anterior, o que significa que ela deve incluir o `x-ms-keyvault-network-info` cabeçalho com o mesmo valor. O `/healthstatus` ponto de extremidade não se importa se você estiver usando o nome de host ou o endereço IP do cofre de chaves.

Se você vir `x-ms-keyvault-network-info` retornando um valor para a solicitação usando o nome de host do cofre de chaves e outro valor para a solicitação usando o endereço IP, cada solicitação direcionará um ponto de extremidade diferente. Consulte a explicação do `addr` campo `x-ms-keyvault-network-info` na seção anterior para decidir qual caso está errado e precisa ser corrigido.

## <a name="8-other-changes-and-customizations-that-cause-impact"></a>8. outras alterações e personalizações que causam impacto

Os itens a seguir são ações de investigação não exaustivas. Eles informarão onde procurar problemas adicionais, mas você deve ter um conhecimento avançado de rede para corrigir problemas nesses cenários.

### <a name="diagnose-custom-dns-servers-at-virtual-network"></a>Diagnosticar servidores DNS personalizados na rede virtual

No portal, abra o recurso de rede virtual. No menu à esquerda, abra **servidores DNS**. Se você estiver usando "personalizado", a resolução de DNS poderá não ser descrita neste documento. Você precisa diagnosticar como os servidores DNS estão resolvendo o nome do host do cofre de chaves.

Se você estiver usando os servidores DNS padrão fornecidos pelo Azure, esse documento inteiro será aplicável.

### <a name="diagnose-hosts-overriding-or-custom-dns-servers-at-virtual-machine"></a>Diagnosticar hosts substituindo ou servidores DNS personalizados na máquina virtual

Muitos sistemas operacionais permitem definir um endereço IP fixo explícito por nome de host, normalmente alterando o `hosts` arquivo. Eles também podem permitir a substituição dos servidores DNS. Se você usar um desses cenários, prossiga com as opções de diagnóstico específicas do sistema.

### <a name="promiscuous-proxies-fiddler-etc"></a>Proxies promíscuos (Fiddler, etc.)

Exceto quando explicitamente indicado, as opções de diagnóstico neste artigo só funcionarão se não houver nenhum proxy promíscuo presente no ambiente. Embora esses proxies sejam frequentemente instalados exclusivamente no computador que está sendo diagnosticado (o Fiddler é o exemplo mais comum), os administradores avançados podem substituir CAs (autoridades de certificação) raiz e instalar um proxy promíscuo em dispositivos de gateway que atendem a vários computadores na rede. Esses proxies podem afetar substancialmente a segurança e a confiabilidade. A Microsoft não oferece suporte a configurações que usam esses produtos.

### <a name="other-things-that-may-affect-connectivity"></a>Outras coisas que podem afetar a conectividade

Esta é uma lista não exaustiva de itens que podem ser encontrados em cenários avançados ou complexos:

- Configurações de firewall, o Firewall do Azure conectado à rede virtual ou uma solução de firewall personalizada que está sendo implantada na rede virtual ou no computador.
- Emparelhamento de rede, que pode afetar quais servidores DNS são usados e como o tráfego é roteado.
- Soluções de gateway personalizado (NAT), que podem afetar como o tráfego é roteado, incluindo o tráfego de consultas DNS.