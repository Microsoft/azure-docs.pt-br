---
title: "Proteger ativos da CDN do Azure com autenticação de token | Microsoft Docs"
description: "Usar a autenticação de token para proteger o acesso aos ativos da CDN do Azure."
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: 
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/11/2016
ms.author: mezha
ms.openlocfilehash: 42b182c314795b1ebf69639ec7ac5583208dc7c1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="securing-azure-cdn-assets-with-token-authentication"></a>Proteger ativos da CDN do Azure com autenticação de token

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

##<a name="overview"></a>Visão geral

A autenticação de token é um mecanismo que permite impedir que a CDN do Azure forneça ativos a clientes não autorizados.  Isso normalmente é feito para evitar "hotlinking" de conteúdo, em que um site diferente, geralmente um quadro de mensagens, usa seus ativos sem sua permissão.  Isso pode ter um impacto sobre os custos de fornecimento de conteúdo. Habilitando esse recurso na CDN, as solicitações serão autenticadas por POPs de borda da CDN antes de entregar o conteúdo. 

## <a name="how-it-works"></a>Como ele funciona

A autenticação de token verifica se as solicitações são geradas por um site confiável, exigindo que as solicitações contenham um valor de token com informações codificadas sobre o solicitante. O conteúdo será fornecido ao solicitante apenas as informações codificadas atenderem aos requisitos. Caso contrário, as solicitações serão negadas. Você pode configurar o requisito usando um ou vários parâmetros abaixo.

- País: permitir ou negar solicitações originadas de países especificados.  [Lista de códigos de país válidos.](https://msdn.microsoft.com/library/mt761717.aspx) 
- URL: permitir somente o ativo especificado ou o caminho para a solicitação.  
- Host: permitir ou negar as solicitações de hosts especificados no cabeçalho da solicitação.
- Referenciador: permitir ou negar o referenciador especificado para a solicitação.
- Endereço IP: permitir somente solicitações originadas de determinado endereço IP ou sub-rede IP.
- Protocolo: permitir ou bloquear solicitações com base no protocolo usado para solicitar o conteúdo.
- Tempo de expiração: atribua um período de data e hora para garantir que um link só permaneça válido por um período de tempo limitado.

Confira o exemplo de configuração detalhado de cada parâmetro.

## <a name="reference-architecture"></a>Arquitetura de referência

Veja abaixo uma arquitetura de referência de configuração de autenticação de token na CDN para trabalhar com o aplicativo Web.

![botão gerenciar da folha Perfil CDN](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>Lógica de validação de token no ponto de extremidade da CDN
    
Este gráfico descreve como a CDN do Azure valida a solicitação do cliente quando a autenticação de token é configurada no ponto de extremidade da CDN.

![botão gerenciar da folha Perfil CDN](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Configurar autenticação de token

1. No [portal do Azure](https://portal.azure.com), navegue até seu perfil da CDN e clique no botão **Gerenciar** para iniciar o portal suplementar.

    ![botão gerenciar da folha Perfil CDN](./media/cdn-rules-engine/cdn-manage-btn.png)

2. Passe o mouse sobre **HTTP Grande** e clique em **Token de Autenticação** no submenu. Você definirá a chave de criptografia e os parâmetros de criptografia nessa guia.

    1. Insira uma chave de criptografia exclusiva para **Primary Key**.  Insira outra para **Chave de Backup**

        ![Chave de configuração de autenticação de token da CDN](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
    2. Configure os parâmetros de criptografia com a ferramenta de criptografia (permitir ou negar solicitações com base no tempo de expiração, país, referenciador, protocolo, IP do cliente. Você pode usar qualquer combinação.)

        ![botão gerenciar da folha Perfil CDN](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

        - ec-expirer: atribui um tempo de expiração de um token após um período de tempo especificado. Solicitações enviadas depois do tempo de expiração serão negadas. Esse parâmetro usa o carimbo de data/hora do Unix (com base em segundos desde a época padrão de 1/1/1970 00:00:00 GMT. Você pode usar ferramentas online para fornecer a conversão entre o horário padrão e o horário Unix.)  Por exemplo, se você quiser configurar o token para expirar em 31/12/2016 às 12h00 GMT, use o horário Unix:1483185600, conforme mostrado a seguir:
    
        ![botão gerenciar da folha Perfil CDN](./media/cdn-token-auth/cdn-token-auth-expire2.png)
    
        - ec-url-allow: permite personalizar tokens para determinado ativo ou caminho. Restringe o acesso às solicitações cuja URL começa com um caminho relativo específico. Você pode inserir vários caminhos, separando cada caminho com uma vírgula. URLs diferenciam maiúsculas de minúsculas. Dependendo do requisito, você pode definir um valor diferente para fornecer um nível de acesso diferente. Abaixo estão alguns cenários:
        
            Se você tiver uma URL: http://www.mydomain.com/pictures/city/strasbourg.png. Confira o valor de entrada "" e o nível de acesso correspondente

            1. Valor de entrada "/": todas as solicitações serão permitidas
            2. Valor de entrada "/pictures": todas as solicitações a seguir serão permitidas
            
                - http://www.mydomain.com/pictures.png
                - http://www.mydomain.com/pictures/city/strasbourg.png
                - http://www.mydomain.com/picturesnew/city/strasbourgh.png
            3. Valor de entrada "/pictures/": somente as solicitações de /pictures/ serão permitidas
            4. Valor de entrada "/pictures/city/strasbourg.png": só permite a solicitação para este ativo
    
        ![botão gerenciar da folha Perfil CDN](./media/cdn-token-auth/cdn-token-auth-url-allow4.png)
    
        - ec-country-allow: permite somente solicitações originadas de um ou mais países especificados. Solicitações originadas em todos os outros países serão negadas. Use o código do país para configurar os parâmetros e separe cada código de país por uma vírgula. Por exemplo, se você quiser permitir o acesso dos Estados Unidos e da França, insira US, FR na coluna, como indicado abaixo.  
        
        ![botão gerenciar da folha Perfil CDN](./media/cdn-token-auth/cdn-token-auth-country-allow.png)

        - ec-country-deny: nega solicitações originadas de um ou mais países especificados. Solicitações originadas em todos os outros países serão permitidas. Use o código do país para configurar os parâmetros e separe cada código de país por uma vírgula. Por exemplo, se você deseja negar o acesso dos Estados Unidos e da França, insira US, FR na coluna.
    
        - ec-ref-allow: permite solicitações da referência especificada. Um referenciador identifica a URL da página da Web vinculada ao recurso que está sendo solicitado. O valor do parâmetro de referência não deve incluir o protocolo. Você pode inserir um nome de host e/ou um caminho específico no nome do host. Você também pode adicionar vários referenciadores em um único parâmetro, separando cada um com uma vírgula. Se você especificar um valor de referência, mas as informações de referência não forem enviadas na solicitação devido a algumas configurações do navegador, essas solicitações serão negadas por padrão. Você pode atribuir "Ausente" ou um valor em branco no parâmetro para permitir solicitações com ausência de informações de referência. Você também pode usar "*.contoso.com" para permitir todos os subdomínios de contoso.com.  Por exemplo, se você quiser permitir o acesso para solicitações de www.contoso.com, todos os subdomínios em contoso2.com e solicitações com referenciais em branco ou ausentes, insira o valor abaixo:
        
        ![botão gerenciar da folha Perfil CDN](./media/cdn-token-auth/cdn-token-auth-referrer-allow2.png)
    
        - ec-ref-deny: nega solicitações da referência especificada. Confira os detalhes e o exemplo no parâmetro "ec-ref-allow".
         
        - ec-proto-allow: permite somente solicitações do protocolo especificado. Por exemplo, http ou https.
        
        ![botão gerenciar da folha Perfil CDN](./media/cdn-token-auth/cdn-token-auth-url-allow4.png)
            
        - ec-proto-deny: nega solicitações do protocolo especificado. Por exemplo, http ou https.
    
        - ec-clientip: restringe o acesso ao endereço IP do solicitante especificado. Há suporte para IPV4 e IPV6. Você pode especificar o endereço IP de solicitação única ou sub-rede IP.
            
        ![botão gerenciar da folha Perfil CDN](./media/cdn-token-auth/cdn-token-auth-clientip.png)
        
    3. Você pode testar o token com a ferramenta de descrição.

    4. Você também pode personalizar o tipo de resposta que será retornado ao usuário quando a solicitação for negada. Por padrão, usamos 403.

3. Agora, clique na guia **Mecanismo de Regras** em **HTTP Grande**. Você usará essa guia para definir os caminhos para aplicar o recurso, habilitar o recurso de autenticação de token e habilitar recursos adicionais relacionados à autenticação de token.

    - Use a coluna "IF" para definir o caminho ou ativo que você deseja aplicar para a autenticação de token. 
    - Clique para adicionar o "Token de Autenticação" no menu suspenso do recurso para habilitar a autenticação de token.
        
    ![botão gerenciar da folha Perfil CDN](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. Na guia **Mecanismo de Regras**, há alguns recursos adicionais que você pode habilitar.
    
    - Código de negação de autenticação de token: determina o tipo de resposta que será retornada ao usuário quando uma solicitação for negada. As regras definidas aqui substituirão a configuração de código de negação na guia de autenticação de token.
    - Ignorar autenticação de token: determina se a URL usada para validar o token diferenciará maiúsculas de minúsculas.
    - Parâmetro de autenticação de token: renomeie o parâmetro de cadeia de caracteres de consulta do token de autenticação mostrado na URL solicitada. 
        
    ![botão gerenciar da folha Perfil CDN](./media/cdn-token-auth/cdn-rules-engine2.png)

5. Você pode personalizar o token, que é um aplicativo que gera um token para recursos de autenticação com base em token. O código-fonte pode ser acessado aqui no [GitHub](https://github.com/VerizonDigital/ectoken).
Os idiomas disponíveis incluem:
    
    - C
    - C#
    - PHP
    - Perl
    - Java
    - Python    


## <a name="azure-cdn-features-and-provider-pricing"></a>Preços de provedor e recursos da Azure CDN

Confira o tópico [Visão geral da CDN](cdn-overview.md).
