# Jogo da Velha em Python
# Desenvolvido e adaptado por Danilo Valim

import random

def drawPlaca(placa):
    # Esta função desenha as linhas horizontais e verticais do jogo na tela do computador.

    # A placa é uma lista de 10 strings
    print('   |   |')
    print(' ' + placa[7] + ' | ' + placa[8] + ' | ' + placa[9])
    print('   |   |')
    print('-----------')
    print('   |   |')
    print(' ' + placa[4] + ' | ' + placa[5] + ' | ' + placa[6])
    print('   |   |')
    print('-----------')
    print('   |   |')
    print(' ' + placa[1] + ' | ' + placa[2] + ' | ' + placa[3])
    print('   |   |')

def inputJogadorLetra():
    # Aqui o jogador deverá digita a letra que deseja usar no jogo
    # Retorna uma lista com a letra do jogador como o primeiro item e a letra do computador como o segundo.
    letra = ''
    while not (letra == 'X' or letra == 'O'):
        print('Você deve escolher uma destas letras para começar: X ou O?')
        letra = input().upper()

    # O primeiro elemento na tupla é a letra do jogador, o segundo é a letra do computador.
    if letra == 'X':
        return ['X', 'O']
    else:
        return ['O', 'X']

def quemVaiPrimeiro():
    # Escolhe aleatoriamente quem vai começar o jogo.
    if random.randint(0, 1) == 0:
        return 'computer'
    else:
        return 'player'

def jogarNovamente():
    # Esta função retorna True se o jogador quiser jogar novamente, caso contrário, retorna False.
    print('Você gostaria de jogar comigo novamente? (sim ou não)')
    return input().lower().startswith('s')

def makeMove(placa, letra, move):
    placa[move] = letra

def isWinner(pl, le):
    # Retorna Verdadeiro se o jogador tiver ganho.
    # pl e le são abreviações de placa e letra
    return ((pl[7] == le and pl[8] == le and pl[9] == le) or # no topo
    (pl[4] == le and pl[5] == le and pl[6] == le) or # no meio
    (pl[1] == le and pl[2] == le and pl[3] == le) or # no canto
    (pl[7] == le and pl[4] == le and pl[1] == le) or # em baixo lado esquerdo
    (pl[8] == le and pl[5] == le and pl[2] == le) or # em baixo no meio
    (pl[9] == le and pl[6] == le and pl[3] == le) or # em baixo lado direito
    (pl[7] == le and pl[5] == le and pl[3] == le) or # diagonal
    (pl[9] == le and pl[5] == le and pl[1] == le)) # diagonal

def getPlacaCopy(placa):
    # Faz uma cópia do tabuleiro e retorna preenchido
    dupePlaca = []

    for i in placa:
        dupePlaca.append(i)

    return dupePlaca

def isSpaceFree(placa, move):
    # Retorna verdadeiro se a jogada estiver livre no tabuleiro.
    return placa[move] == ' '

def getJogadorMove(placa):
    # Libera o jogador para digitar seu movimento.
    move = ' '
    while move not in '1 2 3 4 5 6 7 8 9'.split() or not isSpaceFree(placa, int(move)):
        print('Entre com seu próximo movimento? (1-9)')
        move = input()
    return int(move)

def chooseRandomMoveFromList(placa, movesList):
    # Retorna um movimento válido da lista de movimento do tabuleiro.
    # Retorna nulo se não houver um movimento válido.
    possibleMoves = []
    for i in movesList:
        if isSpaceFree(placa, i):
            possibleMoves.append(i)

    if len(possibleMoves) != 0:
        return random.choice(possibleMoves)
    else:
        return None

def getComputerMove(placa, computerLetra):
    # Com a letra do computador executada, etermine para onde mover.
    if computerLetra == 'X':
        playerLetra = 'O'
    else:
        playerLetra = 'X'

    # Aqui está o algoritmo de IA coração do nosso jogo:
    # Verifique se podemos vencer na próxima jogada.
    for i in range(1, 10):
        copy = getPlacaCopy(placa)
        if isSpaceFree(copy, i):
            makeMove(copy, computerLetra, i)
            if isWinner(copy, computerLetra):
                return i

    # Verifique se o jogador pode ganhar na próxima jogada e bloqueie.
    for i in range(1, 10):
        copy = getPlacaCopy(placa)
        if isSpaceFree(copy, i):
            makeMove(copy, playerLetra, i)
            if isWinner(copy, playerLetra):
                return i

    # Tente pegar um dos cantos e estiverem livres.
    move = chooseRandomMoveFromList(placa, [1, 3, 7, 9])
    if move != None:
        return move

    # Tente pegar o centro e estiver livre.
    if isSpaceFree(placa, 5):
        return 5

    # Mova para um dos lados.
    return chooseRandomMoveFromList(placa, [2, 4, 6, 8])

def isPlacaFull(placa):
    # Return True if every space on the board has been taken. Otherwise return False.
    for i in range(1, 10):
        if isSpaceFree(placa, i):
            return False
    return True


print('Seja bem vindo ao Jogo da Velha')

while True:
    # Resetando a placa do jogo
    thePlaca = [' '] * 10
    playerLetra, computerLetra = inputJogadorLetra()
    turn = quemVaiPrimeiro()
    print('O ' + turn + ' irá começar.')
    gameIsPlaying = True

    while gameIsPlaying:
        if turn == 'player':
            # A vez do jogador.
            drawPlaca(thePlaca)
            move = getJogadorMove(thePlaca)
            makeMove(thePlaca, playerLetra, move)

            if isWinner(thePlaca, playerLetra):
                drawPlaca(thePlaca)
                print('Uau! Você ganhou o jogo!')
                gameIsPlaying = False
            else:
                if isPlacaFull(thePlaca):
                    drawPlaca(thePlaca)
                    print('O jogo terminou empatado!')
                    break
                else:
                    turn = 'computer'

        else:
            # A vez co computador.
            move = getComputerMove(thePlaca, computerLetra)
            makeMove(thePlaca, computerLetra, move)

            if isWinner(thePlaca, computerLetra):
                drawPlaca(thePlaca)
                print('O computador venceu! Você perdeu.')
                gameIsPlaying = False
            else:
                if isPlacaFull(thePlaca):
                    drawPlaca(thePlaca)
                    print('O jogo empatou!')
                    break
                else:
                    turn = 'player'

    if not jogarNovamente():
        break

