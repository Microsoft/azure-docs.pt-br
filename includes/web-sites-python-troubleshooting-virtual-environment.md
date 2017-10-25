O script de implantação vai ignorar a criação do ambiente virtual no Azure, caso detecte que um ambiente virtual compatível já existe.  Isso pode acelerar consideravelmente a implantação.  Pacotes que já estão instalados serão ignorados pelo pip.

Em determinadas situações, talvez você queira excluir forçadamente esse ambiente virtual.  Você desejará fazer isso se decidir incluir um ambiente virtual como parte do seu repositório.  Também convém fazer isso se você precisar se livrar de determinados pacotes ou testar alterações ao requirements.txt.

Há algumas opções para gerenciar o ambiente virtual existente no Azure:

### <a name="option-1-use-ftp"></a>Opção 1: usar o FTP
Com um cliente FTP, conecte-se ao servidor e você poderá excluir a pasta env.  Observe que alguns clientes FTP (como navegadores da web) podem ser somente leitura e não permitirão que você exclua pastas, portanto você deve certificar-se de usar um cliente FTP com esse recurso.  O usuário e o nome do host FTP são exibidos na folha de seu aplicativo Web no [Portal do Azure](https://portal.azure.com).

### <a name="option-2-toggle-runtime"></a>Opção 2: alternar o tempo de execução
Aqui está uma alternativa que tira proveito do fato de que o script de implantação excluirá a pasta env quando esta não corresponder à versão desejada do Python.  Isso excluirá efetivamente o ambiente existente e criará um novo.

1. Alternar para uma versão diferente do Python (por meio de runtime.txt ou da folha **Configurações do Aplicativo** no Portal do Azure)
2. use git push e faça algumas mudanças (ignore quaisquer eventuais erros de instalação de pip)
3. Alterne de volta para a versão inicial do Python
4. use git push e faça algumas mudanças novamente

### <a name="option-3-customize-deployment-script"></a>Opção 3: personalizar o script de implantação
Se você personalizou o script de implantação, você pode alterar o código em deploy.cmd para forçá-lo para excluir a pasta env.

