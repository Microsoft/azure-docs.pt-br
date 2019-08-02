---
title: Tutorial – Acessar blobs de armazenamento usando um domínio personalizado da CDN do Azure via HTTPS | Microsoft Docs
description: ''
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: e3b10760b95662570c8a6e802a57e1073e2faa0a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593380"
---
# <a name="tutorial-access-storage-blobs-using-an-azure-cdn-custom-domain-over-https"></a>Tutorial: Acessar blobs de armazenamento usando um domínio personalizado da CDN do Azure via HTTPS

Depois de integrar sua conta de armazenamento do Azure à CDN (Rede de Distribuição de Conteúdo) do Azure, você poderá adicionar um domínio personalizado e habilitar o HTTPS nesse domínio para o ponto de extremidade de Armazenamento de Blobs personalizado. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de concluir as etapas deste tutorial, primeiro você precisará integrar sua conta de armazenamento do Azure à CDN do Azure. Para saber mais, confira [Início Rápido: Integrar uma conta de armazenamento do Azure à CDN do Azure](cdn-create-a-storage-account-with-cdn.md).

## <a name="add-a-custom-domain"></a>Adicionar um domínio personalizado
Quando que você criar um ponto de extremidade da CDN em seu perfil, o nome do ponto de extremidade, que é um subdomínio de azureedge.net, será incluído na URL que distribui o conteúdo da CDN por padrão. Você também tem a opção de associar um domínio personalizado a um ponto de extremidade da CDN. Com essa opção, você distribui o conteúdo com um domínio personalizado na URL em vez de em um nome de ponto de extremidade. Para adicionar um domínio personalizado ao ponto de extremidade, siga as instruções deste tutorial: [Adicionar um domínio personalizado ao ponto de extremidade da CDN do Azure](cdn-map-content-to-custom-domain.md).

## <a name="configure-https"></a>Configurar o HTTPS
Usando o protocolo HTTPS no domínio personalizado, você garante que seus dados sejam entregues com segurança na Internet por meio da criptografia TLS/SSL. Quando seu navegador da Web é conectado a um site da Web via HTTPS, ele valida o certificado de segurança do site da Web e verifica se ele é emitido por uma autoridade de certificação legítima. Para configurar o HTTPS no domínio personalizado, siga as instruções deste tutorial: [Configurar o HTTPS em um domínio personalizado da CDN do Azure](cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>As Assinaturas de Acesso Compartilhado
Se o ponto de extremidade do Armazenamento de Blobs estiver configurado para não permitir o acesso anônimo de leitura, você deverá fornecer um token [SAS (Assinatura de Acesso Compartilhado)](cdn-sas-storage-support.md) em cada solicitação que fizer ao domínio personalizado. Por padrão, os pontos de extremidade de Armazenamento de Blobs não permitem acesso de leitura anônimo. Para obter mais informações sobre o SAS, confira [Gerenciando o acesso anônimo de leitura a contêineres e blobs](../storage/blobs/storage-manage-access-to-resources.md).

A CDN do Azure ignora as restrições adicionadas ao token SAS. Por exemplo, todos os tokens SAS têm um tempo de expiração, o que significa que o conteúdo ainda pode ser acessado com uma SAS expirada até que o conteúdo seja limpo dos servidores POP (ponto de presença) da CDN. Você pode controlar por quanto tempo os dados são armazenados em cache na CDN do Azure definindo o cabeçalho de resposta do cache. Para obter mais informações, confira [Gerenciando a expiração dos blobs do Armazenamento do Azure na CDN do Azure](cdn-manage-expiration-of-blob-content.md).

Se você criar várias URLs SAS para o mesmo ponto de extremidade de blob, considere a possibilidade de habilitar o cache da cadeia de consulta. Isso garante que cada URL seja tratada como uma entidade exclusiva. Para obter mais informações, confira [Controlando o comportamento de cache da CDN do Azure com cadeias de consulta](cdn-query-string.md).

## <a name="http-to-https-redirection"></a>Redirecionamento de HTTP para HTTPS
Você pode optar por redirecionar o tráfego HTTP para HTTPS com a criação de uma [regra de Redirecionamento de URL](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect) com o [mecanismo de regras da CDN do Azure](cdn-verizon-premium-rules-engine.md). Essa opção exige um perfil da **CDN do Azure Premium da Verizon**.

![Regra de redirecionamento de URL](./media/cdn-storage-custom-domain-https/cdn-url-redirect-rule.png)

Nessa regra, *Cdn-endpoint-name* refere-se ao nome que você configurou para o ponto de extremidade da CDN, que pode ser selecionado na lista suspensa. O valor de *origin-path* refere-se ao caminho em sua conta de armazenamento de origem na qual reside o conteúdo estático. Se você estiver hospedando todo o conteúdo estático em um único contêiner, substitua *origin-path* pelo nome do contêiner.

## <a name="pricing-and-billing"></a>Preços e cobrança
Quando você acessa blobs por meio da CDN do Azure, você paga os [preços do Armazenamento de Blobs](https://azure.microsoft.com/pricing/details/storage/blobs/) para o tráfego entre os servidores POP e a origem (Armazenamento de Blobs) e os [preços da CDN do Azure](https://azure.microsoft.com/pricing/details/cdn/) para os dados acessados por meio dos servidores POP.

Se, por exemplo, você tiver uma conta de armazenamento nos Estados Unidos que esteja sendo acessada usando a CDN do Azure e alguém na Europa tentar acessar um dos blobs nessa conta de armazenamento por meio da CDN do Azure, a CDN do Azure verificará primeiro o POP mais próximo da Europa para esse blob. Se ele for encontrado, a CDN do Azure acessará essa cópia do blob e usará o preço da CDN, porque ela está sendo acessada na CDN do Azure. Se ele não for encontrado, a CDN do Azure copiará o blob para o servidor POP, que resultará em encargos de saída e de transação, conforme especificado no preço do Armazenamento de Blobs e, em seguida, acessará o arquivo no servidor POP, que resultará na cobrança da CDN do Azure.

## <a name="next-steps"></a>Próximas etapas
[Tutorial: definir regras de cache da CDN do Azure](cdn-caching-rules-tutorial.md)




