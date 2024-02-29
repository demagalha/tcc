# tcc
Simplesmente um local para armazenar o **Trabalho de Conclusão de Curso** do aluno **André Demetrio de Magalhães**, da **Universidade Federal de Santa Catarina (UFSC) - Curso de Graduação em Engenharia Mecânica**.  Embora de responsabilidade e obrigação da coordenação do curso, o arquivo final não é depositado no repositório oficial da universidade, portanto [aqui](TCC_ANDRE_MAGALHAES.pdf) o deixo disponível para a eternidade.

>Este arquivo serve mais como uma coleção de pensamentos meus, onde levanto alguns pontos principais do trabalho e faço alguns comentários extras. Ele não substitui o trabalho original, tanto que a maior parte das ideias aqui expostas não é acompanhada de citações ou no caso de equações, muitos dos termos não são explicados. Deve-se procurar no Trabalho de Conclusão de Curso para maiores esclarecimentos.

## Ideia e Contexto
Perto do fim do curso de graduação, surgiu a necessidade de escolher um tema para o trabalho de conclusão de curso (TCC). Entre tantas opções, sempre me inclinei para temas relacionados ao método dos elementos finitos, dada minha familiaridade com essa abordagem durante as atividades de extensão e pesquisa ao longo da graduação. Após considerações, defini um tema que abordava "a implementação (em elementos finitos) de um modelo de campo de fase para otimização de topologia estrutural".

Ao discutir otimização no contexto matemático, é crucial definir a função custo/objetivo a ser minimizada. Dentro da engenharia, a mais comumente utilizada é a energia potencial elástica (conhecida como _strain energy_ em inglês ou também chamada de compliância estrutural) de uma estrutura em deformação, como uma viga, ponte, peça ou componente. Do ponto de vista físico, minimizar a compliância estrutural implica em obter uma estrutura o mais rígida possível, considerando as condições de contorno do problema (carregamentos e restrições de movimento).

**A compliância estrutural, sendo _σ_ o tensor tensão, _ε_ o tensor deformação e _u_ o deslocamento:**
<p align="center" width="100%">
    <a href="https://latex.codecogs.com/svg.image?\int_{\Omega}\sigma(u):\epsilon(u)\;d\Omega#gh-light-mode-only">
        <img width="15%" src="https://latex.codecogs.com/svg.image?\int_{\Omega}\sigma(u):\epsilon(u)\;d\Omega">
    </a>
    <a href="https://latex.codecogs.com/svg.image?{\color{Gray}\int_{\Omega}\sigma(u):\epsilon(u)\;d\Omega}#gh-dark-mode-only">
        <img width="15%" src="https://latex.codecogs.com/svg.image?{\color{Gray}\int_{\Omega}\sigma(u):\epsilon(u)\;d\Omega}">
    </a>
</p>

> É comum encontrar também definido como a seguir, onde **f** são as forças de corpo e **T** as forças de superfície impostas. Uma simples análise da forma fraca das equações da elasticidade linear indica que são equivalentes.
<p align="center" width="100%">
    <a href="https://latex.codecogs.com/svg.image?\int_{\Omega}\bold{f}\cdot\bold{u}\;d\Omega+\int_{\Gamma_N}\bold{T}\cdot\bold{u}\;d\Gamma#gh-light-mode-only">
        <img width="20%" src="https://latex.codecogs.com/svg.image?\int_{\Omega}\bold{f}\cdot\bold{u}\;d\Omega+\int_{\Gamma_N}\bold{T}\cdot\bold{u}\;d\Gamma">
    </a>
        <a href="\color{Gray}\int_{\Omega}\bold{f}\cdot\bold{u}\;d\Omega+\int_{\Gamma_N}\bold{T}\cdot\bold{u}\;d\Gamma}#gh-dark-mode-only">
        <img width="20%" src="\color{Gray}\int_{\Omega}\bold{f}\cdot\bold{u}\;d\Omega+\int_{\Gamma_N}\bold{T}\cdot\bold{u}\;d\Gamma}">
    </a>
</p>

No contexto da otimização de _topologia_, o objetivo é determinar a distribuição ótima de material dentro de um domínio predefinido. Isso implica em estabelecer uma restrição volumétrica e buscar a melhor configuração de material (seja sólido ou vazio) na estrutura, de modo a minimizar nossa função objetivo (compliância estrutural). Esse tipo de abordagem é interessante para a concepção de projetos mais eficientes e econômicos, pois permite a maximização do desempenho estrutural com o uso mínimo de material. Essa abordagem tem sido amplamente estudada e aplicada em diversas áreas da engenharia, especialmente com o auxílio da manufatura aditiva (muitas vezes os resultados obtidos são difíceis de ser manufaturados com os métodos convencionais de fabricação, pois normalmente apresentam muitas ramificações e complexidades elevadas).

![exemplo topop](https://formlabs-media.formlabs.com/filer_public_thumbnails/filer_public/a0/1e/a01ef3f3-b0b3-4295-befb-dbd02a43c6a7/image4.png__1354x0_q85_subsampling-2.png) 
>Imagem exemplo de otimização topológica acima tirada de [^1]

[^1]: Disponível em https://formlabs.com/blog/topology-optimization/

### Métodos de otimização topológica
Existem N diferentes métodos para otimização estrutural presentes na literatura, provavelmente o mais célebre é o _SIMP_ (do inglês "Solid Isotropic Material with Penalization"). A chave do método SIMP está na forma como as propriedades dos materiais são modeladas. Ele utiliza uma abordagem de penalidade para impor uma relação entre a densidade de material e a rigidez do elemento (já entrando no contexto dos elementos finitos). Isso significa que à medida que a densidade de material de um elemento diminui, sua rigidez também diminui e permitindo que a densidade de material seja tratada como uma variável de projeto (isto é, é definido  uma função densidade, com valor 0 representando vazio e 1 presença de material, definida em um espaço de elementos finitos). Sendo **C** o tensor de elasticidade (chamado de _elasticity tensor_, _stiffness tensor_ ou _elastic modulus tensor_ no inglês) do material penalizado, tem-se sua relação com a densidade do material e a do material não penalizado por:

<p align="center" width="100%">
    <a href="https://latex.codecogs.com/svg.image?\bold{C}=\rho^p\bold{C_0}#gh-light-mode-only">
        <img width="10%" src="https://latex.codecogs.com/svg.image?\bold{C}=\rho^p\bold{C_0}">
    </a>
    <a href="https://latex.codecogs.com/svg.image?\bold{C}=\rho^p\bold{C_0}#gh-dark-mode-only">
        <img width="10%" src="https://latex.codecogs.com/svg.image?{\color{Gray}\bold{C}=\rho^p\bold{C_0}}">
    </a>
</p>

O valor de _p_ é um coeficiente arbitrário, em usual 2 ou maior. Para fins desse trabalho, foi considerado apenas casos de elasticidade linear isotrópica, bidimensional e estado plano de tensões. Isso significa que efetivamente penaliza-se somente o módulo de elasticidade e o coeficiente de poisson do material, já que são as propriedades envolvidas na definição do tensor. Com uso dessa forma de representação imposta pelo SIMP e usando-se, normalmente, análise de sensibilidade e critérios de otimalidade (_optimality criteria method_ (OCA) do inglês), a densidade do material é atualizada até que algum critério de convergência seja atingido (outros algoritmos de otimização como o MMA, SLP, podem ser utilizados para a atualização da densidade).

### Modelos de Campo de Fase
A Modelagem de campo de fase é uma abordagem utilizada em várias áreas da física e engenharia para descrever sistemas complexos que envolvem a evolução de interfaces entre diferentes fases. Em vez de rastrear diretamente a posição da interface, como em modelos de fronteira móvel, a modelagem de campo de fase atribui um campo de ordem (também chamado aqui por _parâmetro de ordem_, tradução literal de _order parameter_ do inglês) termodinâmica contínuo que varia suavemente através da interface entre as fases. Esse parâmetro de ordem, muitas vezes representado por uma variável de ordem chamada de "campo de fase", é governado por uma equação de evolução que descreve como a interface entre as fases se move ao longo do tempo. A modelagem de campo de fase é especialmente útil para estudar fenômenos como a solidificação de materiais, crescimento de grãos em sólidos policristalinos, fenômenos de interface em fluidos, entre outros.

<p align="center" width="100%">
    <img width="45%" src="/img/cahn2.gif"> 
</p>

A imagem acima mostra um fenômeno conhecido por decomposição espinodal. A decomposição espinodal é um fenômeno em que uma mistura homogênea de duas fases sofre uma separação espontânea em duas fases distintas. Isso ocorre devido a flutuações termodinâmicas no sistema que levam a uma instabilidade na mistura. Ao longo do tempo, essas flutuações crescem e as fases separadas se tornam cada vez mais distintas, resultando em uma microestrutura característica.

Pode-se indagar onde e como isso tem a ver com a otimização estrutural. O fenômeno acima é modelado pela equação de _Cahn-Hilliard_.

<p align="center" width="100%">
    <a href="https://latex.codecogs.com/svg.latex?%5Cfrac%7B%5Cpartial%20c%7D%7B%5Cpartial%20t%7D%3D%5Cnabla%5Ccdot%5Cleft%5BM%28c%29%5Cnabla%28f%28c%29-%5Clambda%5CDelta%20c%29%5Cright%5D#gh-light-mode-only">
        <img width="25%" src="https://latex.codecogs.com/svg.latex?%5Cfrac%7B%5Cpartial%20c%7D%7B%5Cpartial%20t%7D%3D%5Cnabla%5Ccdot%5Cleft%5BM%28c%29%5Cnabla%28f%28c%29-%5Clambda%5CDelta%20c%29%5Cright%5D">
    </a>
    <a href="https://latex.codecogs.com/svg.latex?%7B%5Ccolor%7BGray%7D%5Cfrac%7B%5Cpartial%20c%7D%7B%5Cpartial%20t%7D%20%3D%20%5Cnabla%20%5Ccdot%20%5Cleft%5B%20M%28c%29%20%5Cnabla%20%28f%28c%29%20-%20%5Clambda%20%5CDelta%20c%29%20%5Cright%5D%7D#gh-dark-mode-only">
        <img width="25%" src="https://latex.codecogs.com/svg.latex?%7B%5Ccolor%7BGray%7D%5Cfrac%7B%5Cpartial%20c%7D%7B%5Cpartial%20t%7D%20%3D%20%5Cnabla%20%5Ccdot%20%5Cleft%5B%20M%28c%29%20%5Cnabla%20%28f%28c%29%20-%20%5Clambda%20%5CDelta%20c%29%20%5Cright%5D%7D">
    </a>
</p>

Uma explicação mais detalhada é encontrada dentro do próprio trabalho. De forma geral, alguns pontos gerais: a solução da equação determina a evolução no tempo de _c_ (o parâmetro de ordem), onde 0 indica a presença de uma fase e 1 a presença de outra. Dadas condições de contorno e condição inicial para c (é uma equação dependente do tempo) , a solução é equivalente a minimizar o seguinte funcional energético.

<p align="center" width="100%">
    <a href="https://latex.codecogs.com/svg.latex?min%20%5C%3B%20E%28c%29%3D%5Cint_%7B%5COmega%7D%28f%28c%29&plus;%5Cfrac%7B%5Clambda%7D%7B2%7D%7C%5Cnabla%20c%7C%5E2%29%5C%2Cd%5COmega%20%5C%3B...#gh-light-mode-only">
        <img width="30%" src="https://latex.codecogs.com/svg.latex?min%20%5C%3B%20E%28c%29%3D%5Cint_%7B%5COmega%7D%28f%28c%29&plus;%5Cfrac%7B%5Clambda%7D%7B2%7D%7C%5Cnabla%20c%7C%5E2%29%5C%2Cd%5COmega%20%5C%3B..."> 
    </a>
        <a href="https://latex.codecogs.com/svg.latex?%7B%5Ccolor%7BGray%7DE%28c%29%20%3D%20%5Cint_%7B%5COmega%7D%20%28f%28c%29%20&plus;%20%5Cfrac%7B%5Clambda%7D%7B2%7D%20%7C%5Cnabla%20c%7C%5E2%29%20%5C%2C%20d%5COmega%20%5C%3B...%7D#gh-dark-mode-only"">
        <img width="30%" src="https://latex.codecogs.com/svg.latex?%7B%5Ccolor%7BGray%7DE%28c%29%20%3D%20%5Cint_%7B%5COmega%7D%20%28f%28c%29%20&plus;%20%5Cfrac%7B%5Clambda%7D%7B2%7D%20%7C%5Cnabla%20c%7C%5E2%29%20%5C%2C%20d%5COmega%20%5C%3B...%7D"> 
    </a>
</p>

> ... dado as restrições do problema (restrição volumétrica por exemplo)

O "_link_" entre o modelo de campo de fase e a otimização topológica, acontece ao se resolver uma equação de Cahn-Hilliard um pouco modificada, onde se insira também no funcional energético a energia elástica proveniente das equações da elasticidade linear (a _compliance_, _"stored strain energy"_, etc, é a energia elástica armazenada durante a deformação). Então, ao resolver um sistema que engloba essa equação de Cahn-Hilliard modificada junto às equações da elasticidade linear, o resultado final obtido no tempo é um resultado ótimo do ponto de vista energético! E uma das parcelas de energia a ser minimizada é a que corresponde a _compliance_ da estrutura. Obviamente, não é tão simples como aparenta. Mas a ideia geral é essa, e bem melhor discutida no trabalho.

<p align="center" width="100%">
    <img width="45%" src="/img/cahn_elasticity.png"> 
</p>


>A ideia de resolver uma equação evolutiva remete a similaridades com o método _Level Set_ (de curva de nível). Ambos possuem muitos pontos em comum. O método _Level Set_, no entanto, não é capaz de "criar furos" ao longo da evolução da função _level set_.

Resolver o sistema acima significa resolver o seguinte problema de otimização:

<p align="center" width="100%">
    <a href="https://latex.codecogs.com/svg.latex?min%20%5C%3B%20%5Cwidetilde%7BE%7D%28c%2C%20u%29%20%3D%20%5Cint_%7B%5COmega%7D%20f%28c%29%20&plus;%20%5Cfrac%7B%5Clambda%7D%7B2%7D%20%5Cleft%7C%5Cnabla%20c%5Cright%7C%5E2%20&plus;%20%5Ceta%20%5Csigma%28c%2C%20u%29%20%3A%20%5Cepsilon%28u%29%20%5C%2C%20d%5COmega%20%5C%3B...#gh-light-mode-only">
        <img width="45%" src="https://latex.codecogs.com/svg.latex?min%20%5C%3B%20%5Cwidetilde%7BE%7D%28c%2C%20u%29%20%3D%20%5Cint_%7B%5COmega%7D%20f%28c%29%20&plus;%20%5Cfrac%7B%5Clambda%7D%7B2%7D%20%5Cleft%7C%5Cnabla%20c%5Cright%7C%5E2%20&plus;%20%5Ceta%20%5Csigma%28c%2C%20u%29%20%3A%20%5Cepsilon%28u%29%20%5C%2C%20d%5COmega%20%5C%3B...">
    </a>
    <a href="https://latex.codecogs.com/svg.latex?%7B%5Ccolor%7BGray%7D%5Cwidetilde%7BE%7D%28c%2C%20u%29%20%3D%20%5Cint_%7B%5COmega%7D%20f%28c%29%20&plus;%20%5Cfrac%7B%5Clambda%7D%7B2%7D%20%5Cleft%7C%5Cnabla%20c%5Cright%7C%5E2%20&plus;%20%5Ceta%20%5Csigma%28c%2C%20u%29%20%3A%20%5Cepsilon%28u%29%20%5C%2C%20d%5COmega%20%5C%3B%20...%7D#gh-dark-mode-only">
        <img width="45%" src="https://latex.codecogs.com/svg.latex?%7B%5Ccolor%7BGray%7D%5Cwidetilde%7BE%7D%28c%2C%20u%29%20%3D%20%5Cint_%7B%5COmega%7D%20f%28c%29%20&plus;%20%5Cfrac%7B%5Clambda%7D%7B2%7D%20%5Cleft%7C%5Cnabla%20c%5Cright%7C%5E2%20&plus;%20%5Ceta%20%5Csigma%28c%2C%20u%29%20%3A%20%5Cepsilon%28u%29%20%5C%2C%20d%5COmega%20%5C%3B%20...%7D">
    </a>
</p>

>... sujeito as restrição volumétrica do problema

A seguir, um exemplo da solução ao longo do tempo de uma viga em L, restrição volumétrica 0,4 do volume total, e com força unitária aplicada na extremidade direita. O parâmetro de ordem, c, que indica a presença de fases distintas, agora representa quando próximo de 0 o vazio (sem material) e quando 1 a presença de material.

![exemplo viga em formato L](/img/lshape.gif)

## Aspectos da implementação
O Método dos Elementos Finitos é um dos muitos métodos numéricos existentes para a solução aproximada de equações diferenciais. Praticamente um sinônimo do método (contínuo) de Galerkin, uma boa parte do trabalho foi dedicada a discussão sobre sua implementação aplicada ao problema proposto. Quando foi definido o tema, uma das possibilidades era fazer _toda_ implementação do FEM. Isto é, fazer _todo_ programa que envolveria todo pré-processamento, geração das _shape functions_, montagem das matrizes de rigidez e massa, aplicações das BCs, malha, solução, pós processamento. Uma ideia inicial poderia ser fazer o uso de [outro projeto já desenvolvido aqui](https://github.com/demagalha/IRACEMA). No entanto, um dos grandes empecilhos era o tempo: um semestre de 4 meses para implementar, testar, debugar, e modificar os códigos já existentes seria algo praticamente impossível. 

A alternativa encontrada foi utilizar _solvers_ de código livre existentes que "fariam o trabalho pesado", tais como FreeFem, FEniCS, etc. Restaria "somente" entrar com as formas fracas das equações e utilizar a linguagem de programação suportada por esses solvers para chamar as funções que montariam o problema de finitos e resolveriam o sistema de equações gerados.
>Na verdade não somente definir as formas fracas, mas sim toda uma gama de coisas: foi necessário definir qual método de integração no tempo escolher, além de valores para diversos parâmetros que acabariam por definir se alguns problemas levariam a um resultado desejado.

## Desvantagens
Uma das grandes desvantagens de métodos baseados em modelo de campo de fase está na sua convergência. A taxa de convergência é muitas vezes prejudicada e depende dos parâmetros escolhidos. O tempo empregado pode também se tornar demasiado, como visto no exemplo abaixo.

<p align="center" width="100%">
    <img width="45%" src="/img/ex_cahnelasticity.gif"> 
</p>

Outro problema presente é que, na topologia ótima, há a presença de um ângulo de contato perpendicular entre a borda do contorno e a configuração ótima (sendo portanto um fator limitador aos resultados obtidos). Isso porque as duas condições de contorno a seguir possuem uma interpretação geométrica.

<p align="center" width="100%">
    <img width="45%" src="/img/bcs.png"> 
</p>

As condições de contorno especificadas para o parâmetro de ordem c e o potencial químico μ indicam que o vetor normal n aponta para fora do domínio Ω. A segunda condição implica que o gradiente do parâmetro de ordem c é perpendicular à interface na fronteira. Essa condição representa uma situação onde não há fluxo de massa através da fronteira, o que geralmente corresponde a uma situação em que a interface não penetra na superfície sólida. Da mesma forma, a primeira representa uma condição onde o gradiente do potencial químico ∇μ também é perpendicular à interface na fronteira. 

No contexto do ângulo de contato, essas condições implicam que tanto o gradiente do parâmetro de ordem quanto o gradiente do potencial químico são perpendiculares à superfície sólida. O ângulo de contato pode então ser definido como o ângulo entre a tangente à interface e a superfície sólida. Como ambos os gradientes são perpendiculares à superfície sólida, a tangente à interface também é perpendicular à superfície sólida. Portanto, nesse cenário, o ângulo de contato seria 
90∘, indicando que a interface é perpendicular à superfície sólida na fronteira.

## Vantagens
Ao contrário de outros métodos clássicos de otimização topológica presentes na literatura (tal como o SIMP), o presente método não necessita de técnicas extras para regularização do problema de otimização (como o uso de filtros e restrições de raio mínimo para os furos - essas considerações são meio que resolvidas de forma implícita na formulação da equação do campo de fase). O método presente é um método bem mais "plug n play" do que outros métodos de otimização existentes. Isto é, "só" se faz necessário resolver a equação evolutiva do campo de fase. Uma pessoa com pouco conhecimento de algoritmos de otimização, mas com conhecimento de métodos numéricos para solução de equações diferenciais, é capaz de implementá-lo e obter um resultado ótimo ao problema de otimização topológica.

### Errata
>Dentro da conclusão desse trabalho, afirma-se que "Problemas típicos como dependência de malha e padrões tabuleiro não são presentes e o resultado é obtido como solução direta do acoplamento das equações da elasticidade linear na equação de Cahn-Hilliard". Essa primeira parte não faz o menor sentido. O refinamento da malha é fundamental para a convergência de métodos de Galerkin, sendo o principal preditor de erros na aproximação da solução. Portanto, é **óbvio** que a solução obtida é dependente da malha.

>Embora não haja tanto rigor com trabalhos acadêmicos de graduação como para dissertações de mestrado ou teses de doutorado, essa parte não foi apontada pela banca e passou batida mesmo após as correções... Me vejo na obrigação de apontar um erro tão grosseiro.

>Esse trabalho foi muito criticado pela banca devido "ao alto teor matemático e pouco de engenharia"... O que não concordo devido ao fato de que: o Método dos Elementos Finitos em específico foi, históricamente, em sua maior parte desenvolvido _para_ a engenharia e _por_ engenheiros. Além de que o tema do trabalho tem total relação com diversos assuntos em voga da engenharia. O "alto teor matemático" também não se sustenta porque: não há qualquer dedução de equações, definição, lemas, corolário, provas... que caracterizariam um trabalho exclusivamente matemático. Embora o uso desses artifícios, principalmente para demonstrar a dedução das equações utilizadas, serviriam muito bem para contextualizar a funcionalidade do método implementado.
