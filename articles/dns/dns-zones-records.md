---
title: Visão geral de Registros e Zonas DNS - DNS do Azure | Microsoft Docs
description: Visão geral do suporte à hospedagem de zonas e registros DNS no DNS do Microsoft Azure.
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
editor: ''
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: victorh
ms.openlocfilehash: 7da382a644c1db92b9915f1d3f1f3a459e8893b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60563321"
---
# <a name="overview-of-dns-zones-and-records"></a>Visão geral de zonas e registros DNS

Esta página explica os principais conceitos de domínios, zonas DNS, conjuntos de registros e registros DNS, e como eles têm suporte no DNS do Azure.

## <a name="domain-names"></a>Nomes de domínio

O Sistema de Nomes de Domínio é uma hierarquia de domínios. A hierarquia começa no domínio 'raiz', cujo nome é simplesmente '**.**'.  Abaixo dele vêm domínios de nível superior, como 'com', 'net', 'org', 'uk' ou 'jp'.  Abaixo desses estão domínios de segundo nível, como 'org.uk' ou 'co.jp'. Os domínios na hierarquia de DNS são distribuídos globalmente, hospedados por servidores de nomes DNS em todo o mundo.

Um registrador de nomes de domínio é uma organização que permite a você adquirir um nome de domínio, como 'contoso.com'.  A aquisição de um nome de domínio lhe dá o direito de controlar a hierarquia de DNS com esse nome, por exemplo, permitindo que você direcione o nome 'www.contoso.com' para o site da sua empresa. O registrador pode hospedar o domínio em seus próprios servidores de nomes em seu nome ou você permitir que você especifique servidores de nomes alternativos.

O DNS do Azure fornece uma infraestrutura de servidores de nomes de alta disponibilidade e distribuída globalmente que você pode usar para hospedar seu domínio. Ao hospedar seus domínios no DNS do Azure, você poderá gerenciar seus registros DNS usando as mesmas credenciais, APIs, ferramentas, cobrança e suporte que seus outros serviços do Azure.

O DNS do Azure não dá suporte a compra de nomes de domínio. Se você deseja adquirir um nome de domínio, precisa usar um registrador de nomes de domínio de terceiros. O registrador normalmente cobra uma pequena taxa anual. Os domínios, em seguida, podem ser hospedados no DNS do Azure para gerenciamento de registros DNS. Consulte [Delegar um domínio ao DNS do Azure](dns-domain-delegation.md) para obter detalhes.

## <a name="dns-zones"></a>Zonas DNS

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>Registros DNS

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>Vida útil

O tempo de vida, ou TTL, especifica por quanto tempo cada registro é armazenado em cache pelos clientes antes de ser consultado. No exemplo acima, o TTL tem 3600 segundos ou 1 hora.

No DNS do Azure, o TTL é especificado para o conjunto de registros, não para cada registro, portanto, o mesmo valor é usado para todos os registros no conjunto de registros.  Você pode especificar qualquer valor de TTL entre 1 e 2.147.483.647 segundos.

### <a name="wildcard-records"></a>Registros curinga

O DNS do Azure dá suporte a [registros curinga](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Registros curinga são retornados como resposta a consultas com um nome correspondente (a menos que haja uma correspondência mais próxima de um conjunto de registros não curinga). O DNS do Azure dá suporte a conjuntos de registros curinga para todos os tipos de registro, exceto NS e SOA.

Para criar um conjunto de registros curinga, use o nome do conjunto de registros '\*'. Como alternativa, você também pode usar um nome com '\*'como seu rótulo na extremidade esquerda, por exemplo,'\*.foo '.

### <a name="caa-records"></a>Registros CAA

Os registros CAA permitem aos proprietários do domínio especificar quais ACs (Autoridades de Certificação) estão autorizadas a emitir certificados para seus domínios. Isso permite que as ACs evitem emitir certificados incorretamente em algumas circunstâncias. Os registros CAA têm três propriedades:
* **Sinalizadores de**: Esse é um inteiro entre 0 e 255, usado para representar o sinalizador crítico que tem um significado especial pelo [RFC](https://tools.ietf.org/html/rfc6844#section-3)
* **Marcação**: uma string ASCII que pode ser uma das seguintes:
    * **issue**: use essa opção se desejar especificar as ACs que têm permissão para emitir certificados (todos os tipos)
    * **issuewild**: use essa opção se desejar especificar as ACs que têm permissão para emitir certificados (somente certificados curinga)
    * **iodef**: especifique um endereço de email ou o nome do host o qual as ACs podem notificar para solicitações de problema de certificado não autorizado
* **Valor**: o valor da Marcação específica escolhida

### <a name="cname-records"></a>Registros CNAME

Conjuntos de registros CNAME não podem coexistir com outros conjuntos de registros com o mesmo nome. Por exemplo, você não pode criar um conjunto de registros CNAME com o nome relativo 'www' e um registro A com o nome relativo 'www' ao mesmo tempo.

Como o ápice da zona (nome = '\@') sempre contém os conjuntos de registro NS e SOA criados quando a zona foi criada, não é possível criar um conjunto de registros CNAME no ápice da zona.

Essas restrições são provenientes dos padrões DNS e não são limitações do DNS do Azure.

### <a name="ns-records"></a>Registros NS

O conjunto de registros de NS no apex da zona (nome '\@') é criado automaticamente com cada zona DNS e excluído automaticamente quando a zona é excluída (não pode ser excluído separadamente).

Esse conjunto de registros contém os nomes dos servidores de nome DNS do Azure atribuídos à zona. Você pode adicionar servidores de nome adicionais a esse conjunto de registros NS para dar suporte à coospedagem de domínios com mais de um provedor DNS. Você também pode modificar o TTL e os metadados para esse conjunto de registros. No entanto, você não pode remover nem modificar os servidores de nome DNS do Azure previamente populados. 

Isso se aplica somente ao registro NS definido no ápice da zona. Outros conjuntos de registros NS na sua zona (conforme utilizados para delegar zonas filho) podem ser criados, modificados, e excluídos sem restrição.

### <a name="soa-records"></a>Registros SOA

Um conjunto de registros SOA é criado automaticamente no ápice de cada zona (nome ='\@') e é excluído automaticamente quando a zona é excluída.  Os registros SOA não podem ser criados ou excluídos separadamente.

Você pode modificar todas as propriedades do registro SOA, exceto a propriedade 'host', que é pré-configurada para se referir ao nome do servidor de nomes primário fornecido pelo DNS do Azure.

### <a name="spf-records"></a>Registros SPF

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="srv-records"></a>Registros SRV

Os [registros SRV](https://en.wikipedia.org/wiki/SRV_record) são usados por vários serviços para especificar locais de servidor. Ao especificar um registro SRV no DNS do Azure:

* O *serviço* e o *protocolo* devem ser especificados como parte do nome do conjunto de registros, prefixado com sublinhados.  Por exemplo, '\_sip.\_tcp.nome'.  Para um registro no ápice da zona, não é necessário especificar '\@' no nome do registro; basta usar o serviço e o protocolo, por exemplo, '\_sip.\_tcp'.
* A *prioridade*, o *peso*, a *porta* e o *destino* são especificados como parâmetros de cada registro no conjunto de registros.

### <a name="txt-records"></a>Registros TXT

Os registros TXT são usados para mapear nomes de domínio em cadeias de caracteres de texto arbitrárias. Eles são usados em vários aplicativos, em particular relacionados à configuração de email, como o [SPF (Sender Policy Framework)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) e o [DKIM (DomainKeys Identified Mail)](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail).

Os padrões do DNS permitem que um único registro TXT contenha várias cadeias de caracteres, cada uma delas pode ter até 254 caracteres. Quando várias cadeias de caracteres são usadas, elas são concatenadas pelos clientes e tratadas como uma única cadeia de caracteres.

Ao chamar a API REST de DNS do Azure, é necessário especificar cada cadeia de caracteres TXT separadamente.  Ao usar interfaces do Portal do Azure, PowerShell ou CLI é necessário especificar uma cadeia de caracteres única por registro, que é dividida automaticamente em segmentos de 254 caracteres, se necessário.

As várias cadeias de caracteres em um registro DNS não devem ser confundidas com os vários registros TXT em um conjunto de registros TXT.  Um conjunto de registros TXT pode conter vários registros e *cada um deles* pode conter várias cadeias de caracteres.  O DNS do Azure dá suporte a um tamanho total de até 1024 caracteres em cada conjunto de registros TXT (entre todos os registros combinados).

## <a name="tags-and-metadata"></a>Marcas e metadados

### <a name="tags"></a>Marcas

As marcas consistem em uma lista de pares de nome/valor e são usadas pelo Azure Resource Manager na rotulagem de recursos.  O Azure Resource Manager usa marcas para habilitar exibições filtradas de sua fatura do Azure e também permite que você defina uma política sobre as marcas que são obrigatórias. Para obter mais informações sobre marcas, consulte [Usando marcas para organizar os recursos do Azure](../azure-resource-manager/resource-group-using-tags.md).

O DNS do Azure dá suporte ao uso de marcas do Azure Resource Manager em recursos de zona DNS.  Ele não oferece suporte a marcas em conjuntos de registros DNS, embora "metadados" tenham suporte nos Conjuntos de registros DNS como uma alternativa, conforme explicado abaixo.

### <a name="metadata"></a>Metadados

Como uma alternativa às marcas de conjunto de registros, o DNS do Azure dá suporte à anotação de conjuntos de registros usando 'metadados'.  Semelhante às marcas, os metadados permitem que você associe pares de nome-valor a cada conjunto de registros.  Isso pode ser útil, por exemplo, para registrar a finalidade de cada conjunto de registros.  Diferentemente das marcas, os metadados não podem ser usados para fornecer uma exibição filtrada de sua fatura do Azure e não podem ser especificados em uma política do Azure Resource Manager.

## <a name="etags"></a>ETags

Suponha que duas pessoas ou dois processos tentem modificar um registro DNS ao mesmo tempo. Qual vence? E o vencedor sabe que substituiu as alterações criadas por outra pessoa?

O DNS do Azure usa as Etags para tratar alterações simultâneas para o mesmo recurso com segurança. As Etags são separadas das ["Marcações" do Azure Resource Manager](#tags). Cada recurso DNS (zona ou conjunto de registros) tem uma Etag associada a ele. Sempre que um recurso é recuperado, a Etag também é recuperada. Ao atualizar um recurso, você pode escolher devolver a Etag para que o DNS do Azure possa verificar se a Etag no servidor é correspondente. Uma vez que cada atualização a um recurso resulta em uma Etag sendo gerada novamente, uma incompatibilidade de Etag indica que ocorreu uma alteração simultânea. As Etags também podem ser usadas ao criar um novo recurso para garantir que o recurso ainda não existe.

Por padrão, o PowerShell do DNS do Azure usa as Etags bloquear alterações simultâneas às zonas e conjuntos de registro. O switch opcional *-Overwrite* pode ser usado para suprimir as verificações de Etag. Nesse caso, as alterações simultâneas que ocorrerem são substituídas.

No nível da API REST do DNS do Azure, as Etags são especificadas usando cabeçalhos HTTP.  Seu comportamento é descrito na tabela a seguir:

| Cabeçalho | Comportamento |
| --- | --- |
| Nenhum |PUT sempre terá êxito (nenhuma verificação de Etag) |
| If-match <etag> |PUT só terá êxito se o recurso existir e a Etag corresponder |
| If-match * |PUT só terá êxito se houver recursos |
| If-none-match * |PUT só terá êxito se não houver recursos |


## <a name="limits"></a>limites

Os limites padrão abaixo se aplicam ao usar o DNS do Azure:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>Próximas etapas

* Para começar a usar o DNS do Azure, aprenda a [criar uma zona DNS](dns-getstarted-create-dnszone-portal.md) e a [criar registros DNS](dns-getstarted-create-recordset-portal.md).
* Para migrar uma zona DNS existente, saiba como [importar e exportar um arquivo de zona DNS](dns-import-export.md).
