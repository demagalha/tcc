# tcc
Simplesmente um local para armazenar o **Trabalho de Conclusão de Curso** do aluno **André Demetrio de Magalhães**, da **Universidade Federal de Santa Catarina (UFSC) - Curso de Graduação em Engenharia Mecânica**.  Embora de responsabilidade e obrigação da coordenação do curso, o arquivo final não é depositado no repositório oficial da universidade, portanto aqui o deixo disponível para a eternidade.

>Este arquivo serve como um breve resumo onde levanto alguns pontos principais do trabalho. Ele não substitui o trabalho original, tanto que a maior parte das ideias aqui expostas não é acompanhada de citações ou no caso de equações, muitos dos termos não são explicados. Deve se procurar no Trabalho de Conclusão de Curso para maior esclarecimento.

## Ideia e Contexto
Perto do fim do curso de graduação, surgiu a necessidade de escolher um tema para o trabalho de conclusão de curso (TCC). Entre tantas opções, sempre me inclinei para temas relacionados ao método dos elementos finitos, dada minha familiaridade com essa abordagem durante as atividades de extensão e pesquisa ao longo da graduação. Após considerações, defini um tema que abordava "a implementação (em elementos finitos) de um modelo de campo de fase para otimização de topologia estrutural".

Ao discutir otimização no contexto matemático, é crucial definir a função custo/objetivo a ser minimizada. Dentro da engenharia, a mais comumente utilizada é a energia potencial elástica (conhecida como _strain energy_ em inglês ou também chamada de compliância estrutural) de uma estrutura em deformação, como uma viga, ponte, peça ou componente. Do ponto de vista físico, minimizar a compliância estrutural implica em obter uma estrutura o mais rígida possível, considerando as condições de contorno do problema (carregamentos e restrições de movimento).

**A compliância estrutural, sendo _σ_ o tensor tensão, _ε_ o tensor deformação e _u_ o deslocamento:**
<p align="center" width="100%">
    <img width="15%" src="https://latex.codecogs.com/svg.image?\int_{\Omega}\sigma(u):\epsilon(u)\;d\Omega"> 
</p>

> É comum encontrar também definido como a seguir, onde **f** são as forças de corpo e **T** as forças de superfície impostas. Uma simples análise da forma fraca das equações da elasticidade linear indica que são equivalentes.
<p align="center" width="100%">
    <img width="20%" src="https://latex.codecogs.com/svg.image?\int_{\Omega}\bold{f}\cdot\bold{u}\;d\Omega+\int_{\Gamma_N}\bold{T}\cdot\bold{u}\;d\Gamma"> 
</p>

No contexto da otimização de _topologia_, o objetivo é determinar a distribuição ótima de material dentro de um domínio predefinido. Isso implica em estabelecer uma restrição volumétrica e buscar a melhor configuração de material (seja sólido ou vazio) na estrutura, de modo a minimizar nossa função objetivo (compliância estrutural). Esse tipo de abordagem é interessante para a concepção de projetos mais eficientes e econômicos, pois permite a maximização do desempenho estrutural com o uso mínimo de material. Essa abordagem tem sido amplamente estudada e aplicada em diversas áreas da engenharia, especialmente com o o auxílio da manufatura aditiva (muitas vezes os resultados obtidos são difíceis de ser manufaturados com os métodos convencionais de fabricação, pois normalmente apresentam muitas ramificações e complexidades elevadas).

![exemplo topop](https://formlabs-media.formlabs.com/filer_public_thumbnails/filer_public/a0/1e/a01ef3f3-b0b3-4295-befb-dbd02a43c6a7/image4.png__1354x0_q85_subsampling-2.png)

Existem N diferentes métodos para otimização estrutural presentes na literatura, provavelmente o mais célebre é o _SIMP_ (do inglês "Solid Isotropic Material with Penalization"). A chave do método SIMP está na forma como as propriedades dos materiais são modeladas. Ele utiliza uma abordagem de penalidade para impor uma relação entre a densidade de material e a rigidez do elemento (já entrando no contexto dos elementos finitos). Isso significa que à medida que a densidade de material de um elemento diminui, sua rigidez também diminui e permitindo que a densidade de material seja tratada como uma variável de projeto (isto é, é definido  uma função densidade, com valor 0 representando vazio e 1 presença de material, definida em um espaço de elementos finitos). Sendo **C** o tensor de elasticidade (chamado de _elasticity tensor_, _stiffness tensor_ ou _elastic modulus tensor_ no inglês) do material penalizado, tem-se a relação entre a densidade do material e a do material não penalizado por:

<p align="center" width="100%">
    <img width="10%" src="https://latex.codecogs.com/svg.image?\bold{C}=\rho^p\bold{C_0}"> 
</p>

O valor de _p_ é um coeficiente arbitrário, em usual 2 ou maior. Para fins desse trabalho, foi considerado apenas casos de elasticidade linear isotrópica, bidimensional e estado plano de tensões. Isso significa que efetivamente penaliza-se somente o módulo de elasticidade e o coeficiente de poisson do material, já que são as propriedades envolvidas na definição do tensor. Com uso dessa forma de representação imposta pelo SIMP e usando-se, normalmente, análise de sensibilidade e critérios de otimalidade (_optimality criteria method_ (OCA) do inglês), a densidade do material é atualizada até que algum critério de convergência seja atingido (outros algoritmos de otimização como o MMA, SLP, podem ser utilizados para a atualização da densidade).

### Modelos de Campo de Fase
A Modelagem de campo de fase é uma abordagem utilizada em várias áreas da física e engenharia para descrever sistemas complexos que envolvem a evolução de interfaces entre diferentes fases. Em vez de rastrear diretamente a posição da interface, como em modelos de fronteira móvel, a modelagem de campo de fase atribui um campo de ordem (também chamado aqui por _parâmetro de ordem_, tradução literal de _order parameter_ do inglês) termodinâmica contínuo que varia suavemente através da interface entre as fases. Esse parâmetro de ordem, muitas vezes representado por uma variável de ordem chamada de "campo de fase", é governado por uma equação de evolução que descreve como a interface entre as fases se move ao longo do tempo. A modelagem de campo de fase é especialmente útil para estudar fenômenos como a solidificação de materiais, crescimento de grãos em sólidos policristalinos, fenômenos de interface em fluidos, entre outros.

<p align="center" width="100%">
    <img width="45%" src="/img/cahn2.gif"> 
</p>

A imagem acima mostra um fenômeno conhecido por decomposição espinodal. A decomposição espinodal é um fenômeno em que uma mistura homogênea de duas fases sofre uma separação espontânea em duas fases distintas. Isso ocorre devido a flutuações termodinâmicas no sistema que levam a uma instabilidade na mistura. Ao longo do tempo, essas flutuações crescem e as fases separadas se tornam cada vez mais distintas, resultando em uma microestrutura característica.

Pode-se indagar onde e como isso tem a ver com otimização estrutural. O fenômeno acima é modelado pela equação de _Cahn-Hilliard_.

<p align="center" width="100%">
    <img width="25%" src="https://latex.codecogs.com/svg.latex?%5Cfrac%7B%5Cpartial%20c%7D%7B%5Cpartial%20t%7D%3D%5Cnabla%5Ccdot%5Cleft%5BM%28c%29%5Cnabla%28f%28c%29-%5Clambda%5CDelta%20c%29%5Cright%5D"> 
</p>

Uma explicação mais detalhada é encontrada dentro do próprio trabalho. De forma geral, alguns pontos gerais: a solução da equação determina a evolução no tempo de _c_ (o parâmetro de ordem), onde 0 indica a presença de uma fase e 1 a presença de outra. Dadas condições de contorno e condição inicial para c (é uma equação dependente do tempo) , a solução é equivalente a minimizar o seguinte funcional energético.

<p align="center" width="100%">
    <img width="25%" src="https://latex.codecogs.com/svg.latex?min%20%5C%3B%20E%28c%29%3D%5Cint_%7B%5COmega%7D%28f%28c%29&plus;%5Cfrac%7B%5Clambda%7D%7B2%7D%7C%5Cnabla%20c%7C%5E2%29%5C%2Cd%5COmega%20%5C%3B..."> 
</p>

> ... dado as restrições do problema (restrição volumétrica)

O grande "pulo do gato" é resolver uma equação de Cahn-Hilliard um pouco modificada, onde se insira também no funcional energético a energia elástica proveniente das equações da elasticidade linear (a _compliance_, _"stored strain energy", etc, é a energia elástica armazenada durante a deformação). Então, ao resolver um sistema que engloba essa equação de Cahn-Hilliard modificada junto as equações da elasticidade linear, o resultado final obtido no tempo é um resultado ótimo do ponto de vista energético! E uma das parcelas de energia a ser minimizada é a que corresponde a _compliance_ da estrutura. Obviamente, não é tão simples como aparenta. Mas a ideia geral é essa, e bem melhor discutida no trabalho

![exemplo viga em formato L](/img/lshape.gif)

