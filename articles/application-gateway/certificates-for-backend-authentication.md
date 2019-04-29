---
title: Certificados necessários para o back-ends de lista de permissões no Gateway de aplicativo do Azure
description: Este artigo fornece exemplos de como um certificado SSL pode ser convertido para o certificado de autenticação e o certificado raiz confiável que são necessários para a lista de permissões instâncias de back-end no Gateway de aplicativo do Azure
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/14/2019
ms.author: absha
ms.openlocfilehash: 72ee9123ad959c0c7240d4f7a906adc1a4dd1a93
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831477"
---
# <a name="create-certificates-for-whitelisting-backend-with-azure-application-gateway"></a>Criar certificados para o back-end de lista de permissões com o Gateway de aplicativo do Azure

Para executar o SSL de ponta a ponta, o gateway de aplicativo requer as instâncias de back-end para estar na lista branca ao carregar/confiável para autenticação de certificados de raiz. No caso de SKU do v1, certificados de autenticação são necessários, enquanto que no caso de SKU do v2, trusted certificados raiz são necessários para a lista de permissões os certificados

Neste artigo, você aprenderá a:

> [!div class="checklist"]
>
> - Exportar o certificado de autenticação de um certificado de back-end (por SKU v1)
> - Exportar o certificado raiz confiável de um certificado de back-end (por SKU v2)

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de um certificado existente do back-end para gerar os certificados de autenticação ou certificados raiz confiáveis necessários para instâncias de back-end de lista de permissões com o gateway de aplicativo. O certificado de back-end pode ser o mesmo que o certificado SSL ou diferentes para aumentar a segurança. O gateway de aplicativo não oferece nenhum mecanismo para criar ou adquirir um certificado SSL. Para fins de teste, você pode criar um certificado autoassinado, mas você não deve usá-lo para cargas de trabalho de produção. 

## <a name="export-authentication-certificate-for-v1-sku"></a>Exportar o certificado de autenticação (para SKU v1)

Certificado de autenticação é necessário para colocar instâncias de back-end na v1 do gateway de aplicativo SKU. Certificado de autenticação é a chave pública dos certificados de servidor de back-end em Base 64 x. 509 codificado (. Formato CER). Neste exemplo, usaremos um certificado SSL para o certificado de back-end e exportar sua chave pública a ser usado como a certificação de autenticação. Além disso, neste exemplo, usaremos a ferramenta Gerenciador de certificados do Windows para exportar os certificados necessários. Você pode optar por usar qualquer outra ferramenta, de acordo com sua conveniência.

Em seu certificado SSL, exporte o arquivo. cer de chave pública (não a chave privada). As etapas a seguir ajuda a exportar o arquivo. cer X.509 codificado do arquivo na Base 64 (. Formato CER) para o seu certificado:

1. Para obter um arquivo .cer do certificado, abra **Gerenciar certificados de usuário**. Localize o certificado, normalmente em 'Certificates - Current User\Personal\Certificates' e com o botão direito. Clique em **Todas as Tarefas** e, em seguida, em **Exportar**. Isso abre o **Assistente para Exportação de Certificados**. Se você não encontrar o certificado em Usuário\Pessoal\Certificados, pode ser que você tenha aberto acidentalmente “Certificados - Computador Local” em vez de “Certificados - Usuário Atual”. Se você quiser abrir o Gerenciador de Certificados no escopo do usuário atual usando o PowerShell, digite *certmgr* na janela do console.

   ![Exportação](./media/certificates-for-backend-authentication/export.png)

2. No Assistente, clique em **Avançar**.

   ![Exportar o certificado](./media/certificates-for-backend-authentication/exportwizard.png)

3. Selecione **Não exportar a chave privada** e clique em **Avançar**.

   ![Não exportar a chave privada](./media/certificates-for-backend-authentication/notprivatekey.png)

4. Na página **Exportar Formato de Arquivo**, selecione **X.509 codificado em Base 64 (.CER).** e clique em **Avançar**.

   ![Codificado em Base 64](./media/certificates-for-backend-authentication/base64.png)

5. Para o **Arquivo a ser Exportado**, use **Procurar** para encontrar a localização para a qual você deseja exportar o certificado. Em **Nome do arquivo**, dê um nome ao arquivo de certificado. Em seguida, clique em **Avançar**.

   ![Procurar](./media/certificates-for-backend-authentication/browse.png)

6. Clique em **Concluir** para exportar o certificado.

   ![Concluir](./media/certificates-for-backend-authentication/finish.png)

7. O certificado foi exportado com êxito.

   ![Sucesso](./media/certificates-for-backend-authentication/success.png)

   O certificado exportado é semelhante a este:

   ![Exportado](./media/certificates-for-backend-authentication/exported.png)

8. Se você abrir o certificado exportado usando o Bloco de Notas, verá algo semelhante a este exemplo. A seção em azul contém as informações que são carregadas para o gateway de aplicativo. Se você abrir o certificado com o Bloco de Notas e ele não for semelhante a este, isso normalmente significa que o certificado não foi exportado usando o formato X.509 (.CER) codificado em Base-64. Além disso, se você quiser usar um editor de texto diferente, é importante saber que alguns editores podem introduzir formatação não intencional em segundo plano. Isso pode criar problemas ao fazer upload do texto desse certificado para o Azure.

   ![Abrir com Bloco de Notas](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>Exportar o certificado raiz confiável (por SKU v2)

Confiável do certificado raiz é necessário para instâncias de back-end de lista de permissões na v2 do gateway de aplicativo SKU. O certificado raiz é x. 509 codificado de Base 64 (. Certificado de raiz de formato CER) de certificados de servidor back-end. Neste exemplo, podemos será usar um certificado SSL para o certificado de back-end, exportar sua chave pública e, em seguida, exportar o certificado raiz da autoridade de certificação confiável da chave pública no formato codificado na base64 para obter o certificado raiz confiável. 

As etapas a seguir ajudarão você a exportar o arquivo. cer do certificado:

1. Use as etapas 1 a 9 mencionado na seção **certificado de autenticação de exportação de um certificado de back-end (para SKU v1)** acima para exportar a chave pública do seu certificado de back-end.

2. Depois que a chave pública foi exportada, abra o arquivo.

   ![Certificado de autorização aberta](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![sobre o certificado](./media/certificates-for-backend-authentication/general.png)

3. Mova para a exibição do caminho de certificação a autoridade de certificação.

   ![detalhes do certificado](./media/certificates-for-backend-authentication/certdetails.png)

4. Selecione o certificado raiz e clique em **Exibir certificado**.

   ![caminho do certificado](./media/certificates-for-backend-authentication/rootcert.png)

   Você deve ser capaz de exibir os detalhes do certificado raiz.

   ![informações de certificado](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. Mover para o **detalhes** exibir e clique em **copiar para arquivo...**

   ![certificado de raiz da cópia](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. Neste ponto, você extraiu os detalhes do certificado raiz do certificado de back-end. Você verá a **Assistente para exportação de certificado**. Agora use as etapas 2 a 9 mencionado na seção **certificado de autenticação de exportação de um certificado de back-end (para SKU v1)** acima para exportar a raiz confiável certificado na Base 64 x. 509 codificado (. Formato CER).

## <a name="next-steps"></a>Próximas etapas

Agora você tem a autenticação do certificado/confiável para o certificado raiz em Base 64 x. 509 codificado (. Formato CER). Você pode adicionar isso ao gateway de aplicativo à lista de permissões seus servidores de back-end para criptografia SSL de ponta a ponta. Ver [como configurar a criptografia SSL de ponta a ponta](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).