---
title: Recursos do mecanismo de regras da CDN do Azure da Verizon Premium
description: Documentação de referência para recursos do mecanismo de regras da CDN do Azure da Verizon Premium.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 06/02/2020
ms.author: allensu
ms.openlocfilehash: 0ea4f167b992ccfbc4156ac06c8f636d2ef4a355
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96020408"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-features"></a>Recursos do mecanismo de regras da CDN do Azure da Verizon Premium

Este artigo lista descrições detalhadas dos recursos disponíveis para o [Mecanismo de regras](cdn-verizon-premium-rules-engine.md)da CDN (Rede de Distribuição de Conteúdo) do Azure.

A terceira parte de uma regra é o recurso. Um recurso define o tipo de ação que é aplicada ao tipo de solicitação que é identificado por um conjunto de condições de correspondência.

## <a name="azure-cdn-from-verizon-premium-rules-engine-features-reference"></a><a name="top"></a>Referência a recursos do mecanismo de regras da CDN do Azure da Verizon Premium

Os tipos de recursos disponíveis são:

* [Acesso](#access)
* [Cache](#caching)
* [Comentário](#comment)
* [Cabeçalhos](#headers)
* [Logs](#logs)
* [Otimizar](#optimize)
* [Ter](#origin)
* [Especialidade](#specialty)
* [URL](#url)
* [Firewall do Aplicativo Web](#waf)

### <a name="access"></a><a name="access"></a>Access

Esses recursos são projetados para controlar o acesso ao conteúdo.

| Nome       | Finalidade                                                           |
|------------|-------------------------------------------------------------------|
| [Negar Acesso (403)](https://docs.vdms.com/cdn/Content/HRE/F/Deny-Access-403.htm) | Determina se todas as solicitações são rejeitadas com uma resposta 403 Proibido. |
| [Autenticação de Token](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth.htm) | Determina se a autenticação baseada em Token será aplicada a uma solicitação. |
| [Código de Negação de Autenticação de Token](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Denial-Code.htm) | Determina o tipo de resposta que será retornado a um usuário quando uma solicitação for negada devido a autenticação baseada em token. |
| [Autenticação de Token Ignorar Maiúsculas e Minúsculas da URL](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Ignore-URL-Case.htm) | Determina se as comparações de URL feitas por autenticação baseada em token diferenciam maiúsculas de minúsculas. |
| [Parâmetros de Autenticação de Token](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Parameter.htm) | Determina se o parâmetro de cadeia de caracteres de consulta da autenticação baseada em token deve ser renomeado. |

**[Voltar ao início](#top)**

### <a name="caching"></a><a name="caching"></a>Colocar

Esses recursos são projetados para personalizar quando e como o conteúdo é armazenado.

| Nome       | Finalidade                                                           |
|------------|-------------------------------------------------------------------|
| [Parâmetros de Largura de Banda](https://docs.vdms.com/cdn/Content/HRE/F/Bandwidth-Parameters.htm) | Determina se os parâmetros de limitação de largura de banda (isto é, ec_rate e ec_prebuf) estarão ativos. |
| [Limitação de largura de banda](https://docs.vdms.com/cdn/Content/HRE/F/Bandwidth-Throttling.htm) | Limita a largura de banda para a resposta fornecida por nossos servidores de borda. |
| [Ignorar o Cache](https://docs.vdms.com/cdn/Content/HRE/F/Bypass-Cache.htm) | Determina se a solicitação pode aproveitar nossa tecnologia de cache. |
| [Tratamento de Cabeçalho Cache-Control](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Control-Header-Treatment.htm) |  Controla a geração de cabeçalhos de Controle de Cache pelo servidor de borda quando o recurso Idade Máxima Externa está ativo. |
| [Cadeia de Caracteres da Consulta da Chave de Cache](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Query-String.htm) | Determina se o **cache-Key** _ incluirá ou excluirá parâmetros de cadeia de caracteres de consulta associados a uma solicitação. <br> _ É um caminho relativo que identifica exclusivamente um ativo para fins de cache.  Nossos servidores de borda usam esse caminho relativo ao verificar o conteúdo armazenado em cache.  Por padrão, uma chave de cache não conterá a cadeia de caracteres de consulta parameters._ |
| [Regravação da Chave de Cache](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Rewrite.htm) | Regrava a chave de cache associada a uma solicitação. |
| [Concluir o Preenchimento do Cache](https://docs.vdms.com/cdn/Content/HRE/F/Complete-Cache-Fill.htm) | Determina o que acontece quando uma solicitação resulta em uma perda no cache parcial em um servidor de borda. |
| [Compactar Tipos de Arquivo](https://docs.vdms.com/cdn/Content/HRE/F/Compress-File-Types.htm) | Define os formatos de arquivo que serão compactados no servidor. | 
| [Idade Máxima Interna Padrão](https://docs.vdms.com/cdn/Content/HRE/F/Default-Internal-Max-Age.htm) | Determina o intervalo de idade máxima padrão para a revalidação de cache do servidor de borda para o servidor de origem. |
| [Tratamento do Cabeçalho Expira](https://docs.vdms.com/cdn/Content/HRE/F/Expires-Header-Treatment.htm) | Controla a geração de cabeçalhos Expira por um servidor de borda quando o recurso Idade Máxima Externa está ativo. |
| [Idade Máxima Externa](https://docs.vdms.com/cdn/Content/HRE/F/External-Max-Age.htm) | Determina o intervalo de idade máxima para a revalidação de cache do navegador para o servidor de borda. |
| [Forçar Idade Máxima Interna](https://docs.vdms.com/cdn/Content/HRE/F/Force-Internal-Max-Age.htm) | Determina o intervalo de idade máxima para a revalidação de cache do servidor de borda para o servidor de origem. |
| [Suporte a H.264 (Download Progressivo de HTTP)](https://docs.vdms.com/cdn/Content/HRE/F/H.264-Support-HPD.htm) | Determina os tipos de formatos de arquivo H.264 que podem ser usados para transmitir conteúdo. |
| [H. 264 dá suporte a parâmetros de busca de vídeo](https://docs.vdms.com/cdn/Content/HRE/F/H.264-Support-VSP-HPD.htm) | Substitui os nomes atribuídos aos parâmetros que controlam a busca pela mídia H. 264 ao usar o download progressivo de HTTP. |
| [Respeitar Solicitação Sem Cache](https://docs.vdms.com/cdn/Content/HRE/F/Honor-No-Cache-Request.htm) | Determina se solicitações sem cache de um cliente HTTP serão encaminhadas para o servidor de origem. |
| [Ignorar Ausência de Cache de Origem](https://docs.vdms.com/cdn/Content/HRE/F/Ignore-Origin-No-Cache.htm) | Determina se nosso CDN irá ignorar determinadas diretivas atendidas por um servidor de origem. |
| [Ignorar Intervalos Insatisfatórios](https://docs.vdms.com/cdn/Content/HRE/F/Ignore-Unsatisfiable-Ranges.htm) | Determina a resposta que será retornada aos clientes quando uma solicitação gerar um código de status 416 Intervalo Solicitado Insatisfatório. |
| [Máximo de Estado Obsoleto Interno](https://docs.vdms.com/cdn/Content/HRE/F/Internal-Max-Stale.htm) | Controla quanto tempo após o tempo de expiração normal um ativo em cache pode ser atendido por um servidor de borda quando este não puder revalidar o ativo em cache no servidor de origem. |
| [Compartilhamento de Cache Parcial](https://docs.vdms.com/cdn/Content/HRE/F/Partial-Cache-Sharing.htm) | Determina se uma solicitação pode gerar conteúdo parcialmente em cache. |
| [Pré-validar Conteúdo Armazenado em Cache](https://docs.vdms.com/cdn/Content/HRE/F/Prevalidate-Cached-Content.htm) | Determina se o conteúdo armazenado em cache será qualificado para revalidação antecipada antes que o TTL expire. |
| [Atualizar Arquivos de Cache de Zero Byte.](https://docs.vdms.com/cdn/Content/HRE/F/Refresh-Zero-Byte-Cache-Files.htm) | Determina como a solicitação de cliente HTTP para um ativo de cache de zero byte é tratado por nossos servidores de borda. |
| [Definir Códigos de Status Armazenáveis em Cache](https://docs.vdms.com/cdn/Content/HRE/F/Set-Cacheable-Status-Codes.htm) | Define o conjunto de códigos de status que pode resultar em conteúdo armazenado em cache. |
| [Distribuição de Conteúdo Obsoleta em Erro](https://docs.vdms.com/cdn/Content/HRE/F/Stale-Content-Delivery-on-Error.htm) | Determina se o conteúdo armazenado em cache expirado será entregue quando ocorre um erro durante a revalidação do cache ou ao recuperar o conteúdo solicitado do servidor de origem do cliente. | 
| [Obsoleto Ao Revalidar](https://docs.vdms.com/cdn/Content/HRE/F/Stale-While-Revalidate.htm) | Melhora o desempenho permitindo que os nossos servidores de borda atendam clientes obsoletos para o solicitante enquanto ocorre a revalidação. |

**[Voltar ao início](#top)**

### <a name="comment"></a><a name="comment"></a>Comentário

O recurso Comentário permite a adição de detalhes a uma regra.

**[Voltar ao início](#top)**

### <a name="headers"></a><a name="headers"></a>Cabeçalhos

Esses recursos são projetados para adicionar, modificar ou excluir os cabeçalhos da solicitação ou da resposta.

| Nome       | Finalidade                                                           |
|------------|-------------------------------------------------------------------|
| [Cabeçalho de Resposta de Idade](https://docs.vdms.com/cdn/Content/HRE/F/Age-Response-Header.htm) | Determina se um cabeçalho de resposta de idade será incluído na resposta enviada ao solicitante. |
| [Depurar Cabeçalhos de Resposta do Cache](https://docs.vdms.com/cdn/Content/HRE/F/Debug-Cache-Response-Headers.htm) | Determina se uma resposta pode incluir o [cabeçalho de resposta X-EC-Debug,](https://docs.vdms.com/cdn/Content/Knowledge_Base/X_EC_Debug.htm) que fornece informações sobre a política de cache para o ativo solicitado. |
| [Modificar Cabeçalho de Solicitação do Cliente](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Request-Header.htm) | Substitui, acrescenta ou exclui um cabeçalho de uma solicitação. |
| [Modificar Cabeçalho de Resposta do Cliente](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Response-Header.htm) | Substitui, acrescenta ou exclui um cabeçalho de uma resposta. |
| [Definir Cabeçalho Personalizado da IP do Cliente](https://docs.vdms.com/cdn/Content/HRE/F/Set-Client-IP-Custom-Header.htm) | Permite que o endereço IP do cliente solicitante seja adicionado à solicitação como um cabeçalho de solicitação personalizado. |

**[Voltar ao início](#top)**

### <a name="logs"></a><a name="logs"></a>Logs

Esses recursos são projetados para personalizar os dados armazenados em arquivos de log brutos.

| Nome       | Finalidade                                                           |
|------------|-------------------------------------------------------------------|
| [Campo de Log Personalizado 1](https://docs.vdms.com/cdn/Content/HRE/F/Custom-Log-Field-1.htm) | Determina o formato e o conteúdo que será atribuído ao campo de log personalizado em um arquivo de log bruto. |
| [Cadeia de Caracteres de Consulta de Log](https://docs.vdms.com/cdn/Content/HRE/F/Log-Query-String.htm) | Determina se uma cadeia de caracteres de consulta será armazenada juntamente com a URL nos logs de acesso. |

**[Voltar ao início](#top)**

### <a name="optimize"></a><a name="optimize"></a>Formato

Esses recursos determinam se uma solicitação passará as otimizações fornecidas pelo Otimizador de Borda.

| Nome       | Finalidade                                                           |
|------------|-------------------------------------------------------------------|
| [Otimizador de Borda](https://docs.vdms.com/cdn/Content/HRE/F/Edge-Optimizer.htm) | Determina se o Otimizador de Borda pode ser aplicado a uma solicitação. |
| [Otimizador de Borda – Instanciar Configuração](https://docs.vdms.com/cdn/Content/HRE/F/Edge-Optimizer-Instantiate-Configuration.htm) | Cria uma instância ou ativa a configuração do Otimizador de Borda associada a um site. |

**[Voltar ao início](#top)**

### <a name="origin"></a><a name="origin"></a>Ter

Esses recursos são criados para controlar como a CDN se comunica com um servidor de origem.

| Nome       | Finalidade                                                           |
|------------|-------------------------------------------------------------------|
| [Máximo de Solicitações Keep-Alive](https://docs.vdms.com/cdn/Content/HRE/F/Maximum-Keep-Alive-Requests.htm) | Define o número máximo de solicitações para uma conexão Keep-Alive antes de ser fechado. |
| [Cabeçalhos Especiais de Proxy](https://docs.vdms.com/cdn/Content/HRE/F/Proxy-Special-Headers.htm) | Define o conjunto de [cabeçalhos de solicitação específicos da CDN](https://docs.vdms.com/cdn/Content/Knowledge_Base/Request-Format.htm#RequestHeaders) que será encaminhado de um servidor de borda para um servidor de origem. |

**[Voltar ao início](#top)**

### <a name="specialty"></a><a name="specialty"></a>Especialidade

Esses recursos fornecem funcionalidade avançada que deve ser usada somente por usuários avançados.

| Nome       | Finalidade                                                           |
|------------|-------------------------------------------------------------------|
| [Métodos HTTP Armazenáveis em Cache](https://docs.vdms.com/cdn/Content/HRE/F/Cacheable-HTTP-Methods.htm) | Determina o conjunto de métodos HTTP adicionais que podem ser armazenados em cache em nossa rede. |
| [Tamanho do Corpo da Solicitação Armazenável em Cache](https://docs.vdms.com/cdn/Content/HRE/F/Cacheable-Request-Body-Size.htm) | Define o limite para determinar se uma resposta POST pode ser armazenada em cache. |
| [QUIC](https://docs.vdms.com/cdn/Content/HRE/F/QUIC.htm) | Determina se o cliente será informado de que nosso serviço CDN dá suporte a QUIC. |
| [Otimização de streaming](https://docs.vdms.com/cdn/Content/HRE/F/Streaming-Optimization.htm) | Ajusta sua configuração de cache para otimizar o desempenho de fluxos ao vivo e para reduzir a carga no servidor de origem. |
| [Variável do Usuário](https://docs.vdms.com/cdn/Content/HRE/F/User-Variable.htm) | Atribui um valor a uma variável definida pelo usuário que é passada para sua solução de processamento de tráfego do bespoke. |

**[Voltar ao início](#top)**

### <a name="url"></a><a name="url"></a>URL

Esses recursos permitem que uma solicitação seja redirecionada ou reescrita em uma URL diferente.

| Nome       | Finalidade                                                           |
|------------|-------------------------------------------------------------------|
| [Seguir Redirecionamentos](https://docs.vdms.com/cdn/Content/HRE/F/Follow-Redirects.htm) | Determina se as solicitações podem ser redirecionadas para o nome do host definido no cabeçalho Local retornado por um servidor de origem do cliente. |
| [Redirecionamento de URL](https://docs.vdms.com/cdn/Content/HRE/F/URL-Redirect.htm) | Redireciona as solicitações por meio do cabeçalho Local. |
| [Regravação de URL](https://docs.vdms.com/cdn/Content/HRE/F/URL-Rewrite.htm) | Regrava a URL da solicitação. |

**[Voltar ao início](#top)**

### <a name="web-application-firewall"></a><a name="waf"></a>Firewall do aplicativo Web

O recurso de [Firewall do aplicativo Web](https://docs.vdms.com/cdn/Content/HRE/F/Web_Application_Firewall.htm) determina se uma solicitação será filtrada pelo firewall do aplicativo Web.

**[Voltar ao início](#top)**

Para obter os recursos mais recentes, confira a [documentação do mecanismo de regras da Verizon](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Actions).

## <a name="next-steps"></a>Próximas etapas

- [Referência do mecanismo de regras](cdn-verizon-premium-rules-engine-reference.md)
- [Expressões condicionais do mecanismo de regras](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Condições de correspondência do mecanismo de regras](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Substitua o comportamento HTTP usando o mecanismo de regras](cdn-verizon-premium-rules-engine.md)
- [Visão geral da CDN do Azure](cdn-overview.md)
