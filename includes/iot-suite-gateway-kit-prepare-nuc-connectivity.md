## <a name="prepare-your-intel-nuc"></a>Preparar o Intel NUC

Para concluir a configuração de hardware, você precisa:

- Conectar seu Intel NUC à fonte de alimentação incluída no kit.
- Conectar seu Intel NUC à sua rede usando um cabo Ethernet.

Agora você concluiu a configuração de hardware de seu dispositivo de gateway Intel NUC.

### <a name="sign-in-and-access-the-terminal"></a>Entre e acesse o terminal

Você tem duas opções para acessar um ambiente de terminal em seu Intel NUC:

- Se você tiver um teclado e um monitor conectado ao seu Intel NUC, você pode acessar o shell diretamente. As credenciais padrão são nome de usuário **raiz** e senha **raiz**.

- Acesse o shell em seu Intel NUC usando SSH em seu computador desktop.

#### <a name="sign-in-with-ssh"></a>Entre com o SSH

Para entrar com SSH, você precisa do endereço IP de seu Intel NUC. Se você tiver um teclado e um monitor conectado ao seu Intel NUC, use o comando `ifconfig` para localizar o endereço IP. Como alternativa, conecte-se ao seu roteador para listar os endereços de dispositivos em sua rede.

Entre com o nome de usuário **raiz** e a senha **raiz**.

#### <a name="optional-share-a-folder-on-your-intel-nuc"></a>Opcional: Compartilhar uma pasta em seu Intel NUC

Opcionalmente, talvez você queira compartilhar uma pasta em seu Intel NUC com seu ambiente de área de trabalho. Compartilhar uma pasta permite que você use seu editor de texto preferido de área de trabalho (como [Visual Studio Code](https://code.visualstudio.com/) ou [Sublime Text](http://www.sublimetext.com/)) para editar arquivos em seu Intel NUC em vez de usar `nano` ou `vi`.

Para compartilhar uma pasta com o Windows, configure um servidor Samba no Intel NUC. Como alternativa, use o servidor SFTP no Intel NUC com um cliente SFTP em seu computador desktop.
