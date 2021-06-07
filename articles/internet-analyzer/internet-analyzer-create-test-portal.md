---
title: Criar um teste do Analisador de Internet usando o Portal | Microsoft Docs
description: Neste artigo, saiba como criar seu primeiro teste do Analisador de Internet.
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 64bbed9b558d4c20889b28a5247e2113d20daa77
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "73509830"
---
# <a name="create-an-internet-analyzer-test-using-portal-preview"></a>Criar um teste do Analisador de Internet usando o Portal (Versão prévia)

Há duas maneiras de criar um recurso do Analisador de Internet – por meio do portal do Azure ou usando a [CLI](internet-analyzer-cli.md). Esta seção ajuda você a criar um novo recurso do Analisador de Internet do Azure por meio da experiência com o portal.

> [!IMPORTANT]
> Essa versão prévia pública é fornecida sem um SLA e não deve ser usada para cargas de trabalho de produção. Determinados recursos podem não ter suporte, podem ter restrição ou podem não estar disponíveis em todos os locais do Azure. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.
>

## <a name="before-you-begin"></a>Antes de começar

A versão prévia pública está disponível para uso global. No entanto, o armazenamento de dados está limitado ao *Oeste dos EUA 2* durante a versão prévia.

## <a name="basics"></a>Noções básicas

1. Obtenha acesso à versão prévia do Analisador de Internet seguindo as instruções em **Como fazer para participar da versão prévia?** nas [Perguntas frequentes do Analisador de Internet do Azure](internet-analyzer-faq.md).
2. Na página inicial do [portal do Azure](https://preview.portal.azure.com), clique em **+ Criar um recurso**. Atualmente, o Analisador de Internet só está disponível na versão prévia do portal do Azure.
3. Na página **Novo**, pesquise "Analisador de Internet" no campo *Pesquisar no Marketplace*.
4. Clique em **Analisador de Internet (versão prévia)**. Verifique se o fornecedor é a *Microsoft* e se a categoria é *Rede*.
5. Na página **Analisador de Internet (versão prévia)**, clique em **Criar** para abrir a página **Criar um Analisador de Internet**.
6. Especifique as seguintes definições de configuração para o recurso do Analisador de Internet:

    * **Assinatura:** a assinatura do Azure para hospedar o novo recurso do Analisador de Internet. **_Use a mesma ID de Assinatura usada para solicitar acesso à versão prévia._**
    * **Grupo de Recursos:** o grupo de recursos do Azure no qual o recurso do Analisador de Internet será criado. Se você não tiver um grupo de recursos atualmente, crie um.
    * **Nome:** o nome do novo perfil de recurso do Analisador de Internet.
    * **Região**: a região pública do Azure na qual o recurso será criado. Durante a versão prévia, somente a região *Oeste dos EUA 2* estará disponível.

7. Quando terminar de especificar as configurações de perfil, clique em **Revisar + criar**.

## <a name="configuration"></a>Configuração

A conclusão das etapas básicas é um pré-requisito para a configuração de um teste e a incorporação do cliente JavaScript. Depois de criar um perfil, acesse **Configurações > Configuração** para configurar seu primeiro teste.

1. Dê um nome ao teste na caixa **Nome do Teste**.
2. Adicione uma descrição para o teste no campo **Descrição**.
3. Clique em **Configurar ponto de extremidade** e uma guia será exibida do lado direito. Escolha o tipo de ponto de extremidade que você deseja configurar: uma única região do Azure, várias regiões do Azure ou um ponto de extremidade personalizado.

    >
    ***Pontos de extremidade pré-configurados: combinações de uma única região e várias regiões do Azure***
    * Escolha uma região ou um conjunto em uma [lista pré-configurada de pontos de extremidade do Azure](internet-analyzer-faq.md).
    * Em seguida, escolha o tipo de aplicativo ou a arquitetura de entrega de conteúdo que você gostaria de avaliar.
        * Região do Azure única: aceleração do site ([Azure Front Door](https://azure.microsoft.com/services/frontdoor/)), armazenamento em cache de conteúdo estático ([CDN do Azure para Microsoft](https://azure.microsoft.com/services/cdn/)) ou nenhum.
        * Várias regiões do Azure: aceleração do site ([Azure Front Door](https://azure.microsoft.com/services/frontdoor/)), direcionamento de tráfego de DNS ([Gerenciador de Tráfego do Azure](https://azure.microsoft.com/services/traffic-manager/))  

    ***Pontos de extremidade personalizados***
    * Siga as instruções na página [Criar ponto de extremidade personalizado](internet-analyzer-custom-endpoint.md).
    * Cole o local da URL HTTPS da imagem de um pixel no portal.
    >

4. Clique em **Adicionar** para adicionar o ponto de extremidade ao seu teste.
5. Repita as etapas 1 a 4 para configurar o segundo ponto de extremidade. O ponto de extremidade B sempre é medido em relação ao ponto de extremidade A – quando configurar pontos de extremidades, considere qual ponto de extremidade deverá ser seu controle de teste.
6. Clique no botão **Salvar** para salvar o teste. Depois de salvar um teste, você já não poderá editar os pontos de extremidade de um teste específico.
7. Escolha os testes que você deseja iniciar e clique em **Iniciar teste**. Isso alterará o **_Estado_ *_ de seus testes para _* _Em execução_**. Você pode iniciar testes a qualquer momento, mas o cliente JavaScript deve ser inserido para que o teste comece a coletar medidas.
8. Adicione mais testes quando desejar. Observe que o cliente JavaScript exclusivo não será gerado até que um teste seja criado.

## <a name="embed-client"></a>Inserir cliente

Para iniciar um teste, o cliente JavaScript deverá estar inserido em seu aplicativo Web. Depois de configurar pelo menos um teste, clique em **Revisar + criar**, vá para **Configurações > Configuração** e copie o cliente JavaScript. Confira instruções específicas na página [Inserir cliente do Analisador de Internet](internet-analyzer-embed-client.md).  

## <a name="next-steps"></a>Próximas etapas

* Leia as [Perguntas frequentes do Analisador de Internet](internet-analyzer-faq.md)
* Saiba mais sobre como inserir o [Cliente do Analisador de Internet](internet-analyzer-embed-client.md) e criar um [ponto de extremidade personalizado](internet-analyzer-custom-endpoint.md).
