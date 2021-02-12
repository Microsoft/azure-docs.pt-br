---
title: Entidades nomeadas gerais
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/15/2021
ms.author: aahi
ms.openlocfilehash: c1ff099dd6dffe06e9707ff23fffd57ae753ab64
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99500403"
---
O recurso NER para Análise de Texto retorna as seguintes categorias de entidade geral (sem identificação). por exemplo, ao enviar solicitações para o `/entities/recognition/general` ponto de extremidade.


| Categoria | Descrição                          |
|------------|-------------|--------------------------------------|-------------------------------------------------------------|--------------------------------------|
| [Person](#category-person)     | Nomes de pessoas.  |
| [Persontype](#category-persontype) | Tipos de trabalho ou funções mantidas por uma pessoa. |
| [Localidade](#category-location)    | Pontos de referência naturais e humanos, estruturas, recursos geográficos e entidades geopolítica |
| [Organização](#category-organization)  | Empresas, grupos políticos, faixas musicais, clubes esporte, órgãos governamentais e organizações públicas.  |
| [Evento](#category-event)  | Eventos históricos, sociais e naturalmente ocorridos. |
| [Product](#category-product) | Objetos físicos de várias categorias. |
| [Habilidade](#category-skill) | Capacidade, habilidade ou experiência.  |
| [Endereço](#category-address) | Endereços de endereçamento completos.  |
| [Número de telefone](#category-phonenumber) | Números de telefone. |
| [Email](#category-email) | Endereços de email. |
| [URL](#category-url) | URLs para sites. |
| [IP](#category-ip) | Endereços IP de rede. |
| [DateTime](#category-datetime) | Datas e horas do dia. |
| [Quantidade](#category-quantity) | Medidas numéricas e unidades. |


### <a name="category-person"></a>Categoria: pessoa

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        Person

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Nomes de pessoas.
      
    :::column-end:::
    :::column span="2":::
      **Idiomas de documento com suporte**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, <br> `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt`-`pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

### <a name="category-persontype"></a>Categoria: Persontype

Esta categoria contém a seguinte entidade:


:::row:::
    :::column span="":::
        **Entidade**

        Persontype

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Tipos de trabalho ou funções mantidas por uma pessoa
      
    :::column-end:::
    :::column span="2":::
      **Idiomas de documento com suporte**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="category-location"></a>Categoria: local

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        Localização

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Pontos de referência naturais e humanos, estruturas, recursos geográficos e entidades geopolítica.
      
    :::column-end:::
    :::column span="2":::
      **Idiomas de documento com suporte**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt-pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Subcategorias

A entidade nessa categoria pode ter as subcategorias a seguir.

:::row:::
    :::column span="":::
        **Subcategoria da entidade**

        Entidade geopolítica (GPE)

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Cidades, países/regiões, Estados.
      
    :::column-end:::
    :::column span="2":::
      **Idiomas de documento com suporte**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Visualização

    :::column-end:::
    :::column span="2":::

        Estruturas artificiais. 
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Geográfico

    :::column-end:::
    :::column span="2":::

        Recursos geográficos e naturais, como rios, oceanos e desertos.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-organization"></a>Categoria: organização

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        Organização

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Empresas, grupos políticos, faixas musicais, clubes esporte, órgãos governamentais e organizações públicas. As nacionalidades e Religions não são incluídas nesse tipo de entidade.
      
    :::column-end:::
    :::column span="2":::
      **Idiomas de documento com suporte**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt-pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Subcategorias

A entidade nessa categoria pode ter as subcategorias a seguir.

:::row:::
    :::column span="":::
        **Subcategoria da entidade**

        Médicos

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Empresas e grupos médicos.
      
    :::column-end:::
    :::column span="2":::
      **Idiomas de documento com suporte**

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Troca de estoque

    :::column-end:::
    :::column span="2":::

        Grupos de troca de estoque. 
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Esportes

    :::column-end:::
    :::column span="2":::

        Organizações relacionadas a esportes.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-event"></a>Categoria: evento

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        Evento

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Eventos históricos, sociais e naturalmente ocorridos.
      
    :::column-end:::
    :::column span="2":::
      **Idiomas de documento com suporte**

      `en`,,,,,, `es` `fr` `de` `it` `zh-hans` `ja` , `ko` `pt-pt` e `pt-br`  
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Subcategorias

A entidade nessa categoria pode ter as subcategorias a seguir.

:::row:::
    :::column span="":::
        **Subcategoria da entidade**

        Cultural

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Eventos culturais e feriados.
      
    :::column-end:::
    :::column span="2":::
      **Idiomas de documento com suporte**

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Natural

    :::column-end:::
    :::column span="2":::

        Eventos que ocorrem naturalmente.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Esportes

    :::column-end:::
    :::column span="2":::

        Eventos esportivos.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-product"></a>Categoria: produto

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        Produto

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Objetos físicos de várias categorias.
      
    :::column-end:::
    :::column span="2":::
      **Idiomas de documento com suporte**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::


#### <a name="subcategories"></a>Subcategorias

A entidade nessa categoria pode ter as subcategorias a seguir.

:::row:::
    :::column span="":::
        **Subcategoria da entidade**

        Produtos de computação
    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Computando produtos.
      
    :::column-end:::
    :::column span="2":::
      **Idiomas de documento com suporte**

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-skill"></a>Categoria: habilidade

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        Habilidade

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Capacidade, habilidade ou experiência.
      
    :::column-end:::
    :::column span="2":::
      **Idiomas de documento com suporte**

      `en`  
      
   :::column-end:::
:::row-end:::

### <a name="category-address"></a>Categoria: endereço

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        Endereço

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Endereço de endereçamento completo.
      
    :::column-end:::
    :::column span="2":::
      **Idiomas de documento com suporte**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-phonenumber"></a>Categoria: PhoneNumber

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        PhoneNumber

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Números de telefone (somente números de telefone dos EUA e da UE).
      
    :::column-end:::
    :::column span="2":::
      **Idiomas de documento com suporte**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-email"></a>Categoria: email

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        Email

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Endereços de email.
      
    :::column-end:::
    :::column span="2":::
      **Idiomas de documento com suporte**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-url"></a>Categoria: URL

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        URL

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        URLs para sites. 
      
    :::column-end:::
    :::column span="2":::
      **Idiomas de documento com suporte**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-ip"></a>Categoria: IP

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        IP

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        endereços IP de rede. 
      
    :::column-end:::
    :::column span="2":::
      **Idiomas de documento com suporte**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-datetime"></a>Categoria: DateTime

Essa categoria contém as seguintes entidades:

:::row:::
    :::column span="":::
        **Entidade**

        DateTime

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Datas e horas do dia. 
      
    :::column-end:::
    :::column span="2":::
      **Idiomas de documento com suporte**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

As entidades nesta categoria podem ter as seguintes subcategorias

#### <a name="subcategories"></a>Subcategorias

A entidade nessa categoria pode ter as subcategorias a seguir.

:::row:::
    :::column span="":::
        **Subcategoria da entidade**

        Data

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Datas do calendário.
      
    :::column-end:::
    :::column span="2":::
      **Idiomas de documento com suporte**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Hora

    :::column-end:::
    :::column span="2":::

        Horas do dia.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        DateRange

    :::column-end:::
    :::column span="2":::

        Intervalos de datas.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        TimeRange

    :::column-end:::
    :::column span="2":::

        Intervalos de tempo.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Duration

    :::column-end:::
    :::column span="2":::

        Durações.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Definir

    :::column-end:::
    :::column span="2":::

        Definido, horários repetidos.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="category-quantity"></a>Categoria: quantidade

Essa categoria contém as seguintes entidades:

:::row:::
    :::column span="":::
        **Entidade**

        Quantidade

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Números e quantidades numéricas.
      
    :::column-end:::
    :::column span="2":::
      **Idiomas de documento com suporte**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Subcategorias

A entidade nessa categoria pode ter as subcategorias a seguir.

:::row:::
    :::column span="":::
        **Subcategoria da entidade**

        Número

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Números.
      
    :::column-end:::
    :::column span="2":::
      **Idiomas de documento com suporte**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Porcentagem

    :::column-end:::
    :::column span="2":::

        Porcentagens
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Números ordinais

    :::column-end:::
    :::column span="2":::

        Números ordinais.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Idade

    :::column-end:::
    :::column span="2":::

        Paralisa.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Moeda

    :::column-end:::
    :::column span="2":::

        Moedas
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Dimensões

    :::column-end:::
    :::column span="2":::

        Dimensões e medidas.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Temperatura

    :::column-end:::
    :::column span="2":::

        Temperatura.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
