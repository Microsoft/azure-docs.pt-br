---
title: Tutorial – Introdução às Credenciais Verificáveis do Azure Active Directory usando um aplicativo de exemplo (versão prévia)
description: Neste tutorial, você aprenderá a emitir credenciais verificáveis usando nosso aplicativo de exemplo e o locatário de teste
ms.service: identity
ms.subservice: verifiable-credentials
author: barclayn
ms.author: barclayn
ms.topic: tutorial
ms.date: 04/01/2021
ms.openlocfilehash: deebaf31197d8b7335f887ae447f05add45278b2
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222878"
---
#  <a name="tutorial---get-started-with-azure-active-directory-verifiable-credentials-using-a-sample-app-preview"></a>Tutorial – Introdução às Credenciais Verificáveis do Azure Active Directory usando um aplicativo de exemplo (versão prévia)

Neste tutorial, abordaremos as etapas necessárias para emitir sua primeira credencial verificável: um cartão de especialista em credenciais verificado. Depois, você poderá usar esse cartão para provar a um verificador que você é um especialista em credenciamento verificado, com domínio da arte de credenciais digitais. Comece a usar as Credenciais Verificáveis do Azure Active Directory usando o aplicativo de exemplo de Credenciais Verificáveis para emitir sua primeira credencial verificável.

![Esta é uma imagem de um cartão de exemplo](media/get-started-verifiable-credentials/verifiedcredentialexpert-card.png)

> [!IMPORTANT]
> No momento, as Credenciais Verificáveis do Azure Active Directory estão em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

- [NodeJS](https://nodejs.org/en/download/) versão 10.14 ou superior instalada em nosso sistema de teste.
- Você precisará ter o [GIT](https://git-scm.com/downloads) instalado se quiser clonar o repositório que hospeda o aplicativo de exemplo:
- [Visual Studio Code](https://code.visualstudio.com/Download)
- Um sistema para hospedar nosso site de exemplo.
- Um dispositivo móvel com o Microsoft Authenticator versão 6.2005.3599 ou superior instalado.
- [NGROK](https://ngrok.com/) gratuito.

## <a name="download-the-sample-code"></a>Baixe o código de exemplo

Para emitir um cartão de especialista em credenciais verificado para si mesmo, execute um site no computador local. O site é usado para iniciar um processo de emissão de credenciais verificáveis. Fornecemos um site simples, escrito em NodeJS, que usamos em todo este tutorial.

Primeiro, baixe o código de exemplo do GitHub [aqui](https://github.com/Azure-Samples/active-directory-verifiable-credentials) ou clone o repositório no computador local:

```terminal
git clone https://github.com/Azure-Samples/active-directory-verifiable-credentials.git
```

O ideal é se familiarizar com o código nos sites de exemplo. A pasta `issuer` contém todo o código usado para emitir uma credencial verificável. Mais detalhes estão disponíveis no [Leiame](https://github.com/Azure-Samples/active-directory-verifiable-credentials) do exemplo.

## <a name="run-the-issuer-website"></a>Executar o site do emissor

Você pode executar as etapas no Visual Studio Code ou em qualquer linha de comando disponível no sistema operacional. 

1. Navegue até a pasta `issuer`. 

    ```terminal
    cd issuer
    ```

2. Nela, precisaremos instalar todos os pacotes necessários e iniciar o site.

   ```terminal
    npm install
    node app.js
    ```

3. No terminal, você verá que o seu aplicativo emissor está escutando a porta 8081. Agora, vamos configurar um proxy reverso com o ngrok, para que o Authenticator possa se comunicar com o seu aplicativo. 

## <a name="creating-a-reverse-proxy-with-ngrok"></a>Criando um proxy reverso com o ngrok

Quando você executa o site de exemplo, seu dispositivo precisa se comunicar com o servidor do Node em execução no computador local. Recomendamos usar o [ngrok](https://ngrok.com/) como uma forma fácil de disponibilizar o servidor de desenvolvimento local pela Internet.

1.  Depois que você baixar e extrair o **ngrok**, precisaremos executar:

    ```terminal
     ngrok http 8081
    ```

Por padrão, o site de exemplo é executado na porta `8081`. O **ngrok** gera duas URLs de encaminhamento para o servidor. Copie a URL com o prefixo `https://`.

![O ngrok ajuda a disponibilizar os pontos de extremidade do aplicativo pela Internet](media/get-started-verifiable-credentials/ngrok.png)

>[!NOTE] 
> Caso você esteja usando o PowerShell, talvez precise digitar `./ngrok` para que o comando seja reconhecido.

Agora que a porta local está exposta à Internet com o ngrok, o site de exemplo usa automaticamente o nome do host gerado por ele. Abra o navegador e navegue até a URL de encaminhamento HTTPS do ngrok. Você acessará com êxito a home page do site de exemplo. Se a página for aberta, o dispositivo poderá se comunicar com o aplicativo de exemplo em execução no servidor local. Agora você está pronto para emitir um cartão de especialista em credenciais verificado para si mesmo.

## <a name="issue-a-credential"></a>Emitir uma credencial

1. Instale o Authenticator no seu dispositivo móvel. O Microsoft Authenticator é usado para receber, armazenar e apresentar suas credenciais verificáveis às partes interessadas.

2. Em seguida, emita uma credencial verificável para você mesmo. **Clique** no botão **Obter Credencial**. Quando você clicar no botão **Obter Credencial**, o site de exemplo exibirá um código QR, que pode ser lido com o Authenticator. Se você vir o site do navegador no seu dispositivo móvel, o clique do botão **Obter Credencial** vai disparar um link profundo que abre o aplicativo Authenticator e não exigirá a leitura de um código QR.

   ![Botão Obter credencial](media/get-started-verifiable-credentials/credential-expert-get.png)

3. Leia o código QR do site usando o Authenticator ou, se estiver acessando o site por meio de um dispositivo móvel, clique no botão Obter credencial para disparar o link profundo. 

   ![Código QR ](media/get-started-verifiable-credentials/credential-expert-issue.png)

4. Observe que o botão **Adicionar** está esmaecido no momento. Escolha **Entrar na sua conta** abaixo da imagem do cartão.

   ![Entrar ](media/get-started-verifiable-credentials/add-verified-credential-expert.png)

5. Antes de você obter o cartão de especialista em credenciais, o locatário que estamos usando exigirá que você forneça informações de autenticação. Se esta for a primeira vez que você realiza esse tutorial e você não tiver uma conta de especialista em credenciais, crie uma conta de usuário em nosso locatário B2C.

   ![autenticar-se antes de continuar](media/get-started-verifiable-credentials/authenticate-credential-expert.png)

6. Depois que você estiver conectado, o botão **Adicionar** não ficará mais esmaecido. Escolha **Adicionar** para aceitar a nova VC.

   ![Escolha Adicionar após a autenticação](media/get-started-verifiable-credentials/add-verified-credential-expert-after-auth.png) 


7. Parabéns! Agora você tem uma VC de especialista em credenciais verificado.

   ![VC de especialista de credenciais adicionada](media/get-started-verifiable-credentials/credential-expert-add-card.png) 
 
Em seguida, será hora de verificar sua credencial.

## <a name="validate-credentials"></a>Validar credenciais

Agora que você concluiu a parte de emissão do tutorial e tem uma credencial verificável no Authenticator, é hora de validá-la no seu aplicativo verificador.

1.  Pare de executar o serviço ngrok do emissor.

    ```terminal
     control+c
    ```


2. Em outra janela de terminal, abra a pasta do aplicativo verificador e execute-a de maneira semelhante à forma como executamos o aplicativo emissor.

    ```terminal
     cd verifier
     npm install
     node app.js
    ```

3. Agora, execute o ngrok com a porta do verificador 8082.

    ```terminal
    ngrok http 8082
    ```

4. Abra a URL de encaminhamento HTTPS do ngrok no navegador e toque no botão **VERIFICAR CREDENCIAL**.  

   ![botão Verificar credencial de especialista](media/get-started-verifiable-credentials/prove-credential-expert.png)

5. Abra o Authenticator e leia o código QR.

   ![ler o código QR para verificar a credencial](media/get-started-verifiable-credentials/scan-verify.png)

  > [!IMPORTANT]
  > No iOS, ele está no canto superior direito e, no Android, no canto inferior direito. Leia o código QR.

6. Escolha **Permitir** na tela de nova solicitação de permissão do Authenticator. Ao pressionar Permitir, você está assinando uma apresentação verificável com a DID (identidade descentralizada) para provar que realmente controla essa Credencial Verificável.

   ![nova solicitação de permissão](media/get-started-verifiable-credentials/new-permission-request.png)

    Após uma apresentação bem-sucedida, três itens devem ter sido atualizados:

   1. A página da Web agora exibirá "Parabéns, seu nome" + é um especialista em credenciais verificado!".
   
    ![parabéns, verificar novamente](media/get-started-verifiable-credentials/congratulations.png)


   2. O terminal do aplicativo verificador também exibirá a mesma mensagem nos logs.
   
    ![atividade do aplicativo no prompt de comando](media/get-started-verifiable-credentials/cmd-verified-expert.png)

   3. No Authenticator, deve haver uma entrada para a atividade recente dessa apresentação.

    ![Atividade no Authenticator](media/get-started-verifiable-credentials/recent-activity.png)

   
>[!NOTE]
> Durante a execução do aplicativo verificador, o ngrok poderá parar de funcionar e exibir um erro informando que há um excesso de conexões. Descobrimos que isso pode ser evitado registrando sua conta no ngrok. 

## <a name="next-steps"></a>Próximas etapas

Agora que você concluiu com êxito o guia de início rápido, é hora de criar sua identidade descentralizada no serviço de credenciais verificáveis do Azure AD e emitir uma credencial verificável própria.

>[!div class="nextstepaction"] 
>[Configurar seu emissor usando o aplicativo de exemplo de credenciais verificáveis](./enable-your-tenant-verifiable-credentials.md)
