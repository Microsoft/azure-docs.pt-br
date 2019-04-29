---
title: Transição para Certificados de Autoridade de Certificação públicos para gateways P2S | Gateway de VPN do Azure | Microsoft Docs
description: Este artigo ajuda você a realizar a transição com êxito para os novos Certificados de Autoridade de Certificação públicos para gateways P2S.
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: conceptual
origin.date: 03/12/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.openlocfilehash: 29f2aeee53e07adfeafb8017c489c0b830f24b36
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60859640"
---
# <a name="transition-to-a-public-ca-gateway-certificate-for-p2s"></a>Fazer a transição para um certificado de gateway de autoridade de certificação pública para P2S

O Gateway de VPN do Azure não emite mais certificados autoassinados no nível do Azure para seus gateways em conexões P2S. Os certificados emitidos agora são assinados por uma AC (Autoridade de Certificação) pública. No entanto, alguns gateways mais antigos podem ainda estar usando certificados autoassinados. Esses certificados autoassinados estão próximos de suas datas de validade e será necessário fazer a transição para os Certificados de Autoridade de Certificação públicos.

>[!NOTE]
> * Os certificados autoassinados usados para autenticação do cliente de P2S não são afetados por essa alteração de certificado no nível do Azure. Você pode continuar a emitir e usar certificados autoassinados como de costume.
>

Os certificados no contexto em questão são um certificado no nível do Azure adicional. Eles não fazem parte da cadeia confiável que você usa ao gerar seus próprios certificados raiz autoassinados e certificados de cliente para autenticação. Esses certificados não serão afetados e vão expirar nas datas que você estipulou.

Anteriormente, um certificado autoassinado para o gateway (emitido pelo Azure nos bastidores) precisava ser atualizado a cada 18 meses. Os arquivos de configuração do cliente VPN precisavam ser gerados e reimplantados para todos os clientes P2S. Migrar para Certificados de Autoridade de Certificação públicos eliminará essa limitação. Além da transição para certificados, essa alteração também fornece melhorias de plataforma, métricas aprimoradas e maior estabilidade.

Apenas os gateways mais antigos serão afetados por essa alteração. Se o certificado do gateway precisar ser transferido, você receberá comunicação ou notificação no portal do Azure. É possível verificar se o seu gateway é afetado, usando as etapas neste artigo.

> [!IMPORTANT]
> A transição está agendada para 12 de março de 2019, a partir das 18:00 UTC. Se você preferir uma janela de tempo diferente, poderá criar um caso de suporte. Faça e finalize sua solicitação com pelo menos 24 horas de antecedência.  É possível solicitar uma das seguintes janelas:
>
> * 06:00 UTC em 25 de fevereiro
> * 18:00 UTC em 25 de fevereiro
> * 06:00 UTC em 1º de março
> * 18:00 UTC em 1º de março
>
> **Todos os demais gateways farão a transição em 12 de março de 2019, a partir das 18:00 UTC**.
>
> Os clientes receberão um email quando o gateway concluir o processo de transição.
> 

## <a name="1-verify-your-certificate"></a>1. Verificar o certificado

### <a name="resource-manager"></a>Gerenciador de Recursos

1. Verifique se você é afetado por esta atualização. Baixe a configuração atual do cliente VPN, usando as etapas [neste artigo](point-to-site-vpn-client-configuration-azure-cert.md).

2. Abra ou extraia o arquivo zip e navegue até a pasta "Genérico". Na pasta Genérico você verá dois arquivos, um dos quais é *VPNSettings.xml*.
3. Abra *VPNSettings.xml* em qualquer editor/visualizador de XML. No arquivo XML, pesquise os seguintes campos:

   * `<ServerCertRootCn>DigiCert Global Root CA</ServerCertRootCn>`
   * `<ServerCertIssuerCn>DigiCert Global Root CA</ServerCertIssuerCn>`
4. Se *ServerCertRotCn* e *ServerCertIssuerCn* estiverem como “DigiCert Global Root CA”, você não será afetado pela atualização e não precisará seguir as etapas descritas neste artigo. No entanto, se exibirem informações diferentes, o certificado do gateway faz parte da atualização e será transitado.

### <a name="classic"></a>Clássico

1. Em um computador cliente, navegue até o caminho `%appdata%/Microsoft/Network/Connections/Cm/<gatewayID>`. Na pasta de ID do Gateway, você pode exibir o certificado.
2. Na guia Geral do certificado, verifique se a autoridade emissora é “DigiCert Global Root CA”. Se aparecer algo diferente dessa autoridade emissora, seu certificado de gateway é parte da atualização e fará parte da transição.

## <a name="2-check-certificate-transition-schedule"></a>2. Verificar agendamento da transição de certificado

Se o certificado fizer parte da atualização, o certificado do gateway será transitado. Consulte a observação **Importante** para verificar os tempos de transição. Após a atualização, clientes P2S não poderão conectar usando o perfil antigo. Será necessário gerar novos perfis de clientes VPN e instalá-los aos respectivos clientes.

## <a name="3-generate-vpn-client-configuration-profile"></a>3. Gerar perfil de configuração do cliente VPN

Após a transição do certificado, baixe o novo perfil de VPN (arquivos de configuração de cliente VPN) no portal do Azure. Para obter as etapas, consulte [Criar e instalar arquivos de configuração de cliente VPN](point-to-site-vpn-client-configuration-azure-cert.md). Não é necessário gerar novos certificados do cliente.

## <a name="4-deploy-vpn-client-profile"></a>4. Implantar perfil de cliente VPN

Implante o novo perfil em todos os clientes VPN ponto a site. Os clientes VPN perderão a conectividade até que o novo perfil de VPN para conexões ponto a site seja baixado e implantado nos dispositivos clientes. Os certificados cliente que já estão instalados em computadores clientes VPN não precisarão ser reemitidos.

## <a name="5-connect-the-vpn-client"></a>5. Conectar o cliente VPN

Conecte o Azure a partir do cliente VPN como faria normalmente.