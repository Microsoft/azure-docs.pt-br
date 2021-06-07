---
title: Referência de esquema de normalização de dados do Azure Sentinel | Microsoft Docs
description: Este artigo exibe o esquema de normalização de dados do Azure Sentinel.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 09/08/2020
ms.author: yelevin
ms.openlocfilehash: 4cd97aef5d8c959aeb2e0314e051790fd0421585
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99806928"
---
# <a name="azure-sentinel-data-normalization-schema-reference"></a>Referência de esquema de normalização de dados do Azure Sentinel

## <a name="terminology"></a>Terminologia

A seguinte terminologia é usada em esquemas do Azure Sentinel:

| Termo | Definição |
| ---- | ---------- |
| Dispositivo de relatório | O sistema que envia os registros para o Azure Sentinel. Ele pode não ser o sistema de assunto do registro. |
| Record | Uma unidade de dados enviada do dispositivo de relatório. Isso é geralmente chamado de "log", "evento" ou "alerta", mas não precisa necessariamente ser um deles. |
|

## <a name="data-types-and-formats"></a>Tipos de dados e formatos

Os valores devem ser normalizados com base nas diretrizes abaixo. Isso é obrigatório para campos normalizados e recomendado para outros campos. 

| Tipo de dados | Tipo físico | Formato e valor |
| --------- | ------------- | ---------------- |
| **Data/hora** | Dependendo da capacidade do método de ingestão, use a prioridade decrescente:<ul><li>Log Analytics tipo de data e hora interno</li><li>Um campo inteiro usando a representação numérica Log Analytics DateTime</li><li>Um campo de cadeia de caracteres usando Log Analytics representação numérica de DateTime</li></ul> | Representação Log Analytics DateTime. <br></br>A data de Log Analytics & representação de hora é semelhante, mas diferente da representação de tempo do UNIX. Consulte essas diretrizes de conversão. <br></br>A data & hora deve ser ajustada ao fuso horário. |
| **Endereço MAC** | String | Notação de Colon-Hexadecimal |
| **Endereço IP** | Endereço IP | O esquema não tem endereços IPv4 e IPv6 separados. Qualquer campo de endereço IP pode incluir um endereço IPv4 ou o endereço IPv6:<ul><li>IPv4 em uma notação de ponto decimal</li><li>IPv6 na notação de 8 hextets, permitindo os pequenos formulários descritos aqui.</li></ul> |
| **Usuário** | String | Os três campos de usuário a seguir estão disponíveis:<ul><li>Nome de usuário</li><li>UPN do usuário</li><li>Domínio do usuário</li></ul> |
| **ID de usuário** | String | Atualmente, há suporte para as duas IDs de usuário a seguir:<ul><li>SID de usuário</li><li>ID do Azure Active Directory</li></ul> |
| **Dispositivo** | String | Há suporte para as três colunas de dispositivo/host a seguir:<ul><li>ID</li><li>Nome</li><li>FQDN (nome de domínio totalmente qualificado)</li></ul> |
| **País** | String | Uma cadeia de caracteres usando ISO 3166-1 de acordo com essa prioridade:<ul><li>Códigos alfa-2 (ou seja, US para o Estados Unidos)</li><li>Códigos alfa-3 (ou seja, EUA para o Estados Unidos)</li><li>Nome curto</li></ul> |
| **Região** | String | O nome da subdivisão do país usando ISO 3166-2 |
| **Cidade** | String | |
| **Longitude** | Double | Representação de coordenada ISO 6709 (decimal assinado) |
| **Latitude** | Double | Representação de coordenada ISO 6709 (decimal assinado) |
| **Algoritmo de hash** | String | Há suporte para as 4 colunas de hash a seguir:<ul><li>MD5</li><li>SHA1</li><li>SHA256</li><li>SHA512</li></ul> |
| **Tipo de arquivo** | String | O tipo do tipo de arquivo:<ul><li>Extensão</li><li>Classe</li><li>Nome de chamada</li></ul> |
| 

## <a name="network-sessions-table-schema"></a>Esquema de tabela de sessões de rede

Veja abaixo o esquema da tabela sessões de rede, com versão 1.0.0

| Nome do campo | Tipo de valor | Exemplo | Descrição | Entidades OSSEM associadas |
|-|-|-|-|-|
| EventType | String | Tráfego | Tipo de evento que está sendo coletado | Evento |
| EventSubType | String | Autenticação | Descrição adicional do tipo, se aplicável | Evento |
| EventCount | Integer  | 10 | O número de eventos agregados, se aplicável. | Evento |
| EventEndTime | Data/Hora | Consulte "tipos de dados" | A hora em que o evento terminou | Evento |
| EventMessage | string |  acesso negado | Uma mensagem ou descrição geral, incluída no ou gerada a partir do registro | Evento |
| DvcIpAddr | Endereço IP |  23.21.23.34 | O endereço IP do dispositivo que gera o registro | Dispositivo,<br>IP |
| DvcMacAddr | String | 06:10:9F: EB: 8F: 14 | O endereço MAC da interface de rede do dispositivo de relatório do qual o evento foi enviado. | Dispositivo,<br>Mac |
| DvcHostname | Nome do dispositivo (cadeia de caracteres) | syslogserver1.contoso.com | O nome do dispositivo do dispositivo que gera a mensagem. | Dispositivo |
| EventProduct | String | OfficeSharepoint | O produto que gera o evento. | Evento |
| EventProductVersion | string | 9.0 |  A versão do produto que gera o evento. | Evento |
| EventResourceId | ID do dispositivo (cadeia de caracteres) | /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /syslogserver1 | A ID de recurso do dispositivo que gera a mensagem. | Evento |
| EventReportUrl | String | https://192.168.1.1/repoerts/ae3-56.htm | Um link para o relatório completo criado pelo dispositivo de relatório | Evento |
| EventVendor | String | Microsoft | O fornecedor do produto que gera o evento. | Evento |
| EventResult | Múltiplos valores: êxito, parcial, falha, [vazio] (cadeia de caracteres) | Êxito | O resultado relatado para a atividade. Valor vazio quando não aplicável. | Evento |
| EventResultDetails | String | Senha incorreta | Motivo ou detalhes para o resultado relatado em EventResult | Evento |
| EventSchemaVersion | Real | 0,1 | Versão do esquema do Azure Sentinel. No momento, 0,1. | Evento |
| EventSeverity | String | Baixo | Se a atividade relatada tiver um impacto de segurança, o indicará a gravidade do impacto. | Evento |
| EventOriginalUid | String | af6ae8fe-ff43-4a4c-b537-8635976a2b51 | A ID do registro do dispositivo de relatório. | Evento |
| EventStartTime | Data/Hora | Consulte "tipos de dados" | A hora em que o evento foi declarado | Evento |
| TimeGenerated | Data/Hora | Consulte "tipos de dados" | A hora em que o evento ocorreu, conforme relatado pela fonte de relatório. | Campo personalizado |
| EventTimeIngested | Data/Hora | Consulte "tipos de dados" | A hora em que o evento foi ingerido para o Azure Sentinel. Serão adicionados pelo Azure Sentinel. | Evento |
| EventUid | GUID (cadeia de caracteres) | 516a64e3-8360-4f1e-a67c-d96b3d52df54 | Identificador exclusivo usado pelo Azure Sentinel para marcar uma linha. | Evento |
| NetworkApplicationProtocol | String | HTTPS | O protocolo de camada de aplicativo usado pela conexão ou sessão. | Rede |
| DstBytes | INT | 32455 | O número de bytes enviados do destino para a origem da conexão ou da sessão. | Destino |
| SrcBytes | INT | 46536 | O número de bytes enviados da origem para o destino para a conexão ou a sessão. | Fonte |
| NetworkBytes | INT | 78991 | Número de bytes enviados em ambas as direções. Se BytesReceived e BytesSent existirem, BytesTotal deverá ser igual à soma. | Rede |
| NetworkDirection | Múltiplos valores: entrada, saída (cadeia de caracteres) | Entrada | A direção da conexão ou da sessão, dentro ou fora da organização. | Rede |
| DstGeoCity | String | Burlington | A cidade associada ao endereço IP de destino | Destino<br>Localização geográfica |
| DstGeoCountry | País (cadeia de caracteres) | EUA | O país associado ao endereço IP de origem | Destino<br>Localização geográfica |
| DstDvcHostname | Nome do dispositivo (cadeia de caracteres) |  victim_pc | O nome do dispositivo do dispositivo de destino | Destino<br>Dispositivo |
| DstDvcFqdn | String | victim_pc. contoso. local | O nome de domínio totalmente qualificado do host em que o log foi criado | Destino<br>Dispositivo |
| DstDomainHostname | string | CONTOSO | O domínio do destino, o domínio do host de destino (site, nome de domínio, etc.), por exemplo, para pesquisas de DNS ou pesquisas do NS | Destino |
| DstInterfaceName | string | Adaptador de rede Microsoft Hyper-V | A interface de rede usada para a conexão ou sessão pelo dispositivo de destino. | Destino |
| DstInterfaceGuid | string | 2BB33827-6BB6-48DB-8DE6-DB9E0B9F9C9B | GUID da interface de rede que foi usada para solicitação de autenticação  | Destino |
| DstIpAddr | Endereço IP | 2001: DB8:: FF00:42:8329 | O endereço IP do destino da conexão ou da sessão, mais comumente referido como o IP de destino no pacote de rede | Destino<br>IP |
| DstDvcIpAddr | Endereço IP | 75.22.12.2 | O endereço IP de destino de um dispositivo que não está diretamente associado ao pacote de rede | Destino<br>Dispositivo,<br>IP
| DstGeoLatitude | Latitude (duplo) | 44,475833 | A latitude da coordenada geográfica associada ao endereço IP de destino | Destino<br>Localização geográfica |
| DstMacAddr | String | 06:10:9F: EB: 8F: 14 | O endereço MAC da interface de rede na qual a conexão ou sessão foi encerrada, mais comumente referenciada ao MAC de destino no pacote de rede | Destino<br>MAC |
| DstDvcMacAddr | String | 06:10:9F: EB: 8F: 14 | O endereço MAC de destino de um dispositivo que não está diretamente associado ao pacote de rede. | Destino<br>Dispositivo,<br>MAC |
| DstDvcDomain | String | CONTOSO | O domínio do dispositivo de destino. | Destino<br>Dispositivo |
| DstPortNumber | Inteiro | 443 | A porta do IP de destino. | Destino<br>Porta |
| DstGeoRegion | Região (cadeia de caracteres) | Vermont | A região em um país associado ao endereço IP de destino | Destino<br>Localização geográfica |
| DstResourceId | ID do dispositivo (cadeia de caracteres) |  /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /victim | A ID de recurso do dispositivo de destino. | Destino |
| DstNatIpAddr | Endereço IP | 2::1 | Se for relatado por um dispositivo NAT intermediário, como um firewall, o endereço IP usado pelo dispositivo NAT para comunicação com a origem. | NAT de destino,<br>IP |
| DstNatPortNumber | INT | 443 | Se for relatado por um dispositivo NAT intermediário, como um firewall, a porta usada pelo dispositivo NAT para comunicação com a origem. | NAT de destino,<br>Porta |
| DstUserSid | SID de usuário |  S-12-1445 | A ID de usuário da identidade associada ao destino da sessão. Normalmente, a identidade usada para autenticar um servidor. Consulte "tipos de dados" para obter detalhes. | Destino<br>Usuário |
| DstUserAadId | Cadeia de caracteres (GUID) | ae92b0b4-cfba-4b42-85a0-fbd862f4df54 | A ID de objeto da conta do Azure AD do usuário na extremidade de destino da sessão | Destino<br>Usuário |
| DstUserName | Nome de usuário (cadeia de caracteres) | João | O nome de usuário da identidade associada ao destino da sessão.  | Destino<br>Usuário |
| DstUserUpn | string | johnd@anon.com | O UPN da identidade associada ao destino da sessão. | Destino<br>Usuário |
| DstUserDomain | string | GRUPO DE TRABALHO | O nome de domínio ou computador da conta no destino da sessão | Destino<br>Usuário |
| DstZone | String | DMZ | A zona de rede do destino, conforme definido pelo dispositivo de relatório. | Destino |
| DstGeoLongitude | Longitude (dupla) | -73,211944 | A longitude da coordenada geográfica associada ao endereço IP de destino | Destino<br>Localização geográfica |
| DvcAction | Vários valores: permitir, negar e descartar (cadeia de caracteres) | Allow | Se for relatado por um dispositivo intermediário, como um firewall, a ação realizada pelo dispositivo. | Dispositivo |
| DvcInboundInterface | String | eth0 | Se for relatado por um dispositivo intermediário, como um firewall, a interface de rede usada por ele para a conexão com o dispositivo de origem. | Dispositivo |
| DvcOutboundInterface | String  | Adaptador Ethernet Ethernet 4 | Se for relatado por um dispositivo intermediário, como um firewall, a interface de rede usada por ele para a conexão com o dispositivo de destino. | Dispositivo |
| NetworkDuration | Inteiro | 1500 | A quantidade de tempo, em milissegundo, para a conclusão da conexão ou sessão de rede | Rede |
| NetworkIcmpCode | Inteiro | 34 | Para uma mensagem ICMP, o valor numérico de tipo de mensagem ICMP (RFC 2780 ou RFC 4443). | Rede |
| NetworkIcmpType | String | Destino Inacessível | Para uma mensagem ICMP, tipo de mensagem ICMP representação de texto (RFC 2780 ou RFC 4443). | Rede |
| DstPackets | INT  | 446 | O número de pacotes enviados do destino para a origem da conexão ou da sessão. O significado de um pacote é definido pelo dispositivo de relatório. | Destino |
| SrcPackets | INT  | 6478 | O número de pacotes enviados da origem para o destino para a conexão ou a sessão. O significado de um pacote é definido pelo dispositivo de relatório. | Fonte |
| NetworkPackets | INT  | 0 | Número de pacotes enviados em ambas as direções. Se PacketsReceived e PacketsSent existirem, BytesTotal deverá ser igual à soma. | Rede |
| HttpRequesttime | Inteiro | 700 | A quantidade de tempo necessário para enviar a solicitação ao servidor, se aplicável. | Http |
| HttpResponseTime | Inteiro | 800 | A quantidade de tempo que leva para receber uma resposta no servidor, se aplicável. | Http |
| NetworkRuleName | String | AnyAnyDrop | O nome ou a ID da regra pela qual o deviceaction foi decidido | Rede |
| NetworkRuleNumber | INT |  23 | Número de regra correspondente  | Rede |
| NetworkSessionId | string | 172_12_53_32_4322__123_64_207_1_80 | O identificador de sessão, conforme relatado pelo dispositivo de relatório. Por exemplo, identificador de sessão L7 para aplicativos específicos após a autenticação | Rede |
| SrcGeoCity | String | Burlington | A cidade associada ao endereço IP de origem | Original<br>Localização geográfica |
| SrcGeoCountry | País (cadeia de caracteres) | EUA | O país associado ao endereço IP de origem | Original<br>Localização geográfica |
| SrcDvcHostname | Nome do dispositivo (cadeia de caracteres) |  villain | O nome do dispositivo do dispositivo de origem | Original<br>Dispositivo |
| SrcDvcFqdn | string | Villain.malicious.com | O nome de domínio totalmente qualificado do host em que o log foi criado | Original<br>Dispositivo |
| SrcDvcDomain | string | EVILORG | Domínio do dispositivo do qual a sessão foi iniciada | Original<br>Dispositivo |
| SrcDvcOs | String | iOS | O sistema operacional do dispositivo de origem | Original<br>Dispositivo |
| SrcDvcModelName | String | Observação do Samsung Galaxy | O nome do modelo do dispositivo de origem | Original<br>Dispositivo |
| SrcDvcModelNumber | String | 10.0 | O número do modelo do dispositivo de origem | Original<br>Dispositivo |
| SrcDvcType | String | Dispositivos móveis | O tipo do dispositivo de origem | Original<br> Dispositivo |
| SrcIntefaceName | String | eth01 | A interface de rede usada para a conexão ou a sessão pelo dispositivo de origem. | Fonte |
| SrcInterfaceGuid | String | 46ad544b-eaf0-47ef-827c-266030f545a6 | GUID da interface de rede usada | Fonte |
| SrcIpAddr | Endereço IP | 77.138.103.108 | O endereço IP do qual a conexão ou a sessão foi originada. | Original<br>IP |
| SrcDvcIpAddr | Endereço IP | 77.138.103.108 | O endereço IP de origem de um dispositivo não diretamente associado ao pacote de rede (coletado por um provedor ou explicitamente calculado). | Original<br>Dispositivo,<br>IP |
| SrcGeoLatitude | Latitude (duplo) | 44,475833 | A latitude da coordenada geográfica associada ao endereço IP de origem | Original<br>Localização geográfica |
| SrcGeoLongitude | Longitude (dupla) | -73,211944 | A longitude da coordenada geográfica associada ao endereço IP de origem | Original<br>Localização geográfica |
| SrcMacAddr | String | 06:10:9F: EB: 8F: 14 | O endereço MAC da interface de rede da qual a sessão OD de conexão foi originada. | Original<br>Mac |
| SrcDvcMacAddr | String | 06:10:9F: EB: 8F: 14 | O endereço MAC de origem de um dispositivo que não está diretamente associado ao pacote de rede. | Original<br>Dispositivo,<br>Mac |
| SrcPortNumber | Inteiro | 2335 | A porta IP da qual a conexão foi originada. Pode não ser relevante para uma sessão que abrange várias conexões. | Original<br>Porta |
| SrcGeoRegion | Região (cadeia de caracteres) | Vermont | A região em um país associado ao endereço IP de origem | Original<br>Localização geográfica |
| SrcResourceId | String | /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /syslogserver1 | A ID de recurso do dispositivo que gera a mensagem. | Fonte |
| SrcNatIpAddr | Endereço IP | 4.3.2.1 | Se for relatado por um dispositivo NAT intermediário, como um firewall, o endereço IP usado pelo dispositivo NAT para comunicação com o destino. | NAT de origem,<br>IP |
| SrcNatPortNumber | Inteiro | 345 | Se for relatado por um dispositivo NAT intermediário, como um firewall, a porta usada pelo dispositivo NAT para comunicação com o destino. | NAT de origem,<br>Porta |
| SrcUserSid | ID de usuário (cadeia de caracteres) | S-15-1445 | A ID de usuário da identidade associada à origem das sessões. Normalmente, o usuário realiza uma ação no cliente. Consulte "tipos de dados" para obter detalhes. | Original<br>Usuário |
| SrcUserAadId | Cadeia de caracteres (GUID) | 16c8752c-7dd2-4cad-9e03-fb5d1cee5477 | A ID de objeto da conta do Azure AD do usuário na extremidade de origem da sessão | Original<br>Usuário |
| SrcUserName | Nome de usuário (cadeia de caracteres) | José | O nome de usuário da identidade associada à origem das sessões. Normalmente, o usuário realiza uma ação no cliente. Consulte "tipos de dados" para obter detalhes. | Fonte<br>Usuário |
| SrcUserUpn | string | bob@alice.com | O UPN da conta que inicia a sessão | Original<br>Usuário |
| SrcUserDomain | string | PLACA | O domínio para a conta que inicia a sessão | Original<br>Usuário |
| SrcZone | String | Toque | A zona de rede da origem, conforme definido pelo dispositivo de relatório. | Fonte |
| NetworkProtocol | String | TCP | O protocolo IP usado pela conexão ou sessão. Normalmente, TCP, UDP ou ICMP | Rede |
| CloudAppName | String | Facebook | O nome do aplicativo de destino para um aplicativo HTTP, conforme identificado por um proxy. | Nuvem |
| CloudAppId | String | 124 | A ID do aplicativo de destino para um aplicativo HTTP, conforme identificado por um proxy. Esse valor é geralmente específico para o proxy usado. | Nuvem |
| CloudAppOperation | String | DeleteFile | A operação que o usuário realizou no contexto do aplicativo de destino para um aplicativo HTTP, conforme identificado por um proxy. Esse valor é geralmente específico para o proxy usado. | Nuvem |
| CloudAppRiskLevel | String | 3 | O nível de risco associado a um aplicativo HTTP, conforme identificado por um proxy. Esse valor é geralmente específico para o proxy usado. | Nuvem |
| FileName | Cadeia de caracteres | ImNotMalicious.exe | O nome de arquivo transmitido pelas conexões de rede para protocolos como FTP e HTTP que fornecem as informações de nome de arquivo. | Arquivo |
| FilePath | String | C:\Malicious\ImNotMalicious.exe | O caminho completo, incluindo o nome do arquivo, do arquivo | Arquivo |
| FileHashMd5 | String | 51BC68715FC7C109DCEA406B42D9D78F | O valor de hash MD5 do arquivo transmitido pelas conexões de rede para protocolos. | Arquivo |
| FileHashSha1 | String | 491AE3... C299821476F4 | O valor de hash SHA1 do arquivo transmitido pelas conexões de rede para protocolos. | Arquivo |
| FileHashSha256 | String | 9B8F8EDB... C129976F03 | O valor de hash SHA256 do arquivo transmitido pelas conexões de rede para protocolos. | Arquivo |
| FileHashSha512 | String | 5E127D... F69F73F01F361 | O valor de hash SHA512 do arquivo transmitido pelas conexões de rede para protocolos. | Arquivo |
| FileExtension |  String | exe | O tipo de arquivo transmitido pelas conexões de rede para protocolos como FTP e HTTP. | Arquivo
| Filemimetype | String | aplicativo/MSWord | O tipo MIME do arquivo transmitido pelas conexões de rede para protocolos como FTP e HTTP | Arquivo |
| FileSize | Inteiro | 23500 | O tamanho do arquivo, em bytes, do arquivo transmitido pelas conexões de rede para protocolos. | Arquivo |
| HttpVersion | String | 2,0 | A versão da solicitação HTTP para conexões de rede HTTP/HTTPS. | Http |
| HttpRequestMethod | String | GET | O método HTTP para sessões de rede HTTP/HTTPS. | Http |
| HttpStatusCode | String | 404 | O código de status HTTP para sessões de rede HTTP/HTTPS. | Http |
| HttpContentType | String | dados de várias partes/formulário; limite = algo | O cabeçalho de tipo de conteúdo de resposta HTTP para sessões de rede HTTP/HTTPS. | Http |
| HttpReferrerOriginal | String | https://developer.mozilla.org/en-US/docs/Web/JavaScript | O cabeçalho referenciador HTTP para sessões de rede HTTP/HTTPS. | Http |
| HttpUserAgentOriginal | String | Mozilla/5.0 (Windows NT 10,0; WOW64) AppleWebKit/537.36 (KHTML, como Gecko) Chrome/83.0.4103.97 Safari/537.36 | O cabeçalho do agente de usuário HTTP para sessões de rede HTTP/HTTPS. | Http |
| HttpRequestXff | String | 120.12.41.1 | O cabeçalho HTTP X-Forwarded-for para sessões de rede HTTP/HTTPS. | Http |
| UrlCategory | String | Mecanismos de pesquisa | O agrupamento definido de uma URL (ou poderia ser apenas com base no domínio na URL) relacionado ao que está (ou seja: adulto, notícias, publicidade, domínios estacionados etc.) | url |
| UrlOriginal | String | https://contoso.com/fo/?k=v&q = u # f | A URL de solicitação HTTP para sessões de rede HTTP/HTTPS. | Url |
| UrlHostname | String | contoso.com | A parte do domínio de uma URL de solicitação HTTP para sessões de rede HTTP/HTTPS. | Url |
| ThreatCategory | String | Limpo | A categoria de uma ameaça identificada por um sistema de segurança, como o gateway de segurança da Web de um IPS e está associada a essa sessão de rede. | Ameaça |
| Threatid | String | TR. 124 | A ID de uma ameaça identificada por um sistema de segurança, como o gateway de segurança da Web de um IPS e está associada a essa sessão de rede. | Ameaça |
| ThreatName | String | Arquivo de teste EICAR | O nome da ameaça ou do malware identificado | Ameaça |
| AdditionalFields | Dinâmico (saco JSON) | {<br>Property1: "val1",<br>Property2: "val2"<br>} | Quando nenhuma coluna correspondente no esquema corresponde, os campos adicionais podem ser armazenados em um conjunto JSON.<br>Para a análise de tempo de consulta, é recomendável não usar esse método, pois os dados de empacotamento em um JSON diminuirão o desempenho da consulta. Em vez disso, é recomendável promover colunas adicionais.<br>Para cenários de análise de tempo de ingestão futura, os dados adicionais são, naturalmente, coletados nessa coluna de conjunto JSON. | Campo personalizado |
| 
