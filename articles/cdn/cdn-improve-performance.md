---
title: Melhorar o desempenho compactando os arquivos na CDN do Azure | Microsoft Docs
description: "Saiba como melhorar a velocidade de transferência do arquivo e aumentar o desempenho de carregamento da página compactando os arquivos na CDN do Azure."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 86f1f8f0aaa9a699d2f816781cef65cda868ee55
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>Melhorar o desempenho compactando os arquivos na CDN do Azure
A compactação é um método simples e eficiente para melhorar a velocidade de transferência de arquivos e aumentar o desempenho de carregamento de páginas, reduzindo o tamanho de arquivos antes de serem enviados do servidor. Ela reduz os custos de largura de banda e oferece uma experiência mais responsiva para os seus usuários.

Há duas maneiras de habilitar a compactação:

* Habilite a compactação no servidor de origem. Nesse caso, a CDN passa os arquivos compactados e entrega-os aos clientes que os solicitam.
* Habilite a compactação diretamente nos servidores de borda da CDN. Nesse caso, a CDN compacta os arquivos e entrega-os aos usuários finais, mesmo se eles não são compactados pelo servidor de origem.

> [!IMPORTANT]
> As alterações de configuração da CDN podem levar algum tempo para serem propagadas pela rede.  Para perfis <b>CDN do Azure do Akamai</b> , a propagação normalmente é concluída em menos de um minuto.  Para perfis <b>CDN do Azure da Verizon</b>, as alterações geralmente são aplicadas em até 90 minutos.  Se estiver configurando a compactação pela primeira vez para o ponto de extremidade CDN, considere a possibilidade de aguardar de 1 a 2 horas antes de solucionar problemas para garantir que as configurações de compactação tenham sido propagadas para os POPs.
> 
> 

## <a name="enabling-compression"></a>Habilitando a compactação
> [!NOTE]
> As camadas CDN Standard e Premium fornecem a mesma funcionalidade de compactação, mas a interface do usuário varia.  Para saber mais sobre as diferenças entre as camadas CDN Standard e Premium, confira [Visão geral da CDN do Azure](cdn-overview.md).
> 
> 

### <a name="standard-tier"></a>Camada padrão
> [!NOTE]
> Esta seção aplica-se aos perfis da **CDN Standard do Azure da Verizon** e da **CDN Standard do Azure da Akamai**.
> 
> 

1. Na página de perfil da CDN, clique no ponto de extremidade da CDN que deseja gerenciar.
   
    ![Pontos de extremidade de perfil CDN](./media/cdn-file-compression/cdn-endpoints.png)
   
    A página do ponto de extremidade da CDN se abre.
2. Clique no botão **Configurar** .
   
    ![Botão Gerenciar perfil da CDN](./media/cdn-file-compression/cdn-config-btn.png)
   
    A página de Configuração da CDN é aberta.
3. Habilitar **Compactação**.
   
    ![Opções de compactação da CDN](./media/cdn-file-compression/cdn-compress-standard.png)
4. Use os tipos padrão ou modifique a lista removendo ou adicionando tipos de arquivo.
   
   > [!TIP]
   > Embora seja possível, não é recomendável aplicar a compactação a formatos compactados. Por exemplo, ZIP, MP3, MP4 ou JPG.
   > 
 
5. Após fazer suas alterações, clique no botão **Salvar** .

### <a name="premium-tier"></a>Camada premium
> [!NOTE]
> Esta seção aplica-se aos perfis **CDN do Azure Premium da Verizon** .
> 
> 

1. Na página de perfil da CDN, clique no botão **Gerenciar**.
   
    ![Botão Gerenciar perfil da CDN](./media/cdn-file-compression/cdn-manage-btn.png)
   
    O portal de gerenciamento da CDN é aberto.
2. Passe o mouse sobre a guia **HTTP Grande**, em seguida, sobre o submenu **Configurações do Cache**.  Clique em **Compactação**.

    ![Seleção de compactação de arquivos](./media/cdn-file-compression/cdn-compress-select.png)
   
    As opções de compactação são exibidas.
   
    ![Opções de compactação de arquivo](./media/cdn-file-compression/cdn-compress-files.png)
3. Habilite a compactação clicando no botão de opção **Compactação Habilitada** .  Insira os tipos MIME que você deseja compactar como uma lista delimitada por vírgula (sem espaços) na caixa de texto **Tipos de Arquivo** .
   
   > [!TIP]
   > Embora seja possível, não é recomendável aplicar a compactação a formatos compactados. Por exemplo, ZIP, MP3, MP4 ou JPG.
   > 
4. Depois de fazer as alterações, clique no botão **Atualizar** .

## <a name="compression-rules"></a>Regras de compactação
Essas tabelas descrevem o comportamento de compactação CDN do Azure para cada cenário.

> [!IMPORTANT]
> Para os perfis **CDN do Azure da Verizon** (Standard e Premium), apenas os arquivos qualificados são compactados.  Para se qualificar para a compactação, um arquivo deve:
> 
> * Ser maior que 128 bytes.
> * Ser menor que 1 MB.
> 
> Esses perfis são suporte à codificação **gzip** (GNU zip), **deflate**, **bzip2** ou **br** (Brotli). Para a codificação Brotli, a compactação é realizada apenas na borda. O cliente/navegador deve enviar a solicitação para codificação Brotli, e o ativo compactado deve ter sido compactado primeiro no lado de origem. 

> [!IMPORTANT]
> Para perfis **CDN do Azure da Akamai**, todos os arquivos são qualificados para compactação. No entanto, um arquivo deve ser um tipo MIME que foi [configurado para compactação](#enabling-compression).
> 
>Esses perfis dão suporte apenas à codificação **gzip**. Quando um ponto de extremidade do perfil solicita arquivos codificados em **gzip**, eles sempre são solicitados da origem, independentemente da solicitação do cliente. 

### <a name="compression-disabled-or-file-is-ineligible-for-compression"></a>Compactação desabilitada ou arquivo não qualificado para compactação
| Formato solicitado pelo cliente (por meio do cabeçalho Accept-Encoding) | Formato de arquivo armazenado em cache | Resposta CDN para o cliente | Observações |
| --- | --- | --- | --- |
| Compactado |Compactado |Compactado | |
| Compactado |Não compactado |Não compactado | |
| Compactado |Não armazenado em cache |Compactada ou descompactada |Depende da resposta de origem |
| Não compactado |Compactado |Não compactado | |
| Não compactado |Não compactado |Não compactado | |
| Não compactado |Não armazenado em cache |Não compactado | |

### <a name="compression-enabled-and-file-is-eligible-for-compression"></a>Compactação habilitada ou arquivo qualificado para compactação
| Formato solicitado pelo cliente (por meio do cabeçalho Accept-Encoding) | Formato de arquivo armazenado em cache | Resposta CDN para o cliente | Observações |
| --- | --- | --- | --- |
| Compactado |Compactado |Compactado |CDN transcodifica entre os formatos com suporte |
| Compactado |Não compactado |Compactado |CDN executa compactação |
| Compactado |Não armazenado em cache |Compactado |O CDN executará compactação se a origem for retornada descompactada.  **CDN do Azure da Verizon** passa o arquivo descompactado na primeira solicitação e, em seguida, compacta e armazena em cache o arquivo para solicitações subsequentes.  Arquivos com o cabeçalho `Cache-Control: no-cache` nunca são compactados. |
| Não compactado |Compactado |Não compactado |CDN executa descompactação |
| Não compactado |Não compactado |Não compactado | |
| Não compactado |Não armazenado em cache |Não compactado | |

## <a name="media-services-cdn-compression"></a>Compactação de CDN dos Serviços de Mídia
Para pontos de extremidade de streaming habilitados para CDN dos Serviços de Mídia, a compactação está habilitada por padrão nos seguintes tipos de conteúdo: 
- application/vnd.ms-sstr+xml 
- application/dash+xml
- application/vnd.apple.mpegurl
- application/f4m+xml. 

Não é possível habilitar nem desabilitar a compactação nos tipos mencionados usando o portal do Azure.  

## <a name="see-also"></a>Consulte também
* [Solucionando problemas de compactação de arquivo CDN](cdn-troubleshoot-compression.md)    

