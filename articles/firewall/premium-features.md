---
title: Recursos de versão prévia do Firewall do Azure Premium
description: O firewall Premium do Azure é um serviço de segurança de rede gerenciado baseado em nuvem que protege os recursos de rede virtual do Azure.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 03/12/2021
ms.author: victorh
ms.custom: references_regions
ms.openlocfilehash: 4a8efff7ef53753e15a47e87a2bb82d0124ae997
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104590442"
---
# <a name="azure-firewall-premium-preview-features"></a>Recursos de versão prévia do Firewall do Azure Premium

Logotipo da:::image type="content" source="media/premium-features/pci-logo.png" alt-text="certificação PCI" border="false"::: do :::image type="content" source="media/premium-features/icsa-cert-firewall-small.png" alt-text="logotipo de certificação ICSA" border="false":::


> [!IMPORTANT]
> O firewall Premium do Azure está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 O Firewall do Azure Premium Versão Prévia é um firewall de última geração com funcionalidades necessárias para ambientes altamente sensíveis e regulamentados.

:::image type="content" source="media/premium-features/premium-overview.png" alt-text="Diagrama de visão geral do firewall do Azure Premium":::

O Azure firewall Premium Preview usa a política de firewall, um recurso global que pode ser usado para gerenciar centralmente seus firewalls usando o Gerenciador de firewall do Azure. Ao iniciar esta versão, todos os novos recursos são configuráveis somente por meio da política de firewall. As regras de firewall (clássicas) continuam com suporte e podem ser usadas para configurar os recursos de firewall padrão existentes.  A política de firewall pode ser gerenciada de forma independente ou com o Gerenciador de firewall do Azure. Uma política de firewall associada a um único firewall não tem nenhum custo.

> [!IMPORTANT]
> Atualmente, o SKU do firewall Premium não tem suporte em implantações de Hub seguro e configurações de túnel forçadas. 

O Azure firewall Premium Preview inclui os seguintes recursos:

- **Inspeção de TLS** – descriptografa o tráfego de saída, processa os dados e, em seguida, criptografa os dados e os envia para o destino.
- **IDPS** -um sistema de detecção e prevenção de intrusão na rede (IDPS) permite que você monitore atividades de rede para atividades mal-intencionadas, registre informações sobre essa atividade, relate-o e, opcionalmente, tente bloqueá-lo.
- **Filtragem de URL** – estende a capacidade de filtragem de FQDN do firewall do Azure para considerar uma URL inteira. Por exemplo, `www.contoso.com/a/c` em vez de `www.contoso.com` .
- **Categorias da Web** -os administradores podem permitir ou negar o acesso do usuário a categorias de sites, como sites de jogos de azar, sites de mídia social e outros.


## <a name="tls-inspection"></a>Inspeção TLS

O firewall Premium do Azure encerra as conexões TLS de saída e leste do oeste. Há suporte para a inspeção TLS de entrada com [aplicativo Azure gateway](../web-application-firewall/ag/ag-overview.md) permitindo criptografia de ponta a ponta. O Firewall do Azure faz as funções de segurança de valor agregado necessárias e criptografa novamente o tráfego que é enviado para o destino original.

> [!TIP]
> O TLS 1,0 e 1,1 estão sendo preteridos e não terão suporte. As versões TLS 1,0 e 1,1 do TLS/protocolo SSL (SSL) foram considerados vulneráveis e, embora ainda funcionem para permitir a compatibilidade com versões anteriores, elas não são recomendadas. Migre para o TLS 1,2 assim que possível.

Para saber mais sobre os requisitos de certificado de AC intermediária do Azure firewall Premium Preview, confira [certificados do Azure firewall Premium Preview](premium-certificates.md).

## <a name="idps"></a>IDPS

Um IDPS (sistema de detecção e prevenção de intrusão de rede) permite monitorar a sua rede em busca de atividades mal-intencionadas, registrar informações sobre essa atividade, relatá-las e, opcionalmente, tentar bloqueá-las. 

O Azure firewall Premium Preview fornece IDPS baseadas em assinatura para permitir a detecção rápida de ataques procurando padrões específicos, como sequências de bytes no tráfego de rede ou sequências de instruções mal-intencionadas conhecidas usadas por malware. As assinaturas do IDPS são totalmente gerenciadas e atualizadas continuamente.

As assinaturas/conjuntos de regras do firewall do Azure incluem:
- Ênfase na impressão digital de malware, comando e controle, kits de exploração e na atividade mal-intencionada intencionada perdida por métodos de prevenção tradicionais.
- Mais de 35.000 regras em mais de 50 categorias.
    - As categorias incluem comando e controle de malware, ataques de DoS, botnets, eventos informativos, explorações, vulnerabilidades, protocolos de rede SCADA, atividade de kit de exploração e muito mais.
- 20 a 40 + novas regras são lançadas todos os dias.
- Baixa classificação de falsos positivos usando a área restrita de malware de última geração e o loop de comentários de rede do sensor global.

O IDPS permite que você detecte ataques em todas as portas e protocolos para tráfego não criptografado. No entanto, quando o tráfego HTTPS precisa ser inspecionado, o Firewall do Azure pode usar seu recurso de inspeção TLS para descriptografar o tráfego e detectar melhor as atividades mal-intencionadas.  

A lista de bypass IDPS permite que você não filtre o tráfego para nenhum dos endereços IP, intervalos e sub-redes especificados na lista de bypass. 

## <a name="url-filtering"></a>Filtragem de URL

A filtragem de URL estende a capacidade de filtragem de FQDN do firewall do Azure para considerar uma URL inteira. Por exemplo, `www.contoso.com/a/c` em vez de `www.contoso.com` .  

A filtragem de URL pode ser aplicada em tráfego HTTP e HTTPS. Quando o tráfego HTTPS é inspecionado, o Azure firewall Premium Preview pode usar seu recurso de inspeção TLS para descriptografar o tráfego e extrair a URL de destino para validar se o acesso é permitido. A inspeção TLS requer aceitação no nível de regra do aplicativo. Uma vez habilitado, você pode usar URLs para filtragem com HTTPS. 

## <a name="web-categories"></a>Categorias da Web

As categorias da Web permitem que os administradores permitam ou neguem acesso de usuário a categorias de sites, como sites de jogos de azar, sites de mídia social e outros. As categorias da Web também serão incluídas no Azure firewall Standard, mas serão mais ajustadas no Azure firewall Premium Preview. Ao contrário do recurso de categorias da Web no SKU padrão que corresponde à categoria com base em um FQDN, a SKU Premium corresponde à categoria de acordo com a URL inteira para o tráfego HTTP e HTTPS. 

Por exemplo, se o Firewall do Azure interceptar uma solicitação HTTPS para `www.google.com/news` , a seguinte categorização será esperada: 

- Padrão de firewall – somente a parte FQDN será examinada e, portanto, será `www.google.com` categorizada como *mecanismo de pesquisa*. 

- Firewall Premium – a URL completa será examinada, portanto, `www.google.com/news` será categorizada como *notícias*.

As categorias são organizadas com base na gravidade sob **responsabilidade**, **alta largura de banda**, **uso comercial**, **perda de produtividade**, **navegação geral** e não **Categorizado**.

### <a name="category-exceptions"></a>Exceções de categoria

Você pode criar exceções para suas regras de categoria da Web. Crie uma coleção de regras Allow ou Deny separada com prioridade mais alta dentro do grupo de coleta de regras. Por exemplo, você pode configurar uma coleção de regras que permita `www.linkedin.com` com prioridade 100, com uma coleção de regras que nega a **rede social** com prioridade 200. Isso cria a exceção para a categoria da Web de **rede social** predefinida.

### <a name="categorization-change"></a>Alteração de categorização

Você pode solicitar uma alteração de categorização se:

 - Imagine que um FQDN ou uma URL estejam em uma categoria diferente 
 
ou 

- ter uma categoria sugerida para um FQDN ou uma URL não categorizada

Você é bem-vindo a enviar uma solicitação em [https://aka.ms/azfw-webcategories-request](https://aka.ms/azfw-webcategories-request) .
 
## <a name="supported-regions"></a>Regiões com suporte

O Azure firewall Premium Preview tem suporte nas seguintes regiões:

- Europa Ocidental (pública/Europa)
- Leste dos EUA (público/Estados Unidos)
- Leste da Austrália (pública/Austrália)
- Sudeste Asiático (público/Pacífico Asiático)
- Sul do Reino Unido (público/Reino Unido)
- Europa Setentrional (pública/Europa)
- Leste dos EUA 2 (público/Estados Unidos)
- EUA Central do Sul (pública/Estados Unidos)
- Oeste dos EUA 2 (público/Estados Unidos)
- Oeste dos EUA (público/Estados Unidos)
- EUA Central (pública/Estados Unidos)
- EUA Central Norte (pública/Estados Unidos)
- Leste do Japão (público/Japão)
- Ásia Oriental (pública/Pacífico Asiático)
- Centro do Canadá (público/Canadá)
- França central (pública/França)
- Norte da África do Sul (África pública/sul)
- Norte dos EAU (pública/dos EAU)
- Norte da Suíça (pública/Suíça)
- Sul do Brasil (público/Brasil)
- Leste da Noruega (público/Noruega)
- Austrália Central (pública/Austrália)
- Austrália Central 2 (pública/Austrália)
- Sudeste da Austrália (pública/Austrália)
- Leste do Canadá (público/Canadá)
- EUA Central EUAP (pública/canário (EUA))
- Sul da França (público/França)
- Oeste do Japão (público/Japão)
- Sul da Coreia (público/Coreia)
- EAU Central (pública/dos EAU)
- Oeste do Reino Unido (público/Reino Unido)
- EUA Central ocidental (pública/Estados Unidos)
- Índia ocidental (pública/Índia)


## <a name="known-issues"></a>Problemas conhecidos

O Azure firewall Premium Preview tem os seguintes problemas conhecidos:

|Problema  |Descrição  |Atenuação  |
|---------|---------|---------|
|A inspeção TLS tem suporte apenas na porta HTTPS Standard|A inspeção TLS dá suporte somente a HTTPS/443. |Nenhum. Outras portas terão suporte no GA.|
|Suporte ESNI para resolução de FQDN em HTTPS|Não há suporte para SNI criptografada no handshake HTTPS.|Hoje, somente o Firefox dá suporte a ESNI por meio de configuração personalizada. A solução alternativa sugerida é desabilitar esse recurso.|
|Certificados de cliente (TLS)|Os certificados de cliente são usados para criar uma confiança de identidade mútua entre o cliente e o servidor. Os certificados de cliente são usados durante uma negociação de TLS. O Firewall do Azure renegocia uma conexão com o servidor e não tem acesso à chave privada dos certificados do cliente.|Nenhum|
|QUIC/HTTP3|QUIC é a nova versão principal do HTTP. É um protocolo baseado em UDP em 80 (plano) e 443 (SSL). Não haverá suporte para a inspeção FQDN/URL/TLS.|Configure passando UDP 80/443 como regras de rede.|
|Não há suporte para o Secure Hub e o túnel forçado no Premium|Atualmente, o SKU do firewall Premium não tem suporte em implantações de Hub seguro e configurações de túnel forçadas.|Correção agendada para GA.|
Certificados assinados de cliente não confiáveis|Os certificados assinados pelo cliente não são confiáveis pelo firewall uma vez recebidos de um servidor Web baseado na intranet.|Correção agendada para GA.
|Endereços IP de origem e de destino errados em alertas para IDPS com a inspeção TLS.|Quando você habilita a inspeção TLS e IDPS emite um novo alerta, o endereço IP de origem/destino exibido está errado (o endereço IP interno é exibido em vez do endereço IP original).|Correção agendada para GA.|
|Endereço IP de origem errado em alertas com IDPS para HTTP (sem a inspeção TLS).|Quando o tráfego HTTP de texto sem formatação está em uso e IDPS emite um novo alerta e o destino é público como um endereço IP, o endereço IP de origem exibido está errado (o endereço IP interno é exibido em vez do endereço IP original).|Correção agendada para GA.|
|Propagação de Certificado|Depois que um certificado de autoridade de certificação é aplicado no firewall, pode levar entre 5-10 minutos para que o certificado entre em vigor.|Correção agendada para GA.|
|Bypass de IDPS|O bypass de IDPS não funciona para tráfego de protocolo TLS finalizado, e não há suporte para endereços IP de origem e grupos de IPS de origem.|Correção agendada para GA.|
|Suporte a TLS 1,3|O TLS 1,3 tem suporte parcial. O túnel TLS do cliente para o firewall baseia-se no TLS 1,2 e o firewall para o servidor Web externo é baseado no TLS 1,3.|As atualizações estão sendo investigadas.|
|Ponto de extremidade particular do keyvault|O keyvault dá suporte ao acesso de ponto de extremidade privado para limitar sua exposição à rede. Os serviços confiáveis do Azure podem ignorar essa limitação se uma exceção for configurada conforme descrito na [documentação do keyvault](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services). O Firewall do Azure não está listado atualmente como um serviço confiável e não pode acessar o Key Vault.|Correção agendada para GA.|


## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre os certificados do firewall Premium do Azure](premium-certificates.md)
- [Implantar e configurar a versão prévia do Firewall do Azure Premium](premium-deploy.md)
- [Migrar para o Azure firewall Premium Preview](premium-migrate.md)
