---
title: Saiba mais sobre as versões mais recentes de SO convidado do Azure | Microsoft Docs
description: As últimas notícias de versão e a compatibilidade do SDK para o SO convidado dos serviços de nuvem do Azure.
services: cloud-services
documentationcenter: na
author: gunnarcms
editor: ''
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 3/28/2021
ms.author: gunnarc
ms.openlocfilehash: 0bd0257c3b99e343f1aebba31867f687b522c6fb
ms.sourcegitcommit: dae6b628a8d57540263a1f2f1cdb10721ed1470d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "105709558"
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Matriz de compatibilidade de versões de SOs Convidados e do SDK do Azure
Fornece a você informações atualizadas sobre as versões mais recentes do SO convidado do Azure para serviços de nuvem. Essas informações ajudam a planejar seu caminho de atualização antes que um SO convidado seja desabilitado. Se você configurar suas funções para usar atualizações *automáticas* de SO convidado, conforme descrito em [Configurações de atualização de SO convidado do Azure][Azure Guest OS Update Settings], não é essencial ler esta página.

> [!IMPORTANT]
> Esta página se aplica às funções de trabalho e da Web dos Serviços de Nuvem, que são executados em um SO convidado. Ela **não se aplica** a Máquinas Virtuais IaaS.
>
>


> [!TIP]
>  Assine o [RSS feed de atualização do SO convidado] para receber as notificações mais recentes sobre todas as alterações do SO convidado.
>
>

> [!IMPORTANT]
> Somente as 2 versões mais recentes do sistema operacional convidado serão suportadas e estarão disponíveis no portal do Azure.
>
>

Você não tem certeza de como atualizar o SO convidado? Confira [isto][cloud updates].

## <a name="news-updates"></a>Notícias atualizadas

###### <a name="march-28-2021"></a>**28 de março de 2021**
O SO convidado de março foi lançado. 

###### <a name="february-19-2021"></a>**19 de fevereiro de 2021**
O SO convidado de fevereiro foi lançado. 

###### <a name="february-5-2021"></a>**5 de fevereiro de 2021**
O SO convidado de janeiro foi lançado. 

###### <a name="january-15-2021"></a>**15 de janeiro de 2021**
O sistema operacional convidado de dezembro foi lançado. 

###### <a name="december-19-2020"></a>**19 de dezembro de 2020**
O SO convidado de novembro foi lançado. 

###### <a name="november-17-2020"></a>**17 de novembro de 2020**
O SO convidado de outubro foi lançado. 

###### <a name="october-10-2020"></a>**10 de outubro de 2020**
O SO convidado de setembro foi lançado. 

###### <a name="september-5-2020"></a>**5 de setembro de 2020**
O sistema operacional convidado de agosto foi lançado. 

###### <a name="august-17-2020"></a>**17 de agosto de 2020**
O SO convidado de julho foi lançado. 

###### <a name="august-10-2020"></a>**10 de agosto de 2020**
O SO convidado de junho foi lançado. 

###### <a name="june-2-2020"></a>**2 de junho de 2020**
O SO convidado de maio foi lançado. 

###### <a name="may-4-2020"></a>**4 de maio de 2020**
O SO convidado de abril foi lançado. 

###### <a name="april-2-2020"></a>**2 de abril de 2020**
O SO convidado de março foi lançado. 

###### <a name="march-5-2020"></a>**5 de março de 2020**
O SO convidado de fevereiro foi lançado. 

###### <a name="january-24-2020"></a>**24 de janeiro de 2020**
O SO convidado de janeiro foi lançado. 

###### <a name="january-8-2020"></a>**8 de janeiro de 2020**
O sistema operacional convidado de dezembro foi lançado.

###### <a name="december-5-2019"></a>**5 de dezembro de 2019**
O SO convidado de novembro foi lançado.

###### <a name="november-1-2019"></a>**01 de novembro de 2019**
O SO convidado de outubro foi lançado.

###### <a name="october-7-2019"></a>**7 de outubro de 2019**
O SO convidado de setembro foi lançado.

###### <a name="september-4-2019"></a>**4 de setembro de 2019**
O sistema operacional convidado de agosto foi lançado.

###### <a name="july-26-2019"></a>**26 de julho de 2019**
O SO convidado de julho foi lançado.

###### <a name="july-8-2019"></a>**8 de julho de 2019**
O SO convidado de junho foi lançado.

###### <a name="june-6-2019"></a>**6 de junho de 2019**
O SO convidado de maio foi lançado.

###### <a name="may-7-2019"></a>**7 de maio de 2019**
O SO convidado de abril foi lançado.

###### <a name="march-26-2019"></a>**26 de março de 2019**
O SO convidado de março foi lançado.

###### <a name="march-12-2019"></a>**12 de março de 2019**
O SO convidado de fevereiro foi lançado.

###### <a name="february-5-2019"></a>**5 de fevereiro de 2019**
O SO convidado de janeiro foi lançado.

###### <a name="january-24-2019"></a>**24 de janeiro de 2019**
O SO convidado Family 6 (Windows Server 2019) foi lançado.

###### <a name="january-7-2019"></a>**7 de janeiro de 2019**
O sistema operacional convidado de dezembro foi lançado.

###### <a name="december-14-2018"></a>**14 de dezembro de 2018**
O SO convidado de novembro foi lançado.

###### <a name="november-8-2018"></a>**8 de novembro de 2018**
O SO convidado de outubro foi lançado.

###### <a name="october-12-2018"></a>**12 de outubro de 2018**
O SO convidado de setembro foi lançado.

## <a name="releases"></a>Versões

## <a name="family-6-releases"></a>Lançamentos do Family 6
**Windows Server 2019**

.NET Framework instalado: 3,5, 4.7.2

> [!NOTE]
> O SDK do Microsoft Azure para .NET 3.0 pode ser baixado [aqui][Windows Azure SDK].
>
>Etapas da instalação:
>1. Desinstale todas as versões mais antigas do MicrosoftAzureAuthoringTools*. msi
>2. Instalar o [SDK do Azure para .net-3,0][Windows Azure SDK]
>3. Reinicie o computador
>4. Criar um novo projeto de serviço de nuvem e adicionar uma única função de trabalho
>5. Altere a família do sistema operacional para 6 e crie um pacote
>6. Implantar o pacote no Azure usando o portal do Azure ou o Visual Studio
>
>A versão da família de sistemas operacionais convidados 6 impõe o TLS 1,2 desabilitando explicitamente o TLS 1,0 e 1,1 e definindo um conjunto específico de conjuntos de codificação. Saiba [mais].


| Cadeia de caracteres de configuração | Data de liberação | Data da desabilitação |
| --- | --- | --- |
|  WA-GUEST-OS-6.29 _202103-01 |  28 de março de 2021  |  Post 6,31  |  
|  WA-GUEST-OS-6.28 _202102-01 |  19 de fevereiro de 2021  |  Post 6,30  |  
|~~WA-GUEST-OS-6.27 _202101-01~~|  5 de fevereiro de 2021  |  28 de março de 2021  |  
|~~WA-GUEST-OS-6.26 _202012-01~~|  15 de janeiro de 2021  |  19 de fevereiro de 2021  |  
|~~WA-GUEST-OS-6,25 _202011-01~~|  19 de dezembro de 2020  |  5 de fevereiro de 2021  |  
|~~WA-GUEST-OS-6.24 _202010-02~~|  17 de novembro de 2020  |  15 de janeiro de 2021  |  
|~~WA-GUEST-OS-6.23 _202009-01~~|  10 de outubro de 2020  |  19 de dezembro de 2020  |  
|~~WA-GUEST-OS-6.22 _202008-02~~|  5 de setembro de 2020  |  17 de novembro de 2020  |  
|~~WA-GUEST-OS-6.21 _202007-01~~|  17 de agosto de 2020  |  10 de outubro de 2020  |  
|~~WA-GUEST-OS-6.20 _202006-02~~|  10 de agosto de 2020  |  5 de setembro de 2020  |  
|~~WA-GUEST-OS-6.19 _202005-02~~|  2 de junho de 2020  |  17 de agosto de 2020  |  
|~~WA-GUEST-OS-6.18 _202004-01~~|  4 de maio de 2020  |  10 de agosto de 2020  |  
|~~WA-GUEST-OS-6.17 _202003-01~~|  2 de abril de 2020  |  2 de junho de 2020  |  
|~~WA-GUEST-OS-6.16 _202002-01~~|  5 de março de 2020  |  4 de maio de 2020  |  
|~~WA-GUEST-OS-6.15 _202001-01~~|  24 de janeiro de 2020  |  2 de abril de 2020  |  
|~~WA-GUEST-OS-6.14 _201912-01~~| 8 de janeiro de 2020 | 5 de março de 2020 |  
|~~WA-GUEST-OS-6.13 _201911-01~~| 5 de dezembro de 2019 | 24 de janeiro de 2020 |  
|~~WA-GUEST-OS-6.12 _201910-01~~| 01 de novembro de 2019 | 8 de janeiro de 2020 |  
|~~WA-GUEST-OS-6.11 _201909-01~~| 7 de outubro de 2019 | 5 de dezembro de 2019 |  
|~~WA-GUEST-OS-6.10 _201908-01~~| 4 de agosto de 2019 | 01 de novembro de 2019  |  
|~~WA-GUEST-OS-6,9 _201907-0~~|26 de julho de 2019 | 7 de outubro de 2019 |
|~~WA-GUEST-OS-6.8 _201906-01~~|8 de julho de 2019 |4 de agosto de 2019 |
|~~WA-GUEST-OS-6.7 _201905-01~~ |6 de junho de 2019 |26 de julho de 2019 |
|~~WA-GUEST-OS-6.6 _201904-01~~ |7 de maio de 2019 |8 de julho de 2019 |
|~~WA-GUEST-OS-6.5 _201903-01~~ |26 de março de 2019 |6 de junho de 2019 |
|~~WA-GUEST-OS-6.4 _201902-01~~ |12 de março de 2019 |7 de maio de 2019 |
|~~WA-GUEST-OS-6.3_201901-01~~ |5 de fevereiro de 2019 |26 de março de 2019 |
|~~WA-GUEST-OS-6.2_201812-01~~ |24 de janeiro de 2019 |12 de março de 2019 |
|~~WA-GUEST-OS-6.1_201811-01~~ |24 de janeiro de 2019 |5 de fevereiro de 2019 |

## <a name="family-5-releases"></a>Versões da Família 5
**Windows Server 2016**

.NET framework instalado: 3.5, 4.6.2

> [!NOTE]
> A senha de RDP para a família do sistema operacional 5 deverá ter um mínimo de 10 caracteres.
>


| Cadeia de caracteres de configuração | Data de liberação | Data da desabilitação |
| --- | --- | --- |
|  WA-GUEST-OS-5.53 _202103-01  |  28 de março de 2021  |  Post 5,55  | 
|  WA-GUEST-OS-5.52 _202102-01  |  19 de fevereiro de 2021  |  Post 5,54  | 
|~~WA-GUEST-OS-5.51 _202101-01~~|  5 de fevereiro de 2021  |  28 de março de 2021  | 
|~~WA-GUEST-OS-5.50 _202012-01~~|  15 de janeiro de 2021  |  19 de fevereiro de 2021  | 
|~~WA-GUEST-OS-5.49 _202011-01~~|  19 de dezembro de 2020  |  5 de fevereiro de 2021  | 
|~~WA-GUEST-OS-5.48 _202010-02~~|  17 de novembro de 2020  |  15 de janeiro de 2021  | 
|~~WA-GUEST-OS-5.47 _202009-01~~|  10 de outubro de 2020  |  19 de dezembro de 2020  | 
|~~WA-GUEST-OS-5.46 _202008-02~~|  5 de setembro de 2020  |  17 de novembro de 2020  |  
|~~WA-GUEST-OS-5.45 _202007-01~~|  17 de agosto de 2020  |  10 de outubro de 2020  |  
|~~WA-GUEST-OS-5.44 _202006-02~~|  10 de agosto de 2020  |  5 de setembro de 2020  |  
|~~WA-GUEST-OS-5.43 _202005-02~~|  2 de junho de 2020  |  17 de agosto de 2020  |  
|~~WA-GUEST-OS-5.42 _202004-01~~|  4 de maio de 2020  |  10 de agosto de 2020  |  
|~~WA-GUEST-OS-5.41 _202003-01~~|  2 de abril de 2020  |  2 de junho de 2020  |  
|~~WA-GUEST-OS-5.40 _202002-01~~|  5 de março de 2020  |  4 de maio de 2020  |  
|~~WA-GUEST-OS-5.39 _202001-01~~|  24 de janeiro de 2020  |  2 de abril de 2020  |  
|~~WA-GUEST-OS-5.38 _201912-01~~| 8 de janeiro de 2020 | 5 de março de 2020 |  
|~~WA-GUEST-OS-5.37 _201911-01~~| 5 de dezembro de 2019 | 24 de janeiro de 2020 |  
|~~WA-GUEST-OS-5.36 _201910-01~~| 01 de novembro de 2019 | 8 de janeiro de 2020 |  
|~~WA-GUEST-OS-5.35 _201909-01~~| 7 de outubro de 2019 | 5 de dezembro de 2019 |  
|~~WA-GUEST-OS-5.34 _201908-01~~|  4 de agosto de 2019  | 01 de novembro de 2019 |  
|~~WA-GUEST-OS-5.33 _201907-01~~| 26 de julho de 2019 | 7 de outubro de 2019 |  
|~~WA-GUEST-OS-5.32 _201906-01~~|8 de julho de 2019 |4 de agosto de 2019 |
|~~WA-GUEST-OS-5.31 _201905-01~~ |6 de junho de 2019 |26 de julho de 2019 |
|~~WA-GUEST-OS-5.30 _201904-01~~ |7 de maio de 2019 |8 de julho de 2019 |
|~~WA-GUEST-OS-5.29 _201903-01~~ |26 de março de 2019 |6 de junho de 2019 |
|~~WA-GUEST-OS-5.28 _201902-01~~ |12 de março de 2019 |7 de maio de 2019 |
|~~WA-GUEST-OS-5.27_201901-01~~ |5 de fevereiro de 2019 |26 de março de 2019 |
|~~WA-GUEST-OS-5.26_201812-01~~ |7 de janeiro de 2019 |12 de março de 2019 |
|~~WA-GUEST-OS-5.25_201811-01~~ |14 de dezembro de 2018 |5 de fevereiro de 2019 |
|~~WA-GUEST-OS-5.24_201810-01~~ |8 de novembro de 2018 |7 de janeiro de 2019 |
|~~WA-GUEST-OS-5.23_201809-01~~ |12 de outubro de 2018 |14 de dezembro de 2018 |

## <a name="family-4-releases"></a>Versões da Família 4
**Windows Server 2012 R2**

.NET Framework instalado: 3,5, 4.5.1, 4.5.2

| Cadeia de caracteres de configuração | Data de liberação | Data da desabilitação |
| --- | --- | --- |
|  WA-GUEST-OS-4.88 _202103-01  |  28 de março de 2021  |  Post 4,90  | 
|  WA-GUEST-OS-4.87 _202102-01  |  19 de fevereiro de 2021  |  Post 4,89  | 
|~~WA-GUEST-OS-4.86 _202101-01~~|  5 de fevereiro de 2021  |  28 de março de 2021  | 
|~~WA-GUEST-OS-4.85 _202012-01~~|  15 de janeiro de 2021  |  19 de fevereiro de 2021  | 
|~~WA-GUEST-OS-4.84 _202011-01~~|  19 de dezembro de 2020  |  5 de fevereiro de 2021  | 
|~~WA-GUEST-OS-4.83 _202010-02~~|  17 de novembro de 2020  |  15 de janeiro de 2021  | 
|~~WA-GUEST-OS-4.82 _202009-01~~|  10 de outubro de 2020  |  19 de dezembro de 2020  | 
|~~WA-GUEST-OS-4.81 _202008-02~~|  5 de setembro de 2020  |  17 de novembro de 2020  | 
|~~WA-GUEST-OS-4.80 _202007-01~~|  17 de agosto de 2020  |  10 de outubro de 2020  | 
|~~WA-GUEST-OS-4.79 _202006-02~~|  10 de agosto de 2020  |  5 de setembro de 2020  | 
|~~WA-GUEST-OS-4.78 _202005-02~~|  2 de junho de 2020  |  17 de agosto de 2020  |  
|~~WA-GUEST-OS-4.77 _202004-01~~|  4 de maio de 2020  |  10 de agosto de 2020  |  
|~~WA-GUEST-OS-4.76 _202003-01~~|  2 de abril de 2020  |  2 de junho de 2020  |  
|~~WA-GUEST-OS-4,75 _202002-01~~|  5 de março de 2020  |  4 de maio de 2020  |  
|~~WA-GUEST-OS-4.74 _202001-01~~|  24 de janeiro de 2020  |  2 de abril de 2020  |  
|~~WA-GUEST-OS-4.73 _201912-01~~| 8 de janeiro de 2020 | 5 de março de 2020 |  
|~~WA-GUEST-OS-4.72 _201911-01~~| 5 de dezembro de 2019 | 24 de janeiro de 2020 |  
|~~WA-GUEST-OS-4.71 _201910-01~~| 01 de novembro de 2019 | 8 de janeiro de 2020 |  
|~~WA-GUEST-OS-4.70 _201909-01~~| 7 de outubro de 2019 | 5 de dezembro de 2019 |  
|~~WA-GUEST-OS-4.69 _201908-01~~| 4 de agosto de 2019 | 01 de novembro de 2019 |  
|~~WA-GUEST-OS-4.68 _201907-01~~| 26 de julho de 2019  | 7 de outubro de 2019 |
|~~WA-GUEST-OS-4.67 _201906-01~~| 8 de julho de 2019 |4 de agosto de 2019 |
|~~WA-GUEST-OS-4.66 _201905-01~~ |6 de junho de 2019 |26 de julho de 2019 |
|~~WA-GUEST-OS-4.65 _201904-01~~ |7 de maio de 2019 |8 de julho de 2019 |
|~~WA-GUEST-OS-4.64 _201903-01~~ |26 de março de 2019 |6 de junho de 2019 |
|~~WA-GUEST-OS-4.63 _201902-01~~ |12 de março de 2019 |7 de maio de 2019 |
|~~WA-GUEST-OS-4.62_201901-01~~ |5 de fevereiro de 2019 |26 de março de 2019 |
|~~WA-GUEST-OS-4.61_201812-01~~ |7 de janeiro de 2019 |12 de março de 2019 |
|~~WA-GUEST-OS-4.60_201811-01~~ |14 de dezembro de 2018 |5 de fevereiro de 2019 |
|~~WA-GUEST-OS-4.59_201810-01~~ |8 de novembro de 2018 |7 de janeiro de 2019 |
|~~WA-GUEST-OS-4.58_201809-01~~ |12 de outubro de 2018 |14 de dezembro de 2018 |

## <a name="family-3-releases"></a>Versões da Família 3
**Windows Server 2012**

.NET Framework instalado: 3.5, 4.5

| Cadeia de caracteres de configuração | Data de liberação | Data da desabilitação |
| --- | --- | --- |
|  WA-GUEST-OS-3.95 _202103-01  |  28 de março de 2021  |  Post 3,97  |
|  WA-GUEST-OS-3.94 _202102-01  |  19 de fevereiro de 2021  |  Post 3,96  |
|~~WA-GUEST-OS-3.93 _202101-01~~|  5 de fevereiro de 2021  |  28 de março de 2021  |
|~~WA-GUEST-OS-3.92 _202012-01~~|  15 de janeiro de 2021  |  19 de fevereiro de 2021  |  
|~~WA-GUEST-OS-3.91 _202011-01~~|  19 de dezembro de 2020  |  5 de fevereiro de 2021  |  
|~~WA-GUEST-OS-3.90 _202010-02~~|  17 de novembro de 2020  |  15 de janeiro de 2021  |  
|~~WA-GUEST-OS-3.89 _202009-01~~|  10 de outubro de 2020  |  19 de dezembro de 2020  |  
|~~WA-GUEST-OS-3.88 _202008-02~~|  5 de setembro de 2020  |  17 de novembro de 2020  |  
|~~WA-GUEST-OS-3.87 _202007-01~~|  17 de agosto de 2020  |  10 de outubro de 2020  |  
|~~WA-GUEST-OS-3.86 _202006-02~~|  10 de agosto de 2020  |  5 de setembro de 2020  |  
|~~WA-GUEST-OS-3.85 _202005-02~~|  2 de junho de 2020  |  17 de agosto de 2020  |  
|~~WA-GUEST-OS-3.84 _202004-01~~|  4 de maio de 2020  |  10 de agosto de 2020  |  
|~~WA-GUEST-OS-3.83 _202003-01~~|  2 de abril de 2020  |  2 de junho de 2020  |  
|~~WA-GUEST-OS-3.82 _202002-01~~|  5 de março de 2020  |  4 de maio de 2020  |  
|~~WA-GUEST-OS-3.81 _202001-01~~|  24 de janeiro de 2020  |  2 de abril de 2020  |  
|~~WA-GUEST-OS-3,80 _201912-01~~| 8 de janeiro de 2020 | 5 de março de 2020 |  
|~~WA-GUEST-OS-3.79 _201911-01~~| 5 de dezembro de 2019 | 24 de janeiro de 2020 |  
|~~WA-GUEST-OS-3.78 _201910-01~~| 01 de novembro de 2019 | 8 de janeiro de 2020 |  
|~~WA-GUEST-OS-3.77 _201909-01~~| 7 de outubro de 2019 | 5 de dezembro de 2019 |  
|~~WA-GUEST-OS-3.76 _201908-01~~|  4 de agosto de 2019  |  01 de novembro de 2019  |  
|~~WA-GUEST-OS-3,75 _201907-01~~| 26 de julho de 2019 | 7 de outubro de 2019 |
|~~WA-GUEST-OS-3.74 _201906-01~~| 8 de julho de 2019 |4 de agosto de 2019 |
|~~WA-GUEST-OS-3.73 _201905-01~~ |6 de junho de 2019 |26 de julho de 2019 |
|~~WA-GUEST-OS-3.72 _201904-01~~ |7 de maio de 2019 |8 de julho de 2019 |
|~~WA-GUEST-OS-3.71 _201903-01~~ |26 de março de 2019 |6 de junho de 2019 |
|~~WA-GUEST-OS-3.70 _201902-01~~ |12 de março de 2019 |7 de maio de 2019 |
|~~WA-GUEST-OS-3.69_201901-01~~ |5 de fevereiro de 2019 |26 de março de 2019 |
|~~WA-GUEST-OS-3.68_201812-01~~ |7 de janeiro de 2019 |12 de março de 2019 |
|~~WA-GUEST-OS-3.67_201811-01~~ |14 de dezembro de 2018 |5 de fevereiro de 2019 |
|~~WA-GUEST-OS-3.66_201810-01~~ |8 de novembro de 2018 |7 de janeiro de 2019 |
|~~WA-GUEST-OS-3.65_201809-01~~ |12 de outubro de 2018 |14 de dezembro de 2018 |

## <a name="family-2-releases"></a>Versões da Família 2
**Windows Server 2008 R2 SP1**

.NET Framework instalado: 3,5 (inclui 2,0 e 3,0), 4,5

| Cadeia de caracteres de configuração | Data de liberação | Data da desabilitação |
| --- | --- | --- |
|  WA-GUEST-OS-2.108 _202103-01  |  28 de março de 2021  |  Post 2,110  |
|  WA-GUEST-OS-2.107 _202102-01  |  19 de fevereiro de 2021  |  Post 2,109  |  
|~~WA-GUEST-OS-2.106 _202101-01~~|  5 de fevereiro de 2021  |  28 de março de 2021  |  
|~~WA-GUEST-OS-2.105 _202012-01~~|  15 de janeiro de 2021  |  19 de fevereiro de 2021  |  
|~~WA-GUEST-OS-2.104 _202011-01~~|  19 de dezembro de 2020  |  5 de fevereiro de 2021  |  
|~~WA-GUEST-OS-2.103 _202010-02~~|  17 de novembro de 2020  |  15 de janeiro de 2021  |  
|~~WA-GUEST-OS-2.102 _202009-01~~|  10 de outubro de 2020  |  19 de dezembro de 2020  |  
|~~WA-GUEST-OS-2.101 _202008-02~~|  5 de setembro de 2020  |  17 de novembro de 2020 |    
|~~WA-GUEST-OS-2.100 _202007-01~~|  17 de agosto de 2020  |  10 de outubro de 2020  |  
|~~WA-GUEST-OS-2.99 _202006-02~~|  10 de agosto de 2020  | 5 de setembro de 2020  |  
|~~WA-GUEST-OS-2.98 _202005-02~~|  2 de junho de 2020  |  17 de agosto de 2020  |  
|~~WA-GUEST-OS-2.97 _202004-01~~|  4 de maio de 2020  |  10 de agosto de 2020  |  
|~~WA-GUEST-OS-2.96 _202003-01~~|  2 de abril de 2020  |  2 de junho de 2020  |  
|~~WA-GUEST-OS-2.95 _202002-01~~|  5 de março de 2020  |  4 de maio de 2020  |  
|~~WA-GUEST-OS-2.94 _202001-01~~|  24 de janeiro de 2020  |  2 de abril de 2020  |  
|~~WA-GUEST-OS-2,93 _201912-01~~| 8 de janeiro de 2020 | 5 de março de 2020 |  
|~~WA-GUEST-OS-2.92 _201911-01~~| 5 de dezembro de 2019 | 24 de janeiro de 2020 |  
|~~WA-GUEST-OS-2.91 _201910-01~~| 01 de novembro de 2019 | 8 de janeiro de 2020 |  
|~~WA-GUEST-OS-2.90 _201909-01~~| 7 de outubro de 2019 | 5 de dezembro de 2019 |  
|~~WA-GUEST-OS-2.89 _201908-01~~| 4 de agosto de 2019 | 01 de novembro de 2019 |  
|~~WA-GUEST-OS-2,88 _201907-01~~| 26 de julho de 2019 | 7 de outubro de 2019 |
|~~WA-GUEST-OS-2.87 _201906-01~~|8 de julho de 2019 | 4 de agosto de 2019 |
|~~WA-GUEST-OS-2.86 _201905-01~~ |6 de junho de 2019 |26 de julho de 2019 |
|~~WA-GUEST-OS-2,85 _201904-01~~ |7 de maio de 2019 |8 de julho de 2019 |
|~~WA-GUEST-OS-2.84 _201903-01~~ |26 de março de 2019 |6 de junho de 2019 |
|~~WA-GUEST-OS-2.83 _201902-01~~ |12 de março de 2019 |7 de maio de 2019 |
|~~WA-GUEST-OS-2.82_201901-01~~ |5 de fevereiro de 2019 |26 de março de 2019 |
|~~WA-GUEST-OS-2.81_201812-01~~ |7 de janeiro de 2019 |12 de março de 2019 |
|~~WA-GUEST-OS-2.80_201811-01~~ |14 de dezembro de 2018 |5 de fevereiro de 2019 |
|~~WA-GUEST-OS-2.79_201810-01~~ |8 de novembro de 2018 |7 de janeiro de 2019 |
|~~WA-GUEST-OS-2.78_201809-01~~ |12 de outubro de 2018 |14 de dezembro de 2018 |

## <a name="msrc-patch-updates"></a>Atualizações de patch do MSRC
A lista de patches incluídos em cada lançamento mensal de SO convidado está disponível [aqui][patches].

## <a name="sdk-support"></a>Suporte a SDK
Embora a [política de desativação do SDK do Azure][retire policy sdk] indique que somente as versões acima da 2.2 têm suporte, as famílias de SO convidado específicas permitem que você use as versões anteriores. Você sempre deve usar o SDK mais recente com suporte.

| Família de SO convidado | Versões compatíveis do SDK |
| --- | --- |
| 6 |Versão 2.9.6 e superiores |
| 5 |Versão 2.9.5.1+ |
| 4 |Versão 2.1+ |
| 3 |Versão 1.8+ |
| 2 |Versão 1.3+ |
| 1 |Versão 1.0+ |

## <a name="guest-os-release-information"></a>Informações de versão do SO convidado
Existem três datas que são importantes para as versões de SO Convidado: data de **lançamento**, data de **desabilitação** e data de **validade**. Um SO convidado será considerado disponível quando estiver no Portal e puder ser selecionado como o SO convidado de destino. Quando um SO convidado alcança a data de **desabilitação**, ele é removido do Azure. No entanto, qualquer Serviço de Nuvem direcionado àquele SO convidado ainda funcionará normalmente.

A janela entre a data de **desabilitação** e a de **validade** fornece um buffer para que você faça a transição facilmente de um SO convidado para um mais recente. Se você estiver usando *automático* como o SO Convidado, sempre estará na versão mais recente e não precisará se preocupar com sua validade.

Quando a data de **validade** vence, qualquer Serviço de nuvem que ainda esteja usando o SO convidado é interrompido, excluído ou forçado a atualizar. Você pode ler mais sobre a política de desativação [aqui][retirepolicy].

## <a name="guest-os-family-version-explanation"></a>Família do SO convidado – explicação de versão
As famílias de SO convidado são baseadas em versões lançadas do Microsoft Windows Server. O SO convidado é o sistema operacional subjacente sobre o qual os Serviços de Nuvem do Azure são executados. Cada SO convidado tem uma família, uma versão e um número de lançamento.

* **Guest OS family**  
   Uma versão do sistema operacional Windows Server na qual o SO Convidado se baseia. Por exemplo, a *família 3* baseia-se no Windows Server 2012.
* **Versão do SO Convidado**  
  Específica de uma imagem da família de SO convidado mais os patches relevantes do [MSRC (Microsoft Security Response Center)][msrc] disponíveis na data em que a nova versão do SO convidado é produzida. Nem todos os patches estarão necessariamente incluídos.

    Os números começam em 0 e são incrementados em 1 cada vez que um novo conjunto de atualizações é adicionado. Os zeros à direita são mostrados apenas se forem importantes. Ou seja, a versão 2.10 é uma versão diferente e muito posterior à versão 2.1.
* **Versão do SO convidado**  
   Um relançamento de uma versão do SO Convidado. Um relançamento ocorre quando a Microsoft encontra, durante os testes, problemas que exigem alterações. A versão mais recente sempre substitui quaisquer lançamentos anteriores, públicos ou não. O portal do Azure só permitirá que os usuários escolham o lançamento mais recente de uma determinada versão. Implantações executadas em uma versão anterior geralmente não sofrem atualização forçada, o que depende da gravidade do bug.

No exemplo abaixo, 2 é a família, 12 é a versão e "rel2" é o lançamento.

**Versão de SO convidado** - 2.12 rel2

**Cadeia de caracteres de configuração para essa versão** - WA-GUEST-OS-2.12_201208-02

A cadeia de caracteres de configuração para um SO convidado tem inseridas nela essas mesmas informações, junto com uma data mostrando quais patches MSRC foram considerados para esse lançamento. Neste exemplo, os patches do MSRC gerados para Windows Server 2008 R2 até (e incluindo) agosto de 2012 foram considerados para inclusão. Apenas os patches que se aplicam especificamente a essa versão do Windows Server são incluídos. Por exemplo, se um patch de MSRC se aplica ao Microsoft Office, ele não será incluído porque esse produto não faz parte da imagem base do Windows Server.

## <a name="guest-os-system-update-process"></a>Processo de atualização de sistema do SO convidado
Esta página contém informações sobre as próximas versões do SO convidado. Os clientes indicaram que desejam saber quando um lançamento ocorre, porque suas funções de serviço de nuvem reinicializarão se elas estiverem definidas para atualização "Automática". Geralmente, os lançamentos do sistema operacional do convidado ocorrem de 2 a 3 semanas após o lançamento da atualização do MSRC, que ocorre na segunda terça-feira de cada mês. As novas versões incluem todos os patches do MSRC relevantes para cada família de SOs convidados.

O Microsoft Azure está constantemente lançando atualizações. O SO convidado é apenas uma atualização desse tipo no pipeline. Uma versão pode ser afetada por um número muito grande de fatores para que se possa listá-los aqui. Além disso, o Azure é executado em literalmente centenas de milhares de computadores. Isso significa que é impossível fornecer uma data e hora exatas em que a função (ou funções) será reinicializada. Estamos trabalhando em um plano para limitar ou programar as reinicializações.

Quando um novo lançamento do SO convidado é realizado, pode levar tempo para que ele se propague totalmente pelo Azure. Como os serviços são atualizados para o novo SO convidado, eles são reinicializados respeitando os domínios de atualização. Os serviços nos quais está definido o uso de atualizações "Automáticas" receberão um lançamento primeiro. Após a atualização, você verá a nova versão do SO convidado listada para seu serviço no portal do Azure. Relançamentos podem ocorrer durante esse período. Algumas versões podem ser implantadas por longos períodos de tempo e reinicializações de atualização automática podem não ocorrer por muitas semanas após a data de lançamento oficial. A partir do momento em que um SO convidado está disponível você pode escolher explicitamente essa versão a partir do portal ou em seu arquivo de configuração.

Para uma grande variedade de informações valiosas sobre reinicializações e indicações para mais detalhes técnicos sobre atualizações de SO de host e convidados, consulte a postagem no blog do MSDN intitulada [Instância de Função reinicia devido a atualizações do SO][restarts].

Se você atualizar manualmente o SO convidado, veja [Política de desativação do SO convidado][retirepolicy] para obter informações adicionais.

## <a name="guest-os-supportability-and-retirement-policy"></a>Política de suporte e desativação do SO convidado
A política de suporte e desativação do SO convidado é explicada [aqui][retirepolicy].

[cloud updates]: ./cloud-services-update-azure-service.md
[Feed RSS de atualização do SO convidado]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
[Install .NET on a Cloud Service Role]: ./cloud-services-dotnet-install-dotnet.md?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Azure Guest OS Update Settings]: cloud-services-how-to-configure-portal.md
[ssl3 announcement]: https://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[Microsoft Security Advisory 3009008]: /security-updates/SecurityAdvisories/2015/3009008
[ssl3-fixit]: https://go.microsoft.com/?linkid=9863266
[MS14-066]: /security-updates/SecurityBulletins/2014/ms14-066
[MS14-046]: /security-updates/SecurityBulletins/2014/ms14-046
[retire policy sdk]: /previous-versions/azure/reference/dn479282(v=azure.100)
[server and gos]: https://msdn.microsoft.com/library/dn775043.aspx
[azuresupport]: https://azure.microsoft.com/support/options/
[net install pkg]: https://www.microsoft.com/download/details.aspx?id=42643
[msrc]: https://technet.microsoft.com/security/dn440717.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: /archive/blogs/kwill/role-instance-restarts-due-to-os-upgrades
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md
[fix]: /security-updates/SecurityBulletins/2017/ms17-010
[Windows Azure SDK]: https://www.microsoft.com/en-us/download/details.aspx?id=54917
[more]: ./applications-dont-support-tls-1-2.md
