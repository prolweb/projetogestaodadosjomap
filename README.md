# projetogestaodadosjomap
Fork js13kgames, A short 3D action game competition.

Projeto implementado para ilustrar otmizações 3D  em javascript e aprendizado sobre as tecnologias envolvidas no game,
desde o processo de criação ate a hospedagem.
a ideia do game é apresentar um estudo sobre o protocolo HTTP, Request Response (lado cliente e sevidor), protocolos de segurança SSL e TLS,
lógica e linguagem de programação, Javascript, bibliotecas 3D e boas práticas de programação
Projeto cuminância alunos da escola JOMAP.


A historia do Game.

A maligna 404 Megacorp ameaça dominar a internet. Você deve detê-los!

Jogue aqui: https://js13kgames.com/entries/minipunk

Controles: WASD - mover Clique esquerdo - ataque Clique direito - zoom Barra de espaço - pular Dica: Tente zoom + ataque

captura de tela-1 captura de tela-2 captura de tela-3 captura de tela-4

Testado no Chrome, Firefox e Edge no Mac e Windows.

Galhos
submissionramo é o que foi enviado para a competição

masterramo inclui correções de bugs pós-competição

Desenvolvimento
A fonte é toda em JavaScript vanilla, que pode ser executado diretamente pelo navegador.

Use um servidor HTTP estático simples (ou seja, Python http.server ou NPM http-server ) para servir o diretório.

Abra index.htmlno seu navegador, comohttp://localhost:8000/index.html

Prédio
Requisitos:

wget (para baixar o Google Closure Compiler)
Java 7+ (para executar o Google Closure Compiler)
Ferramenta de Compressão Eficiente
Correr:

./build.sh
Que faz o seguinte:

Produz um arquivo de índice independente emdist/index.html
Produz um arquivo zip pronto para uso empublic.zip
Para testar, abra dist/index.htmlem seu navegador, comohttp://localhost:8000/dist/index.html

pós-morte
O projeto está feito! Aqui estão algumas notas e reflexões para qualquer futuro viajante do tempo que esteja procurando lições, dicas, truques ou explicações.

Contexto
Este projeto é derivado da minha entrada js13k de 2018, Battlegrounds , um pequeno clone do PUBG. Escrevi uma autópsia para aquele projeto, que pode ser um contexto útil para este projeto e esta autópsia.

A maior lição de 2018 foi a importância de boas impressões rápidas. Se o jogador não estiver engajado no primeiro minuto, você está perdendo. Em particular:

Jogos multiplayer são difíceis para game jams. Esperar em um lobby de servidor por outros estranhos da Internet não é divertido.
Deixe o jogador entrar em interações divertidas (IA, quebra-cabeças, etc.) o mais rápido possível.
Em 2019, iniciei uma entrada com base nessas ideias. Era brilhante, caricatural, um tanto Mario-esque:

screenshot-2019

Nesse projeto, houve três mudanças arquitetônicas:

Todo o código do servidor/multijogador foi arrancado
Todo o código de textura foi removido e substituído por sombreamento plano
O antigo gerador de mapas foi substituído por um mecanismo de voxel adequado
Infelizmente, a vida foi corrida em 2019 e não tive tempo de enviar😢

Essas mudanças ficaram em uma pasta acumulando poeira por um ano.

Escolhendo o projeto
O tema de "404" + debates contínuos sobre censura na Internet + o próximo lançamento de Cyberpunk 2077 = ideias conjuradas de um pequeno jogo com tema cyberpunk sobre uma megacorporação maligna tentando dominar a Internet.

Imaginei um jogo escuro, corajoso, com luzes neon brilhantes.

captura de tela cyberpunk captura de tela neon

Ferramentas
Continuo a ser um fanboy do Google Closure Compiler . Closure está mostrando sua idade e perdendo popularidade. Eu amo isso por alguns motivos:

Produz os melhores resultados de minificação possíveis
Ele usa javascript vanilla puro, então você pode executar o código diretamente no navegador sem transpilar
Não tenho uma pasta GB node_modules🤣
Eu uso um script bash simples build.shpara baixar e executar o compilador.

Este ano mudei de 7-Zip para ECT para criar o arquivo zip final. Esta foi uma escolha puramente pragmática, já que a ECT produz zíperes menores.

Noções básicas de WebGL e 3D
A principal filosofia de renderização 3D é baseada no conselho WebGL de Brandon Jones : descarregar o máximo de trabalho possível para a GPU. Para cada quadro, há apenas algumas chamadas de desenho:

Renderizar geometria estática (voxels e skybox)
Renderizar geometria dinâmica (jogador, inimigos, projéteis, etc)
Pós-processamento (efeito flor)
O resultado é um desempenho decente em tempo de execução com pouco código.

A matemática 3D é feita com uma versão simplificada de glMatrix , apenas vec3e mat4.

Motor Voxel
O jogo usa um mecanismo voxel simples semelhante ao Minecraft. Como o Minecraft, uma unidade voxel tem 1 metro. Ao contrário do Minecraft, os voxels não são quadrados. Em vez disso, os voxels têm 4 metros de largura, 4 metros de profundidade e 1 metro de altura, como um tijolo de lego:

tijolo de lego
https://camo.githubusercontent.com/e3c98b342206c7d2a52e39ddccf4621350f816df17621ae54547225732300d5e/68747470733a2f2f636f64792e6562626572736f6e2e636f6d2f6d696e6970756e6b2f73637265656e73686f74732f6c65676f2d627269636b2e6a7067

Isso cria um mundo mais em blocos, mas tem vários benefícios de desempenho. Isso acelera a geração processual (porque processa 1/16 do número de voxels) e a renderização (porque renderiza menos triângulos).

Em um mecanismo voxel real, você teria "pedaços" para manter a contagem de polígonos baixa. No Minipunk, há apenas um grande bloco e um grande buffer de vértice. Ao usar voxels 4x4x1, ele manteve o total de triângulos por quadro abaixo de 1 milhão.

O próprio mundo tem 512 metros de largura, 512 metros de profundidade e 256 metros de altura. Ou, em voxels, 128 voxels de largura, 128 voxels de profundidade e 256 voxels de altura.

Por motivos de desempenho e simplicidade, o mundo voxel é construído no carregamento da página e estático durante o restante da sessão. A geração processual constrói um gigante Uint8Array(512 * 512 * 256). Então todos os voxels são convertidos em triângulos. Faces invisíveis são descartadas.

Ele não faz otimização de span, o que ajudaria no desempenho do tempo de execução, mas custaria tempo e bytes. Talvez algo para a próxima vez.

Efeito Bloom
Para obter o efeito de brilho neon, gastei muito tempo implementando um sombreador Bloom .

florescer
https://camo.githubusercontent.com/97a886d9ff41139dc4d7754a27ef43d9a799192f906d1aabf8c9b7c516b5ae94/68747470733a2f2f636f64792e6562626572736f6e2e636f6d2f6d696e6970756e6b2f73637265656e73686f74732f626c6f6f6d2e706e67

A versão curta é:

Renderizar a cena normalmente
Filtre a imagem para manter as luzes e descartar todo o resto
borrão borrão borrão
Alpha mistura as luzes borradas de volta no topo da cena normal
Eu criei uma versão deste tutorial do OpenGL e o portei para o WebGL.

Estou satisfeito com os resultados, apesar de gastar muito tempo e muitos bytes na implementação.

Geração processual
Existe um bloco de código bastante simples que gera alguns quarteirões de cidade.

captura de tela do progen

A "cidade" tem aproximadamente 4 quarteirões por 4 quarteirões. Cada quarteirão da cidade tem 4 edifícios. Originalmente, eu esperava que os edifícios fossem mais variados em tamanho, forma e cor, mas, francamente, isso não importava muito.

O jogador começa no subsolo e navega por alguns obstáculos. Em seguida, o jogador sobe ao nível do solo e navega até o prédio do chefe. Os quarteirões subterrâneos e acima do solo são os mesmos.

A construção do chefe tem mais detalhes e complexidade. É um edifício de 3 níveis, entrada grande, pilares internos, piso quadriculado, escada nos fundos, etc. Há também um grande "404" brilhante para enfatizar 404 Megacorp e o tema 404.

A parte mais difícil de gerenciar a geração processual era fazer malabarismos com todas as constantes codificadas. O código é feio e nunca passaria na revisão de código🤣

Renderização e animação de personagens
Os personagens são Minecraft-ish:

Um cubo para uma cabeça
Um cubo para o corpo
Dois cubos para braços
Dois cubos para as pernas
Cubo opcional para espada ou faca
Os cubos são ligeiramente inclinados para ter dimensões diferentes na parte superior e inferior.

correndo oscilante
https://camo.githubusercontent.com/d5047a3517a42a4e1f0c6a5043c2c5fead8ca5ec4331e972a6311e0aeea6ff37/68747470733a2f2f636f64792e6562626572736f6e2e636f6d2f6d696e6970756e6b2f73637265656e73686f74732f6d696e6970756e6b2d72756e6e696e672e676966
https://camo.githubusercontent.com/4fdd185edf0d486de7158dcf2a93be054fa3a0ea0e31c849cb55973f2f931285/68747470733a2f2f636f64792e6562626572736f6e2e636f6d2f6d696e6970756e6b2f73637265656e73686f74732f6d696e6970756e6b2d7377696e67696e672e676966

A animação é toda feita algoritmicamente usando ondas senoidais e transformações simples. Estou bastante satisfeito com o resultado. As primeiras versões eram caixas não animadas, o que era profundamente insatisfatório. Comecei a extrair quadros-chave dos modelos Mixamo , mas os primeiros protótipos ocupavam muito espaço. Pesquisei o sistema Voidcall do PhobosLab , que era impressionante, mas eu queria mais controle, então optei pela abordagem algorítmica.

inimigos
Existem três tipos de inimigos no jogo:

punk soldado chefe
https://camo.githubusercontent.com/7a065052c6a36c3a3f4530de2d9ff92eccaadedaf57281102f7b2c5f848c0009/68747470733a2f2f636f64792e6562626572736f6e2e636f6d2f6d696e6970756e6b2f73637265656e73686f74732f70756e6b2e6a7067

https://camo.githubusercontent.com/6098ca75af0ce45b63ccd86c22e99cbbb30b6bf9ea6c0962ca4e33797d4b6afd/68747470733a2f2f636f64792e6562626572736f6e2e636f6d2f6d696e6970756e6b2f73637265656e73686f74732f736f6c646965722e6a7067

https://camo.githubusercontent.com/cd437c57efb4375bcc5985ca3e8c190535dad3bbdd83f60d1115ed92ef610b3b/68747470733a2f2f636f64792e6562626572736f6e2e636f6d2f6d696e6970756e6b2f73637265656e73686f74732f626f73732e6a7067

Os punks são rápidos e simples. Seu objetivo principal é ensinar ao jogador o básico do jogo e fornecer um aquecimento para os desafios maiores. Nas primeiras versões do jogo, os punks eram mais lentos que o jogador, então você podia passar direto por eles. Se você joga jogos Soulsborne, essa estratégia pode parecer normal e aceitável. No entanto, foi um dos aspectos menos apreciados nos testes iniciais, então tornei-os mais rápidos e fáceis.

Soldados disparam projéteis. Eles se movem lentamente. Eles travam em um alvo e disparam um segundo depois. Isso dá ao jogador a chance de se esquivar e fugir. Nos primeiros encontros, as matilhas têm apenas um soldado. Isso foi feito para ensinar a mecânica básica do jogador. Na sala do chefe, as coisas ficam mais caóticas.

E então há o chefe final. Esteticamente, eu queria algo como Wolfenstein 3D Hitler e Into the Spider-Verse Kingpin:

hitler chefão
https://camo.githubusercontent.com/d90e9f12cd59e14262518965bcbf9e196bacd0edc15657bba8cfb6aca2e01214/68747470733a2f2f636f64792e6562626572736f6e2e636f6d2f6d696e6970756e6b2f73637265656e73686f74732f626f73732d6869746c65722e706e67

https://camo.githubusercontent.com/f64c375115024b874c5c47ed2101d4b28672e5e396304d6430160efddaa80b80/68747470733a2f2f636f64792e6562626572736f6e2e636f6d2f6d696e6970756e6b2f73637265656e73686f74732f626f73732d6b696e6770696e2e6a7067

O objetivo era uma luta de chefe épica com mecânicas interessantes. Devido a restrições de bytes e tempo, o resultado final dificilmente é épico, mas ainda assim um desafio satisfatório. O chefe é lento e perigoso (um tiro mata). Ele está cercado por soldados, o que exige que o jogador continue se movendo. Ele tem um efeito knockback que acabou sendo mais irritante do que desafiador, mas ainda acrescentou variedade à experiência.

Meus planos originais incluíam uma terceira fase da luta contra o chefe, que envolvia pular de prédio em prédio. Ainda acho que teria sido divertido, mas simplesmente fiquei sem tempo e bytes.

efeito traço
https://camo.githubusercontent.com/8087d89166873fa5a9a617874281be8aa1293302bc6bb35a9167f985b25e7bf0/68747470733a2f2f636f64792e6562626572736f6e2e636f6d2f6d696e6970756e6b2f73637265656e73686f74732f6d696e6970756e6b2d646173682e676966

O ataque de espada básico é um ataque de espada básico: moderadamente eficaz, mas torna-se entediante rapidamente. Para tornar as coisas mais interessantes, adicionei um "ataque rápido" baseado na Halo Energy Sword. Se você ainda não jogou Halo, a espada opera em uma pequena e divertida mecânica: se o cursor estiver focado em um inimigo, ele fica vermelho e você pode realizar um ataque rápido em alta velocidade. Eu amo esse mecânico e estou muito satisfeito com o resultado.

traço

áudio
Usei uma versão simplificada do Sonant-X para gerar todos os efeitos sonoros e música. Especificamente, eliminei ainda mais a versão do Phoboslab de sua entrada de 2018, Underrun.

Não sou talentoso musicalmente, mas consegui juntar algo que soa bem. Peguei minha esposa cantarolando a música, o que sugeriu que a música seria boa🤣

Além das modificações impressionantes do Phoboslab, dei alguns passos adiante:

Estéreo de 2 canais convertido para mono de 1 canal
Substituiu os 4 osciladores básicos por 1 oscilador personalizado
LFO removido
Use um único globalAudioContext
Mutilou manualmente todos os nomes de variáveis
O oscilador personalizado foi um mergulho profundo e divertido em overdrive e distorção . Sinto como se estivesse apenas arranhando a superfície aqui e adoraria encontrar tempo para ir mais fundo. Música como Hyper do Cyberpunk 2077 deve ser possível sem muito código adicional.

A mutilação manual foi lamentável, porque a manutenção tornou-se virtualmente impossível depois. O Google Closure Compiler não conseguiu minificar as propriedades JSON sem refatoração pesada, então manual era a única opção. No entanto, economizou cerca de 250 bytes pós-minificação e pós-zip, o que valeu a pena.

Economia de espaço
Outras dicas de economia de espaço:

Achatando classes em globais. Encontre um equilíbrio entre a qualidade e o tamanho do código. Durante a maior parte do desenvolvimento, o projeto teve classes adicionais para Gameo estado, mas todas elas this.xse somam em comparação com x. Achei mais fácil manter as aulas de higiene até o final e depois nivelar em globais conforme necessário para o espaço.
Substituindo tudo constpor let. Mudança menor que rendeu economias saudáveis.
Cordas de sombreamento. Eu fiz isso manualmente, mas confira a postagem no blog de Elliot Nelson sobre ferramentas automatizadas.
Anotações do Google Closure Compiler. Adicione agressivamente os comentários JSDoc para @typee @constpara ajudar o Closure a fazer sua mágica.
Uma outra ideia que eu esperava investigar era construir uma versão de força mais industrial do RegPack . RegPack destina-se a js1k. Tecnicamente funciona com arquivos de até 4kb, mas na minha experiência fica lento e instável. Talvez algo para o próximo ano!

Conclusão
No final, estou satisfeito com o resultado do jogo. Como em qualquer projeto, há coisas que gostaria de ter acrescentado, coisas com as quais gostaria de não ter perdido tempo, etc. Agora é esperar a votação, os jurados e os resultados. Ao olhar para as inscrições enviadas até agora, não posso deixar de admirar o quão longe a competição chegou e o quanto a qualidade dos resultados melhora a cada ano.
