# Implementação de código em Python para simulação de um JOGO DA VELHA em que o computador sempre vence ou empata.

# Importação de pacote.

import random

# Início da definição de variáveis.


def drawBoard(board):

    # Esta função imprime na tela as posições que deverão ser escolhidas pelo jogador, de baixo para cima, entre 1 e 9.

    print('   |   |')
    print(' ' + board[7] + ' | ' + board[8] + ' | ' + board[9])
    print('   |   |')
    print('-----------')
    print('   |   |')
    print(' ' + board[4] + ' | ' + board[5] + ' | ' + board[6])
    print('   |   |')
    print('-----------')
    print('   |   |')
    print(' ' + board[1] + ' | ' + board[2] + ' | ' + board[3])
    print('   |   |')


def inputjogletra():

    # Dá ao jogador a opção de escolher que letra utilizar.

    letra = ''
    while not (letra == 'X' or letra == 'O'):
        print('Qual letra você prefere, X ou O?')
        letra = input().upper()

    # O primeiro elemento da tupla é a letra do jogador, o segundo é a letra do computador.

    if letra == 'X':
        return ['X', 'O']
    else:
        return ['O', 'X']


def whoGoesFirst():

    # Escolhe aleatoriamente quem vai começar.

    if random.randint(0, 1) == 0:
        return 'computador'
    else:
        return 'jogador'


def playAgain():

    # Esta função reinicia o código caso o jogador opte por escolher o Sim.

    print('Você gostaria de recomeçar o jogo? (sim or não)')
    return input().lower().startswith('s')


def makeMove(board, letra, move):
    board[move] = letra


def isWinner(bo, le):

    # Dada as oções do tabuleiro, esta função retorna verdadeiro caso o jogador ganhe.

    return ((bo[7] == le and bo[8] == le and bo[9] == le)
            or (bo[4] == le and bo[5] == le and bo[6] == le)
            or (bo[1] == le and bo[2] == le and bo[3] == le)
            or (bo[7] == le and bo[4] == le and bo[1] == le)
            or (bo[8] == le and bo[5] == le and bo[2] == le)
            or (bo[9] == le and bo[6] == le and bo[3] == le)
            or (bo[7] == le and bo[5] == le and bo[3] == le)
            or (bo[9] == le and bo[5] == le and bo[1] == le))


def getBoardCopy(board):

    # Duplica o board do jogo e reapresenta.
    dupeBoard = []
    for i in board:
        dupeBoard.append(i)
    return dupeBoard


    # Retorna verdadeiro se ainda tiver espaço no tabuleiro para jogar.
def isSpaceFree(board, move):
    return board[move] == ' '


def getPlayerMove(board):
    # Let the player type in his move.
    move = ' '
    while move not in '1 2 3 4 5 6 7 8 9'.split() or not isSpaceFree(
            board, int(move)):
        print('Insira seu próximo movimento de 1-9')
        move = input()
    return int(move)


def chooseRandomMoveFromList(board, movesList):
    # Returns a valid move from the passed list on the passed board.
    # Returns None if there is no valid move.
    possibleMoves = []
    for i in movesList:
        if isSpaceFree(board, i):
            possibleMoves.append(i)

    if len(possibleMoves) != 0:
        return random.choice(possibleMoves)
    else:
        return None


def getComputerMove(board, completra):
    # Given a board and the computer's letter, determine where to move and return that move.
    if completra == 'X':
        jogletra = 'O'
    else:
        jogletra = 'X'

    # Here is our algorithm for our Tic Tac Toe AI:
    # First, check if we can win in the next move
    for i in range(1, 10):
        copy = getBoardCopy(board)
        if isSpaceFree(copy, i):
            makeMove(copy, completra, i)
            if isWinner(copy, completra):
                return i

    # Check if the player could win on his next move, and block them.
    for i in range(1, 10):
        copy = getBoardCopy(board)
        if isSpaceFree(copy, i):
            makeMove(copy, jogletra, i)
            if isWinner(copy, jogletra):
                return i

    # Try to take one of the corners, if they are free.
    move = chooseRandomMoveFromList(board, [1, 3, 7, 9])
    if move != None:
        return move

    # Try to take the center, if it is free.
    if isSpaceFree(board, 5):
        return 5

    # Move on one of the sides.
    return chooseRandomMoveFromList(board, [2, 4, 6, 8])


def isBoardFull(board):
    # Return True if every space on the board has been taken. Otherwise return False.
    for i in range(1, 10):
        if isSpaceFree(board, i):
            return False
    return True


print('Seja bem vindo ao jogo da velha.')
print('Desista enquanto é tempo...')

while True:
    # Resetar o tabuleiro.
    theBoard = [' '] * 10
    jogletra, completra = inputjogletra()
    turn = whoGoesFirst()
    print(' ' + turn + ' começará.')
    gameIsPlaying = True

    while gameIsPlaying:
        if turn == 'player':
            # Vez do jogador.
            drawBoard(theBoard)
            move = getPlayerMove(theBoard)
            makeMove(theBoard, jogletra, move)

            if isWinner(theBoard, jogletra):
                drawBoard(theBoard)
                print('Hooray! You have won the game!')
                gameIsPlaying = False
            else:
                if isBoardFull(theBoard):
                    drawBoard(theBoard)
                    print('The game is a tie!')
                    break
                else:
                    turn = 'computer'

        else:
            # Vez do computador.
            move = getComputerMove(theBoard, completra)
            makeMove(theBoard, completra, move)

            if isWinner(theBoard, completra):
                drawBoard(theBoard)
                print('O computador ganhou de você!')
                gameIsPlaying = False
            else:
                if isBoardFull(theBoard):
                    drawBoard(theBoard)
                    print('The game is a tie!')
                    break
                else:
                    turn = 'player'

    if not playAgain():
        break
