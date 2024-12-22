# TIC-TAC-TOE
import math

# Constants for the players
PLAYER_X = "X"
PLAYER_O = "O"
EMPTY = " "

# Function to print the Tic-Tac-Toe board
def print_board(board):
    print("\n")
    for row in board:
        print(" | ".join(row))
        print("-" * 5)
    print("\n")

# Function to check if a player has won
def check_winner(board, player):
    # Check rows, columns, and diagonals
    for i in range(3):
        if all([cell == player for cell in board[i]]):  # Check row
            return True
        if all([board[j][i] == player for j in range(3)]):  # Check column
            return True
    
    # Check diagonals
    if all([board[i][i] == player for i in range(3)]):  # Check main diagonal
        return True
    if all([board[i][2 - i] == player for i in range(3)]):  # Check anti-diagonal
        return True

    return False

# Function to check if the board is full
def is_board_full(board):
    return all([cell != EMPTY for row in board for cell in row])

# Function to evaluate the board
def evaluate_board(board):
    if check_winner(board, PLAYER_O):
        return 1  # AI wins
    elif check_winner(board, PLAYER_X):
        return -1  # Player X wins
    else:
        return 0  # Draw or ongoing game

# Minimax function with alpha-beta pruning
def minimax(board, depth, is_maximizing, alpha, beta):
    score = evaluate_board(board)

    # If the game is over, return the score
    if score == 1:  # AI wins
        return score - depth
    if score == -1:  # Player X wins
        return score + depth
    if is_board_full(board):  # It's a draw
        return 0

    if is_maximizing:
        max_score = -math.inf
        # Try all possible moves for AI (Player O)
        for i in range(3):
            for j in range(3):
                if board[i][j] == EMPTY:
                    board[i][j] = PLAYER_O
                    score = minimax(board, depth + 1, False, alpha, beta)
                    board[i][j] = EMPTY
                    max_score = max(max_score, score)
                    alpha = max(alpha, score)
                    if beta <= alpha:  # Beta cut-off
                        break
        return max_score
    else:
        min_score = math.inf
        # Try all possible moves for Player X
        for i in range(3):
            for j in range(3):
                if board[i][j] == EMPTY:
                    board[i][j] = PLAYER_X
                    score = minimax(board, depth + 1, True, alpha, beta)
                    board[i][j] = EMPTY
                    min_score = min(min_score, score)
                    beta = min(beta, score)
                    if beta <= alpha:  # Alpha cut-off
                        break
        return min_score

# Function to get the best move for AI
def get_best_move(board):
    best_move = None
    best_value = -math.inf

    # Try all possible moves for AI (Player O)
    for i in range(3):
        for j in range(3):
            if board[i][j] == EMPTY:
                board[i][j] = PLAYER_O
                move_value = minimax(board, 0, False, -math.inf, math.inf)
                board[i][j] = EMPTY
                if move_value > best_value:
                    best_value = move_value
                    best_move = (i, j)

    return best_move

# Function to make a move for the player (human)
def player_move(board):
    while True:
        try:
            row, col = map(int, input("Enter your move (row, col) as two numbers (0-2): ").split())
            if board[row][col] == EMPTY:
                board[row][col] = PLAYER_X
                break
            else:
                print("This cell is already taken. Choose another one.")
        except (ValueError, IndexError):
            print("Invalid input. Please enter row and column values between 0 and 2.")

# Function to decide who plays first
def who_plays_first():
    choice = input("Do you want to play first? (yes/no): ").lower()
    if choice == "yes":
        return PLAYER_X
    else:
        return PLAYER_O

# Function to ask if the player wants to play again
def play_again():
    choice = input("Do you want to play again? (yes/no): ").lower()
    return choice == "yes"

# Main game loop
def play_game():
    print("Welcome to Tic-Tac-Toe!")
    
    # Let player choose who plays first
    current_player = who_plays_first()
    
    while True:
        board = [[EMPTY] * 3 for _ in range(3)]
        print_board(board)

        while True:
            if current_player == PLAYER_X:
                # Player's turn
                print("Your turn (Player X):")
                player_move(board)
                print_board(board)
                if check_winner(board, PLAYER_X):
                    print("Player X wins!")
                    break
                current_player = PLAYER_O  # Switch to AI
            else:
                # AI's turn
                print("AI's turn (Player O):")
                move = get_best_move(board)
                board[move[0]][move[1]] = PLAYER_O
                print_board(board)
                if check_winner(board, PLAYER_O):
                    print("AI wins!")
                    break
                current_player = PLAYER_X  # Switch to Player X
            
            if is_board_full(board):
                print("It's a draw!")
                break

        # Ask if the player wants to play again
        if not play_again():
            print("Thanks for playing!")
            break

# Start the game
if __name__ == "__main__":
    play_game()
