---
title: Guia de solução de problemas – DNS do Azure
description: Neste roteiro de aprendizagem, comece a solucionar problemas comuns com o DNS do Azure
services: dns
author: rohinkoul
ms.service: dns
ms.topic: troubleshooting
ms.date: 09/20/2019
ms.author: rohink
ms.openlocfilehash: fae63c61949302e25c9dee2899577fa4f0d2a975
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94965571"
---
# <a name="azure-dns-troubleshooting-guide"></a>Guia de solução de problemas do DNS do Azure

Este artigo apresenta informações sobre a solução de problemas de DNS do Azure.

Se essas etapas não resolverem o problema, também é possível procurar ou postar o problema na nossa [página de perguntas e respostas da Microsoft para suporte da comunidade](/answers/topics/azure-virtual-network.html). Como alternativa, abra uma solicitação de Suporte do Azure.


## <a name="i-cant-create-a-dns-zone"></a>Não consigo criar uma zona DNS

Para resolver problemas comuns, tente uma ou mais das etapas a seguir:

1.  Examine os logs de auditoria do DNS do Azure para determinar o motivo da falha.
2.  Cada nome de zona DNS deve ser exclusivo dentro de seu grupo de recursos. Ou seja, duas zonas DNS com o mesmo nome não podem compartilhar um grupo de recursos. Tente usar um nome de zona diferente ou outro grupo de recursos.
3.  Será exibido um erro "Você atingiu ou excedeu o número máximo de zonas na assinatura {ID da assinatura}." Use uma assinatura do Azure diferente, exclua algumas zonas ou entre em contato com o Suporte do Azure para aumentar o limite de sua assinatura.
4.  Você verá um erro "A zona '{nome da zona}' não está disponível." Esse erro significa que o DNS do Azure não pôde alocar servidores de nome para esta zona DNS. Tente usar um nome de zona diferente. Como alternativa, se você for o proprietário do nome de domínio, pode entrar em contato com o Suporte do Azure, que poderá alocar servidores de nomes para você.


### <a name="recommended-articles"></a>Artigos recomendados

* [Registros e zonas DNS](dns-zones-records.md)
* [Criar uma zona DNS](./dns-getstarted-portal.md)

## <a name="i-cant-create-a-dns-record"></a>Não consigo criar um registro DNS

Para resolver problemas comuns, tente uma ou mais das etapas a seguir:

1.  Examine os logs de auditoria do DNS do Azure para determinar o motivo da falha.
2.  O conjunto de registros já existe?  O DNS do Azure gerencia registros usando *conjuntos* de registros, que são a coleção de registros com o mesmo nome e o mesmo tipo. Se já existe um registro com o mesmo nome e o mesmo tipo, para adicionar outro registro desse você deve editar o conjunto de registros existente.
3.  Você está tentando criar um registro no ápice da zona DNS (na 'raiz' da zona)? Se sim, a convenção DNS é usar o caractere “@” como o nome do registro. Observe também que os padrões de DNS não permitem registros CNAME no ápice da zona.
4.  Existe um conflito de CNAME?  Os padrões de DNS não permitem um registro CNAME com o mesmo nome de um registro de qualquer outro tipo. Se já houver um CNAME existente, a criação de um registro com o mesmo nome de um tipo diferente falhará.  Da mesma forma, a criação de um CNAME falhará se o nome corresponder a um registro existente de um tipo diferente. Remova o conflito removendo o outro registro ou escolha um nome de registro diferente.
5.  Você atingiu o limite do número de conjuntos de registros permitidos em uma zona DNS? O número atual de conjuntos de registros e o número máximo de conjuntos de registros são mostrados no Portal do Azure, nas 'Propriedades' da zona. Se você atingiu esse limite, exclua alguns conjuntos de registros ou entre em contato com o Suporte do Azure para aumentar seu limite de conjuntos de registros para essa zona. Em seguida, tente novamente. 


### <a name="recommended-articles"></a>Artigos recomendados

* [Registros e zonas DNS](dns-zones-records.md)
* [Criar uma zona DNS](./dns-getstarted-portal.md)



## <a name="i-cant-resolve-my-dns-record"></a>Não consigo resolver meu registro DNS

A resolução de nome DNS é um processo de várias etapas que pode falhar por várias razões. As etapas abaixo ajudam a investigar por que a resolução DNS está falhando para um registro DNS em uma zona hospedada no DNS do Azure.

1.  Confirme se os registros DNS foram configurados corretamente no DNS do Azure. Examine os registros DNS no Portal do Azure, verificando se o nome da zona, o nome do registro e o tipo de registro estão corretos.
2.  Confirme se os registros DNS são resolvidos corretamente nos servidores de nomes DNS do Azure.
    - Se você faz consultas DNS do seu computador local, podem aparecer resultados em cache que não refletem o estado atual dos servidores de nomes.  Além disso, as redes corporativas geralmente usam servidores proxy DNS que impedem que as consultas DNS sejam direcionadas a servidores de nomes específicos.  Para evitar esses problemas, use um serviço de resolução de nome baseado na Web, como [digwebinterface](https://digwebinterface.com).
    - Especifique os servidores de nomes corretos para sua zona DNS, conforme exibido no Portal do Azure.
    - Verifique se o nome DNS está correto (será necessário especificar o nome totalmente qualificado, incluindo o nome da zona) e se o tipo de registro está correto
3.  Confirme se o nome de domínio DNS foi [delegado corretamente aos servidores de nomes DNS do Azure](dns-domain-delegation.md). Há [vários sites da Web de terceiros que oferecem validação de delegação DNS](https://www.bing.com/search?q=dns+check+tool). Esse é um teste de delegação de *zona*, portanto você deve inserir apenas o nome da zona DNS e não o nome totalmente qualificado do registro.
4.  Após a conclusão da ação acima, o registro DNS deverá ser resolvido corretamente. Para verificar, você pode usar [digwebinterface](https://digwebinterface.com) novamente, desta vez, usando as configurações de servidor de nomes padrão.


### <a name="recommended-articles"></a>Artigos recomendados

* [Delegar um domínio ao DNS do Azure](dns-domain-delegation.md)



## <a name="how-do-i-specify-the-service-and-protocol-for-an-srv-record"></a>Como especificar o 'serviço' e o 'protocolo' para um registro SRV?

O DNS do Azure gerencia os registros DNS como conjuntos de registros, ou seja, a coleção de registros com o mesmo nome e o mesmo tipo. Para um conjunto de registros SRV, o 'serviço' e o 'protocolo' precisam ser especificados como parte do nome do conjunto de registros. Os outros parâmetros de SRV ('prioridade', 'peso', 'porta' e 'destino') são especificados separadamente para cada registro no conjunto de registros.

Exemplo de nomes de registro SRV (nome de serviço 'sip', protocolo 'tcp'):

- \_sip.\_tcp (cria um conjunto de registros no ápice da zona)
- \_sip.\_tcp.sipservice (cria um conjunto de registros chamado 'sipservice')

### <a name="recommended-articles"></a>Artigos recomendados

* [Registros e zonas DNS](dns-zones-records.md)
* [Criar registros e conjuntos de registros DNS usando o portal do Azure](./dns-getstarted-portal.md)
* [Tipo de registro SRV (Wikipédia)](https://en.wikipedia.org/wiki/SRV_record)


## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [registros e zonas DNS do Azure](dns-zones-records.md)
* Para começar a usar o DNS do Azure, aprenda a [criar uma zona DNS](./dns-getstarted-portal.md) e a [criar registros DNS](./dns-getstarted-portal.md).
* Para migrar uma zona DNS existente, saiba como [importar e exportar um arquivo de zona DNS](dns-import-export.md).