---
title: Melhorar o desempenho compactando os arquivos na CDN do Azure | Microsoft Docs
description: Saiba como melhorar a velocidade de transferência do arquivo e aumentar o desempenho de carregamento da página compactando os arquivos na CDN do Azure.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: magattus
ms.openlocfilehash: afe959e80b339db5112fa97fd79d0528390e3954
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60636937"
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>Melhorar o desempenho compactando os arquivos na CDN do Azure
A compactação de arquivo é um método simples e eficiente para melhorar a velocidade de transferência de arquivos e aumentar o desempenho de carregamento de páginas, reduzindo o tamanho de arquivos antes de serem enviados do servidor. Ela pode reduzir os custos de largura de banda e oferece uma experiência mais responsiva para os seus usuários.

Há duas maneiras de habilitar a compactação de arquivo:

- Habilite a compactação no servidor de origem. Nesse caso, a CDN do Azure passa os arquivos compactados e entrega-os aos clientes que os solicitam.
- Habilite a compactação diretamente nos servidores POP de CDN (*compactação em tempo real*). Neste caso, a CDN compacta os arquivos e entrega-os aos usuários finais, mesmo se eles não são compactados pelo servidor de origem.

> [!IMPORTANT]
> As alterações na configuração da CDN do Azure podem demorar um pouco para serem propagadas pela rede: 
> - Para perfis da **CDN Standard do Azure da Microsoft**, a propagação geralmente é concluída em dez minutos. 
> - Para perfis da **CDN Standard do Azure da Akamai**, a propagação normalmente é concluída em um minuto. 
> - Para perfis da **CDN Standard do Azure da Verizon** e da **CDN Premium do Azure da Verizon**, a propagação geralmente é concluída em 10 minutos. 
> 
> Se estiver configurando a compactação pela primeira vez para o ponto de extremidade CDN, considere a possibilidade de aguardar de 1 a 2 horas antes de solucionar problemas para garantir que as configurações de compactação tenham sido propagadas para os POPs.

## <a name="enabling-compression"></a>Habilitando a compactação
As camadas CDN Standard e Premium fornecem a mesma funcionalidade de compactação, mas a interface do usuário varia. Para saber mais sobre as diferenças entre as camadas CDN Standard e Premium, confira [Visão geral da CDN do Azure](cdn-overview.md).

### <a name="standard-cdn-profiles"></a>Perfis CDN Standard 
> [!NOTE]
> Esta seção aplica-se aos perfis da **CDN Standard do Azure da Microsoft**, **CDN Standard do Azure da Verizon**, e **CDN Standard do Azure da Akamai**.
> 
> 

1. Na página do perfil de CDN, selecione o ponto de extremidade da CDN que deseja gerenciar.

    ![Pontos de extremidade de perfil CDN](./media/cdn-file-compression/cdn-endpoints.png)

    A página do ponto de extremidade da CDN se abre.
2. Selecione **Compactação**.

    ![Seleção de compactação da CDN](./media/cdn-file-compression/cdn-compress-select-std.png)

    A página de compactação é aberta.
3. Selecione **Ativar** para ativar a compactação.

    ![Opções de compactação de arquivo da CDN](./media/cdn-file-compression/cdn-compress-standard.png)
4. Use os tipos MIME padrão ou modifique a lista removendo ou adicionando tipos MIME.

   > [!TIP]
   > Embora seja possível, não é recomendável aplicar a compactação a formatos compactados. Por exemplo, ZIP, MP3, MP4 ou JPG.
   > 

   > [!NOTE]
   > Atualmente, a modificação da lista padrão dos tipos MIME não tem suporte na Rede de Distribuição de Conteúdo Standard do Microsoft Azure.
   > 

5. Depois de fazer suas alterações, selecione **Salvar**.

### <a name="premium-cdn-profiles"></a>Perfis CDN Premium
> [!NOTE]
> Esta seção se aplica somente aos perfis da **CDN do Azure Premium da Verizon**.
> 

1. Na página de perfil da CDN, selecione **Gerenciar**.

    ![Seleção de Gerenciar CDN](./media/cdn-file-compression/cdn-manage-btn.png)

    O portal de gerenciamento da CDN é aberto.
2. Passe o mouse sobre a guia **HTTP Grande**, em seguida, sobre o submenu **Configurações do Cache**. Selecione **Compactação**.

    ![Seleção de compactação da CDN](./media/cdn-file-compression/cdn-compress-select.png)

    As opções de compactação são exibidas.

    ![Opções de compactação de arquivo da CDN](./media/cdn-file-compression/cdn-compress-files.png)
3. Habilite a compactação selecionando **Compactação Habilitada**. Insira os tipos MIME que você deseja compactar como uma lista delimitada por vírgula (sem espaços) na caixa **Tipos de Arquivo**.

   > [!TIP]
   > Embora seja possível, não é recomendável aplicar a compactação a formatos compactados. Por exemplo, ZIP, MP3, MP4 ou JPG.
   > 

4. Depois de fazer suas alterações, selecione **Atualizar**.

## <a name="compression-rules"></a>Regras de compactação

### <a name="azure-cdn-standard-from-microsoft-profiles"></a>CDN Standard do Azure dos perfis da Microsoft

Para o **Padrão CDN do Azure dos perfis da Microsoft**, somente arquivos elegíveis são compactados. Para se qualificar para a compactação, um arquivo deve:
- Ser um tipo MIME que foi [configurado para compactação](#enabling-compression).
- Ser maior que 1 KB
- Ser menor do que 8 MB

Esses perfis dão suporte às seguintes codificações de compactação:
- gzip (GNU zip)
- brotli 

Se a solicitação dá suporte a mais de um tipo de compactação, a compactação brotli terá precedência.

Quando uma solicitação de um ativo especificar a compactação gzip e is resultados da solicitação em um cache se perderem, a CDN do Azure realize compactação gzip do ativo diretamente no servidor POP. Depois disso, o arquivo compactado será servido do cache.

### <a name="azure-cdn-from-verizon-profiles"></a>Perfis da CDN do Azure da Verizon

Para os perfis da **CDN Standard do Azure da Verizon** e  **CDN Premium do Azure da Verizon**, apenas arquivos qualificáveis são comprimidos. Para se qualificar para a compactação, um arquivo deve:
- Ser maior que 128 bytes
- Ser menor do que 3 MB

Esses perfis dão suporte às seguintes codificações de compactação:
- gzip (GNU zip)
- DEFLATE
- bzip2
- brotli 

Se a solicitação for compatível com mais de um tipo de compactação, esses tipos de compactação prevalecerão sobre a compactação brotli.

Quando uma solicitação de um ativo especificar a compactação brotli (o cabeçalho HTTP é `Accept-Encoding: br`) e a solicitação gera uma perda no cache, a CDN do Azure executará a compactação brotli do ativo no servidor POP. Depois disso, o arquivo compactado será servido do cache.

### <a name="azure-cdn-standard-from-akamai-profiles"></a>Perfil CDN Standard do Azure dos perfis da Akamai

Para perfis **CDN Standard do Azure da Akamai**, todos os arquivos são qualificados para compactação. No entanto, um arquivo deve ser um tipo MIME que foi [configurado para compactação](#enabling-compression).

Esses perfis dão suporte somente à codificação de compactação gzip. Quando um ponto de extremidade do perfil solicita arquivos codificados em gzip, eles sempre são solicitados da origem, independentemente da solicitação do cliente. 

## <a name="compression-behavior-tables"></a>Tabelas de comportamento de compactação
As tabelas a seguir descrevem o comportamento de compactação CDN do Azure para cada cenário:

### <a name="compression-is-disabled-or-file-is-ineligible-for-compression"></a>A compactação é desabilitada ou arquivo não está qualificado para compactação
| Formato solicitado pelo cliente (por meio do cabeçalho Accept-Encoding) | Formato de arquivo armazenado em cache | A resposta da CDN para o cliente | Observações&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| --- | --- | --- | --- |
| Compactado |Compactado |Compactado | |
| Compactado |Não compactado |Não compactado | |
| Compactado |Não armazenado em cache |Compactada ou descompactada |A resposta de origem determina se a CDN executa uma compactação. |
| Não compactado |Compactado |Não compactado | |
| Não compactado |Não compactado |Não compactado | |
| Não compactado |Não armazenado em cache |Não compactado | |

### <a name="compression-is-enabled-and-file-is-eligible-for-compression"></a>A compactação está habilitada ou o arquivo está qualificado para compactação
| Formato solicitado pelo cliente (por meio do cabeçalho Accept-Encoding) | Formato de arquivo armazenado em cache | Resposta CDN para o cliente | Observações |
| --- | --- | --- | --- |
| Compactado |Compactado |Compactado |CDN transcodifica entre os formatos com suporte. |
| Compactado |Não compactado |Compactado |CDN executa uma compactação. |
| Compactado |Não armazenado em cache |Compactado |A CDN executará compactação se a origem retornar um arquivo descompactado. <br/>**CDN do Azure da Verizon** passa o arquivo descompactado na primeira solicitação e, em seguida, compacta e armazena em cache o arquivo para solicitações subsequentes. <br/>Arquivos com o cabeçalho `Cache-Control: no-cache` nunca são compactados. |
| Não compactado |Compactado |Não compactado |A CDN executa uma descompactação. |
| Não compactado |Não compactado |Não compactado | |
| Não compactado |Não armazenado em cache |Não compactado | |

## <a name="media-services-cdn-compression"></a>Compactação de CDN dos Serviços de Mídia
Para pontos de extremidade habilitados para streaming da CDN dos Serviços de Mídia, a compactação está habilitada por padrão nos seguintes tipos MIME: 
- application/vnd.ms-sstr+xml 
- application/dash+xml
- application/vnd.apple.mpegurl
- application/f4m+xml 

## <a name="see-also"></a>Consulte também
* [Solucionando problemas de compactação de arquivo CDN](cdn-troubleshoot-compression.md)    

