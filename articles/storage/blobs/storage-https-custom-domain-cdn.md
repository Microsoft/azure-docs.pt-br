---
title: "Usando a CDN do Azure para acessar blobs com domínios personalizados por HTTPS"
description: "Aprenda a integrar a CDN do Azure com o Armazenamento de Blobs para acessar blobs com domínios personalizados por HTTPS"
services: storage
documentationcenter: 
author: michaelhauss
manager: vamshik
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: mihauss
ms.openlocfilehash: 6bad04df324a374f6e8473890345cf516322abd6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-azure-cdn-to-access-blobs-with-custom-domains-over-https"></a>Usando a CDN do Azure para acessar blobs com domínios personalizados por HTTPS

A CDN (Rede de Distribuição de Conteúdo) do Azure agora dá suporte a HTTPS para nomes de domínio personalizados.
Você pode aproveitar esse recurso para acessar blobs de armazenamento usando seu domínio personalizado por HTTPS. Para fazer isso, primeiro será necessário habilitar a CDN do Azure em seu ponto de extremidade de blob e mapear a CDN para um nome de domínio personalizado. Uma vez realizadas essas etapas, a tarefa de habilitar HTTPS para o seu domínio personalizado é simplificada com a habilitação de apenas um clique e o gerenciamento do certificado completo, sem nenhum custo adicional ao preço normal da CDN.

Essa capacidade é importante porque ela permite proteger a privacidade e a integridade dos dados confidenciais do aplicativo Web quando estão em trânsito. Usar o protocolo SSL para assegurar o tráfego via HTTPS garante que esses dados sejam criptografados quando enviados pela Internet. O HTTPS fornece confiabilidade, autenticação e protege seus aplicativos Web contra ataques.

> [!NOTE]
> Além de dar suporte a SSL para nomes de domínio personalizado, a CDN do Azure pode ajudá-lo a dimensionar seu aplicativo para fornecer conteúdo de alta largura de banda em todo o mundo.
> Para obter mais informações, confira [Visão geral da CDN do Azure](../../cdn/cdn-overview.md).
>
>

## <a name="quick-start"></a>Início rápido

Estas são as etapas necessárias para habilitar HTTPS para o ponto de extremidade do Armazenamento de Blobs personalizado:

1.  [Integrar uma conta de armazenamento do Azure com a CDN do Azure](../../cdn/cdn-create-a-storage-account-with-cdn.md).
    Este artigo guia você pela criação de uma conta de armazenamento no Portal do Azure, se você ainda não fez isso.
2.  [Mapeie conteúdo da CDN do Azure para um domínio personalizado](../../cdn/cdn-map-content-to-custom-domain.md).
3.  [Habilite HTTPS em um domínio personalizado da CDN do Azure](../../cdn/cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>As Assinaturas de Acesso Compartilhado

Se o ponto de extremidade do Armazenamento de Blobs estiver configurado para não permitir acesso de leitura anônimo, você precisará fornecer um token [SAS (assinatura de acesso compartilhado)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) em cada solicitação que fizer ao domínio personalizado. Por padrão, os pontos de extremidade de Armazenamento de Blobs não permitem acesso de leitura anônimo. Para obter mais informações sobre assinaturas de acesso compartilhado, veja [Gerenciar acesso de leitura anônimo a contêineres e blobs](storage-manage-access-to-resources.md).

A CDN do Azure não respeita nenhuma restrição adicionada ao token SAS. Por exemplo, todos os tokens SAS têm uma hora da expiração. Isso significa que o conteúdo ainda pode ser acessado com uma SAS expirada até que esse conteúdo seja limpo dos nós de borda da CDN. Você pode controlar por quanto tempo os dados são armazenados em cache na CDN definindo o cabeçalho de resposta do cache. Veja [Gerenciar a expiração dos blobs de Armazenamento do Azure na CDN do Azure](../../cdn/cdn-manage-expiration-of-blob-content.md) para obter instruções.

Se você criar várias URLs de SAS para o mesmo ponto de extremidade de blob, é recomendável ativar o caching de cadeias de consulta da CDN do Azure. Isso é para garantir que cada URL seja tratada como uma entidade exclusiva. Para obter mais informações, confira [Controlar o comportamento de caching da CDN do Azure com cadeias de consulta](../../cdn/cdn-query-string.md).

## <a name="http-to-https-redirection"></a>Redirecionamento de HTTP para HTTPS

Você pode optar por redirecionar o tráfego de HTTP para HTTPS. Isso requer o uso da oferta do Azure CDN Premium da Verizon. Você precisa [Substituir o comportamento HTTP usando o mecanismo de regras da CDN do Azure](../../cdn/cdn-rules-engine.md) com a regra a seguir:

![](./media/storage-https-custom-domain-cdn/redirect-to-https.png)

“Cdn-endpoint-name” refere-se ao nome que você configurou para o ponto de extremidade da CDN. Você pode selecionar esse valor na lista suspensa. “Origin-path” refere-se ao caminho dentro de sua conta de armazenamento de origem em que reside o conteúdo estático.
Se você estiver hospedando todo o conteúdo estático em um único contêiner, substitua “origin-path” pelo nome do contêiner.

Para informações mais aprofundadas sobre regras, consulte os [Recursos do mecanismo de regras da CDN do Azure](../../cdn/cdn-rules-engine-reference-features.md).

## <a name="pricing-and-billing"></a>Preços e cobrança

Quando você acessa blobs por meio de uma CDN do Azure, você paga [preços de Armazenamento de Blobs](https://azure.microsoft.com/pricing/details/storage/blobs/) para o tráfego entre os nós de borda e a origem (Armazenamento de Blobs) e [preços de CDN](https://azure.microsoft.com/pricing/details/cdn/) para dados acessados por meio dos nós de borda.

Por exemplo, digamos que você tenha uma conta de armazenamento no Oeste dos EUA que está sendo acessada usando uma CDN do Azure. Se alguém no Reino Unido tenta acessar um dos blobs na conta de armazenamento por meio da CDN, o Azure verifica primeiro o nó de borda desse blob mais próximo do Reino Unido. Se for encontrado, ele acessará essa cópia do blob e usará preços de CDN, porque ele estará sendo acessado na CDN. Se não for encontrado, o Azure copiará o blob para o nó de borda, o que resultará em cobranças de saída e transação conforme especificado nos preços de Armazenamento de Blobs e, em seguida, acessará o arquivo no nó de borda, o que resultará na cobrança da CDN.

Ao examinar a [Página de preços da CDN](https://azure.microsoft.com/pricing/details/cdn/), observe que o suporte ao protocolo HTTPS para nomes de domínio personalizado só está disponível para a CDN do Azure dos produtos Verizon (Standard e Premium).

## <a name="next-steps"></a>Próximas etapas

[Configurar um nome de domínio personalizado para seu ponto de extremidade de Armazenamento de Blobs](storage-custom-domain-name.md)
