Devido ao desenvolvimento contínuo, a versão do SDK do Android instalada no Android Studio pode não corresponder à versão no código. O SDK do Android referenciado neste tutorial é a versão 23, a mais recente no momento desta edição. O número da versão pode aumentar à medida que aparecem novas versões do SDK e é recomendável usar a versão mais recente disponível.

Dois sintomas da incompatibilidade de versão são:

- Quando você compilar ou recompilar o projeto, você pode receber mensagens de erro de Gradle como "**Falha ao localizar o alvo Google Inc.:Google APIs:n**".
- Os objetos Android padrão no código que deve ser resolvido com base em instruções `import` podem gerar mensagens de erro.

Se um deles for exibido, a versão do SDK do Android instalado no Android Studio poderá não coincidir com o destino do SDK do projeto baixado. Para verificar a versão, faça as seguintes alterações:

1. No Android Studio, clique em **Ferramentas** > **Android** > **Gerenciador de SDK**. Se você não tiver instalado a versão mais recente da plataforma do SDK, clique para instalá-la. Anote o número da versão.
2. Na guia **Explorador de Projeto**, em **Scripts Gradle**, abra o arquivo **build.gradle (módulo: aplicativo)**. Verifique se **compileSdkVersion** e **buildToolsVersion** estão definidos para a versão do SDK mais recente instalada. As marcas podem ter esta aparência:

             compileSdkVersion 'Google Inc.:Google APIs:23'
            buildToolsVersion "23.0.2"
3. No Explorador de Projeto do Android Studio, clique com o botão direito do mouse no nó do projeto, escolha **Propriedades** e, na coluna esquerda, escolha **Android**. Verifique se a opção **Destino da Compilação do Projeto** está definida como a mesma versão do SDK que o **targetSdkVersion**.

No Android Studio, o arquivo de manifesto não é usado para especificar o SDK de destino e a versão mínima do SDK, ao contrário do que ocorre com o Eclipse.
