---
title: "Preparar e testar sua oferta para implantação no Azure Marketplace | Microsoft Docs"
description: "Instruções detalhadas sobre como fornecer conteúdo de marketing, configurar planos de preços e testar sua oferta antes de implantar o Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 3ccd2448-895b-477e-adf6-ab655a21d2fa
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/17/2016
ms.author: hascipio
ms.openlocfilehash: 7db86716cdf8f9eb921c3c1813970acae7a3016b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="complete-the-offer-creation-with-marketing-content"></a>Concluir a criação de oferta com conteúdo de marketing
Nesta etapa do processo de publicação, você precisará de fornecer determinados conteúdos de marketing e detalhes sobre a sua oferta e/ou SKUs no Azure Marketplace. Por exemplo, você irá fornecer uma descrição do seu produto, logotipos da empresa, planos de preços, detalhes de planos e outras informações necessárias para enviar sua oferta de e/ou SKU para preparação. Essas informações são usadas como conteúdo de marketing no portal do Azure. Você começará esse processo no [portal de publicação][link-pubportal].

## <a name="step-1-provide-marketplace-marketing-content"></a>Passo 1: fornecer conteúdo de marketing do Marketplace
**O inglês é o idioma padrão e o único suportado.** Verifique se todas as informações fornecidas nos campos se encontram em inglês. Todas as informações podem ser editadas em qualquer momento até você enviar por push para o preparo.

1. Acesse o portal de publicação, [https://publish.windowsazure.com](https://publish.windowsazure.com).
2. No menu à esquerda, clique na guia **Marketing** .
3. No painel principal, clique no botão **Inglês (EUA)** .
   
   > [!IMPORTANT]
   > Todos os campos devem ter entradas, incluindo as imagens, a fim de poder mover para o preparo.
   > 
   > 

### <a name="details-and-plans"></a>Detalhes e planos
1. Insira o título da oferta (máximo de 50 caracteres), resumo da oferta (máximo de 100 caracteres), resumo longo da oferta (máximo de 256 caracteres), descrição da oferta (máximo de 1300 caracteres) e logotipos na guia **Detalhes**
2. Insira o título do plano (máximo de 50 caracteres), resumo do plano (máximo de 100 caracteres) e descrição do plano (máximo de 2000 caracteres) na guia **Planos** .
   
   > [!NOTE]
   > Você pode usar as seguintes marcas HTML para formatar o resumo, o resumo longo e a descrição da oferta e dos planos. As marcas HTML permitidas são h1, h2, h3, h4, h5, p, ol, ul, li, a[target|href], strong, em, b, i.
   > 
   > 
3. Não insira texto duplicado nas descrições da oferta e do plano.
4. Não insira texto duplicado no título do plano e no resumo longo da oferta.
5. Não insira texto duplicado no título do plano e no resumo da oferta.
6. Não insira títulos de planos idênticos em uma oferta com vários planos.
7. Carregue imagens com as especificações necessárias (mencionadas no Portal de Publicação) no formato PNG e uma para cada tamanho.
8. Verifique se os logotipos seguem as diretrizes de logotipo do Azure Marketplace mencionadas a seguir.
   
   ![desenho](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-02.png)

**Diretrizes de Logotipo do Azure Marketplace**

Todos os logotipos carregados no Portal de Publicação devem seguir as diretrizes abaixo:

* O design do Azure tem uma paleta de cores simples. Mantenha um baixo número de cores primárias e secundárias no seu logotipo.
* As cores do tema do portal do Azure são branco e preto. Portanto, evite usar essas cores como a cor de fundo dos seus logotipos. Use uma cor que destaque seus logotipos no portal do Azure. É recomendável usar cores primárias simples. **Se estiver usando um plano de fundo transparente, certifique-se de que os logotipos/o texto não sejam preto, branco ou azul.**
* Não use uma tela de fundo gradiente no logotipo.
* Evite colocar texto, até mesmo o nome ou marca da sua empresa, no logotipo. A aparência do seu logotipo deve ser “simples” e deve evitar usar gradientes.
* O logotipo não deve ser estendido.
* O logotipo pequeno deve ter o tamanho de 40 X 40 px
* O logotipo médio deve ter o tamanho de 90 X 90 px
* O logotipo grande deve ter o tamanho de 115 X 115 px
* O logotipo largo deve ter o tamanho de 255 X 115 px
* O logotipo Hero deve ter o tamanho de 815 X 290 px

> [!NOTE]
> O logotipo Hero é opcional. O editor pode optar por não carregar um logotipo Hero. No entanto, uma vez carregado, o ícone Hero não pode ser excluído do portal de Publicação. Nessa ocasião, o parceiro deve seguir as diretrizes do Azure Marketplace para ícones Hero.
> 
> 

  ![desenho](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-03.png)

**Diretrizes adicionais para o ícone do logotipo Hero (opcional)**

* O logotipo Hero é opcional. O editor pode optar por não carregar um logotipo Hero. **No entanto, uma vez carregado, o ícone Hero não pode ser excluído do portal de Publicação. Nessa ocasião, o parceiro deve seguir as diretrizes do Azure Marketplace para ícones Hero. Caso contrário, a oferta não será aprovada para produção.**
* O nome de exibição do editor, o título do plano e o resumo longo da oferta são exibidas em uma fonte branca. Portanto, você deve evitar cores claras no plano de fundo do ícone Hero. Planos de fundo pretos, brancos e transparentes não são permitidos para ícones Hero.
* O nome de exibição do editor, o título do plano, o resumo longo da oferta e o botão de criação são inseridos programaticamente no logotipo Hero depois que a oferta é listada. Portanto, você não deve inserir texto ao projetar o logotipo Hero. Apenas deixe o espaço vazio à direita, porque o texto (ou seja, nome de exibição do fornecedor, título do plano, resumo longo da oferta) será incluído programaticamente nesse local. O espaço vazio para o texto deve ser de 415x100 à direita (e ele é deslocado 370px à esquerda).
  
  ![desenho](media/marketplace-publishing-push-to-staging/pubportal-herobanner.png)

### <a name="links"></a>Links
Na guia de **Links** à esquerda da barra, insira quaisquer links com informações que possam ajudar os clientes. Insira um nome e uma URL para cada link.

![desenho](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-link-01.png)

### <a name="sample-images-optional"></a>Imagens de exemplo (opcionais)
> [!NOTE]
> Incluir uma imagem de exemplo é uma etapa opcional.
> Mesmo que seja possível carregar várias imagens de exemplo no portal de Publicação, somente uma imagem (selecionada aleatoriamente pelo sistema) é exibida no portal do Azure. Por esse motivo, recomendamos que você carregue apenas uma imagem de exemplo.
> 
> 

Na guia **Imagens de Exemplo** no menu à esquerda, carregue uma nova imagem clicando em **Carregar uma nova imagem**. Se você tiver uma imagem existente e deseja substituí-la, clique em **Substituir imagem**.

![desenho](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-sampleimg-01.png)

### <a name="legal"></a>Legal
Na guia **Legal** , forneça um link para as políticas/termos de uso. Insira ou cole os termos na caixa grande **Termos de Uso** . O limite de caracteres para os termos legais de uso é 1.000.000 caracteres.

![desenho](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-legal-01.png)

**Observação:** para ofertas de Máquina Virtual, depois que uma oferta/SKU é preparada no Portal do Azure, você não poderá alterar os campos apresentados abaixo:

* **Identificador da Oferta:** [portal de Publicação -> Máquinas Virtuais -> sua Oferta -> guia Imagens de VM -> Identificador da Oferta]
* **Identificador do SKU:** [portal de Publicação -> Máquinas Virtuais -> Selecione sua Oferta -> guia SKUs -> Adicionar um SKU]
* **Namespace do Editor:** [portal de Publicação -> Máquinas Virtuais -> guia Passo a Passo -> Conte-nos Sobre sua Empresa (encontrado em "Etapa 2 Registrar sua Empresa") -> Namespace do Editor -> Namespace]

Para ofertas de Máquina Virtual, depois que uma oferta/SKU é listada no Azure Marketplace, você não poderá alterar os campos apresentados abaixo:

* **Identificador da Oferta:** [portal de publicação -> Máquinas Virtuais -> selecione sua oferta -> Imagens de VM -> Identificador de Oferta]
* **Identificador do SKU:** [portal de Publicação -> Máquinas Virtuais -> Selecione sua Oferta -> guia SKUs -> Adicionar um SKU]
* **Namespace do Editor:** [Portal de publicação -> Máquinas Virtuais -> guia Passo a Passo -> Conte-nos Sobre sua Empresa (Encontrado na Etapa 2 Registrar) -> Namespace do Editor -> Namespace]
* **Portas:** [portal de publicação -> Máquinas Virtuais -> sua oferta -> guia de imagens VM -> Abrir Portas]
* **Alteração de preços de SKUs listados**
* **Modelos de cobrança de SKUs listados**
* **Remoção de regiões de cobrança de SKUs listados**
* **Alteração da contagem de discos de dados dos SKUs listados**

## <a name="step-2-set-your-prices"></a>Etapa 2: Definir seus preços
### <a name="pricing-models"></a>Modelos de preços
| Modelo de preços | Descrição |
| --- | --- |
| Base |Taxa mensal fixa paga no momento da compra, por exemplo, US$10/mês. |
| Consumo (também chamado de uso, medidor) |Pagamento por uso, que é definido pelo editor da oferta. O excedente não pode ser definido por estação, por usuário, etc., pois não há nenhuma definição que estabelece uma fração de um usuário ou capacidade de cálculo pro rata. O uso é relatado pelo parceiro, por hora. O cliente paga no final do ciclo de cobrança mensal em vez de antecipadamente como é normalmente em planos mensais. |
| Avaliação gratuita |O cliente pode usar gratuitamente por um período limitado e, então, paga taxas normais posteriormente. |
| Camada gratuita |O plano é sempre gratuito. |
| Migração (também chamado de conversão ou atualização/downgrade) do plano |Definição que estabelece um usuário mudando de seu plano atual para outro plano aceitável; definido pelo parceiro. |

**Modelos de preços disponíveis por tipo de oferta**

> [!IMPORTANT]
> A disponibilidade de alguns modelos de preços varia por tipo de oferta. Consulte a tabela abaixo.
> 
> 

|  | Apenas base | Apenas consumo | Base + Consumo |
| --- | --- | --- | --- |
| Imagem de máquina virtual |Não |Sim |Não |
| Serviço do desenvolvedor |Sim |Sim |Sim |

### <a name="21-set-your-vm-prices"></a>2.1. Definir seus preços da VM
No momento, para as máquinas virtuais, temos os **três tipos de modelos de cobrança a seguir:**

* **Por hora:** os clientes são cobrados por hora com base nas taxas definidas pelos editores nos tamanhos da VM. No caso do modelo de **cobrança por hora** dos SKUs, o preço total será a soma do custo do software cobrado pelo editor e do custo da infraestrutura cobrado pela Microsoft. Esse custo total será exibido para o cliente como um preço por hora e mensal quando ele estiver considerando a compra (consulte a captura de tela abaixo). **O editor recebe 80% do custo de software cobrado.** Portanto, faça o cálculo adequadamente antes de definir preços para seus SKUs.
  
    ![desenho](media/marketplace-publishing-push-to-staging/img2.1-01.png)
* **Avaliação Gratuita:** este é outro tipo de modelo Por hora. Nesse modelo, o cliente não é cobrado o custo do software pelos primeiros 30 dias (gratuitamente) após implantar a VM. Após os 30 dias, o cliente será cobrado por hora com base nas taxas de definidas pelos editores no modelo por hora.
* **Traga Sua Própria Licença (BYOL):** os editores gerenciam o licenciamento do software executado na VM.

**Importante:** depois da oferta/SKU ser listado no Azure Marketplace, você não poderá alterar os campos apresentados abaixo.

* **Alteração de preços de SKUs listados**
* **Modelos de cobrança de SKUs listados**
* **Remoção de regiões de cobrança de SKUs listados**
* **Alteração da contagem de discos de dados dos SKUs listados**
* **Identificador da Oferta:** [portal de publicação -> Máquinas Virtuais -> selecione sua oferta -> Imagens de VM -> Identificador de Oferta]
* **Identificador do SKU:** [portal de Publicação -> Máquinas Virtuais -> Selecione sua Oferta -> guia SKUs -> Adicionar um SKU]
* **Namespace do Editor:** [Portal de publicação -> Máquinas Virtuais -> guia Passo a Passo -> Conte-nos Sobre sua Empresa (Encontrado na Etapa 2 Registrar) -> Namespace do Editor -> Namespace]
* **Portas:** [portal de publicação -> Máquinas Virtuais -> sua oferta -> guia de imagens VM -> Abrir Portas]

### <a name="sell-to-countries-of-the-sku"></a>Países de venda da SKU
Você precisa considerar com atenção onde você pretende disponibilizar suas SKUs. Alguns países são classificados como "Remessa da Microsoft" e outros são classificados como "Remessa do ISV".

* Em países de "Remessa da Microsoft", a Microsoft coleta os impostos dos clientes e os paga (por remessa) ao governo.
* Em países de "Remessa do ISV", os parceiros são responsáveis por coletar os impostos dos clientes e pagá-los ao governo. Se optar por vender em países de "Remessa do ISV", você deve poder calcular e pagar impostos nos países selecionados.

> [!NOTE]
> O SKU não será disponibilizado nos países a menos que você defina seus preços no [Portal de publicação](https://publish.windowsazure.com). Abaixo são fornecidas orientações para definir os preços por hora e de BYOL dos SKUs.
> 
> 

### <a name="211-how-to-setup-hourly-pricing-model-for-a-sku"></a>2.1.1 Como configurar o modelo de preços por hora para uma SKU
Siga as etapas abaixo para configurar o modelo de preços por hora para uma SKU:

1. Faça logon no [Portal de publicação](https://publish.windowsazure.com).
2. Navegue até a guia **MÁQUINAS VIRTUAIS** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia **SKUs** .
4. Certifique-se de que a SKU esteja marcada como "Modelo de Cobrança por Hora". Se não estiver, clique no botão **Editar** para reverter o modelo de cobrança. Uma janela será aberta. Desmarque a caixa de seleção "A cobrança e o licenciamento são realizados fora do Azure (também conhecido como Traga sua própria licença)" e salve as alterações.
5. Se quiser habilitar a avaliação gratuita pelos primeiros 30 dias da implantação da SKU, selecione a opção "Um Mês" para a pergunta "Há uma avaliação gratuita disponível?" Caso contrário, selecione a opção "Sem Avaliação". Agora, siga as etapas abaixo.
6. No menu do lado esquerdo, clique na guia **PREÇOS** .
7. Selecione sua região de base.
   
   ![desenho](media/marketplace-publishing-push-to-staging/img2.1.1_07.png)
8. Defina os preços de todos os núcleos. **Você deve fornecer o preço para todos os núcleos de uma SKU mesmo que a SKU não ofereça suporte a ele.**
   
    ![desenho](media/marketplace-publishing-push-to-staging/img2.1.1_08.png)
9. Defina os preços para as outras regiões manualmente ou use o assistente de DEFINIÇÃO AUTOMÁTICA DE PREÇOS para definir os preços de outras regiões de acordo com a região de base. Para usar o assistente de DEFINIÇÃO AUTOMÁTICA DE PREÇOS, clique no botão **DEFINIR PREÇOS PARA OUTROS MERCADOS AUTOMATICAMENTE COM BASE NOS PREÇOS DOS ESTADOS UNIDOS.** **Observação:** o rótulo do botão pode ser diferente dependendo da região selecionada. Como nós selecionamos Estados Unidos durante a criação desse documento, o botão está rotulado como "Definir preços para outros mercados automaticamente com base nos preços dos Estados Unidos" na captura de tela abaixo.
   
   ![desenho](media/marketplace-publishing-push-to-staging/img2.1.1_09.png)
10. O assistente de definição automática de preços será aberto. A primeira página exibe a seleção do mercado base. Faça sua seleção e passe para a próxima página clicando no botão "->".
    
    ![desenho](media/marketplace-publishing-push-to-staging/img2.1.1_10.png)
11. A opção para selecionar os núcleos e planos será exibida na página 2. Selecione os planos desejados e clique no botão "->". Clique no botão **Ativar/Desativar Tudo** para selecionar todos os **Planos de serviço** e **Medidores** ou você pode marcar manualmente as caixas de seleção. **Você deve fornecer o preço para todos os núcleos de uma SKU mesmo que a SKU não ofereça suporte a ele.** Portanto, certifique-se de que todos os tamanhos de núcleo estejam selecionados.
    
    ![desenho](media/marketplace-publishing-push-to-staging/img2.1.1_11.png)
12. A página 3 exibe os mercados/as regiões. Clique no botão **Ativar/Desativar Tudo** para selecionar todas as regiões ou marque as caixas das regiões manualmente. Clique no botão "->" para passar para a próxima página. **Observação:** os Países de Remessa de Impostos da Microsoft são indicados por um símbolo semelhante a uma casa. Para obter mais detalhes, consulte a seção de países de venda da SKU nesta página.
    
    ![desenho](media/marketplace-publishing-push-to-staging/img2.1.1_12.png)
13. A página 4 exibe as taxas de câmbio. Clique no botão Concluir para concluir as etapas.

### <a name="212-how-to-setup-byol-pricing-model-for-a-sku"></a>2.1.2 Como configurar o modelo de preços BYOL para uma SKU
Siga as etapas abaixo para configurar o modelo de preços BYOL para uma SKU:

1. Faça logon no [Portal de publicação](https://publish.windowsazure.com).
2. Navegue até a guia **MÁQUINAS VIRTUAIS** e selecione sua oferta.
3. No menu do lado esquerdo, clique na guia **SKUs** .
4. Certifique-se de que a SKU esteja marcada como "SKU Traga sua própria licença". Se não estiver, clique no botão EDITAR para reverter o modelo de cobrança. Uma janela será aberta. Marque a caixa de seleção "A cobrança e o licenciamento são realizados fora do Azure (também conhecido como Traga sua própria licença)" e salve as alterações.
   
   ![desenho](media/marketplace-publishing-push-to-staging/img2.1.2_04.png)
5. No menu do lado esquerdo, clique na guia **PREÇOS** .
6. Selecione sua região de base e disponibilize a SKU na região marcando a caixa de seleção para a SKU em questão na seção DISPONIBILIDADE DA SKU COM LICENÇA EXTERNA (BYOL) (consulte a captura de tela abaixo).
   
   ![desenho](media/marketplace-publishing-push-to-staging/img2.1.2_06.png)
7. Disponibilize a SKU nas outras regiões manualmente ou use o assistente de DEFINIÇÃO AUTOMÁTICA DE PREÇOS para essa finalidade. Consulte os pontos 9 a 13 (que explicam o uso do assistente de DEFINIÇÃO AUTOMÁTICA DE PREÇOS) na seção **"2.1.1 Como configurar o modelo de preços por hora para um SKU"** desta página.

### <a name="22-set-your-developer-service-prices"></a>2.2. Definir seus Preços de serviço do desenvolvedor
Os planos podem ser qualquer combinação de base + consumo, em que a base é o preço mensal e o excedente é o preço de pagamento por uso. (Consulte a seguir para obter mais detalhes.)

**Exemplo:** oferta de serviço do desenvolvedor da Contoso

| Plano | Preço | Inclui | Caminho de migração |
| --- | --- | --- | --- |
| Grátis |US$0/mês |Funcionalidade básica. |Pode migrar para qualquer outro plano |
| Bronze |US $10/ mês |Funcionalidade básica e uma cota de 1.000 do recurso X. |Pode migrar para os planos Bronze Plus, Silver e Gold |
| Bronze Plus |Período de avaliação gratuita: US$0/mês + US$0/meter01 |Funcionalidade básica e uma cota de 10.000 do recurso X. Depois que a cota do recurso X for usada, o cliente pode pagar por uso via meter01. |Pode migrar para os planos Silver Plus e Gold |
| Bronze Plus |Período pago (também conhecido como avaliação gratuita expirada): US$10/mês + US$ 0,05/medidor01 |Funcionalidade básica e uma cota de 10.000 do recurso X. Depois que a cota do recurso X for usada, o cliente pode pagar por uso via meter01. |Pode migrar para os planos Silver Plus e Gold |
| Silver |US$ 0,15/meter01 |O cliente pode pagar por uso via meter01, que é o recurso X. |Pode migrar para os planos Bronze e Gold |
| Silver Plus |US$20/mês + US$ 0,15/meter01 + US$ 0,01/meter02 |Funcionalidade básica e uma cota de 10.000 do recurso X e 100 do recurso Y. Depois que a cota do recurso X for usada, o cliente pode pagar por uso via meter01.  Depois que a cota do recurso Y for usada, o cliente pode pagar por uso via meter02. |Pode migrar para os planos Bronze Plus e Gold |
| Gold |US $ 1.000/mês |Cota de 10.000 de recurso X, 1.000 do recurso Y e ilimitada do recurso Z. |Pode migrar para todos os planos exceto livre |

## <a name="step-3-provide-support-information"></a>Etapa 3: Fornecer informações de suporte
Os detalhes de contato são usados apenas para comunicações internas entre o parceiro e Microsoft. O URL de suporte estará disponível para os clientes finais.

1. Vá para o cabeçalho de **Suporte** no lado esquerdo do portal de publicação.
2. Insira as informações em **Contato de Engenharia**.
3. Insira as informações em **Suporte ao Cliente**. Se você só oferece suporte por email, digite um número de telefone fictício, e seu email fornecido será utilizado no lugar.
4. Insira a URL de suporte.

## <a name="step-4-choose-azure-marketplace-categories"></a>Etapa 4: Escolher categorias do Azure Marketplace
A guia **Categorias** fornece uma matriz de seleções. Sua oferta pode estar entre elas e você pode selecionar até cinco categorias.

## <a name="how-your-marketing-will-appear"></a>Como seu marketing aparecerá
Abaixo está uma exibição detalhada de como a oferta das informações de marketing é usada no [site do Azure Marketplace](https://azure.microsoft.com/marketplace/) e no [portal do Azure](https://portal.azure.com).

### <a name="azure-marketplace-website"></a>Site do Azure Marketplace
![desenho](media/marketplace-publishing-push-to-staging/acom-catalog-01.png)

![desenho](media/marketplace-publishing-push-to-staging/acom-catalog-02.png)

*Listagem de ofertas no site do Azure Marketplace*

![desenho](media/marketplace-publishing-push-to-staging/acom-listing-details-01.png)

*Detalhes da descrição da oferta no site Azure Marketplace*

![desenho](media/marketplace-publishing-push-to-staging/acom-listing-details-02.png)

*Detalhes de preços da descrição da oferta no site Azure Marketplace*

### <a name="azure-portal"></a>portal do Azure
![desenho](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-01.png)

*Listagem de ofertas no Portal do Azure*

![desenho](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-02.png)

*Detalhes da descrição da oferta no Portal do Azure*

## <a name="next-steps"></a>Próximas etapas
Agora que o conteúdo do Marketplace está carregado, seguiremos para a testar sua oferta no preparo. No entanto, você deve selecionar o tipo de oferta apropriado na lista abaixo já que as etapas variam por tipo de oferta.

* [Testar sua oferta de VM em preparo](marketplace-publishing-vm-image-test-in-staging.md)
* [Testar a oferta de modelo de solução em preparo](marketplace-publishing-solution-template-test-in-staging.md)

## <a name="see-also"></a>Consulte também
* [Introdução: como publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md)

[img-map-acom]:media/marketplace-publishing-push-to-staging/pubportal-mapping-acom.jpg
[img-map-portal]:media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg
[img-map-link]:media/marketplace-publishing-push-to-staging/marketing-content-guide-links.jpg
[img-map-logo]:media/marketplace-publishing-push-to-staging/marketing-content-guide-logos.jpg
[img-map-title]:media/marketplace-publishing-push-to-staging/marketing-content-guide-publisher-offer.png

[link-pubportal]:https://publish.windowsazure.com
[link-push-to-production]:marketplace-publishing-push-to-production.md
