---
title: Requisitos de infraestrutura da interface SIP – Serviços de Comunicação do Azure
description: Familiarize-se com os requisitos de infraestrutura para a configuração da interface SIP dos Serviços de Comunicação do Azure
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: ede650ae072ef53ed40a9372a292ab69fe8cc1af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103492720"
---
# <a name="sip-interface-infrastructure-requirements"></a>Requisitos de infraestrutura da interface SIP 

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

 
Este artigo descreve os detalhes de conectividade de infraestrutura, licenciamento e SBC (controlador de borda de sessão) que você deve ter em mente como seu plano de implantação da interface SIP.


## <a name="infrastructure-requirements"></a>Requisitos de infraestrutura
Os requisitos de infraestrutura para os SBCs, domínios e outros requisitos de conectividade de rede com suporte para implantar a interface SIP estão listados na seguinte tabela:  

|Requisito de infraestrutura|Você precisará do seguinte|
|:--- |:--- |
|SBC (controlador de borda de sessão)|Um SBC com suporte. Para obter mais informações, confira [SBCs compatíveis](#supported-session-border-controllers-sbcs).|
|Troncos de telefonia conectados ao SBC|Um ou mais troncos de telefonia conectados ao SBC. Em uma extremidade, o SBC se conecta ao Serviço de Comunicação do Azure via interface SIP. O SBC também pode se conectar a entidades de telefonia de terceiros, como PBXs, Adaptadores de Telefonia Analógica e assim por diante. Qualquer opção de conectividade PSTN conectada ao SBC funcionará. (Para a configuração dos troncos PSTN para o SBC, veja os fornecedores de SBC ou provedores de tronco.)|
|Assinatura do Azure|Uma assinatura do Azure que você usa para criar o recurso do ACS e a configuração e a conexão com o SBC.|
|Token de Acesso dos Serviços de Comunicação|Para fazer chamadas, você precisa de um Token de Acesso válido com o escopo `voip`. Confira [Tokens de Acesso](../identity-model.md#access-tokens)|
|Endereço IP público para o SBC|Um endereço IP público que pode ser usado para se conectar ao SBC. Com base no tipo de SBC, o SBC pode usar NAT.|
|FQDN (nome de domínio totalmente qualificado) para o SBC|Um FQDN para o SBC, em que a parte do domínio do FQDN não corresponde aos domínios registrados em sua organização Microsoft 365 ou Office 365. Para obter mais informações, confira [Nomes de domínio do SBC](#sbc-domain-names).|
|Entrada DNS pública para o SBC |Uma entrada DNS pública que mapeia o FQDN de SBC para o endereço IP público. |
|Certificado público confiável para o SBC |Um certificado para o SBC a ser usado para toda a comunicação com a interface SIP. Para obter mais informações, confira [Certificado confiável público para o SBC](#public-trusted-certificate-for-the-sbc).|
|Portas e endereços IP de firewall para sinalização e mídia SIP |O SBC se comunica com os seguintes serviços na nuvem:<br/><br/>Proxy SIP, que lida com a sinalização<br/>Processador de mídia, que lida com a mídia<br/><br/>Esses dois serviços têm endereços IP separados na nuvem da Microsoft, descritos mais adiante neste documento.


## <a name="sbc-domain-names"></a>Nomes de domínio do SBC

Os clientes sem o Office 365 podem usar qualquer nome de domínio para o qual possam obter um certificado público.

A seguinte tabela mostra exemplos de nomes DNS registrados para o locatário, se o nome pode ser usado como um FQDN (nome de domínio totalmente qualificado) para o SBC e exemplos de nomes de FQDN válidos:

|Nome DNS|Pode ser usado para o FQDN de SBC|Exemplos de nomes FQDN|
|:--- |:--- |:--- |
contoso.com|Yes|**Nomes válidos:**<br/>sbc1.contoso.com<br/>ssbcs15.contoso.com<br/>europe.contoso.com|
|contoso.onmicrosoft.com|No|Não há suporte para o uso de domínios *.onmicrosoft.com para nomes de SBC

Se você for um cliente do Office 365, o nome de domínio SBC não precisará corresponder ao registrado nos domínios do locatário do Office 365. Veja abaixo o exemplo da coexistência do Office 365 e do Serviço de Comunicação do Azure:

|Domínio registrado no Office 365|Exemplos de um FQDN de SBC no Teams|Exemplos de nomes FQDN de SBC no ACS|
|:--- |:--- |:--- |
**contoso.com** (domínio de segundo nível)|**sbc.contoso.com** (nome no domínio de segundo nível)|**sbc.acs.contoso.com** (nome no domínio de terceiro nível)<br/>**sbc.fabrikam.com** (qualquer nome em um domínio diferente)|
|**o365.contoso.com** (domínio de terceiro nível)|**sbc.o365.contoso.com** (nome no domínio de terceiro nível)|**sbc.contoso.com** (nome no domínio de segundo nível)<br/>**sbc.acs.o365.contoso.com** (nome no domínio de quarto nível)<br/>**sbc.fabrikam.com** (qualquer nome em um domínio diferente)

O emparelhamento de SBC funciona no nível de recurso dos Serviços de Comunicação, o que significa que você pode emparelhar muitos SBCs com um só recurso do Serviços de Comunicação, mas não pode emparelhar um SBC com mais de um recurso de Serviços de Comunicação. FQDNs de SBC exclusivos são necessários para o emparelhamento com diferentes recursos.

## <a name="public-trusted-certificate-for-the-sbc"></a>Certificado público confiável para o SBC

A Microsoft recomenda que você solicite o certificado para o SBC gerando uma CSR (solicitação de assinatura de certificação). Para obter instruções específicas sobre como gerar um CSR para um SBC, veja as instruções de interconexão ou a documentação fornecida por seus fornecedores de SBC. 

  > [!NOTE]
  > A maioria das CAs (autoridades de certificação) exige que o tamanho da chave privada seja pelo menos 2048. Tenha isso em mente ao gerar o CSR.

O certificado precisa ter o FQDN de SBC como o CN (nome comum) ou o campo SAN (nome alternativo da entidade). O certificado deve ser emitido diretamente de uma autoridade de certificação, não de um provedor intermediário.

Como alternativa, a interface SIP dos Serviços de Comunicação dá suporte a um caractere curinga no CN e/ou na SAN, o qual precisa estar em conformidade com o [HTTP sobre TLS do RFC](https://tools.ietf.org/html/rfc2818#section-3.1) padrão. 

Um exemplo seria usar `\*.contoso.com` que corresponderia ao FQDN de SBC `sbc.contoso.com`, mas não teria correspondência com `sbc.test.contoso.com`.

O certificado precisa ser gerado por uma das seguintes autoridades de certificação raiz:

- AffirmTrust
- AddTrust External CA Root
- Baltimore CyberTrust Root*
- Buypass
- Cybertrust
- Class 3 Public Primary Certification Authority
- Comodo Secure Root CA
- Deutsche Telekom 
- DigiCert Global Root CA
- DigiCert High Assurance EV Root CA
- Entrust
- GlobalSign
- Go Daddy
- GeoTrust
- Verisign, Inc. 
- SSL.com
- Starfield
- Symantec Enterprise Mobile Root for Microsoft 
- SwissSign
- Thawte Timestamping CA
- Trustwave
- TeliaSonera 
- T-Systems International GmbH (Deutsche Telekom)
- QuoVadis

A Microsoft está trabalhando para adicionar mais autoridades de certificação com base nas solicitações do cliente. 

## <a name="sip-signaling-fqdns"></a>Sinalização SIP: FQDNs 

Os pontos de conexão para a interface SIP dos Serviços de Comunicação são os três FQDNs descritos abaixo:

- **sip.pstnhub.microsoft.com** – FQDN global – precisa ser tentado primeiro. Quando o SBC envia uma solicitação para resolver esse nome, os servidores DNS do Microsoft Azure retornam um endereço IP que aponta para o datacenter primário do Azure atribuído ao SBC. A atribuição baseia-se nas métricas de desempenho dos datacenters e da proximidade geográfica com o SBC. O endereço IP retornado corresponde ao FQDN primário.
- **sip2.pstnhub.microsoft.com** – FQDN secundário – faz o mapeamento geográfico para a segunda região prioritária.
- **sip3.pstnhub.microsoft.com** – FQDN terciário – faz o mapeamento geográfico para a terceira região prioritária.

É necessário colocar esses três FQDNs em ordem para:

- Fornecer uma experiência ideal (menos carregada e mais próxima do datacenter do SBC atribuído pela consulta do primeiro FQDN).
- Fornecer failover quando a conexão de um SBC é estabelecida com um datacenter que está apresentando um problema temporário. Para obter mais informações, confira [Mecanismo de failover](#failover-mechanism-for-sip-signaling) abaixo.  

Os FQDNs – sip.pstnhub.microsoft.com, sip2.pstnhub.microsoft.com e sip3.pstnhub.microsoft.com – serão resolvidos para um dos seguintes endereços IP:

- `52.114.148.0`
- `52.114.132.46`
- `52.114.75.24` 
- `52.114.76.76` 
- `52.114.7.24` 
- `52.114.14.70`
- `52.114.16.74`
- `52.114.20.29`

Abrir portas de firewall para esses endereços IP para permitir o tráfego de entrada e saída entre os endereços para sinalização. Se o firewall der suporte a nomes DNS, o FQDN `sip-all.pstnhub.microsoft.com` será resolvido para todos esses endereços IP. 

## <a name="sip-signaling-ports"></a>Sinalização SIP: Portas

Use as seguintes portas para a interface SIP dos Serviços de Comunicação:

|Tráfego|De|Para|Porta de origem|Porta de destino|
|:--- |:--- |:--- |:--- |:--- |
|SIP/TLS|Proxy SIP|SBC|1024 – 65535|Definido no SBC (Para GCC High/DoD do Office 365, somente a porta 5061 precisa ser usada)|
SIP/TLS|SBC|Proxy SIP|Definido no SBC|5061|

### <a name="failover-mechanism-for-sip-signaling"></a>Mecanismo de failover para a sinalização SIP

O SBC faz uma consulta DNS para resolver sip.pstnhub.microsoft.com. Com base na localização do SBC e nas métricas de desempenho do datacenter, o datacenter primário é selecionado. Se o datacenter primário apresentar um problema, o SBC tentará sip2.pstnhub.microsoft.com, que resolve para o segundo datacenter atribuído e, caso os datacenters em duas regiões não estejam disponíveis (o que é raro), o SBC tentará novamente o último FQDN (sip3.pstnhub.microsoft.com), que fornece o IP do datacenter terciário.

## <a name="media-traffic-ip-and-port-ranges"></a>Tráfego de mídia: intervalos de porta e IP

O tráfego de mídia flui entre um serviço separado denominado Processador de Mídia. No momento da publicação, o Processador de Mídia para ACS pode usar qualquer endereço IP do Azure. Baixe [a lista completa de endereços](https://www.microsoft.com/download/details.aspx?id=56519).

### <a name="port-range"></a>Intervalo de portas
O intervalo de portas dos Processadores de Mídia é mostrado na seguinte tabela: 

|Tráfego|De|Para|Porta de origem|Porta de destino|
|:--- |:--- |:--- |:--- |:--- |
|UDP/SRTP|Processador de mídia|SBC|3478-3481 e 49152 – 53247|Definido no SBC|
|UDP/SRTP|SBC|Processador de mídia|Definido no SBC|3478-3481 e 49152 – 53247|

  > [!NOTE]
  > A Microsoft recomenda pelo menos duas portas por chamada simultânea no SBC.


## <a name="media-traffic-media-processors-geography"></a>Tráfego de mídia: geografia dos processadores de mídia

O tráfego de mídia flui por meio de componentes denominados processadores de mídia. Os processadores de mídia são colocados nos mesmos datacenters que os proxies SIP. Além disso, há processadores de mídia adicionais para otimizar o fluxo de mídia. Por exemplo, não temos no momento um componente de proxy SIP na Austrália (o SIP flui via Singapura ou RAE de Hong Kong), mas temos o processador de mídia localmente na Austrália. A necessidade dos processadores de mídia localmente é determinada pela latência que experimentamos ao enviar o tráfego para longas distâncias, por exemplo, da Austrália para Singapura ou para a RAE de Hong Kong. Embora a latência no exemplo de tráfego que flui da Austrália para Singapura ou para a RAE de Hong Kong seja aceitável para preservar a boa qualidade da chamada no tráfego SIP, no tráfego de mídia em tempo real ela não é.

Locais em que os componentes de proxy SIP e processador de mídia estão implantados:
- EUA (dois nos datacenters das regiões Oeste dos EUA e Leste dos EUA)
- Europa (datacenters de Amsterdã e Dublin)
- Ásia (datacenters de Singapura e da RAE de Hong Kong)
- Austrália (datacenters das regiões Leste e Sudeste da Austrália)

Locais em que apenas os processadores de mídia são implantados (o SIP flui por meio do datacenter mais próximo listado acima):
- Japão (datacenters das regiões Leste e Oeste do Japão)


## <a name="media-traffic-codecs"></a>Tráfego de mídia: codecs

### <a name="leg-between-sbc-and-cloud-media-processor-or-microsoft-teams-client"></a>Segmento entre o SBC e o Processador de Mídia na nuvem ou o cliente do Microsoft Teams.
Aplica-se ao caso de bypass de mídia e casos que não são bypass.

A interface de Roteamento Direto no segmento entre o Controlador de Borda de Sessão e o Processador de Mídia na nuvem pode usar os seguintes codecs:

- SILK, G.711, G.722, G.729

Você pode forçar o uso do codec específico no Controlador de Borda de Sessão, excluindo codecs indesejáveis da oferta.

### <a name="leg-between-acs-sdk-app-and-cloud-media-processor"></a>Segmento entre o aplicativo SDK do ACS e o Processador de Mídia na nuvem

No segmento entre o Processador de Mídia na nuvem e o aplicativo SDK do ACS, o SILK ou o G.722 é usado. A escolha do codec nesse segmento é baseada em algoritmos da Microsoft, que levam em consideração vários parâmetros. 

## <a name="supported-session-border-controllers-sbcs"></a>SBCs (Controladores de Borda de Sessão) com suporte

A certificação está em andamento. Enquanto isso, os clientes podem usar os [Controladores de Borda de Sessão certificados para Teams](/MicrosoftTeams/direct-routing-border-controllers). 

## <a name="next-steps"></a>Próximas etapas

### <a name="conceptual-documentation"></a>Documentação conceitual

- [Conceito de Telefonia](./telephony-concept.md)
- [Tipos de número de telefone nos Serviços de Comunicação do Azure](./plan-solution.md)
- [Preços](../pricing.md)

### <a name="quickstarts"></a>Inícios rápidos

- [Ligar para um telefone](../../quickstarts/voice-video-calling/pstn-call.md)