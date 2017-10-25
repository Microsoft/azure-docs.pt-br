---
title: "Saiba mais sobre as versões mais recentes de SO convidado do Azure | Microsoft Docs"
description: "As últimas notícias de versão e a compatibilidade do SDK para o SO convidado dos serviços de nuvem do Azure."
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: 
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 10/6/2017
ms.author: raiye
ms.openlocfilehash: 7d3541e6c437212139553d975fa756084da4f757
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
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
> A partir da distribuição de novembro, haverá suporte e disponibilidade apenas para as duas versões mais recentes do SO convidado no Portal do Azure.
>
>

Não está certo quanto ao que é o SO convidado ou como as versões do SO convidado funcionam? Leia [esta](#how-it-works) seção.

## <a name="news-updates"></a>Notícias atualizadas


###### <a name="october-6-2017"></a>**6 de outubro de 2017**
O SO convidado de setembro foi lançado. Para a versão do Windows Server de setembro de 2016, netfx3 foi habilitado por padrão. Os clientes deverão adicionar ‘dism /online /disable-feature /featurename:netfx3’ em seu OnStart se o fluxo de trabalho exigir que eles executem um aplicativo .NET 2.x com um tempo de execução 4.x ou se eles executaram um aplicativo .NET 2.x, trataram um erro e, em seguida, executaram um aplicativo .NET 4.x.

###### <a name="september-14-2017"></a>**14 de setembro de 2017**
A distribuição do SO convidado de setembro começará no dia 14 de setembro de e tem lançamento previsto para 9 de outubro.

###### <a name="august-24-2017"></a>**24 de agosto de 2017**
O SO convidado de agosto foi lançado.

###### <a name="august-3-2017"></a>**3 de agosto de 2017**
O SO convidado de julho foi lançado.

###### <a name="july-19-2017"></a>**19 de julho de 2017**
A distribuição do SO convidado de julho começa em 19 de julho e está projetada para ser lançada em 8 de agosto.

###### <a name="july-7-2017"></a>**7 de julho de 2017**
O SO convidado de junho foi lançado.

###### <a name="june-16-2017"></a>**16 de junho de 2017**
A distribuição do SO convidado de junho começa dia 16 de junho e está projetada para ser lançada em 11 de julho.

###### <a name="june-5-2017"></a>**5 de junho de 2017**
Talvez o SO convidado tenha sido lançado.

###### <a name="may-17-2017"></a>**17 de maio de 2017**
Devido a um bug de segurança, estamos desabilitando os seguintes Lançamentos do Sistema Operacional de dezembro de 2016 e janeiro de 2017 que não têm a [correção] do portal: WA-GUEST-OS-5.4_201612-01, WA-GUEST-OS-4.39_201612-01, WA-GUEST-OS-3.46_201612-01, WA-GUEST-OS-2.59_201701-01

###### <a name="may-12-2017"></a>**12 de maio de 2017**
A distribuição do SO convidado de maio começa dia 12 de maio e está projetada para ser lançada em 13 de junho.

###### <a name="april-18-2017"></a>**18 de abril de 2017**
A distribuição do SO convidado de abril começa dia 18 de abril e está projetada para ser lançada em 9 de maio.


## <a name="releases"></a>Lançamentos
## <a name="family-5-releases"></a>Versões da Família 5
**Windows Server 2016**

.NET Framework instalado: 4.0, 4.5, 4.5.1, 4.5.2, 4.6, 4.6.1, 4.6.2

> [!NOTE]
> As datas com * estão sujeitas a alterações.
>
> A senha de RDP para a Família do sistema operacional 5 deverá ter um mínimo de 10 caracteres.
>

| Cadeia de caracteres de configuração | Data do lançamento | Data da desabilitação | Data de validade |
| --- | --- | --- | --- |
| WA-GUEST-OS-5.11_201709-01 |6 de outubro de 2017 |Post 5.13 |TBD |
| WA-GUEST-OS-5.10_201708-01 |24 de agosto de 2017 |Post 5.12 |TBD |
| WA-GUEST-OS-5.9_201707-01 |3 de agosto de 2017 |Post 5.11 |TBD |
|~~WA-GUEST-OS-5.8_201706-01~~ |7 de julho de 2017 |6 de outubro de 2017 |TBD |
|~~WA-GUEST-OS-5.7_201705-01~~ |5 de junho de 2017 |24 de agosto de 2017 |TBD |
|~~WA-GUEST-OS-5.6_201704-01~~ |9 de maio de 2017 |3 de agosto de 2017 |TBD |
|~~WA-GUEST-OS-5.5_201703-01~~ |10 de abril de 2017 |7 de julho de 2017 |TBD |
|~~WA-GUEST-OS-5.4_201612-01~~ |10 de janeiro de 2017 |5 de junho de 2017|TBD |
|~~WA-GUEST-OS-5.3_201611-01~~ |14 de dezembro de 2016 |9 de maio de 2017 |TBD |
|~~WA-GUEST-OS-5.2_201610-02~~ |1º de novembro de 2016 |10 de abril de 2017 |TBD |

## <a name="family-4-releases"></a>Versões da Família 4
**Windows Server 2012 R2**

É compatível com .NET 4.0, 4.5, 4.5.1, 4.5.2

> [!NOTE]
> As datas com * estão sujeitas a alterações
>
>

| Cadeia de caracteres de configuração | Data do lançamento | Data da desabilitação | Data de validade |
| --- | --- | --- | --- |
| WA-GUEST-OS-4.46_201709-01 |6 de outubro de 2017 |Post 4.48 |TBD |
| WA-GUEST-OS-4.45_201708-01 |24 de agosto de 2017 |Post 4.47 |TBD |
| WA-GUEST-OS-4.44_201707-01 |3 de agosto de 2017 |Post 4.46 |TBD |
|~~WA-GUEST-OS-4.43_201706-01~~ |7 de julho de 2017 |6 de outubro de 2017 |TBD |
|~~WA-GUEST-OS-4.42_201705-01~~ |5 de junho de 2017 |24 de agosto de 2017 |TBD |
|~~WA-GUEST-OS-4.41_201704-01~~ |9 de maio de 2017 |3 de agosto de 2017 |TBD |
|~~WA-GUEST-OS-4.40_201703-01~~ |10 de abril de 2017 |7 de julho de 2017 |TBD |
|~~WA-GUEST-OS-4.39_201612-01~~ |10 de janeiro de 2017 |5 de junho de 2017 |TBD |
|~~WA-GUEST-OS-4.38_201611-01~~ |14 de dezembro de 2016 |9 de maio de 2017 |TBD |
|~~WA-GUEST-OS-4.37_201610-02~~ |16 de novembro de 2016 |10 de abril de 2017 |TBD |
|~~WA-GUEST-OS-4.36_201609-01~~ |13 de outubro de 2016 |14 de janeiro de 2017 |TBD |
|~~WA-GUEST-OS-4.35_201608-01~~ |13 de setembro de 2016 |16 de dezembro de 2016 |TBD |
|~~WA-GUEST-OS-4.34_201607-01~~ |8 de agosto de 2016 |13 de novembro de 2016 |TBD |


## <a name="family-3-releases"></a>Versões da Família 3
**Windows Server 2012**

É compatível com .NET 4.0, 4.5, 4.5.1, 4.5.2

> [!NOTE]
> As datas com * estão sujeitas a alterações
>
>

| Cadeia de caracteres de configuração | Data do lançamento | Data da desabilitação | Data de validade |
| --- | --- | --- | --- |
| WA-GUEST-OS-3.53_201709-01 |6 de outubro de 2017 |Post 3.55 |TBD |
| WA-GUEST-OS-3.52_201708-01 |24 de agosto de 2017 |Post 3.54 |TBD |
| WA-GUEST-OS-3.51_201707-01 |3 de agosto de 2017 |Post 3.53 |TBD |
|~~WA-GUEST-OS-3.50_201706-01~~ |7 de julho de 2017 |6 de outubro de 2017 |TBD |
|~~WA-GUEST-OS-3.49_201705-01~~ |5 de junho de 2017 |24 de agosto de 2017 |TBD |
|~~WA-GUEST-OS-3.48_201704-01~~ |9 de maio de 2017 |3 de agosto de 2017 |TBD |
|~~WA-GUEST-OS-3.47_201703-01~~ |10 de abril de 2017 |7 de julho de 2017 |TBD |
|~~WA-GUEST-OS-3.46_201612-01~~ |10 de janeiro de 2017 |5 de junho de 2017 |TBD |
|~~WA-GUEST-OS-3.45_201611-01~~ |14 de dezembro de 2016 |9 de maio de 2017 |TBD |
|~~WA-GUEST-OS-3.44_201610-02~~ |16 de novembro de 2016 |1º de maio de 2017 |TBD |
|~~WA-GUEST-OS-3.43_201609-01~~ |13 de outubro de 2016 |14 de janeiro de 2017 |TBD |
|~~WA-GUEST-OS-3.42_201608-01~~ |13 de setembro de 2016 |16 de dezembro de 2016 |TBD |
|~~WA-GUEST-OS-3.41_201607-01~~ |8 de agosto de 2016 |13 de novembro de 2016 |TBD |


## <a name="family-2-releases"></a>Versões da Família 2
**Windows Server 2008 R2 SP1**

Dá suporte a .NET 3.5, 4.0, 4.5, 4.5.1, 4.5.2

> [!NOTE]
> As datas com * estão sujeitas a alterações
>
>

| Cadeia de caracteres de configuração | Data do lançamento | Data da desabilitação | Data de validade |
| --- | --- | --- | --- |
| WA-GUEST-OS-2.66_201709-01 |6 de outubro de 2017 |Post 2.68 |TBD |
| WA-GUEST-OS-2.65_201708-01 |24 de agosto de 2017 |Post 2.67 |TBD |
| WA-GUEST-OS-2.64_201707-01 |3 de agosto de 2017 |Post 2.66 |TBD |
|~~WA-GUEST-OS-2.63_201706-01~~ |7 de julho de 2017 |6 de outubro de 2017 |TBD |
|~~WA-GUEST-OS-2.62_201705-01~~ |5 de junho de 2017 |24 de agosto de 2017 |TBD |
|~~WA-GUEST-OS-2.61_201704-01~~ |9 de maio de 2017 |3 de agosto de 2017 |TBD |
|~~WA-GUEST-OS-2.60_201703-01~~ |10 de abril de 2017 |7 de julho de 2017 |TBD |
|~~WA-GUEST-OS-2.59_201701-01~~ |10 de janeiro de 2017 |5 de junho de 2017 |TBD |
|~~WA-GUEST-OS-2.58_201612-01~~ |10 de janeiro de 2017 |9 de maio de 2017|TBD |
|~~WA-GUEST-OS-2.57_201611-01~~ |14 de dezembro de 2016 |10 de abril de 2017 |TBD |
|~~WA-GUEST-OS-2.56_201610-02~~ |16 de novembro de 2016 |10 de fevereiro de 2017 |TBD |
|~~WA-GUEST-OS-2.55_201609-01~~ |13 de outubro de 2016 |14 de janeiro de 2017 |TBD |
|~~WA-GUEST-OS-2.54_201608-01~~ |13 de setembro de 2016 |16 de dezembro de 2016 |TBD |
|~~WA-GUEST-OS-2.53_201607-01~~ |8 de agosto de 2016 |13 de novembro de 2016 |TBD |



## <a name="msrc-patch-updates"></a>Atualizações de patch do MSRC
A lista de patches incluídos em cada lançamento mensal de SO convidado está disponível [aqui][patches].

## <a name="sdk-support"></a>Suporte a SDK
Embora a [política de desativação do SDK do Azure][retire policy sdk] indique que somente as versões acima da 2.2 têm suporte, as famílias de SO convidado específicas permitem que você use as versões anteriores. Você sempre deve usar o SDK mais recente com suporte.

| Família do SO convidado | Versões compatíveis do SDK |
| --- | --- |
| 5 |Versão 2.9.5.1+ |
| 4 |Versão 2.1+ |
| 3 |Versão 1.8+ |
| 2 |Versão 1.3+ |
| 1 |Versão 1.0+ |

## <a name="guest-os-release-information"></a>Informações de versão do SO convidado
Existem três datas que são importantes para as versões de SO Convidado: data de **lançamento**, data de **desabilitação** e data de **validade**. Um SO convidado será considerado disponível quando estiver no Portal e puder ser selecionado como o SO convidado de destino. Quando um SO convidado alcança a data de **desabilitação**, ele é removido do Azure. No entanto, qualquer Serviço de Nuvem direcionado àquele SO convidado ainda funcionará normalmente.

A janela entre a data de **desabilitação** e a de **validade** fornece um buffer para que você faça a transição facilmente de um SO convidado para um mais recente. Se você estiver usando *automático* como o SO Convidado, sempre estará na versão mais recente e não precisará se preocupar com sua validade.

Quando a data de **validade** vence, qualquer Serviço de nuvem que ainda esteja usando o SO convidado é interrompido, excluído ou forçado a atualizar. Você pode ler mais sobre a política de desativação [aqui][retirepolicy].

## <a name="guest-os-family-version-explanation"></a>Família do SO convidado, explicação de versão
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
Esta página contém informações sobre as próximas versões do SO convidado. Os clientes indicaram que desejam saber quando um lançamento ocorre, porque suas funções de serviço de nuvem reinicializarão se elas estiverem definidas para atualização "Automática". Lançamentos de SO convidado normalmente ocorrem pelo menos cinco (5) dias após o lançamento da atualização do MSRC, que por sua vez ocorre na segunda terça-feira de cada mês. As novas versões incluem todos os patches do MSRC relevantes para cada família de SOs convidados.

O Microsoft Azure está constantemente lançando atualizações. O SO convidado é apenas uma atualização desse tipo no pipeline. Uma versão pode ser afetada por um número muito grande de fatores para que se possa listá-los aqui. Além disso, o Azure é executado em literalmente centenas de milhares de computadores. Isso significa que é impossível fornecer uma data e hora exatas em que a função (ou funções) será reinicializada. Estamos trabalhando em um plano para limitar ou programar as reinicializações.

Quando um novo lançamento do SO convidado é realizado, pode levar tempo para que ele se propague totalmente pelo Azure. Como os serviços são atualizados para o novo SO convidado, eles são reinicializados respeitando os domínios de atualização. Os serviços nos quais está definido o uso de atualizações "Automáticas" receberão um lançamento primeiro. Após a atualização, você verá a nova versão do SO convidado listada para seu serviço no portal do Azure. Relançamentos podem ocorrer durante esse período. Algumas versões podem ser implantadas por longos períodos de tempo e reinicializações de atualização automática podem não ocorrer por muitas semanas após a data de lançamento oficial. A partir do momento em que um SO convidado está disponível você pode escolher explicitamente essa versão a partir do portal ou em seu arquivo de configuração.

Para uma grande variedade de informações valiosas sobre reinicializações e indicações para mais detalhes técnicos sobre atualizações de SO de host e convidados, consulte a postagem no blog do MSDN intitulada [Instância de Função reinicia devido a atualizações do SO][restarts].

Se você atualizar manualmente o SO convidado, veja [Política de desativação do SO convidado][retirepolicy] para obter informações adicionais.

## <a name="guest-os-supportability-and-retirement-policy"></a>Política de suporte e desativação do SO convidado
A política de suporte e desativação do SO convidado é explicada [aqui][retirepolicy].

[RSS feed de atualização do SO convidado]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
[Install .NET on a Cloud Service Role]: https://azure.microsoft.com/en-us/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Azure Guest OS Update Settings]: cloud-services-how-to-configure.md
[ssl3 announcement]: http://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[Microsoft Security Advisory 3009008]: https://technet.microsoft.com/library/security/3009008.aspx
[ssl3-fixit]: http://go.microsoft.com/?linkid=9863266
[MS14-066]: https://technet.microsoft.com/library/security/ms14-066.aspx
[MS14-046]: https://technet.microsoft.com/library/security/ms14-046.aspx
[retire policy sdk]: https://msdn.microsoft.com/library/dn479282.aspx
[server and gos]: https://msdn.microsoft.com/library/dn775043.aspx
[azuresupport]: http://azure.microsoft.com/support/options/
[net install pkg]: http://www.microsoft.com/download/details.aspx?id=42643
[msrc]: http://www.microsoft.com/security/msrc/default.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md
[correção]: https://technet.microsoft.com/en-us/library/security/ms17-010.aspx
