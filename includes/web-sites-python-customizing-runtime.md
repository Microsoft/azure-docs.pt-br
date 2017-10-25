O Azure determinará a versão do Python a usar para seu ambiente virtual com a seguinte ordem de prioridade:

1. versão especificada no runtime.txt na pasta raiz
2. versão especificada pela configuração do Python na configuração do aplicativo Web (a folha **Configurações** > **Configurações do Aplicativo** para seu aplicativo Web no Portal do Azure)
3. Python 2.7 é o padrão, se nenhuma das opções acima for especificada

Os valores válidos para o conteúdo de 

    \runtime.txt

são:

* python-2.7
* python-3.4

Se a versão micro (terceiro dígito) for especificada, ela será ignorada.

