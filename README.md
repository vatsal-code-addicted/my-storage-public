import os
import random

def clear_screen():
    os.system('cls' if os.name == 'nt' else 'clear')

def print_board(board):
    clear_screen()
    print("\n")
    for i, row in enumerate(board):
        print(" | ".join(row))
        if i < 2:
            print("--+---+--")
    print("\n")

def check_winner(board, player):
    win_combos = [
        [board[0][0], board[0][1], board[0][2]],
        [board[1][0], board[1][1], board[1][2]],
        [board[2][0], board[2][1], board[2][2]],
        [board[0][0], board[1][0], board[2][0]],
        [board[0][1], board[1][1], board[2][1]],
        [board[0][2], board[1][2], board[2][2]],
        [board[0][0], board[1][1], board[2][2]],
        [board[0][2], board[1][1], board[2][0]],
    ]
    return [player]*3 in win_combos

def board_full(board):
    return all(cell in ['X', 'O'] for row in board for cell in row)

def get_available_moves(board):
    moves = []
    for i in range(3):
        for j in range(3):
            if board[i][j] not in ['X', 'O']:
                moves.append((i, j))
    return moves

def minimax(board, depth, is_maximizing):
    if check_winner(board, 'O'):
        return 1
    elif check_winner(board, 'X'):
        return -1
    elif board_full(board):
        return 0

    if is_maximizing:
        best_score = -float('inf')
        for row, col in get_available_moves(board):
            board[row][col] = 'O'
            score = minimax(board, depth + 1, False)
            board[row][col] = str(row * 3 + col + 1)
            best_score = max(score, best_score)
        return best_score
    else:
        best_score = float('inf')
        for row, col in get_available_moves(board):
            board[row][col] = 'X'
            score = minimax(board, depth + 1, True)
            board[row][col] = str(row * 3 + col + 1)
            best_score = min(score, best_score)
        return best_score

def get_computer_move(board):
    best_score = -float('inf')
    best_move = None
    for row, col in get_available_moves(board):
        board[row][col] = 'O'
        score = minimax(board, 0, False)
        board[row][col] = str(row * 3 + col + 1)
        if score > best_score:
            best_score = score
            best_move = (row, col)
    return best_move

def get_player_move(board):
    while True:
        try:
            move = int(input("Choose your position (1-9): "))
            if move < 1 or move > 9:
                raise ValueError
            row = (move - 1) // 3
            col = (move - 1) % 3
            if board[row][col] in ['X', 'O']:
                print("That spot is already taken. Try again.")
            else:
                return row, col
        except ValueError:
            print("Invalid input. Please enter a number from 1 to 9.")

def play_game():
    while True:
        board = [['1', '2', '3'], ['4', '5', '6'], ['7', '8', '9']]
        player_turn = True  # Player is 'X'
        game_over = False

        while not game_over:
            print_board(board)

            if player_turn:
                row, col = get_player_move(board)
                board[row][col] = 'X'
            else:
                print("Computer is thinking...")
                row, col = get_computer_move(board)
                board[row][col] = 'O'

            if check_winner(board, 'X'):
                print_board(board)
                print("🎉 You win!")
                game_over = True
            elif check_winner(board, 'O'):
                print_board(board)
                print("😞 Computer wins!")
                game_over = True
            elif board_full(board):
                print_board(board)
                print("It's a draw!")
                game_over = True
            else:
                player_turn = not player_turn

        play_again = input("Play again? (y/n): ").lower()
        if play_again != 'y':
            print("Thanks for playing!")
            break

if __name__ == "__main__":
    play_game()
