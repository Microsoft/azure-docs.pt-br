---
title: Azure Defender para registros de contêiner – Benefícios e recursos
description: Conheça os benefícios e os recursos do Azure Defender para registros de contêiner.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: ab2ad15da9b1676924197d28e734f6baf59a02ef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176630"
---
# <a name="introduction-to-azure-defender-for-container-registries"></a>Introdução ao Azure Defender para registros de contêiner

O ACR (Registro de Contêiner do Azure) é um serviço de registro gerenciado e privado do Docker que armazena e gerencia suas imagens de contêiner para implantações do Azure em um registro central. Ele é baseado no Registro do Docker 2.0 de software livre.

Para proteger todos os registros baseados no Azure Resource Manager da sua assinatura, habilite o **Azure Defender para registros de contêiner** no nível de assinatura. A Central de Segurança examinará as imagens enviadas por push para o registro, importadas para o registro ou todas as imagens extraídas nos últimos 30 dias. Esse recurso é cobrado por imagem.

[!INCLUDE [Defender for container registries availability info](../../includes/security-center-availability-defender-for-container-registries.md)]

## <a name="what-are-the-benefits-of-azure-defender-for-container-registries"></a>Quais são os benefícios do Azure Defender para registros de contêiner?

A Central de Segurança identifica os registros do ACR baseados no Azure Resource Manager da sua assinatura e fornece diretamente a avaliação e o gerenciamento nativos do Azure das vulnerabilidades para as imagens do registro.

O **Azure Defender para registros de contêiner** inclui um verificador de vulnerabilidade para verificar as imagens nos seus registros do Registro de Contêiner do Azure baseados no Azure Resource Manager e fornecer uma visibilidade mais profunda das vulnerabilidades das suas imagens. O verificador integrado é fornecido pela Qualys, o fornecedor líder do setor de verificação de vulnerabilidades.

Quando forem encontrados problemas – pela Qualys ou pela Central de Segurança – você será notificado no painel da Central de Segurança. Para cada vulnerabilidade, a Central de Segurança fornece recomendações práticas, juntamente com uma classificação de severidade e diretrizes sobre como corrigir o problema. Para obter detalhes das recomendações da Central de Segurança para contêineres, confira a [lista de referência de recomendações](recommendations-reference.md#recs-compute).

A Central de Segurança filtra e classifica as descobertas do verificador. Quando uma imagem está íntegra, a Central de Segurança marca a imagem como tal. A Central de Segurança gera recomendações de segurança apenas para as imagens que têm problemas a serem resolvidos. Ela fornece detalhes de cada vulnerabilidade relatada e uma classificação de severidade. Além disso, oferece diretrizes de como corrigir as vulnerabilidades específicas encontradas em cada imagem.

Notificando você apenas quando há problemas, a Central de Segurança reduz o potencial de alertas informativos indesejados.


> [!TIP]
> Para saber mais sobre os recursos de segurança de contêiner da Central de Segurança, confira:
>
> - [Central de Segurança do Azure e segurança do contêiner](container-security.md)
> - [Introdução ao Azure Defender para Kubernetes](defender-for-kubernetes-introduction.md)

## <a name="when-are-images-scanned"></a>Quando as imagens são verificadas?

Há três gatilhos para uma verificação de imagem:

- **No momento do push**: sempre que uma imagem é enviada por push ao registro, a Central de Segurança verifica automaticamente essa imagem. Para disparar a verificação de uma imagem, envie-a por push ao repositório.

- **Recém-extraída**: como novas vulnerabilidades são descobertas todos os dias, o **Azure Defender para registros de contêiner** também verifica qualquer imagem que tenha sido extraída nos últimos 30 dias. Não há nenhum custo adicional para um novo exame; conforme mencionado acima, você é cobrado uma vez por imagem.

- **No momento da importação**: o Registro de Contêiner do Azure tem ferramentas de importação para levar as imagens do Docker Hub, do Registro de Contêiner da Microsoft ou de outro registro de contêiner do Azure para o seu registro. O **Azure Defender para registros de contêiner** examina as imagens compatíveis importadas. Saiba mais em [Importar imagens de contêiner para um registro de contêiner](../container-registry/container-registry-import-images.md).
 
Normalmente, a verificação é concluída em 2 minutos, mas pode levar até 15 minutos. As descobertas são disponibilizadas como recomendações da Central de Segurança, como esta:

[![Exemplo de recomendação da Central de Segurança do Azure sobre as vulnerabilidades descobertas em uma imagem hospedada do ACR (Registro de Contêiner do Azure)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)


## <a name="how-does-security-center-work-with-azure-container-registry"></a>Como a Central de Segurança funciona com o Registro de Contêiner do Azure

Veja abaixo um diagrama de alto nível dos componentes e dos benefícios de proteger seus registros com a Central de Segurança.

![Visão geral de alto nível da Central de Segurança do Azure e do ACR (Registro de Contêiner do Azure)](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq-for-azure-container-registry-image-scanning"></a>Perguntas frequentes sobre a verificação de imagens do Registro de Contêiner do Azure

### <a name="how-does-security-center-scan-an-image"></a>Como a Central de Segurança examina uma imagem?
A Central de Segurança extrai a imagem do registro e a executa em uma área restrita isolada com o verificador Qualys. O verificador extrai uma lista de vulnerabilidades conhecidas.

A Central de Segurança filtra e classifica as descobertas do verificador. Quando uma imagem está íntegra, a Central de Segurança marca a imagem como tal. A Central de Segurança gera recomendações de segurança apenas para as imagens que têm problemas a serem resolvidos. Notificando você apenas quando há problemas, a Central de Segurança reduz o potencial de alertas informativos indesejados.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Posso obter os resultados da verificação por meio da API REST?
Sim. Os resultados ficam na [API REST de Subavaliações](/rest/api/securitycenter/subassessments/list/). Além disso, você pode usar o ARG (Azure Resource Graph), a API semelhante ao Kusto para todos os seus recursos: uma consulta pode buscar uma verificação específica.

### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>Quais tipos de registro são verificados? Quais tipos são cobrados?
Para obter uma lista dos tipos de registros de contêiner compatíveis com o Azure Defender para registros de contêiner, confira [Disponibilidade](#availability).

Se você conecta Registros sem suporte à sua assinatura do Azure, o Azure Defender não os verifica nem cobra por eles.

### <a name="can-i-customize-the-findings-from-the-vulnerability-scanner"></a>Posso personalizar as descobertas do verificador de vulnerabilidades?
Sim. Caso você tenha uma necessidade organizacional para ignorar uma descoberta, em vez de corrigi-la, você pode opcionalmente desabilitá-la. As descobertas desabilitadas não afetam sua classificação de segurança nem geram um ruído indesejado.

[Saiba mais sobre como criar regras para desabilitar as descobertas da ferramenta de avaliação de vulnerabilidade integrada](defender-for-container-registries-usage.md#disable-specific-findings-preview).

### <a name="why-is-security-center-alerting-me-to-vulnerabilities-about-an-image-that-isnt-in-my-registry"></a>Por que a Central de Segurança está me alertando sobre as vulnerabilidades de uma imagem que não está no meu registro?
A Central de Segurança fornece avaliações de vulnerabilidade para todas as imagens enviadas por push ou extraídas de um registro. Algumas imagens podem reutilizar marcações de uma imagem que já foi verificada. Por exemplo, você pode transferir a marcação "Mais recente" sempre que adicionar uma imagem a um resumo. Nesses casos, a imagem antiga ainda existe no registro e pode ser extraída pelo resumo. Se a imagem tiver descobertas de segurança e for extraída, ela vai expor vulnerabilidades.


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Verificar se há vulnerabilidades nas imagens](defender-for-container-registries-usage.md)