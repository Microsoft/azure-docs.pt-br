---
title: Visão geral dos registros de alias do DNS do Azure
description: Visão geral do suporte para registros de alias no DNS do Microsoft Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 3/21/2019
ms.author: victorh
ms.openlocfilehash: 87ca7cae8e9170c8c437d0961cb1acb2e0dd0eb1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60790531"
---
# <a name="azure-dns-alias-records-overview"></a>Visão geral dos registros de alias do DNS do Azure

Os registros de alias do DNS do Azure são qualificações em um conjunto de registros DNS. Eles podem fazer referência a outros recursos do Azure dentro de sua zona DNS. Por exemplo, você pode criar um conjunto de registros de alias que faça referência a um endereço IP público do Azure em vez de um registro A. Seu alias registra os pontos definidos em uma instância do serviço de endereço IP público do Azure dinamicamente. Como resultado, o conjunto de registros de alias se atualiza continuamente durante a resolução de DNS.

Um conjunto de registros de alias é suportado para os seguintes tipos de registro em uma zona DNS do Azure: 

- O 
- AAAA
- CNAME

> [!NOTE]
> Se você pretende usar um registro de alias para os tipos de registros A ou AAAA para apontar para um [perfil do Gerenciador de Tráfego do Azure](../traffic-manager/quickstart-create-traffic-manager-profile.md), verifique se o perfil do Gerenciador de Tráfego tem apenas [pontos de extremidade externos](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints). Você deve fornecer o endereço IPv4 ou IPv6 para pontos de extremidade externos no Gerenciador de Tráfego. O ideal é usar endereços IP estáticos.

## <a name="capabilities"></a>Funcionalidades

- **Aponte para um recurso IP público de um conjunto de registros DNS A / AAAA.** Você pode criar um conjunto de registros A / AAAA e torná-lo um conjunto de registros de alias para apontar para um recurso IP público. O conjunto de registros de DNS é automático se o endereço IP público é alterado ou é excluído. Registros DNS pendentes que apontem para endereços IP incorretos são evitados.

- **Aponte para um perfil do Gerenciador de Tráfego de um conjunto de registros DNS A / AAAA / CNAME.** Você pode criar um conjunto de registros CNAME ou A/AAAA e usar registros de alias para apontar para um perfil do Gerenciador de Tráfego. É especialmente útil quando você precisa rotear o tráfego no ápice da zona, uma vez que registros CNAME tradicionais não têm suporte para um ápice da zona. Por exemplo, digamos que seu perfil do Gerenciador de Tráfego seja myprofile.trafficmanager.net e sua zona DNS empresarial seja contoso.com. Você poderá criar um conjunto de registros de alias do tipo um/AAAA para contoso.com (apex da zona) e apontar para myprofile.trafficmanager.net.
- **Aponte para um ponto de extremidade do Azure Content Delivery Network (CDN)**. Isso é útil quando você cria sites estático usando o armazenamento do Azure e CDN do Azure.
- **Apontar para outro conjunto de registros DNS dentro da mesma zona.** Registros de alias podem referenciar outros conjuntos de registros do mesmo tipo. Por exemplo, um conjunto de registros DNS CNAME pode ser um alias para outro conjunto de registros CNAME. Essa organização é útil se você quiser que alguns conjuntos de registros sejam aliases e alguns não-aliases.

## <a name="scenarios"></a>Cenários

Existem alguns cenários comuns para registros de alias.

### <a name="prevent-dangling-dns-records"></a>Impedir que os registros DNS pendentes

Um problema comum nos registros DNS tradicionais é o registro de registros pendentes. Por exemplo, registros DNS que não foram atualizados para refletir as alterações nos endereços IP. O problema ocorre especialmente com tipos de registro A/AAAA ou CNAME.

Com um registro de zona DNS tradicional, se o CNAME ou o IP de destino não existir, o registro DNS associado a ele deverá ser atualizado manualmente. Em algumas organizações, uma atualização manual pode não acontecer a tempo devido a problemas no processo ou à separação de funções e níveis de permissão associados. Por exemplo, uma função pode ter autoridade para excluir um CNAME ou endereço IP que pertence a um aplicativo. Mas não tem autoridade suficiente para atualizar o registro DNS que aponta para esses destinos. Um atraso ao atualizar o registro DNS pode causar uma interrupção para os usuários.

Registros de alias previnem referências pendentes acoplando fortemente o ciclo de vida de um registro DNS a um recurso do Azure. Por exemplo, considere um registro DNS qualificado como um registro de alias para apontar para um endereço IP público ou um perfil do Gerenciador de Tráfego. Se esses recursos subjacentes forem excluídos, o registro do alias do DNS será removido ao mesmo tempo.

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>Atualize o conjunto de registros DNS automaticamente quando os endereços IP do aplicativo mudam

Este cenário é semelhante ao anterior. Talvez um aplicativo seja movido ou a máquina virtual subjacente seja reiniciada. Um registro de alias é atualizado automaticamente quando o endereço IP é alterado para o recurso IP público subjacente. Isso evita possíveis riscos à segurança de direcionar os usuários para outro aplicativo que recebeu o endereço IP público antigo.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Hospedar aplicativos com balanceamento de carga no ápice da zona

O protocolo DNS previne a atribuição de registros CNAME no ápice da zona. Por exemplo, se o domínio for contoso.com, você poderá criar registros CNAME para somelable.contoso.com, mas não poderá criar um CNAME para contoso.com em si.
Essa restrição apresenta um problema para os proprietários de aplicativos que têm aplicativos com balanceamento de carga por trás do [Gerenciador de Tráfego do Azure](../traffic-manager/traffic-manager-overview.md). Uma vez que usar um perfil do Gerenciador de Tráfego exige a criação de um registro CNAME, não é possível apontar o perfil do Gerenciador de Tráfego do ápice da zona.

Esse problema pode ser resolvido usando os registros de alias. Ao contrário de registros CNAME, registros de alias podem ser criados no apex da zona e os proprietários do aplicativo podem usá-lo para apontar seu registro Apex de zona para um perfil do Gerenciador de Tráfego que tem pontos de extremidade externos. Os proprietários de aplicativos podem apontar para o mesmo perfil do Gerenciador de tráfego que é usado para qualquer outro domínio em sua zona DNS.

Por exemplo, contoso.com e www\.contoso.com pode apontar para o mesmo perfil do Gerenciador de tráfego. Para saber mais sobre como usar os registros de alias com perfis do Gerenciador de Tráfego do Azure, confira a seção Próximas etapas.

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>Aponte o apex de zona para pontos de extremidade da CDN do Azure

Assim como um perfil do Gerenciador de tráfego, você também pode usar os registros de alias para apontar o ápice da zona DNS para pontos de extremidade da CDN do Azure. Isso é útil quando você cria sites estático usando o armazenamento do Azure e CDN do Azure. Em seguida, você pode acessar o site sem prefixação "www" para seu nome DNS.

Por exemplo, se seu site estático chamado www.contoso.com, seus usuários podem acessar seu site usando contoso.com sem a necessidade de prefixar www ao nome DNS.

Conforme descrito anteriormente, os registros CNAME não têm suporte no ápice da zona. Portanto, é possível usar um registro CNAME para apontar o contoso.com para o ponto de extremidade CDN. Em vez disso, você pode usar um registro de alias para apontar o ápice da zona para um ponto de extremidade CDN diretamente.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre registros de alias, consulte os seguintes artigos:

- [Tutorial: Configurar um registro de alias para se referir a um endereço IP público do Azure](tutorial-alias-pip.md)
- [Tutorial: Configurar um registro de alias para dar suporte a nomes de domínio com o Gerenciador de Tráfego do Apex](tutorial-alias-tm.md)
- [PERGUNTAS FREQUENTES SOBRE O DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
