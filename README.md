# SIMULADOR DE ATIVIDADES

Este projeto tem como objetivo analisar os dados de início e término previstos e as datas de início e término aproximadas de atividades de uma obra real de construção civil.

A partir dessa análise, será possível concluir, para a obra em questão, onde está concentrada a maior quantidade de atrasos e a distribuição aproximada desses atrasos para que seja possível construir um modelo que simule datas de início e término de atividades relacionadas à obras de construção civil.

## INTRODUÇÃO

### MOTIVAÇÃO

Em uma obra, costumamos programar o início de atividades estimando o término de outras atividades que a precedem. Mas uma das dificuldades é que, muitas vezes, a empresa que executa o serviço não consegue cumprir com a programação e, esse atraso, acaba por atrasar o início da outra atividade.

Outro ponto interessante é que não se costuma fazer uma análise mais criteriosa de atraso das atividades de uma obra. Geralmente essa análise é muito atrelada com a experiência do engenheiro de obra. Falta algo mais visual para auxiliar o gestor na hora de planejar as atividades para o próximo período.

### TECNOLOGIA

  - Python

### BIBLIOTECAS

  - Numpy;
  - Pandas;
  - Seaborn;
  - Matplotlib;
  - re (Regular Expressions);
  - xlrd;
  - Scipy;
  - Plotly.

## PRIMEIROS PASSOS

Os dados foram extraídos de tabelas mensais de atualização de datas de atividades de uma obra.

O fato de os dados serem reais e não terem o foco de serem utilizados para análises como esta, foi a maior dificuldade encontrada para realizar esse projeto. Isso porque foi necessário muita limpeza e manipulação de dados, além de que as planilhas não representam toda a duração da obra, ou seja, os dados são de apenas um período específico da obra.

![image](https://user-images.githubusercontent.com/95454600/204091367-ad9fbd37-aef0-4321-8b96-8ca98d78f8b2.png)

O primeiro passo foi importar as tabelas de todos os meses, concatená-las para criar uma única tabela e, depois disso, organizar as informações para pegar apenas os dados mais recentes.

Então os dados foram anonimizados e o dataframe final foi exportado para um arquivo .csv que será utilizado por este notebook.

## IMPORTANDO OS DADOS

Os dados importados, então, são os mesmos dados das tabelas manipuladas acima, mas de forma anonimizada e organizada por período.

Os dados, ao serem importados foram importados todos em um único formato:

![image](https://user-images.githubusercontent.com/95454600/204091855-a342f177-bd5b-48ca-b779-4345f73c07ed.png)

## TRATANDO OS DADOS

Através do método .info() utilizado acima, é possível observar que todos os dados estão com Dtype 'object'. É preciso que transformemos esses dados para o tipo correto.

### NÚMEROS DO TIPO FLOAT

A coluna 'Dur' contem a duração de cada atividade, mas contém a letra 'd' para representar a duração em dias após o número. Para transfomrarmos essa coluna em float, vamos utilizar regex para remover a letra 'd' e para substirtuir o separador decimal ',' por '.'.

![image](https://user-images.githubusercontent.com/95454600/204091911-a1e58682-e2ce-4006-8d7d-35c2b4f66e20.png)

Depois da tratativa acima, foi utilizado um .map(float) para transformar os dados para o tipo float.

### NÚMEROS DO TIPO INT

Temos apenas uma coluna do tipo int, que é a 'ID.', então vamos transformar esses dados.

A coluna 'ID.' não contém, necessariamente, a sequência executiva da obra, mas contém a sequência em que foi montado o cronograma e, por isso, é interessante ordenar a tabela de acordo com esse dado utilizando o método .sort_values().

### TIPO DATE_TIME

As datas foram transformadas utilizando pd.to_datetime().

![image](https://user-images.githubusercontent.com/95454600/204092075-9a190cc7-03f5-4e1a-815a-cca90e701228.png)

### DADOS TRANSFORMADOS

![image](https://user-images.githubusercontent.com/95454600/204092110-4b05349e-45de-41a4-b1fa-c67979268b00.png)

## REMOVENDO DADOS DESNECESSÁRIOS

O dataframe ainda contém alguns dados que podem atrapalhar no desenvolvimento da analise e do modelo e, por isso, vamos dropar algumas linhas.

Algumas das linhas contém o acumulado de datas marco de diversas atividades e outras são atividades que não afetam diretamente o cronograma da obra, ou seja, o atraso não é significativo.

Vamos começar removendo as atividades que não pertencem ao caminho crítico da obra. Essa limpeza será fácil, pois a coluna 'Critical' indica se a atividade está ou não no caminho crítico.

Agora iremos remover as atividades que não pertêncem aos andares Tipo, pois as atividades dos pavimentos tipo são as principais atividades do cronograma da obra.

![image](https://user-images.githubusercontent.com/95454600/204092176-65f8b6ea-0719-447f-b500-fd34c8188c32.png)


As colunas 'Início.1' e 'Fim.1' são datas reprogramadas das colunas 'Início' e 'Fim'. E as colunas 'Início.2' e 'Fim.2' são datas reprogamadas das colunas 'Início.1' e 'Fim.1.

Dessa forma, podemos ficar com apenas as colunas 'Início' e 'Fim', pois são datas programadas pelo cronograma inicial da obra, e com as colunas com datas reprogramadas que contenham a data mais tarde.

## CLASSIFICANDO OS DADOS

A fim de organizar as informações, foram criadas colunas contendo a torre (o arquivo possui dados de duas torres da obra), o pavimento e a atividade (usanod regex para retirar o número do andar da string descrição de atividades).

## ANÁLISE INICIAL

Após a classificação dos dados, fica mais fácil observar como as atividades se comportam diferentemente em um cronograma e na realidade.

### CRONOGRAMA POR ANDAR

Cronograma inicial da obra para o andar 37 da torre A:

![image](https://user-images.githubusercontent.com/95454600/204092429-4c8ef386-13aa-4bd0-abd4-16936f8002a8.png)

Cronograma real do andar 37:

![image](https://user-images.githubusercontent.com/95454600/204092446-bb192f52-c494-4603-aabb-4db0eeca49f6.png)

### CRONOGRAMA POR ATIVIDADE

Cronograma inicial para a atividade 'MIOLOS DE QUADROS ELÉTRICOS':

![image](https://user-images.githubusercontent.com/95454600/204092594-40c21576-88af-4aa2-ab1c-26d03c8336ee.png)

Cronograma real para a atividade 'MIOLOS DE QUADROS ELÉTRICOS':

![image](https://user-images.githubusercontent.com/95454600/204092632-145f352e-3b61-4d11-9948-ab4e5c1da056.png)

### CONTINUANDO A ANÁLISE

Com os cronogramas acima, é possível ver que, na realidade, as atividades não costumas seguir perfeitamente o planejamento inicial.

Aparentemente temos dois tipos comuns de atraso:
  - atraso de início;
  - e atraso de duração.

![image](https://user-images.githubusercontent.com/95454600/204093119-bc3a4952-6a3c-4dbe-abd1-1d8f80a46ba0.png)

## CRIANDO AS COLUNAS DE ATRASO

Agora, sabendo que as atividades possuem dois tipos de atrasos, vamos criar algumas colunas para poder estimar a probabilidade de uma atividade atrasar e a respectiva méida de atraso.

### PROBABILIDADES

Para estimar a probabilidade de uma atividade atrasar, criaram-se as colunas 'e_atraso_inicio' e 'e_atraso_dur'. Essas colunas representam, de forma booleana, se a atividade atrasou ou não.

Ao calcular a média dessa coluna, conseguimos estimar a probabilidade de uma atividade atrasar.

### MÉDIA DE ATRASO NA DURAÇÃO

As colunas 'atraso_inicio' e 'atraso_inicio.1' vão calcular, através da função np.busday_count(), o número de dias úteis entre o início da atividade e o fim da atividade predecessora.

Já a coluna 'atraso_inicio.2' calcula a diferença entre essas duas colunas, pois essa diferença em dias nem sempre pode representar um atraso, mas sim um tempo de 'descanço' entre uma atividade e outra.

## MODELO

O modelo foi criado, testado e analisado. Vale ressaltar que, em sua primeira versão, funciona bem para simular poucas atividades, mas não funiona para listas com muitas atividades diferentes, pois o modelo pressupõe que as atividades estão encadeadas de forma que uma só inicia a partir do término da anterior, ou seja, não há antecipação no início de atividades. Listas com grandes números de atividades irão gerar atrasos acumulados que fogem da realidade.

### WRAPPER

O modelo foi simplificado em um wrapper com 4 funções básicas que serão explicadas a seguir:
  - simular_atividade();
  - treinamento();
  - preenchimento_automatico();
  - e monte_carlo_ativ().

### simular_atividade()

Esta função recebe 4 argumentos: um dataframe de treinamento, um dataframe de teste, o nome do serviço que será analisado e a data de início prevista para esse serviço

O dataframe de treinamento deve conter as colunas:
    Descrição dos serviços;
    Início.1 (data de início real);
    Fim.1 (data de término real);
    Dur (duração da atividade em números inteiros)

O dataframe de teste deve conter as colunas abaixo e estar ordenado pelos andares que irão começar primeiro:
    Descrição dos serviços;
    Dur (duração da atividade em números inteiros)

A funçao irá retornar a data final da última atividade do dataframe de teste.

### treinamento()

Esta função irá retornar a média de atraso de início e a média de atraso de duração de uma determinada atividade.

Esta função deve receber um DataFrame com as colunas:
    Descrição dos serviços;
    Início.1 (data de início real);
    Fim.1 (data de término real);
    Dur (duração da atividade em números inteiros)

A função precisa receber um nome de atividade que apareça na coluna Descrição dos seviços e tabém uma data de início no formado YYYY-MM-DD.

### preenchimento_automatico()

Esta função recebe um dataframe e uma data de início e retorna um dataframe com as colunas de datas de início e término sem atrasos.

Serve para padronizar a ordem de início real das atividades e evitar que 'pular andares' seja considerado como atraso, mas sim como uma opção de planejamento da obra.

O dataframe de entrada deve conter uma coluna com a duração de cada atividade em números inteiros.

### monte_carlo_ativ()

Esta função recebe 5 argumentos: um dataframe de treinamento, um dataframe de teste, o nome do serviço que será analisado, a data de início prevista para esse serviço e o número de vezes que as atividades serão simuladas.

A função irá retornar um array com a data final da última atividade do dataframe de teste simulado n vezes.

### RESULTADO DO SIMULADOR

Resultado para 1.000 (mil) simulações da atividade 'ELEVAÇÃO TOTAL DA ALVENARIA' da Torre A, onde o ponto vermelho indica aproximadamente a data em que as atividades realmente teminaram.

![image](https://user-images.githubusercontent.com/95454600/204112016-d238f870-8af4-4578-a7c8-a9a7a10bdfb4.png)

## VALIDAÇÃO DOS RESULTADOS

É de conhecimento do autor, por experiência, que a empresa de pintura interna da Torre B possui um desempenho melhor (menos atrasos) que a empresa de pintura interna da Torre A.

Vamos, então, simular as atividades de 'MASSA PVA/ 1a DEMÃO DE PINTURA' em dois cenários:
  - Primeiro cenário: Vamos simular as atividades da Torre B com a distribuição de atrasos da empresa que fez a Torre A;
  - Segundo cenário: Vamos simular as atividades da Torre B com a distribuição de atrasos da empresa que está fazendo a Torre B.

### Primeiro cenário

Atividades da Torre B com a distribuição de atrasos da empresa da Torre A iniciando na data '2022-02-23' com mil simulações

![image](https://user-images.githubusercontent.com/95454600/204112563-3616dee2-602d-4d5c-bdf3-9219986c5c15.png)

### Segundo cenário

Atividades da Torre B com a distribuição de atrasos da empresa da Torre B iniciando na mesma data ('2022-02-23') com mil simulações

![image](https://user-images.githubusercontent.com/95454600/204112591-c4281f58-96ab-4493-9c01-3538f5075e42.png)

## CONCLUSÕES

A partir da análise acima, é possível concluir que o simulador consegue captar a diferença entre as médias de atraso entre as empresas da Torre A e da Torre B e transportar essa distribuição de atrasos para a simulação.

Com a análise dos dois cenários expostos acima, é possível observar que o modelo demonstra um significativo ganho na precisão do atraso de execução desse serviço ao utilizar a segunda empresa. A empresa 1 terminaria em média no mês 12/22, enquanto que a empresa 2 terminaria em média no mês 11/22 e com um desvio menor entre essa data.

Apesar de as datas não serem precisas, o modelo já é de grande ajuda na identificação de possíveis atrasos devido à produtividade de uma empresa.

Vale ressaltar que os dados utilizados possuem diversas inconsistências (andares faltando, datas não exatas etc.) e isso pode causar alterações significativas no cálculo das distribuições de atraso das atividades.

Dessa forma, se os dados forem coletados com mais precisão, é possível que o simulador tenha uma melhora considerável em suas estimativas.

Por fim, já é possível extrair diversos insights ao continuar a análise além das simulações, como:
  - Empresas que atrasam mais que outras (o que auxilia em contratações para próximas obras);
  - Atividades que possuem maior atraso em sua duração (pode ser um indicativo de que a produção estimada para a atividade é menor do que a realidade, servindo para melhorar a estimativa de duração da atividade no planejamento de próximas obras);
  - Ao utilizar dados mais completos e precisos, é possível que o modelo auxilie na tomada de decisão, como por exemplo, solicitar que o empreteiro reforce a equipe para aumentar a produção ou estimar melhor datas de início de atividades sucessoras;
  - etc.

## PRÓXIMOS PASSOS

Embora essa primeira versão do simulador já traga diversos insights para o usuário, é possível imaginar algumas tarefas que possam melhorar o modelo:
  - Coletar dados mais estruturados para serem usados no modelo a fim de melhorar suas simulações;
  - Criar novas ferramentas de visualização (como gráficos no Tableau ou exibição de média de atrasos por empresa/atividade) que facilitem a interpretação dessas simulações de forma mais simples;
  - Estruturar melhor o modelo para permitir a simulação de mais atividades encadeadas (sem perder a precisão e estimar datas absurdas).

## VERSIONAMENTO

EP 1.0

## AUTOR

Matheus Reali de Oliveira Fabricio (https://github.com/MatheusReali)
