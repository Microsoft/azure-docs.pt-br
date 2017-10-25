---
title: "Guia de solução de problemas do Gerenciador de Armazenamento do Azure | Microsoft Docs"
description: "Visão geral dos dois recursos de depuração do Azure"
services: virtual-machines
documentationcenter: 
author: Deland-Han
manager: cshepard
editor: 
ms.assetid: 
ms.service: virtual-machines
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/08/2017
ms.author: delhan
ms.openlocfilehash: e06c73c2c00b27178f8431b83b5c5a42110b6b1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Guia de solução de problemas do Gerenciador de Armazenamento do Azure

O Gerenciador de Armazenamento do Microsoft Azure (Preview) é um aplicativo autônomo que permite que você trabalhe facilmente com dados do Armazenamento do Azure no Windows, no macOS e no Linux. O aplicativo pode se conectar a contas de Armazenamento hospedadas no Azure, em nuvens independentes e no Azure Stack.

Este guia resume as soluções de problemas comuns encontrados no Gerenciador de Armazenamento.

## <a name="sign-in-issues"></a>Problemas de entrada

Somente contas do AAD (Azure Active Directory) têm suporte. Se você usar uma conta do ADFS, espera-se que a entrada no Gerenciador de Armazenamento não funcione. Antes de continuar, tente reiniciar o aplicativo e verifique se os problemas podem ser corrigidos.

### <a name="error-self-signed-certificate-in-certificate-chain"></a>Erro: Certificado autoassinado na cadeia confiável

Você pode encontrar esse erro por vários motivos, os dois mais comuns são estes:

1. O aplicativo está conectado por meio de um "proxy transparente", o que significa que um servidor (como o servidor de sua empresa) está interceptando o tráfego HTTPS, descriptografando-o e, em seguida, criptografando usando um certificado autoassinado.

2. Você está executando um aplicativo, como um software antivírus, que está injetando um certificado SSL autoassinado para as mensagens HTTPS recebidas.

Quando o Gerenciador de Armazenamento encontra um dos problemas, ele não consegue mais saber se a mensagem HTTPS recebida foi violada. Se você tiver uma cópia do certificado autoassinado, poderá permitir que o Gerenciador de Armazenamento confie nele. Se você não tiver certeza de quem está injetando o certificado, execute estas etapas para encontrá-lo:

1. Instalar o Open SSL

    - [Windows](https://slproweb.com/products/Win32OpenSSL.html) (qualquer uma das versões leves deve ser suficiente)

    - Mac e Linux: deve estar incluído com o sistema operacional

2. Executar Open SSL

    - Windows: abra o diretório de instalação, clique em **/bin/** e clique duas vezes em **openssl.exe**.
    - Mac e Linux: execute **openssl** de um terminal.

3. Execute s_client -showcerts -connect microsoft.com:443

4. Procurar certificados autoassinados. Se você não tiver certeza quais são autoassinados, procure onde o assunto ("s") e o emissor ("i") são os mesmos.

5. Depois de encontrar os certificados autoassinados, para cada um deles, copie e cole tudo a partir, e incluindo, **---BEGIN CERTIFICATE---** até **---END CERTIFICATE---** em um novo arquivo .cer.

6. Abra o Gerenciador de Armazenamento, clique em **Editar** > **Certificados SSL** > **Importar Certificados** e, depois, use o seletor de arquivo para localizar, selecionar e abrir os arquivos .cer que você criou.

Se você não conseguir encontrar um certificado autoassinado usando as etapas acima, entre em contato conosco por meio da ferramenta de comentários para obter mais ajuda.

### <a name="unable-to-retrieve-subscriptions"></a>Não é possível recuperar as assinaturas

Se não for possível recuperar as assinaturas após a entrada bem-sucedida, execute estas etapas para solucionar esse problema:

- Verifique se sua conta tem acesso às assinaturas entrando no Portal do Azure.

- Verifique se você entrou usando o ambiente certo (Azure, Azure China, Azure Alemanha, Azure US Government nos ou Ambiente Personalizado/Azure Stack).

- Se você estiver atrás de um proxy, verifique se você configurou o proxy do Gerenciador de Armazenamento apropriadamente.

- Tente remover e readicionar a conta.

- Tente excluir os seguintes arquivos do diretório raiz (ou seja, C:\Usuários\ContosoUser) e, depois, adicione novamente a conta:

    - .adalcache

    - .devaccounts

    - .extaccounts

- Fique atento ao console de ferramentas de desenvolvedor (pressionando F12) enquanto estiver tentando entrar para ver se aparecem mensagens de erro:

![ferramentas de desenvolvedor](./media/storage-explorer-troubleshooting/4022501_en_2.png)

### <a name="unable-to-see-the-authentication-page"></a>Não é possível ver a página de autenticação

Se não for possível ver a página de autenticação, execute estas etapas para solucionar esse problema:

- Dependendo da velocidade de sua conexão, talvez demore algum tempo para a página de entrada carregar. Aguarde pelo menos um minuto antes de fechar a caixa de diálogo de autenticação.

- Se você estiver atrás de um proxy, verifique se você configurou o proxy do Gerenciador de Armazenamento apropriadamente.

- Exiba o console do desenvolvedor pressionando a tecla F12. Veja as respostas no console do desenvolvedor e tente encontrar alguma dica do motivo do não funcionamento da autenticação.

### <a name="cannot-remove-account"></a>Não é possível remover a conta

Se você estiver conseguindo remover uma conta, ou se o link de reautenticação não responder, execute estas etapas para solucionar esse problema:

- Tente excluir os seguintes arquivos do diretório raiz e adicione novamente a conta:

    - .adalcache

    - .devaccounts

    - .extaccounts

- Se você quiser remover SAS associado a recursos de Armazenamento, exclua os seguintes arquivos:

    - Pasta %AppData%/StorageExplorer para Windows

    - /Usuários/<seu_nome>/Library/Applicaiton SUpport/StorageExplorer para Mac

    - ~/.config/StorageExplorer para Linux

> [!NOTE]
>  Será necessário reinserir todas as suas credenciais se você excluir esses arquivos.

## <a name="proxy-issues"></a>Problemas de proxy

Primeiro, certifique-se de que as seguintes informações inseridas estejam corretas:

- A URL do proxy e o número da porta

- Nome de usuário e senha, caso seja solicitado pelo proxy

### <a name="common-solutions"></a>Soluções comuns

Se você ainda estiver enfrentando problemas, execute estas etapas para solucioná-los:

- Se você puder se conectar à Internet sem usar o proxy, verifique se o Gerenciador de Armazenamento funciona sem as configurações de proxy habilitadas. Se esse for o caso, talvez haja um problema com as configurações de proxy. Trabalhe com seu administrador de proxy para identificar os problemas.

- Verifique se outros aplicativos estão usando o servidor proxy conforme o esperado.

- Verifique se você pode se conectar ao Portal do Microsoft Azure usando seu navegador da Web

- Verifique se que você pode receber respostas de seus pontos de extremidade de serviço. Insira uma das suas URLs de ponto de extremidade em seu navegador. Se você puder se conectar, deverá receber um InvalidQueryParameterValue ou resposta XML semelhante.

- Se outra pessoa também estiver usando o Gerenciador de Armazenamento com o servidor proxy, verifique se eles podem se conectar. Se eles puderem se conectar, talvez seja necessário entrar em contato com o administrador de seu servidor proxy.

### <a name="tools-for-diagnosing-issues"></a>Ferramentas para diagnosticar problemas

Se você tiver ferramentas de rede, como o Fiddler para Windows, poderá diagnosticar os problemas da seguinte maneira:

- Se você tiver que trabalhar por meio do proxy, será necessário configurar a ferramenta de rede para se conectar por meio do proxy.

- Verifique o número da porta usado por sua ferramenta de rede.

- Insira a URL do host local e o número de porta da ferramenta de rede como configurações de proxy no Gerenciador de Armazenamento. Se isso for feito corretamente, a ferramenta de rede passará a registrar solicitações de rede feitas pelo Gerenciador de Armazenamento para pontos de extremidade de serviço e de gerenciamento. Por exemplo, insira https://cawablobgrs.blob.core.windows.net/ do ponto de extremidade de blob em um navegador e você receberá uma resposta semelhante à seguinte, que sugere que o recurso existe, embora não seja possível acessá-lo.

![exemplo de código](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>Entre em contato com o administrador do servidor proxy

Se as configurações de proxy estiverem corretas, talvez seja necessário entrar em contato com seu administrador de servidor proxy e

- Verifique se o seu proxy não bloqueia o tráfego nos pontos de extremidade de gerenciamento ou recurso do Azure.

- Verifique o protocolo de autenticação usado por seu servidor proxy. No momento, o Gerenciador de Armazenamento não dá suporte a proxies NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>Mensagem de erro "Não é Possível Recuperar Filhos"

Se você estiver conectado ao Azure por meio de um proxy, verifique se as configurações do proxy estão corretas. Se você tiver recebido acesso a um recurso do proprietário da assinatura ou conta, verifique se você tem permissões de leitura ou de lista para esse recurso.

### <a name="issues-with-sas-url"></a>Problemas com a URL SAS
Se você estiver se conectando a um serviço usando uma URL SAS e enfrentando este erro:

- Verifique se a URL fornece as permissões necessárias para ler ou lista recursos.

- Verifique se a URL não expirou.

- Se a URL SAS tiver base em uma política de acesso, verifique se a política de acesso não foi revogada.

## <a name="next-steps"></a>Próximas etapas

Se nenhuma das soluções funcionar para você, envie seu problema por meio da ferramenta de comentários com seu email e com o máximo de detalhes possível sobre o problema, para que possamos entrar em contato com você para corrigir o problema.

Para fazer isso, clique no menu **Ajuda** e clique em **Enviar Comentários**.

![Comentários](./media/storage-explorer-troubleshooting/4022503_en_1.png)
