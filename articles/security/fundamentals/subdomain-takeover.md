---
title: Impedir takeovers de subdomínio com registros de alias de DNS do Azure e verificação de domínio personalizado do serviço de Azure App
description: Saiba como evitar a ameaça comum de alta gravidade do subdomínio tomada
services: security
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: c3a821156074727d02ab36cf88f3e998756b8cc4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389443"
---
# <a name="prevent-dangling-dns-entries-and-avoid-subdomain-takeover"></a>Impedir entradas DNS pendente e evitar subdomínio tomada

Este artigo descreve a ameaça de segurança comum do subdomínio tomada e as etapas que você pode executar para atenuá-lo.


## <a name="what-is-a-subdomain-takeover"></a>O que é um subdomínio tomada?

Subdomínio takeovers são uma ameaça comum e de alta gravidade para as organizações que criam e excluem muitos recursos regularmente. Um subdomínio tomada pode ocorrer quando você tem um [registro DNS](../../dns/dns-zones-records.md#dns-records) que aponta para um recurso do Azure desprovisionado. Esses registros DNS também são conhecidos como entradas "DNS pendentes". Os registros CNAME são particularmente vulneráveis a essa ameaça. As invasões de subdomínio permitem que os atores mal-intencionados redirecionem o tráfego destinado ao domínio de uma organização a um site que executa atividades mal-intencionadas.

Um cenário comum para um subdomínio tomada:

1. **CRIAÇÃO**

    1. Você provisiona um recurso do Azure com um FQDN (nome de domínio totalmente qualificado) do `app-contogreat-dev-001.azurewebsites.net` .

    1. Você atribui um registro CNAME em sua zona DNS com o subdomínio `greatapp.contoso.com` que roteia o tráfego para o recurso do Azure.

1. **DESPROVISIONAMENTO**

    1. O recurso do Azure é desprovisionado ou excluído depois que ele não é mais necessário. 
    
        Neste ponto, o registro CNAME `greatapp.contoso.com` *deve* ser removido da zona DNS. Se o registro CNAME não for removido, ele será anunciado como um domínio ativo, mas não roteará o tráfego para um recurso ativo do Azure. Esta é a definição de um registro DNS "pendente".

    1. O subdomínio pendente, `greatapp.contoso.com` , agora é vulnerável e pode ser obtido ao ser atribuído a outro recurso de assinatura do Azure.

1. **TOMADA**

    1. Usando métodos e ferramentas comumente disponíveis, um ator de ameaça descobre o subdomínio pendente.  

    1. O ator de ameaça provisiona um recurso do Azure com o mesmo FQDN do recurso que você controleu anteriormente. Neste exemplo, `app-contogreat-dev-001.azurewebsites.net`.

    1. O tráfego que está sendo enviado para o subdomínio `greatapp.contoso.com` agora é roteado para o recurso do ator mal-intencionado em que ele controla o conteúdo.



![Subdomínio tomada de um site desprovisionado](./media/subdomain-takeover/subdomain-takeover.png)



## <a name="the-risks-of-subdomain-takeover"></a>Os riscos de subdomínio tomada

Quando um registro DNS aponta para um recurso que não está disponível, o próprio registro deve ter sido removido da zona DNS. Se ele não tiver sido excluído, será um registro de "DNS pendente" e criará a possibilidade de subdomínio tomada.

As entradas DNS pendente possibilitam que os atores de ameaça assumam o controle do nome DNS associado para hospedar um site ou serviço mal-intencionado. Páginas e serviços mal-intencionados no subdomínio de uma organização podem resultar em:

- **Perda de controle sobre o conteúdo do subdomínio** -negativo Pressione sobre a incapacidade da sua organização de proteger seu conteúdo, bem como os danos à marca e a perda de confiança.

- **Coleta de cookies de visitantes desconhecidos** – é comum que os aplicativos Web exponham cookies de sessão para subdomínios (*. contoso.com), consequentemente, qualquer subdomínio pode acessá-los. Os atores de ameaça podem usar tomada de subdomínio para criar uma página de aparência autêntica, enganar os usuários que não estão fazendo a visita e colher seus cookies (até mesmo cookies seguros). Um equívoco comum é que o uso de certificados SSL protege seu site e os cookies de seus usuários, de um tomada. No entanto, um ator de ameaça pode usar o subdomínio seqüestrado para se aplicar e receber um certificado SSL válido. Os certificados SSL válidos concedem a eles acesso a cookies seguros e podem aumentar ainda mais a legitimidade percebida do site mal-intencionado.

- **Campanhas de phishing** -os subdomínios de autenticidade podem ser usados em campanhas de phishing. Isso é verdadeiro para sites mal-intencionados e para registros MX que permitiriam que o ator de ameaça recebesse emails endereçados a um subdomínio legítimo de uma marca conhecida.

- **Riscos adicionais** – sites mal-intencionados podem ser usados para escalonar para outros ataques clássicos, como XSS, CSRF, CORS bypass e muito mais.



## <a name="identify-dangling-dns-entries"></a>Identificar entradas DNS pendente

Para identificar as entradas DNS em sua organização que podem ser pendente, use as ferramentas do PowerShell hospedados pelo GitHub da Microsoft ["Get-DanglingDnsRecords"](https://aka.ms/DanglingDNSDomains).

Essa ferramenta ajuda os clientes do Azure a listar todos os domínios com um CNAME associado a um recurso existente do Azure que foi criado em suas assinaturas ou locatários.

Se os CNAMEs estiverem em outros serviços DNS e apontarem para recursos do Azure, forneça os CNAMEs em um arquivo de entrada para a ferramenta.

A ferramenta dá suporte aos recursos do Azure listados na tabela a seguir. A ferramenta extrai ou usa como entradas, todos os CNAMEs do locatário.


| Serviço                   | Digite                                        | FQDNproperty                               | Exemplo                         |
|---------------------------|---------------------------------------------|--------------------------------------------|---------------------------------|
| Porta da frente do Azure          | microsoft.network/frontdoors                | Properties. cName                           | `abc.azurefd.net`               |
| Armazenamento do Blobs do Azure        | microsoft.storage/storageaccounts           | Propriedades. primaryEndpoints. blob           | `abc. blob.core.windows.net`    |
| CDN do Azure                 | microsoft.cdn/profiles/endpoints            | Propriedades. hostName                        | `abc.azureedge.net`             |
| Endereços IP públicos       | microsoft.network/publicipaddresses         | Properties. dnsSettings. FQDN                | `abc.EastUs.cloudapp.azure.com` |
| Gerenciador de Tráfego do Azure     | microsoft.network/trafficmanagerprofiles    | Properties. dnsConfig. FQDN                  | `abc.trafficmanager.net`        |
| Azure Container Instance  | microsoft.containerinstance/containergroups | Propriedades. ipAddress. FQDN                  | `abc.EastUs.azurecontainer.io`  |
| Gerenciamento de API do Azure      | microsoft.apimanagement/service             | Propriedades. hostnameConfigurations. hostName | `abc.azure-api.net`             |
| Serviço de aplicativo do Azure         | microsoft.web/sites                         | Propriedades. DefaultHostName                 | `abc.azurewebsites.net`         |
| Azure App slots de serviço | microsoft.web/sites/slots                   | Propriedades. DefaultHostName                 | `abc-def.azurewebsites.net`     |



### <a name="prerequisites"></a>Pré-requisitos

Execute a consulta como um usuário que tem:

- pelo menos acesso de nível de leitor às assinaturas do Azure
- acesso de leitura ao grafo de recursos do Azure

Se você for um administrador global do locatário de sua organização, eleve sua conta para ter acesso a todas as assinaturas da sua organização usando as diretrizes em [elevar o acesso para gerenciar todas as assinaturas e grupos de gerenciamento do Azure](../../role-based-access-control/elevate-access-global-admin.md).


> [!TIP]
> O grafo de recursos do Azure tem limites de limitação e de paginação que você deve considerar se tiver um ambiente grande do Azure. 
> 
> [Saiba mais sobre como trabalhar com grandes conjuntos de dados de recursos do Azure](../../governance/resource-graph/concepts/work-with-data.md).
> 
> A ferramenta usa o envio em lote de assinatura para evitar essas limitações.

### <a name="run-the-script"></a>Executar o script

Saiba mais sobre o script do PowerShell, **Get-DanglingDnsRecords.ps1** e baixe-o do github: https://aka.ms/Get-DanglingDnsRecords .

## <a name="remediate-dangling-dns-entries"></a>Corrigir entradas DNS pendente 

Examine as zonas DNS e identifique os registros CNAME que são pendente ou que foram assumidos. Se os subdomínios forem considerados pendente ou tiverem sido retirados, remova os subdomínios vulneráveis e reduza os riscos com as seguintes etapas:

1. Em sua zona DNS, remova todos os registros CNAME que apontam para FQDNs de recursos que não são mais provisionados.

1. Para permitir que o tráfego seja roteado para recursos em seu controle, provisione recursos adicionais com os FQDNs especificados nos registros CNAME dos subdomínios pendente.

1. Examine o código do aplicativo para obter referências a subdomínios específicos e atualize todas as referências de subdomínio incorretas ou desatualizadas.

1. Investigue se algum comprometimento ocorreu e tome medidas de acordo com os procedimentos de resposta a incidentes de sua organização. Dicas e práticas recomendadas para investigar esse problema podem ser encontradas abaixo.

    Se a lógica do aplicativo for de tal forma que os segredos, como as credenciais do OAuth, foram enviados ao subdomínio pendente, ou que as informações confidenciais de privacidade tenham sido enviadas para os subdomínios do pendente, esses dados poderão ter sido expostos a terceiros.

1. Entenda por que o registro CNAME não foi removido da zona DNS quando o recurso foi desprovisionado e execute etapas para garantir que os registros DNS sejam atualizados adequadamente quando os recursos do Azure forem desprovisionados no futuro.


## <a name="prevent-dangling-dns-entries"></a>Impedir entradas DNS pendente

Garantir que sua organização tenha implementado processos para evitar entradas DNS pendente e o subdomínio resultante takeovers seja uma parte crucial do seu programa de segurança.

Alguns serviços do Azure oferecem recursos para auxiliar na criação de medidas preventivas e são detalhados abaixo. Outros métodos para evitar esse problema devem ser estabelecidos por meio das práticas recomendadas da sua organização ou dos procedimentos operacionais padrão.

### <a name="enable-azure-defender-for-app-service"></a>Habilitar o Azure defender para o serviço de aplicativo

A CWPP (plataforma de proteção de carga de trabalho de nuvem) integrada da central de segurança do Azure, o Azure defender, oferece uma variedade de planos para proteger seus recursos e cargas de trabalho do Azure, híbridos e de várias nuvens.

O plano do **Azure defender para o serviço de aplicativo** inclui a detecção de DNS pendente. Com esse plano habilitado, você obterá alertas de segurança se desativar um site do serviço de aplicativo, mas não remover seu domínio personalizado do seu registrador de DNS.

A proteção de DNS pendente do Azure defender está disponível se seus domínios são gerenciados com o DNS do Azure ou um registrador de domínio externo e se aplicam ao serviço de aplicativo no Windows e no Linux.

Saiba mais sobre este e outros benefícios deste plano do Azure defender na [introdução ao Azure defender para o serviço de aplicativo](../../security-center/defender-for-app-service-introduction.md).

### <a name="use-azure-dns-alias-records"></a>Usar registros de alias do DNS do Azure

Os registros de [alias](../../dns/dns-alias.md#scenarios) do DNS do Azure podem impedir referências pendentes ao acoplar o ciclo de vida de um registro DNS com um recurso do Azure. Por exemplo, considere um registro DNS qualificado como um registro de alias para apontar para um endereço IP público ou um perfil do Gerenciador de Tráfego. Se você excluir esses recursos subjacentes, o registro de alias de DNS se tornará um conjunto de registros vazio. Ele não faz mais referência ao recurso excluído. É importante observar que há limites para o que você pode proteger com registros de alias. Hoje, a lista está limitada a:

- Porta da frente do Azure
- Perfis do Gerenciador de Tráfego
- Pontos de extremidade da CDN (rede de distribuição de conteúdo) do Azure
- IPs Públicos

Apesar das ofertas de serviço limitadas atuais, recomendamos o uso de registros de alias para se defender do tomada de subdomínio sempre que possível.

[Saiba mais sobre os recursos dos registros de alias do DNS do Azure](../../dns/dns-alias.md#capabilities).



### <a name="use-azure-app-services-custom-domain-verification"></a>Usar a verificação de domínio personalizado do serviço de Azure App

Ao criar entradas DNS para Azure App serviço, crie um asuid. subdomínio Registro TXT com a ID de verificação de domínio. Quando existe um registro TXT, nenhuma outra assinatura do Azure pode validar o domínio personalizado, o que o leva. 

Esses registros não impedem que alguém crie o serviço de Azure App com o mesmo nome que está em sua entrada CNAME. Sem a capacidade de provar a propriedade do nome de domínio, os atores de ameaças não podem receber tráfego nem controlar o conteúdo.

[Saiba mais sobre como mapear um nome DNS personalizado existente para Azure app serviço](../../app-service/app-service-web-tutorial-custom-domain.md).



### <a name="build-and-automate-processes-to-mitigate-the-threat"></a>Crie e automatize processos para atenuar a ameaça

Muitas vezes, os desenvolvedores e as equipes de operações executam processos de limpeza para evitar ameaças de DNS pendente. As práticas a seguir ajudarão a garantir que sua organização Evite perder essa ameaça. 

- **Criar procedimentos para prevenção:**

    - Instrua os desenvolvedores de aplicativos a redirecionar endereços sempre que eles excluirem recursos.

    - Coloque "remover a entrada DNS" na lista de verificações necessárias ao encerrar um serviço.

    - Coloque os [bloqueios de exclusão](../../azure-resource-manager/management/lock-resources.md) em todos os recursos que tenham uma entrada DNS personalizada. Um bloqueio de exclusão serve como um indicador de que o mapeamento deve ser removido antes que o recurso seja desprovisionado. Medidas como essa podem funcionar apenas quando combinadas com programas de educação interna.

- **Criar procedimentos para descoberta:**

    - Examine os registros DNS regularmente para garantir que seus subdomínios estejam todos mapeados para os recursos do Azure que:

        - Exist-consulte as zonas DNS para ver os recursos que apontam para os subdomínios do Azure, como *. azurewebsites.net ou *. cloudapp.azure.com (consulte a [lista de referência de domínios do Azure](azure-domains.md)).
        - Você tem certeza de que possui todos os recursos que seus subdomínios DNS estão direcionando.

    - Mantenha um catálogo de serviços de seus pontos de extremidade de FQDN (nome de domínio totalmente qualificado) do Azure e os proprietários do aplicativo. Para criar o catálogo de serviços, execute o seguinte script de consulta do grafo de recursos do Azure. Esse script projeta as informações do ponto de extremidade FQDN dos recursos aos quais você tem acesso e as gera em um arquivo CSV. Se você tiver acesso a todas as assinaturas para seu locatário, o script considerará todas essas assinaturas, conforme mostrado no script de exemplo a seguir. Para limitar os resultados a um conjunto específico de assinaturas, edite o script conforme mostrado.


- **Criar procedimentos para correção:**
    - Quando as entradas DNS do pendente forem encontradas, sua equipe precisará investigar se algum comprometimento ocorreu.
    - Investigue por que o endereço não foi redirecionado quando o recurso foi encerrado.
    - Exclua o registro DNS se ele não estiver mais em uso ou aponte-o para o recurso correto do Azure (FQDN) de propriedade de sua organização.
 

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os serviços relacionados e os recursos do Azure que você pode usar para se defender do subdomínio tomada, consulte as páginas a seguir.

- [Habilitar o Azure defender para o serviço de aplicativo](../../security-center/defender-for-app-service-introduction.md) -para receber alertas quando as entradas DNS pendente forem detectadas

- [Impedir registros DNS pendente com o DNS do Azure](../../dns/dns-alias.md#prevent-dangling-dns-records)

- [Usar uma ID de verificação de domínio ao adicionar domínios personalizados no serviço Azure App](../../app-service/app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id)

- [Início Rápido: Execute a primeira consulta ao Resource Graph usando o Azure PowerShell](../../governance/resource-graph/first-query-powershell.md)
