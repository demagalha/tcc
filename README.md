# tcc
Simplesmente um local para armazenar o **Trabalho de Conclusão de Curso** do aluno **André Demetrio de Magalhães**, da **Universidade Federal de Santa Catarina (UFSC) - Curso de Graduação em Engenharia Mecânica**.  Embora de responsabilidade e obrigação da coordenação do curso, o arquivo final não é depositado no repositório oficial da universidade, portanto aqui o deixo disponível para a eternidade.

## Ideia e Contexto
Ao finalizar o curso de graduação, surgiu a necessidade de escolher um tema para o trabalho de conclusão de curso (TCC). Entre tantas opções, sempre me inclinei para temas relacionados ao método dos elementos finitos, dada minha familiaridade com essa abordagem durante as atividades de extensão e pesquisa ao longo da graduação. Após considerações, defini um tema que abordava "a implementação (em elementos finitos) de um modelo de campo de fase para otimização de topologia estrutural".

Ao discutir otimização no contexto matemático, é crucial definir a função custo/objetivo a ser minimizada. Dentro da engenharia, a mais comumente utilizada é a energia potencial elástica (conhecida como _strain energy_ em inglês ou também chamada de compliância estrutural) de uma estrutura em deformação, como uma viga, ponte, peça ou componente. Do ponto de vista físico, minimizar a compliância estrutural implica em obter uma estrutura o mais rígida possível, considerando as condições de contorno do problema (carregamentos e restrições de movimento).

**A compliância estrutural, sendo _σ_ o tensor tensão, _ε_ o tensor deformação e _u_ o deslocamento:**
<p align="center" width="100%">
    <img width="15%" src="https://latex.codecogs.com/svg.image?\int_{\Omega}\sigma(u):\epsilon(u)"> 
</p>



No contexto da otimização de topologia, o objetivo é determinar a distribuição ótima de material dentro de um domínio predefinido. Isso implica em estabelecer uma restrição volumétrica e buscar a melhor configuração de material (seja sólido ou vazio) na estrutura, de modo a minimizar nossa função objetivo (compliância estrutural). Esse tipo de abordagem é interessante para a concepção de projetos mais eficientes e econômicos, pois permite a maximização do desempenho estrutural com o uso mínimo de material. Essa abordagem tem sido amplamente estudada e aplicada em diversas áreas da engenharia, como mencionado por Bendsoe e Sigmund (2003) em seu trabalho sobre otimização de topologia em design estrutural.

### Modelos de Campo de Fase

![exemplo viga em formato L](/img/lshape.gif)

