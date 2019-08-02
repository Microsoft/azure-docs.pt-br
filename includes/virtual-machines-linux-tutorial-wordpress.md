---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 5df1f7ff44a1603dd03d1d803ae9960dc124781e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171929"
---
## <a name="install-wordpress"></a>Instalar o WordPress

Se você quiser experimentar sua pilha, instale um aplicativo de exemplo. Por exemplo, as etapas a seguir instalam a plataforma [WordPress](https://wordpress.org/) de código-fonte aberto para criar sites e blogs. Outras cargas de trabalho tentam incluir [Drupal](http://www.drupal.org) e [Moodle](https://moodle.org/). 

Esta instalação do WordPress destina-se apenas à prova de conceito. Para instalar o WordPress mais recente em produção com as configurações de segurança recomendadas, consulte a [Documentação do WordPress](https://codex.wordpress.org/Main_Page). 



### <a name="install-the-wordpress-package"></a>Instalar o pacote de WordPress

Execute o comando a seguir:

```bash
sudo apt install wordpress
```

### <a name="configure-wordpress"></a>Configurar WordPress

Configure o WordPress para usar PHP e MySQL.

Em um diretório de trabalho, crie um arquivo de texto `wordpress.sql` para configurar o banco de dados MySQL do WordPress: 

```bash
sudo sensible-editor wordpress.sql
```

Adicione os comandos a seguir, substituindo uma senha de banco de dados de sua escolha por *suaSenha* (não altere outros valores). Caso você tenha configurado uma política de segurança do MySQL para validar a força da senha, garanta que a senha atenda aos requisitos de força de senha. Salve o arquivo.

```sql
CREATE DATABASE wordpress;
GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER
ON wordpress.*
TO wordpress@localhost
IDENTIFIED BY 'yourPassword';
FLUSH PRIVILEGES;
```

Execute o comando a seguir para criar o banco de dados:

```bash
cat wordpress.sql | sudo mysql --defaults-extra-file=/etc/mysql/debian.cnf
```

Como o arquivo `wordpress.sql` contém as credenciais do banco de dados, exclua-o após o uso:

```bash
sudo rm wordpress.sql
```

Para configurar o PHP, execute o seguinte comando para abrir um editor de texto de sua preferência e criar o arquivo `/etc/wordpress/config-localhost.php`:

```bash
sudo sensible-editor /etc/wordpress/config-localhost.php
```
Copie as linhas a seguir para o arquivo, substituindo a senha do banco de dados do WordPress por *suaSenha* (não altere outros valores). Em seguida, salve o arquivo.

```php
<?php
define('DB_NAME', 'wordpress');
define('DB_USER', 'wordpress');
define('DB_PASSWORD', 'yourPassword');
define('DB_HOST', 'localhost');
define('WP_CONTENT_DIR', '/usr/share/wordpress/wp-content');
?>
```


Mova a instalação do WordPress para a raiz do documento do servidor Web:

```bash
sudo ln -s /usr/share/wordpress /var/www/html/wordpress

sudo mv /etc/wordpress/config-localhost.php /etc/wordpress/config-default.php
```

Agora você pode concluir a instalação do WordPress e publicar na plataforma. Abra um navegador e acesse `http://yourPublicIPAddress/wordpress`. Substitua o endereço IP público de sua VM. A página deve ser semelhante a esta imagem.

![Página de instalação do WordPress](./media/virtual-machines-linux-tutorial-wordpress/wordpressstartpage.png)