---
title: Condição de localização em Azure Active Directory acesso condicional
description: Saiba como usar a condição de localização para controlar o acesso aos seus aplicativos na nuvem com base no local de rede de um usuário.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 777fc60f76692734ea34ff3cdf8f6bc6e5e8316b
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97615704"
---
# <a name="using-the-location-condition-in-a-conditional-access-policy"></a>Usando a condição de localização em uma política de acesso condicional 

Conforme explicado no [artigo de visão geral](overview.md) , as políticas de acesso condicional estão no seu mais básico uma instrução if-then que combina sinais, para tomar decisões e impor políticas da organização. Um desses sinais que podem ser incorporados ao processo de tomada de decisão é o local de rede.

![Sinal condicional conceitual mais decisão para obter a imposição](./media/location-condition/conditional-access-signal-decision-enforcement.png)

As organizações podem usar esse local de rede para tarefas comuns, como: 

- Exigir autenticação multifator para usuários que acessam um serviço quando estão fora da rede corporativa.
- Bloqueando o acesso para usuários que acessam um serviço de países e regiões específicas.

O local de rede é determinado pelo endereço IP público que um cliente fornece para Azure Active Directory. Por padrão, as políticas de acesso condicional se aplicam a todos os endereços IPv4 e IPv6. 

> [!TIP]
> Os intervalos de IPv6 só têm suporte na interface **[local nomeada (versão prévia)](#preview-features)** . 

## <a name="named-locations"></a>Localizações nomeadas

Os locais são designados no portal do Azure em **Azure Active Directory**  >    >  **acesso condicional** de segurança  >  **nomear locais nomeados**. Esses locais de rede nomeados podem incluir locais como intervalos de rede da sede de uma organização, intervalos de rede VPN ou intervalos que você deseja bloquear. 

![Locais nomeados no portal do Azure](./media/location-condition/new-named-location.png)

Para configurar um local, será necessário fornecer pelo menos um **nome** e o intervalo de IP. 

O número de localizações que você pode configurar é restrito pelo tamanho do objeto relacionado no Azure AD. Você pode configurar locais com base nas seguintes limitações:

- Um local nomeado com até 1200 intervalos de IPv4.
- Um máximo de 90 localizações nomeadas com um intervalo IP atribuído a cada um delas.

> [!TIP]
> Os intervalos de IPv6 só têm suporte na interface **[local nomeada (versão prévia)](#preview-features)** . 

### <a name="trusted-locations"></a>Locais confiáveis

Ao criar um local de rede, um administrador tem a opção de marcar um local como um local confiável. 

![Locais confiáveis no portal do Azure](./media/location-condition/new-trusted-location.png)

Essa opção pode considerar as políticas de acesso condicional, em que você pode, por exemplo, exigir o registro para autenticação multifator de um local de rede confiável. Ele também considera o cálculo de risco de Azure AD Identity Protection, reduzindo o risco de entrada dos usuários ao chegar de um local marcado como confiável.

### <a name="countries-and-regions"></a>Países e regiões

Algumas organizações podem optar por definir os limites de IP de países ou regiões inteiros como locais nomeados para políticas de acesso condicional. Eles podem usar esses locais ao bloquear o tráfego desnecessário quando sabem que usuários válidos nunca virão de um local como a Coreia do Norte. Esses mapeamentos do endereço IP para o país são atualizados periodicamente. 

> [!NOTE]
> Os intervalos de endereços IPv6 não podem ser mapeados para países. Somente endereços IPv4 são mapeados para países.

![Criar um novo local baseado em país ou região na portal do Azure](./media/location-condition/new-named-location-country-region.png)

#### <a name="include-unknown-areas"></a>Incluir áreas desconhecidas

Alguns endereços IP não são mapeados para um país ou região específica, incluindo todos os endereços IPv6. Para capturar esses locais de IP, marque a caixa **incluir áreas desconhecidas** ao definir um local. Esta opção permite que você escolha se esses endereços IP devem ser incluídos na localização nomeada. Use essa configuração quando a política usando a localização nomeada deve aplicar-se a localizações desconhecidas.

### <a name="configure-mfa-trusted-ips"></a>Configurar IPs confiáveis de MFA

Você também pode configurar intervalos de endereços IP que representam a Intranet local da sua organização nas [configurações do serviço de autenticação multifator](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Esse recurso permite que você configure até 50 intervalos de endereços IP. Os intervalos de endereços IP estão no formato CIDR. Para obter mais informações, consulte [IPs confiáveis](../authentication/howto-mfa-mfasettings.md#trusted-ips).  

Se você tiver IPs confiáveis configurados, eles aparecerão como **IPs confiáveis MFA** na lista de locais para a condição de localização.

### <a name="skipping-multi-factor-authentication"></a>Ignorando a autenticação multifator

Na página de configurações do serviço de autenticação multifator, você pode identificar os usuários da Intranet corporativa selecionando **Ignorar autenticação multifator para solicitações de usuários federados na minha Intranet**. Essa configuração indica que a declaração de rede corporativa interna, que é emitida pelo AD FS, deve ser confiável e usada para identificar o usuário como estando na rede corporativa. Para obter mais informações, consulte [habilitar o recurso de IPs confiáveis usando o acesso condicional](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

Depois de marcar essa opção, incluindo o local nomeado, os **IPs confiáveis do MFA** serão aplicados a qualquer política com essa opção selecionada.

Para aplicativos móveis e de área de trabalho, que têm tempos de vida de longa duração de sessão, o acesso condicional é reavaliado periodicamente. O padrão é uma vez a cada hora. Quando a declaração da rede corporativa interna é emitida somente no momento da autenticação inicial, o Microsoft Azure AD pode não ter uma lista de intervalos de IP confiáveis. Nesse caso, é mais difícil determinar se o usuário ainda está na rede corporativa:

1. Verifique se o endereço IP do usuário está em um dos intervalos de IP confiáveis.
1. Verifique se os três primeiros octetos do endereço IP do usuário correspondem aos três primeiros octetos do endereço IP da autenticação inicial. O endereço IP é comparado com a autenticação inicial quando a declaração de rede corporativa interna foi emitida originalmente e a localização do usuário foi validada.

Se ambas as etapas falharem, um usuário é considerado como não estando em um IP confiável.

## <a name="preview-features"></a>Versão prévia dos recursos

Além do recurso de local nomeado geralmente disponível, há também um local nomeado (versão prévia). Você pode acessar a visualização de local nomeado usando a faixa na parte superior da folha local nomeado atual.

![Experimente a versão prévia dos locais nomeados](./media/location-condition/preview-features.png)

Com a versão prévia do local nomeado, você pode

- Configurar até 195 locais nomeados
- Configurar até 2000 intervalos de IP por local nomeado
- Configurar endereços IPv6 juntamente com endereços IPv4

Também adicionamos algumas verificações adicionais para ajudar a reduzir a alteração de configuração incorreta.

- Os intervalos de IP privado não podem mais ser configurados
- O número de endereços IP que podem ser incluídos em um intervalo é limitado. Somente máscaras CIDR maiores que/8 serão permitidas ao configurar um intervalo de IP.

Com a versão prévia, agora há duas opções de criação: 

- **Local dos países**
- **Local dos intervalos de IP**

> [!NOTE]
> Os intervalos de endereços IPv6 não podem ser mapeados para países. Somente endereços IPv4 são mapeados para países.

![Interface de visualização de locais nomeados](./media/location-condition/named-location-preview.png)

## <a name="location-condition-in-policy"></a>Condição de localização na política

Quando você configurar a condição de localização, você tem a opção de fazer a distinção entre:

- Qualquer local
- Todos os locais confiáveis
- Locais selecionados

### <a name="any-location"></a>Qualquer local

Por padrão, selecionar **Qualquer local** faz com que uma política seja aplicada a todos os endereços IP, o que significa qualquer endereço na Internet. Essa configuração não está limitada aos endereços IP que você configurou como localização nomeada. Quando seleciona **Qualquer local**, você ainda pode excluir locais específicos de uma política. Por exemplo, você pode aplicar uma política para todos os locais confiáveis de exceções de locais para definir o escopo para todos os locais, exceto a rede corporativa.

### <a name="all-trusted-locations"></a>Todos os locais confiáveis

Esta opção se aplica a:

- Todos os locais que foram marcados como local confiável
- **IPs confiáveis de MFA** (se configurado)

### <a name="selected-locations"></a>Locais selecionados

Com essa opção, você pode selecionar uma ou mais localizações nomeadas. Para uma política com essa configuração a ser aplicada, um usuário precisa se conectar de qualquer um dos locais selecionados. Quando você clica em **Selecionar**, é aberto o controle de seleção da rede nomeada que mostra a lista de redes nomeadas. A lista também mostra se o local de rede foi marcado como confiável. A localização nomeada chamada **IPs confiáveis de MFA** é usada para incluir as configurações de IP podem ser configuradas na página de configuração do serviço de autenticação multifator.

## <a name="ipv6-traffic"></a>Tráfego IPv6

Por padrão, as políticas de acesso condicional serão aplicadas a todo o tráfego IPv6. Com a versão [prévia do local nomeado](#preview-features), você pode excluir intervalos de endereços IPv6 específicos de uma política de acesso condicional. Essa opção é útil em casos em que você não deseja que a política seja imposta para intervalos IPv6 específicos. Por exemplo, se você quiser não impor uma política para usos em sua rede corporativa, e sua rede corporativa estiver hospedada em intervalos IPv6 públicos.  

### <a name="when-will-my-tenant-have-ipv6-traffic"></a>Quando meu locatário terá o tráfego IPv6?

O Azure Active Directory (Azure AD) atualmente não oferece suporte a conexões de rede diretas que usam IPv6. No entanto, há alguns casos em que o tráfego de autenticação é proxy por meio de outro serviço. Nesses casos, o endereço IPv6 será usado durante a avaliação da política.

A maior parte do tráfego IPv6 que recebe o proxy do Azure AD vem do Microsoft Exchange Online. Quando disponível, o Exchange prefere as conexões IPv6. **Portanto, se você tiver políticas de acesso condicional para o Exchange, que foram configuradas para intervalos IPv4 específicos, convém ter certeza de que também adicionou os intervalos de IPv6 das organizações.** Não incluir intervalos de IPv6 causará um comportamento inesperado para os dois casos a seguir:

- Quando um cliente de email é usado para se conectar ao Exchange Online com autenticação herdada, o Azure AD pode receber um endereço IPv6. A solicitação de autenticação inicial vai para o Exchange e, em seguida, é colocada em proxy no Azure AD.
- Quando o Outlook Acesso via Web (OWA) é usado no navegador, ele verifica periodicamente se todas as políticas de acesso condicional continuam a ser satisfeitas. Essa verificação é usada para capturar casos em que um usuário pode ter se movido de um endereço IP permitido para um novo local, como o café na loja. Nesse caso, se um endereço IPv6 for usado e se o endereço IPv6 não estiver em um intervalo configurado, o usuário poderá ter sua sessão interrompida e ser direcionado para o Azure AD para autenticar novamente. 

Esses são os motivos mais comuns de que talvez seja necessário configurar intervalos de IPv6 em seus locais nomeados. Além disso, se você estiver usando o Azure VNets, terá o tráfego proveniente de um endereço IPv6. Se você tiver o tráfego de VNet bloqueado por uma política de acesso condicional, verifique seu log de entrada do Azure AD. Depois de identificar o tráfego, você pode obter o endereço IPv6 que está sendo usado e excluí-lo da política. 

> [!NOTE]
> Se você quiser especificar um intervalo de CIDR de IP para um único endereço, aplique a máscara de bits/128. Se você disser o endereço IPv6 2607: fb90: b27a: 6f69: f8d5: dea0: fb39:74a e quisesse excluir esse endereço único como um intervalo, você usaria 2607: fb90: b27a: 6f69: f8d5: dea0: fb39:74a/128.

### <a name="identifying-ipv6-traffic-in-the-azure-ad-sign-in-activity-reports"></a>Identificando o tráfego IPv6 nos relatórios de atividade de entrada do Azure AD

Você pode descobrir o tráfego IPv6 em seu locatário acessando os [relatórios de atividade de entrada do Azure ad](../reports-monitoring/concept-sign-ins.md). Depois de abrir o relatório de atividades, adicione a coluna "endereço IP". Esta coluna fornecerá a você a identificação do tráfego IPv6.

Você também pode encontrar o IP do cliente clicando em uma linha no relatório e, em seguida, acessando a guia "local" nos detalhes da atividade de entrada. 

## <a name="what-you-should-know"></a>O que você deve saber

### <a name="when-is-a-location-evaluated"></a>Quando um local é avaliado?

As políticas de acesso condicional são avaliadas quando:

- Um usuário inicialmente entra em um aplicativo Web, aplicativo da área de trabalho ou móvel.
- Um aplicativo da área de trabalho ou móvel que usa autenticação moderna, usa um token de atualização para adquirir um novo token de acesso. Por padrão, essa verificação é uma vez por hora.

Essa verificação significa que, para aplicativos móveis e de desktop usando autenticação moderna, uma alteração no local seria detectada dentro de uma hora após a alteração do local de rede. Para aplicativos da área de trabalho e móveis que não utilizam autenticação moderna, a política é aplicada em cada solicitação de token. A frequência da solicitação pode variar de acordo com o aplicativo. Da mesma forma, para aplicativos Web, a política é aplicada no início da entrada e é adequada para o tempo de vida da sessão no aplicativo Web. Devido às diferenças nos tempos de vida da sessão em todos os aplicativos, o tempo entre a avaliação de política também varia. Sempre que o aplicativo solicitar um novo token de entrada, a política será aplicada.

Por padrão, o Microsoft Azure AD emite um token de hora em hora. Depois de remover a rede corporativa, em uma hora a política é aplicada para aplicativos que usam autenticação moderna.

### <a name="user-ip-address"></a>Endereço IP do usuário

O endereço IP que é usado na avaliação de política é o endereço IP público do usuário. Para dispositivos em uma rede privada, esse endereço IP não é o IP do dispositivo do usuário na intranet, é o endereço usado pela rede para se conectar à Internet pública.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Carregamento em massa e download de localizações nomeadas

Quando você cria ou atualiza localizações nomeadas, para atualizações em massa, você pode carregar ou fazer o download de um arquivo CSV com os intervalos de IP. Um upload substitui os intervalos de IP na lista por esses intervalos do arquivo. Cada linha do arquivo contém um intervalo de endereços IP no formato CIDR.

### <a name="cloud-proxies-and-vpns"></a>Proxies e VPNs na nuvem

Quando você usa um proxy ou solução VPN hospedado na nuvem, o endereço IP que o Microsoft Azure AD usa ao avaliar uma política é o endereço IP do proxy. O cabeçalho de X-Forwarded-For (XFF) que contém o endereço IP público dos usuários não é usado porque não há validações que venham de uma fonte confiável, portanto apresentaria um método para simular um endereço IP.

Quando um proxy de nuvem está em vigor, uma política usada para exigir um dispositivo ingressado no Azure AD híbrido pode ser usada ou a declaração interna corpnet de AD FS.

### <a name="api-support-and-powershell"></a>Suporte à API e PowerShell

Uma versão de visualização do API do Graph para locais nomeados está disponível. para obter mais informações, consulte a [API namedLocation](/graph/api/resources/namedlocation?view=graph-rest-beta).

> [!NOTE]
> Os locais nomeados que você cria usando o PowerShell são exibidos somente em locais nomeados (versão prévia). Você não pode ver locais nomeados na exibição antiga.  

## <a name="next-steps"></a>Próximas etapas

- Se você quiser saber como configurar uma política de acesso condicional, consulte o artigo [criando uma política de acesso condicional](concept-conditional-access-policies.md).
- Procurando uma política de exemplo usando a condição de localização? Consulte o artigo [acesso condicional: bloquear o acesso por local](howto-conditional-access-policy-location.md)
