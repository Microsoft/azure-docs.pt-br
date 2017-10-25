---
title: Usar chaves SSH com Windows para VMs Linux | Microsoft Docs
description: "Saiba como gerar e usar chaves SSH em um computador Windows para se conectar a uma máquina virtual Linux no Azure."
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 2cacda3b-7949-4036-bd5d-837e8b09a9c8
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: danlep
ms.openlocfilehash: 66837a3a153cda041f5351c52c8ccb1f8ccfea50
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Como usar chaves SSH com o Windows no Azure
> [!div class="op_single_selector"]
> * [Windows](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> * [Linux/Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
>
>

Quando você se conecta a VMs (máquinas virtuais) Linux no Azure, é preciso usar [criptografia de chave pública](https://wikipedia.org/wiki/Public-key_cryptography) para fornecer uma maneira mais segura de fazer logon na VM Linux. Esse processo envolve uma troca de chaves públicas e privadas usando o comando SSH (secure shell) para autenticar a si mesmo em vez de um nome de usuário e uma senha. As senhas são vulneráveis a ataques de força bruta, especialmente em VMs voltadas para a Internet, como os servidores Web. Este artigo oferece uma visão geral das chaves SSH e explica como gerar as chaves apropriadas em um computador Windows.

## <a name="overview-of-ssh-and-keys"></a>Visão geral do SSH e das chaves
Você pode fazer logon com segurança na VM Linux usando chaves públicas e privadas:

* A **chave pública** é colocada na VM Linux ou em qualquer outro serviço que você queira usar com criptografia de chave pública.
* A **chave privada** é o que você apresenta à VM Linux quando faz logon, para verificação da sua identidade. Proteja essa chave privada. Não a compartilhe.

Essas chaves públicas e privadas podem ser usadas em vários serviços e VMs. Não é preciso um par de chaves para cada VM ou serviço que você queira acessar. Para obter uma visão mais detalhada, confira [criptografia de chave pública](https://wikipedia.org/wiki/Public-key_cryptography).

O SSH é um protocolo de conexão criptografada que permite logons seguros por conexões não protegidas. Ele é o protocolo de conexão padrão para as VMs Linux hospedadas no Azure. Embora o SSH em si forneça uma conexão criptografada, usar senhas com conexões SSH ainda deixa a VM vulnerável a ataques de força bruta ou à adivinhação de senhas. Um método mais seguro e preferencial de se conectar a uma VM usando SSH é usar essas chaves públicas e privadas, também conhecidas como chaves SSH.

Se não quiser usar chaves SSH, você ainda pode fazer logon em suas VMs Linux usando uma senha. Se a VM não for exposta à Internet, o uso de senhas pode ser suficiente. No entanto, é preciso gerenciar as senhas para cada VM Linux, bem como manter políticas e práticas íntegras de senha, como comprimento mínimo e atualização regular. O uso de chaves SSH reduz a complexidade do gerenciamento de credenciais individuais em várias VMs.

## <a name="windows-packages-and-ssh-clients"></a>Pacotes do Windows e clientes SSH
Você se conecta às VMs Linux e as gerencia no Azure usando um **cliente SSH**. Os computadores Windows geralmente não têm um cliente SSH instalado. A Atualização de Aniversário do Windows 10 adicionou o Bash for Windows, e a Atualização do Windows 10 para Criadores fornece atualizações adicionais. Esse Subsistema do Windows para Linux permite executar e acessar utilitários como um cliente SSH nativamente dentro de um shell do Bash. Você pode seguir qualquer um dos documentos do Linux, como [Gerar pares de chaves SSH para Linux](mac-create-ssh-keys.md). O Bash para Windows ainda está em desenvolvimento e é considerado uma versão beta. Para saber mais sobre o Bash para Windows, confira [Bash on Ubuntu on Windows](https://msdn.microsoft.com/commandline/wsl/about) (Bash no Ubuntu no Windows).

Se quiser usar algo diferente do Bash for Windows, entre os clientes do Windows SSH que pode instalar estarão os seguintes pacotes:

* [Git for Windows](https://git-for-windows.github.io/)
* [puTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [MobaXterm](http://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)


## <a name="which-key-files-do-you-need-to-create"></a>Quais arquivos de chave você precisa criar?
O Azure requer chaves públicas e privadas de pelo menos 2048 bits em formato **ssh-rsa**. Se estiver gerenciando recursos do Azure usando o modelo de implantação Clássico, você também precisará gerar um PEM (arquivo `.pem`).

Estes são os cenários de implantação e os tipos de arquivo que você usa em cada um deles:

1. As chaves **ssh-rsa** são exigidas para qualquer implantação que use o [portal do Azure](https://portal.azure.com) e implantações do Resource Manager que usem a [CLI do Azure](../../cli-install-nodejs.md).
   * Geralmente, essas chaves são tudo do que a maioria das pessoas precisa.
2. Um arquivo `.pem` é exigido para criar VMs que usam a implantação Clássica. Essas chaves têm suporte em implantações clássicas ao usar o [Portal do Azure](https://portal.azure.com) ou a [CLI do Azure](../../cli-install-nodejs.md).
   * Você só precisará criar esses certificados e chaves adicionais se estiver gerenciando recursos criados usando o modelo de implantação Clássico.

## <a name="install-git-for-windows"></a>Instalar o Git for Windows
A seção anterior lista vários pacotes que incluem a ferramenta `openssl` para Windows. Essa ferramenta é necessária para criar chaves públicas e privadas. Os exemplos a seguir detalham como instalar e usar o **Git para Windows**, embora você possa escolher qualquer pacote de sua preferência. O **Git para Windows** fornece acesso a alguns utilitários e ferramentas [OSS](https://en.wikipedia.org/wiki/Open-source_software) (software livre) adicionais que podem ser úteis enquanto trabalha com VMs Linux.

1. Baixe e instale o **Git for Windows** do seguinte local: [https://git-for-windows.github.io/](https://git-for-windows.github.io/).
2. Aceite as opções padrão durante o processo de instalação, a menos que, especificamente, tenha que alterá-las.
3. Execute **Git Bash** no **Menu Iniciar** > **Git** > **Git Bash**. O console é semelhante ao seguinte exemplo:

    ![Shell do Bash do Git para Windows](./media/ssh-from-windows/git-bash-window.png)

## <a name="create-a-private-key"></a>Criar uma chave privada
1. Na janela **Git Bash**, use `openssl.exe` para criar uma chave privada. O seguinte exemplo cria uma chave chamada `myPrivateKey` e um certificado chamado `myCert.pem`:

    ```bash
    openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 \
        -keyout myPrivateKey.key -out myCert.pem
    ```

    A saída deve ser semelhante ao seguinte exemplo:

    ```bash
    Generating a 2048 bit RSA private key
    .......................................+++
    .......................+++
    writing new private key to 'myPrivateKey.key'
    -----
    You are about to be asked to enter information that will be incorporated
    into your certificate request.
    What you are about to enter is what is called a Distinguished Name or a DN.
    There are quite a few fields but you can leave some blank
    For some fields there will be a default value,
    If you enter '.', the field will be left blank.
    -----
    Country Name (2 letter code) [AU]:
    ```

   Se o bash relatar um erro, tente abrir uma nova janela de **Bash do Git** com privilégios elevados. Em seguida, execute novamente o comando `openssl`.

2. Responda às solicitações de nome do país, localização, nome da organização etc.
3. A chave e o certificado novos são criados no diretório atual de trabalho. Como uma medida de segurança, defina as permissões em sua chave privada para que apenas você possa acessá-la:

    ```bash
    chmod 0600 myPrivateKey.key
    ```

4. A [próxima seção](#create-a-private-key-for-putty) detalha o uso do PuTTYgen tanto para exibir quanto para usar a chave pública, além de criar uma chave privada específica para usar PuTTY para SSH para VMs Linux. O comando a seguir gera um arquivo de chave pública denominado `myPublicKey.key` que você pode utilizar imediatamente:

    ```bash
    openssl.exe rsa -pubout -in myPrivateKey.key -out myPublicKey.key
    ```

5. Se você também precisa gerenciar recursos clássicos, converta `myCert.pem` em `myCert.cer` (certificado X509 codificado em DER). Execute essa etapa opcional apenas se precisar gerenciar especificamente recursos clássicos mais antigos.

    Converta o certificado usando o seguinte comando:

    ```bash
    openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer
    ```

## <a name="create-a-private-key-for-putty"></a>Criar uma chave privada para PuTTY
O PuTTY é um cliente SSH comum do Windows. Você pode usar qualquer cliente SSH que quiser. Para usar o PuTTY, é preciso criar um tipo de chave adicional — uma PPK (chave privada PuTTY). Se não quiser usar PuTTY, pule esta seção.

O exemplo a seguir cria essa chave privada adicional especialmente para uso de PuTTY:

1. Use **Git Bash** para converter a chave privada em uma chave privada RSA que PuTTYgen possa entender. O seguinte exemplo cria uma chave chamada `myPrivateKey_rsa` da chave existente chamada `myPrivateKey`:

    ```bash
    openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
    ```

    Como uma medida de segurança, defina as permissões em sua chave privada para que apenas você possa acessá-la:

    ```bash
    chmod 0600 myPrivateKey_rsa
    ```
2. Baixe e execute PuTTYgen do seguinte local: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
3. Clique no menu: **Arquivo** > **Carregar Chave Privada**
4. Localize sua chave privada (`myPrivateKey_rsa`, no exemplo anterior). O diretório padrão ao iniciar **Git Bash** é `C:\Users\%username%`. Altere o filtro de arquivo para mostrar **Todos os Arquivos (\*.\*)**:

    ![Carregar a chave privada existente em PuTTYgen](./media/ssh-from-windows/load-private-key.png)
5. Clique em **Abrir**. Um prompt indica que a chave foi importada com êxito:

    ![Chave importada com êxito para o PuTTYgen](./media/ssh-from-windows/successfully-imported-key.png)
6. Clique em **OK** para fechar o prompt.
7. A chave pública é exibida na parte superior da janela do **PuTTYgen**. Copie e cole essa chave pública no portal do Azure ou no modelo do Azure Resource Manager quando você cria uma VM Linux. Você também pode clicar em **Salvar chave pública** para salvar uma cópia no computador:

    ![Salvar o arquivo de chave pública PuTTY](./media/ssh-from-windows/save-public-key.png)

    O exemplo a seguir mostra como você pode copiar e colar essa chave pública no portal do Azure ao criar uma VM Linux. A chave pública normalmente é armazenada no `~/.ssh/authorized_keys` na sua nova VM.

    ![Usar chave pública quando você cria uma VM no portal do Azure](./media/ssh-from-windows/use-public-key-azure-portal.png)
8. De volta ao **PuTTYgen**, clique em **Salvar chave privada**:

    ![Salvar o arquivo de chave privada PuTTY](./media/ssh-from-windows/save-ppk-file.png)

   > [!WARNING]
   > Um prompt perguntará se você deseja continuar sem inserir uma frase secreta para a chave. Uma frase secreta é como uma senha anexada à sua chave privada. Mesmo que um indivíduo obtenha sua chave privada, ele não poderá se autenticar usando apenas a chave. Ele também precisa da frase secreta. Sem uma frase secreta, se um indivíduo obtiver a chave privada, ele poderá fazer logon em qualquer VM ou serviço que use essa chave. É recomendável criar uma frase secreta. No entanto, se você esquecer a senha, não é possível recuperá-la.
   >
   >

    Se quiser inserir uma frase secreta, clique em **Não**, insira uma frase secreta na janela principal de PuTTYgen e clique novamente em **Salvar chave privada**. Caso contrário, clique em **Sim** para continuar sem fornecer a frase secreta opcional.
9. Insira um nome e local para salvar o arquivo PPK.

## <a name="use-putty-to-ssh-to-a-linux-machine"></a>Usar Putty para SSH em um computador Linux
Repetindo, PuTTY é um cliente de SSH comum do Windows. Você pode usar qualquer cliente SSH que quiser. As etapas a seguir detalham como usar a chave privada para autenticação com sua VM do Azure usando o SSH. As etapas são semelhantes em outros clientes de chave SSH em termos de precisar carregar a chave privada para autenticar a conexão SSH.

1. Baixe e execute o putty do seguinte local: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2. Preencha o nome do host ou o endereço IP da sua VM no portal do Azure:

    ![Abrir nova conexão PuTTY](./media/ssh-from-windows/putty-new-connection.png)
3. Antes de selecionar **Abrir**, clique em **Conexão** > **SSH** > **guia Autenticação**. Procure e selecione a chave privada:

    ![Selecionar a Chave Privada PuTTY para autenticação](./media/ssh-from-windows/putty-auth-dialog.png)
4. Clique em **Abrir** para se conectar a sua máquina virtual

## <a name="next-steps"></a>Próximas etapas
Você também pode gerar as chaves públicas e privadas [usando OS X e Linux](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para saber mais sobre o Bash para Windows e os benefícios de ter ferramentas OSS prontamente disponíveis no seu computador Windows, confira [Bash on Ubuntu on Windows](https://msdn.microsoft.com/commandline/wsl/about) (Bash no Ubuntu no Windows).

Se você tiver problemas ao usar o SSH para se conectar às suas VMs Linux, confira [Solucionar problemas de conexão SSH com uma VM do Linux do Azure](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
