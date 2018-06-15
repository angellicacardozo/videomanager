# Video Manager

## Objetivo

Modelar a arquitetura de um elemento de interface que execute vídeos, em uma página web. O elemento de interface deve reagir à alguns eventos provocados pelo usuário final, tal como o scroll da página web. Nos casos em que o usuário realize o scroll sobre a página e o elemento de vídeo, o qual ele tenha iniciado uma execução, saia da perspectiva de visão, esse deve ser fixado em algum quadrante da tela.

[Figura 1 : Representação visual da proposta dos elementos de interface](images/fig1)

## Formalização

Quando trabalhamos com aplicações em JavaScript, devemos ter em mente que o planejamento acerca da arquitetura costuma nos tomar mais tempo do que imaginamos, principalmente quando queremos modelar os itens mais relevantes.

Escolher e definir padrões de projeto nos permite dividir partes de nossa aplicação em pequenos módulos. No entanto, durante a manutenção e evolução de um projeto, devemos nos questionar sobre questões básicas:

- Eu estou evoluindo o código de maneira que seja reutilizável?
- Minhas alterações provocam algum tipo de acoplamento entre módulos ou funcionalidades?
- A minha aplicação é sensível à falhas causadas por novas funcionalidades?
- Meu sistema pode ser testedado em suas pequenas unidades?

Portanto, quando trabalhamos no desenvolvimento e manutenção de aplicações robustas em JavaScript, devemos tem em mente as seguintes qualidades para nosso código: sustentável, extensível, flexível e reutilizável.

## Metodologia

Nesta fase apresentamos alguns detalhes de uma arquitetura para os componentes de interface apresentados anteriormente em **Objetivo**, bem como as trocas de estados durante detecção de scroll.

### Angular

A organização lógica dos componentes nos permite visualizar os elementos potencialmente agrupáveis. Quando trabalhamos com o Angular, por exemplo, esses grupos podem ser reunidos em diretivas.

Primeiro, identificamos os elementos que são utilizados repetidas vezes em diferentes áreas. Isso significa que é desejável ter diversas instâncias desses elementos disponíveis em qualquer área do nosso sistema.

Ao inspeciornamos visualmente as figuras 1 e 2, podemos notar que as informações dos vídeos se repetem nas diferentes áreas do site. Possuímos uma lista para vídeos mais assistidos como também para os vídeos mais recentes.

A figura 2 mostra um outro elemento, dessa vez único na tela, que é o modal de um vídeo em execução cuja a finalidade é manter-se fixo em algum quadrante da tela, permitindo que o usuário *gerencie* o vídeo que se encontrava em execução, antes do evento de scroll.

Logo, nossos candidatos são:
- Componente individual para o vídeo com título, descrição e preview de conteúdo
- Componente de gerência de vídeo para vídeos fora da área de visão do usuário
- Componentes para agrupar vídeos em diferentes listas

Como o objetivo do nosso trabalho é exibir vídeos e gerenciar a execução daqueles que fogem do campo de visão de um usuário, vamos focar nos dois primeiros componentes.

```
utils
    /directives
        - scroll-detect.directive.js
videos
    /directives
        - video-item.directive.js
        - video-manager.directive.js
    /views
        - video-item.html
        - video-manager.html
```

**scroll-detect.directive.js** : diretiva que agrega eventos associados ao scroll de tela em um elemento. Com ela, é possível detectar se um elemento está visível ou não para o usuário.

**video-item.directive.js** : representação mais simples de um elemento de vídeo. Fornece em uma view dados como o título, descritção e preview.

**video-manager.directive.js** : diretiva responsável por reunir os elementos video-item em uma tela e coordenar o estado de visão daquele que estiver em execução de acordo com a detecção do scroll.

**video-item.html** : estrutura html para suporte à diretiva **video-item.directive.js** na visualização do item de vídeo.

**video-manager.html** : estrutura html para suporte à diretiva **video-manager.directive.js** na visualização do modal de vídeo, de acordo com deteção do scroll.
