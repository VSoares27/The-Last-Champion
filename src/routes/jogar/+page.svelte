<script lang="ts">
    import { goto } from '$app/navigation';

    class Coordenada {
        linha: number;
        coluna: number;

        constructor(linha: number, coluna: number) {
            this.linha = linha;
            this.coluna = coluna;
        }
    }

    class EstadoJogo {
        posicaoPersonagem: Coordenada;
        posicaoPersonagemB: Coordenada;
        posicaoObjetivo: Coordenada;
        mapa: number[][];

        constructor() {
            this.posicaoPersonagem = new Coordenada(4, 0);
            this.posicaoPersonagemB = new Coordenada(6, 0);
            this.posicaoObjetivo = new Coordenada(5, 9);
            this.mapa = [
                [0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
                [0, 1, 1, 1, 1, 1, 1, 1, 1, 0],
                [0, 1, 1, 1, 1, 1, 1, 1, 1, 0],
                [0, 1, 1, 1, 1, 1, 1, 1, 1, 0],
                [0, 1, 1, 1, 1, 1, 1, 1, 1, 0],
                [1, 1, 1, 1, 1, 1, 1, 1, 1, 2],
                [0, 1, 1, 1, 1, 1, 1, 1, 1, 0],
                [0, 1, 1, 1, 1, 1, 1, 1, 1, 0],
                [0, 1, 1, 1, 1, 1, 1, 1, 1, 0],
                [0, 1, 1, 1, 1, 1, 1, 1, 1, 0],
                [0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
            ];
        }
    }

    class PeR {
        perguntas: string;
        respostas: string[];
        respostacorreta: string;

        constructor(perguntas: string, respostas: string[], respostacorreta: string) {
            this.perguntas = perguntas;
            this.respostas = respostas;
            this.respostacorreta = respostacorreta;
        }
    }

    class RepositorioDesafios {
        desafio: PeR[];
        dificuldade: boolean;

        constructor(desafio: PeR[], dificuldade: boolean) {
            this.desafio = desafio;
            this.dificuldade = dificuldade;
        }
    }

    function inicializarJogo(): EstadoJogo {
        return new EstadoJogo();
    }

    function iniciarQuiz() {
        alert("It's player 1 now!")
        const quizIndexA = Math.floor(Math.random() * perguntaseRespostas.desafio.length);
        const quizIndexB = Math.floor(Math.random() * perguntaseRespostas.desafio.length);

        perguntaAtualA = perguntaseRespostas.desafio[quizIndexA];
        perguntaAtualB = perguntaseRespostas.desafio[quizIndexB];

        quizAtivo = true;
        horadapergunta = true;
        jogadorRespondendo = '1'; // Jogador A começa respondendo

    }


    function verificarResposta(jogador: string, respostaSelecionada: string) {
    let perguntaAtual = jogador === '1' ? perguntaAtualA : perguntaAtualB;
    let respostaCorreta = perguntaAtual?.respostacorreta;

    if (respostaSelecionada === respostaCorreta) {
        alert(`Player ${jogador} answered correctly!`);
    } else {
        alert(`Player ${jogador} got it wrong! The correct answer was: ${respostaCorreta}. \nMove back 2 spaces.`);
        if(jogador === '1'){
            if (jogo.posicaoPersonagem.linha === 0) {
                jogo.posicaoPersonagem.coluna = Math.max(0, jogo.posicaoPersonagem.coluna - 2);
            } else if( jogo.posicaoPersonagem.coluna === 9){
                jogo.posicaoPersonagem.linha = Math.max(0, jogo.posicaoPersonagem.linha - 2);
            }else if(jogo.posicaoPersonagem.coluna === 0){
                jogo.posicaoPersonagem.linha = Math.max(0, jogo.posicaoPersonagem.linha + 2);
            }
        } else{
            if (jogo.posicaoPersonagemB.linha === 10) {
                jogo.posicaoPersonagemB.coluna = Math.max(0, jogo.posicaoPersonagemB.coluna - 2);
            }else if(jogo.posicaoPersonagem.coluna === 0){
                jogo.posicaoPersonagemB.linha = Math.max(0, jogo.posicaoPersonagemB.linha - 2);
            }else if(jogo.posicaoPersonagemB.coluna === 9) {
                jogo.posicaoPersonagemB.linha = Math.max(0, jogo.posicaoPersonagemB.linha + 2);
            }
        }
    }


    if (jogador === '1') {
        alert("It's Player 2 now!")
        jogadorRespondendo = '2'; 
    } else {
        quizAtivo = false;
        horadapergunta = false;
        vezDoJogadorA = true; // Jogador A começa a próxima rodada
        jogadorRespondendo = null;
    }
}



    function rolarDado(): number {
        return Math.floor(Math.random() * 6) + 1;
    }

    function houveColisao(posicao: Coordenada, jogo: EstadoJogo): boolean {
        return (posicao.linha < 0 || posicao.coluna < 0) ||
            (posicao.linha >= jogo.mapa.length || posicao.coluna >= jogo.mapa[1].length) ||
            jogo.mapa[posicao.linha][posicao.coluna] == 1;
    }

    function onKeyDown(evento: { keyCode: any }): void {
    const dado = rolarDado();
    if (horadapergunta) {
        // Impede o movimento dos jogadores durante o quiz
        return;
    }

    // Verifica se é a vez do Jogador A
    if (vezDoJogadorA && horadapergunta == false) {
        if (contadorJogadas >= dado) {
            alert("Oops, your stamina is gone. It's Player 2 time.");
            vezDoJogadorA = false;
            contadorJogadas = 0;
        } else {
            let novaPosicao = new Coordenada(jogo.posicaoPersonagem.linha, jogo.posicaoPersonagem.coluna);

            // Movimento do Jogador A
            if (evento.keyCode === 38) { // up
                if (novaPosicao.linha - 1 >= 0) {
                    novaPosicao.linha -= 1;
                }
            } else if (evento.keyCode === 39) { // right
                if (novaPosicao.coluna + 1 < jogo.mapa[0].length) {
                    novaPosicao.coluna += 1;
                }
            } else if (evento.keyCode === 40) { // down
                if (novaPosicao.linha + 1 < jogo.mapa.length) {
                    novaPosicao.linha += 1;
                }
            }

            // Verifica se há algum ponto onde não possa ir
            if (!houveColisao(novaPosicao, jogo)) {
                jogo.posicaoPersonagem = novaPosicao;
                contadorJogadas++; 
            }
        }
    } else if (!vezDoJogadorA && horadapergunta == false) { // Verifica se é a vez do player b
        if (contadorJogadas >= dado) {
            alert("Oops, Your stamina is gone too. It's time for Quiz.");
            vezDoJogadorA = false;
            contadorJogadas = 0;
            horadapergunta = true;

            iniciarQuiz()
        } else {
            let novaPosicaoB = new Coordenada(jogo.posicaoPersonagemB.linha, jogo.posicaoPersonagemB.coluna);

            if (evento.keyCode === 38) { // up
                if (novaPosicaoB.linha - 1 >= 0) {
                    novaPosicaoB.linha -= 1;
                }
            } else if (evento.keyCode === 39) { // right
                if (novaPosicaoB.coluna + 1 < jogo.mapa[0].length) {
                    novaPosicaoB.coluna += 1;
                }
            } else if (evento.keyCode === 40) { // down
                if (novaPosicaoB.linha + 1 < jogo.mapa.length) {
                    novaPosicaoB.linha += 1;
                }
            }

            // Verificação de colisão antes de mover
            if (!houveColisao(novaPosicaoB, jogo)) {
                jogo.posicaoPersonagemB = novaPosicaoB;
                contadorJogadas++;
            }
        }
    }

    // Verificar se um dos players conseguiu chegar ao final
    if (jogo.posicaoPersonagem.linha === jogo.posicaoObjetivo.linha && jogo.posicaoPersonagem.coluna === jogo.posicaoObjetivo.coluna) {
        alert("Congratulations Player 1, you finished the game. You are the Last Champion");
        goto("/");
    } else if (jogo.posicaoPersonagemB.linha === jogo.posicaoObjetivo.linha && jogo.posicaoPersonagemB.coluna === jogo.posicaoObjetivo.coluna) {
        alert("Congratulations Player 2, you finished the game. You are the Last Champion");
        goto("/");
    }
}

    // Variável responsável por receber as perguntas e respostas
    let perguntaseRespostas = new RepositorioDesafios(
    [
        new PeR("Quantos ossos tem no corpo humano?", ["126", "206", "18", "300", "200"], "206"),
        new PeR("Qual é o maior órgão do corpo humano?", ["Pele", "Fígado", "Pulão", "Cérebro"], "Pele"),
        new PeR("Qual a montanha mais alta do mundo?", ["Mauna Kea", "Dhaulagiri", "Monte Chimborazo", "Monte Everest", "Pico da Neblina"], "Monte Everest"),
        new PeR("Que país tem o formato de uma bota?", ["Butão", "Brasil", "Portugal", "Itália", "México"], "Itália"),
        new PeR("Qual o maior planeta do sistema solar?", ["Marte", "Lua", "Saturno", "Terra", "Júpiter"], "Júpiter"),
        new PeR("Depois do futebol, qual o esporte mais popular no Brasil?", ["Esqui", "Vôlei", "Hóquei no Gelo", "Golfe", "Esgrima"], "Vôlei"),
        new PeR("Em que região se localiza o estado de Minas Gerais?", ["Sudeste", "Centro-Oeste", "Norte", "Sul", "Nordeste"], "Sudeste"),
        new PeR("Quem foi a primeira pessoa a viajar no espaço?", ["Yuri Gagarin", "A Cadela Laika", "Neil Armstrong", "Marcos Pontes", "Buzz Aldrin"], "Yuri Gagarin"),
        new PeR("Qual é o menor país do mundo?", ["Mônaco", "San Marino", "Liechtenstein", "Vaticano", "Andorra"], "Vaticano"),
        new PeR("Quantos lados tem um hexágono?", ["4", "5", "6", "7", "8"], "6"),
        new PeR("Quem pintou a Mona Lisa?", ["Van Gogh", "Picasso", "Leonardo da Vinci", "Michelangelo", "Rembrandt"], "Leonardo da Vinci"),
        new PeR("Qual é o oceano que banha a costa leste do Brasil?", ["Atlântico", "Pacífico", "Índico", "Ártico", "Antártico"], "Atlântico"),
        new PeR("Qual destes animais é um mamífero?", ["Tubarão", "Pinguim", "Golfinho", "Polvo", "Cobra"], "Golfinho"),
        new PeR("Quantas cores tem o arco-íris?", ["5", "6", "7", "8", "9"], "7"),
        new PeR("Em que continente fica o Egito?", ["África", "Ásia", "Europa", "Oceania", "América"], "África"),
        new PeR("Quantas pernas tem uma aranha?", ["4", "6", "8", "10", "12"], "8"),
        new PeR("Qual destes é um instrumento de cordas?", ["Flauta", "Pandeiro", "Violino", "Trompete", "Bateria"], "Violino"),
        new PeR("Qual é a capital da França?", ["Paris", "Roma", "Londres", "Madri", "Berlim"], "Paris"),
        new PeR("Quantos planetas existem no Sistema Solar?", ["7", "8", "9", "10", "6"], "8"),
        new PeR("O que as abelhas produzem?", ["Mel", "Cera", "Pólen", "Néctar", "Geleia Real"], "Mel"),
        new PeR("Qual o maior felino do mundo?", ["Leão", "Puma", "Tigre", "Onça-pintada", "Leopardo"], "Tigre"),
        new PeR("Que animal é conhecido por sua capacidade de mudar de cor?", ["Camaleão", "Polvo", "Sapo", "Lula", "Lagarto"], "Camaleão"),
        new PeR("Qual desses países não faz parte da América do Sul?", ["Brasil", "Argentina", "Chile", "México", "Peru"], "México"),
        new PeR("O que é um animal onívoro?", ["Se alimenta só de carne", "Se alimenta só de plantas", "Se alimenta de carne e plantas", "Não come", "Come só frutas"], "Se alimenta de carne e plantas")
    ],
    true
)

    let jogadorRespondendo: '1' | '2' | null = null; // Variável que vai junto com a função do quiz para decidir quem está respondendo
    let contadorJogadas: number = 0
    let horadapergunta: boolean = false;
    let quizAtivo: boolean = false;
    let perguntaAtualA: PeR | null = null;
    let perguntaAtualB: PeR | null = null;
    let respostaSelecionada: string = "";
    let vezDoJogadorA: boolean = true;
    let jogo: EstadoJogo = inicializarJogo();
</script>

<div class="content">
    <h1 class="titulo">Move your Crown (Player 1) or Star (Player 2) for win this game.</h1>

    <table>
        {#each jogo.mapa as linha, i}
        <tr>
            {#each linha as celula, j}
                {#if i == jogo.posicaoPersonagem.linha && j == jogo.posicaoPersonagem.coluna}
                    <td class="personagem"></td>
                {:else if i == jogo.posicaoPersonagemB.linha && j == jogo.posicaoPersonagemB.coluna}
                    <td class="personagemb"></td>
                {:else if i == jogo.posicaoObjetivo.linha && j == jogo.posicaoObjetivo.coluna}
                    <td class="celula objetivo"></td>
                {:else if jogo.mapa[i][j] == 0}
                    <td class="celula"></td>
                {:else if jogo.mapa[i][j] == 1}
                    <td class="ncelula"></td>
                {:else if jogo.mapa[i][j] == 2}
                    <td class="celula objetivo icon"></td>
                {/if}
            {/each}
        </tr>
        {/each}
    </table>

    {#if quizAtivo}
    <div class="quiz-box">
        {#if jogadorRespondendo === '1'}
            <h3>{perguntaAtualA?.perguntas}</h3>
            <ul>
                {#each perguntaAtualA?.respostas as resposta}
                    <li>
                        <label>
                            <input type="radio" class="caixinhaderespostas" bind:group={respostaSelecionada} value={resposta} />
                            {resposta}
                        </label>
                    </li>
                {/each}
            </ul>
            <button on:click={() => verificarResposta('1', respostaSelecionada)}>Responder</button>
        {:else}
            <h3>{perguntaAtualB?.perguntas}</h3>
            <ul>
                {#each perguntaAtualB?.respostas as resposta}
                    <li>
                        <label>
                            <input type="radio" bind:group={respostaSelecionada} value={resposta} />
                            {resposta}
                        </label>
                    </li>
                {/each}
            </ul>
            <button on:click={() => verificarResposta('2', respostaSelecionada)}>Responder</button>
        {/if}
    </div>
{/if}

    <br/>

    <a class="menuj" href="/">Back to Main Menu</a>

</div>

<svelte:window on:keydown|preventDefault={onKeyDown} />
