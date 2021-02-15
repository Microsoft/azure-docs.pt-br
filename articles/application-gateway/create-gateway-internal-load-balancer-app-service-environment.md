---
title: Solucionar problemas de Gateway de Aplicativo no Azure – ILB ASE | Microsoft Docs
description: Saiba como solucionar problemas de um gateway de aplicativo usando um Balanceador de Carga Interno com um ambiente de serviço de aplicativo no Azure
services: vpn-gateway
documentationCenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/09/2020
ms.author: genli
ms.openlocfilehash: 8861e850e168169762d95c44a54b6a88a036f396
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84628527"
---
# <a name="back-end-server-certificate-is-not-allow-listed-for-an-application-gateway-using-an-internal-load-balancer-with-an-app-service-environment"></a>O certificado de servidor back-end não é permitido listado para um gateway de aplicativo usando um Load Balancer interno com um Ambiente do Serviço de Aplicativo

Este artigo soluciona o problema a seguir: um certificado não é permitido listado quando você cria um gateway de aplicativo usando um Load Balancer interno (ILB) junto com um Ambiente do Serviço de Aplicativo (ASE) no back-end ao usar o TLS de ponta a ponta no Azure.

## <a name="symptoms"></a>Sintomas

Quando você cria um gateway de aplicativo usando um ILB com um ASE no back-end, o servidor back-end pode se tornar não íntegro. Esse problema ocorrerá se o certificado de autenticação do gateway de aplicativo não corresponder ao certificado configurado no servidor back-end. Observe o seguinte cenário como um exemplo:

**Configuração do Gateway de Aplicativo:**

- **Ouvinte:** Multissite
- **Porta:** 443
- **Nome do host:** test.appgwtestase.com
- **Certificado SSL:** CN=test.appgwtestase.com
- **Pool de back-end:** endereço IP ou FQDN
- **Endereço IP:**: 10.1.5.11
- **Configurações de HTTP:** HTTPS
- **Porta:**: 443
- **Investigação personalizada:** nome do host – test.appgwtestase.com
- **Certificado de autenticação:** .cer do test.appgwtestase.com
- **Integridade do back-end:** Não íntegro – o certificado do servidor back-end não é permitido listado com o gateway de aplicativo.

**Configuração do ASE:**

- **IP do ILB:** 10.1.5.11
- **Nome de domínio:** appgwtestase.com
- **Serviço de Aplicativo:** test.appgwtestase.com
- **Associação SSL:** SNI SSL – CN=test.appgwtestase.com

Ao acessar o gateway de aplicativo, você receberá a seguinte mensagem de erro porque o servidor back-end está não íntegro:

**502 – o servidor Web recebeu uma resposta inválida ao atuar como um gateway ou servidor proxy.**

## <a name="solution"></a>Solução

Quando você não usar um nome de host para acessar um site HTTPS, o servidor back-end retornará o certificado configurado no site padrão, caso o SNI esteja desabilitado. Para um ILB ASE, o certificado padrão é fornecido pelo certificado ILB. Se não houver certificados configurados para o ILB, o certificado será fornecido pelo aplicativo ASE.

Quando você usar um FQDN (nome de domínio totalmente qualificado) para acessar o ILB, o servidor back-end retornará o certificado correto que será carregado nas configurações de HTTP. Se esse não for o caso, considere as seguintes opções:

- Use o FQDN no pool de back-end do gateway de aplicativo para apontar para o endereço IP do ILB. Essa opção somente funcionará se você tiver uma zona DNS privada ou um DNS personalizado configurado. Caso contrário, será necessário criar um registro "A" para um DNS público.

- Use o certificado carregado no ILB ou o certificado padrão (certificado ILB) nas configurações de HTTP. O gateway de aplicativo obterá o certificado quando acessar o IP do ILB para a investigação.

- Use um certificado curinga no ILB e no servidor de back-end, de forma que para todos os sites, o certificado seja o mesmo. No entanto, essa solução é possível apenas no caso de subdomínios e não se cada um dos sites exigir nomes de host diferentes.

- Desmarque a opção **Uso para serviço de aplicativo** para o gateway de aplicativo, caso você esteja usando o endereço IP do ILB.

Para reduzir a sobrecarga, você poderá carregar o certificado ILB nas configurações de HTTP para fazer com que o caminho da investigação funcione. (Esta etapa é apenas para a listagem de permissão. Ele não será usado para comunicação TLS.) Você pode recuperar o certificado ILB acessando o ILB com seu endereço IP do seu navegador em HTTPS e exportando o certificado TLS/SSL em um formato CER codificado em base-64 e carregando o certificado nas respectivas configurações de HTTP.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
